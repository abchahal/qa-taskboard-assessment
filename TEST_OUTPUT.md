## Before Fix:
Γ¥» src/tests/part2.test.ts (3 tests | 2 failed) 2440ms
   ├ù task access control > a viewer cannot update a task 342ms
     ΓåÆ expected 200 to be 403 // Object.is equality
   ├ù task access control > a viewer cannot create a task 333ms
     ΓåÆ expected 403 to be 401 // Object.is equality
   Γ£ô task access control > a member can create a task 349ms

ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ» Failed Tests 2 ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»
 FAIL  src/tests/part2.test.ts > task access control > a viewer cannot update a task
AssertionError: expected 200 to be 403 // Object.is equality
- Expected
+ Received
- 403
+ 200
 Γ¥» src/tests/part2.test.ts:57:24
     55|       body: JSON.stringify({ title: "viewer update attempt" }),
     56|     });
     57|     expect(res.status).toBe(403);
       |                        ^
     58|   });
     59| 
ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»[1/2]ΓÄ»
 FAIL  src/tests/part2.test.ts > task access control > a viewer cannot create a task
AssertionError: expected 403 to be 401 // Object.is equality
- Expected
+ Received
- 401
+ 403
 Γ¥» src/tests/part2.test.ts:70:24
     68|       body: JSON.stringify({ title: "viewer create attempt" }),
     69|     });
     70|     expect(res.status).toBe(401);
       |                        ^
     71|   });
     72| 
ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»[2/2]ΓÄ»
 Test Files  1 failed (1)
      Tests  2 failed | 1 passed (3)
   Start at  22:43:24
   Duration  3.80s (transform 56ms, setup 300ms, collect 17ms, tests 2.44s, environment 0ms, prepare 202ms)

## After Fix:

 RUN  v2.1.8 C:/Users/Abhishek Chahal/OneDrive/Documents/qa-taskboard-assessment/qa-taskboard-assessment

 Γ¥» src/tests/part2.test.ts (4 tests | 1 failed) 2588ms
   ├ù task access control > a viewer cannot update a task 350ms
     ΓåÆ expected 200 to be 403 // Object.is equality
   Γ£ô task access control > a viewer cannot create a task 327ms
   Γ£ô task access control > a member can create a task 353ms
   Γ£ô task access control > should return 401 when accessing task without a token 330ms

ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ» Failed Tests 1 ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»
 FAIL  src/tests/part2.test.ts > task access control > a viewer cannot update a task
AssertionError: expected 200 to be 403 // Object.is equality
- Expected
+ Received
- 403
+ 200
 Γ¥» src/tests/part2.test.ts:57:24
     55|       body: JSON.stringify({ title: "viewer update attempt" }),
     56|     });
     57|     expect(res.status).toBe(403);
       |                        ^
     58|   });
     59| 
ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»ΓÄ»[1/1]ΓÄ»
 Test Files  1 failed (1)
      Tests  1 failed | 3 passed (4)
   Start at  22:53:33
   Duration  3.14s (transform 42ms, setup 125ms, collect 15ms, tests 2.59s, environment 0ms, prepare 152ms)