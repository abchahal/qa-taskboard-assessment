## Taskboard Assessment Bug Report
**Tester: ** Abhishek Chahal

## BUG 1: Viewer is able to update the task.
**Priority:** P1
**Category:** Security
**File:** src/app/api/tasks/[id]/route.ts
**Lines:** 16-38
**Description**: When a viewer role tries to update the task in Q3 launch project.
The task gets updated without any errors.
While, the expected is that, the application should not allow this operation. This is a violation of role-based access.


## BUG 2: SQL Injection in Task Search
**Severity: ** High
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

## BUG 3: "Add" Button Silently Does Nothing on Empty Title
**Severity**: Medium
**File:** src/app/projects/[id]/page.tsx:101
**Line:** 133-137
**Description**: When the title field is empty and the user clicks "Add," this line exits silently — no error message is shown, no card opens, no validation hint. The error state exists and is already wired to a display element at lines 133–137, but setError is never called in this path. A viewer or admin clicking "Add" on an empty field gets zero feedback.

## BUG 4:  Password Hashes Leaked in API Response
**File:** src/app/api/projects/[id]/route.ts
**Line:**28-36
**Description**: Every true include here pulls the full User row from the database, including passwordHash. Any authenticated project member who calls GET /api/projects/{id} receives the bcrypt password hashes of the project owner, every member, every task assignee, and every task creator in the JSON response.


