## Taskboard Assessment Bug Report
**Tester: ** Abhishek Chahal

## BUG 1: Viewer is able to update the task.
**Priority:** P1
**Severity:** High
**Category:** Security / Authorization
**File:** src/app/api/tasks/[id]/route.ts
**Lines:** 16-38
**Description**: When a viewer role tries to update the task in Q3 launch project.
The task gets updated without any errors.
While, the expected is that, the application should not allow this operation. This is a violation of role-based access.

**Curl Proof:**
```bash
# 1. Get viewer token (dev@example.com is seeded as viewer on Q3 Launch)
curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"dev@example.com","password":"password123"}' | jq -r '.token'

# 2. PATCH a task using the viewer token (replace $TOKEN and $TASK_ID)
curl -s -X PATCH http://localhost:3000/api/tasks/$TASK_ID \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title":"Pwned by viewer"}' | jq .

# Expected: HTTP 403  {"error":"forbidden"}
# Actual:   HTTP 200  {"task":{...,"title":"Pwned by viewer",...}}
```

**Root cause:** The PATCH handler at [src/app/api/tasks/[id]/route.ts:16-38](src/app/api/tasks/%5Bid%5D/route.ts#L16-L38) authenticates the user but never calls `getProjectMembership` or `canEditTasks`. The sibling DELETE handler at lines 49–53 does this check correctly.


## BUG 2: SQL Injection in Task Search
**Priority:** P1
**Severity:** High
**Category:** Security / Injection
**File:** src/app/api/projects/[id]/tasks/route.ts
**Line:** 27-34
**Description:**
User-supplied q (the search query) is directly interpolated into a raw SQL string and executed with $queryRawUnsafe. A malicious value like %' OR '1'='1 would expose all tasks across all projects. $queryRaw with parameterised placeholders ($1, $2) is the fix.
const sql = `
  SELECT ...
  FROM tasks
  WHERE project_id = '${projectId}'
    AND (title ILIKE '%${q}%' OR description ILIKE '%${q}%')
  ORDER BY position ASC
`;
const tasks = await prisma.$queryRawUnsafe(sql);

**Curl Proof:**
```bash
# Login as dev (viewer — only authorized on Q3 Launch)
curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"dev@example.com","password":"password123"}' | jq -r '.token'

# Inject via q to break the project_id filter (replace $TOKEN and $PROJECT_ID)
# Payload ') OR (1=1) OR (title ILIKE ' causes the WHERE clause to evaluate TRUE
# for every row, leaking tasks from ALL projects including ones the user cannot access.
curl -s -G "http://localhost:3000/api/projects/$PROJECT_ID/tasks" \
  -H "Authorization: Bearer $TOKEN" \
  --data-urlencode "q=') OR (1=1) OR (title ILIKE '" | jq '.tasks | length'

# Expected: 0  (no task matches that literal string)
# Actual:   12 — all 7 Q3 Launch tasks + all 5 Onboarding tasks returned,
#           including tasks from a project dev@example.com is not a member of
```


## BUG 3: "Add" Button Silently Does Nothing on Empty Title
**Priority:** P3
**Severity:** Medium
**Category:** UX / Validation
**File:** src/app/projects/[id]/page.tsx:101
**Line:** 133-137
**Description**: When the title field is empty and the user clicks "Add," this line exits silently — no error message is shown, no card opens, no validation hint. The error state exists and is already wired to a display element at lines 133–137, but setError is never called in this path. A viewer or admin clicking "Add" on an empty field gets zero feedback.

> **Note:** No curl proof applies — this is a client-side validation gap in the React component. The API correctly returns `400 Bad Request` when `title` is missing; the front-end silently returns before ever reaching the API.


## BUG 4:  Password Hashes Leaked in API Response
**Priority:** P1
**Severity:** High
**Category:** Security / Data Exposure
**File:** src/app/api/projects/[id]/route.ts
**Line:** 28-36
**Description**: Every true include here pulls the full User row from the database, including passwordHash. Any authenticated project member who calls GET /api/projects/{id} receives the bcrypt password hashes of the project owner, every member, every task assignee, and every task creator in the JSON response.

**Curl Proof:**
```bash
# Login as any project member (replace $PROJECT_ID with the actual project ID)
curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"arjun@taskboard.dev","password":"password123"}' | jq -r '.token'

# GET the project — response contains bcrypt hashes for every user sub-object
curl -s http://localhost:3000/api/projects/$PROJECT_ID \
  -H "Authorization: Bearer $TOKEN" \
  | jq '{
      owner_hash:         .project.owner.passwordHash,
      member_hash:        .project.memberships[0].user.passwordHash,
      task_assignee_hash: .project.tasks[0].assignee.passwordHash
    }'

# Expected: all values null / fields absent
# Actual:   all populated with bcrypt hashes, e.g. "$2b$10$..."
```
