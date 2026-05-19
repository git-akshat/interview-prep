## 🔹 PART 1: FULL MYSQL MOCK INTERVIEW  
**Rules**  
* Answer **in SQL or in words**, exactly how you would in an interview  
* If you don’t know, say *“don’t know”* — that’s better than guessing  
  
**Q1. Write a query to find the 3rd highest DISTINCT salary.**  
```
employees
id | name | department_id | salary

```
(No window functions first. Use classic SQL.)  
  
✍️ **Your answer:**  
```
select DISTINCT(salary)
From employees
Order By Salary Desc
Limit 1 OFFSET 2

```
  
## Question 2 (GROUP BY + HAVING)  
**Q2. **Find departments where **average salary is greater than overall average salary**.  
✍️ **Your answer:**  
```


```
  
## Question 3 (JOIN logic – common failure)  
**Q3. **Find employees who **do not belong to any department**.  
Assume:  
```
employees.department_id → departments.id

```
✍️ **Your answer:**  
```


```
##   
## Question 4 (Indexes – words, not SQL)  
**Q4. **You have an index:  
```
(department_id, salary)

```
This query is slow:  
```
SELECT *
FROM employees
WHERE salary > 50000;

```
👉 **Why is the index not helping?** 👉 **What would you change?**  
✍️ **Your answer (in words):**  
  
## Question 5 (Transactions – senior signal)  
**Q5. **Two concurrent transactions read the same row, compute a new value, and update it. One update is lost.  
👉 **What is this problem called?** 👉 **How do you fix it in MySQL?**  
✍️ **Your answer:**  
  
👉 **Stop here and answer Q1–Q5.** After that, I’ll evaluate like a real interviewer and score you.  
  
## 🔹 PART 2: REAL PRODUCTION SQL ANTI-PATTERNS  
(Read this even if interviews don’t ask — this saves careers.)  
  
## ❌ Anti-pattern 1: SELECT *  
```
SELECT * FROM employees;

```
**Why it’s bad**  
* Breaks when schema changes  
* Fetches unnecessary data  
* Prevents covering indexes  
✅ **Correct habit**  
```

SELECT id, name, salary FROM employees;

```
  
## ❌ Anti-pattern 2: Functions on indexed columns  
```

WHERE YEAR(created_at) = 2024;

```
**Why it’s bad**  
* Index is ignored  
* Full table scan  
✅ **Correct**  
```

WHERE created_at >= '2024-01-01'
AND created_at <  '2025-01-01';

```
  
## ❌ Anti-pattern 3: OFFSET pagination at scale  
```

LIMIT 20 OFFSET 100000;

```
**Why it’s bad**  
* MySQL scans and discards rows  
* Gets slower with page number  
✅ **Better**  
```

WHERE id > last_seen_id
ORDER BY id
LIMIT 20;

```
(cursor pagination)  
  
## ❌ Anti-pattern 4: No constraints, “we’ll handle it in code”  
```

-- no UNIQUE, no FK, no NOT NULL

```
**Result**  
* Duplicates  
* Orphan rows  
* Corrupt data  
✅ **Database must protect itself**  
```

UNIQUE
FOREIGN KEY
NOT NULL

```
  
## ❌ Anti-pattern 5: Business logic inside triggers  
```

-- salary rules
-- promotions
-- workflow logic

```
**Why it’s bad**  
* Hidden behavior  
* Impossible debugging  
* Tight DB coupling  
✅ **Use triggers only for**  
* audit  
* invariants  
* safety checks  
  
## ❌ Anti-pattern 6: One giant transaction  
```

START TRANSACTION;
-- 50 queries
-- API calls
-- sleeps
COMMIT;

```
**Why it’s bad**  
* Long locks  
* Deadlocks  
* Timeouts  
✅ **Rule**  
Transactions should be short and boring.  
  
## ❌ Anti-pattern 7: Index everything  
```

INDEX(name)
INDEX(salary)
INDEX(created_at)
INDEX(department_id)

```
**Why it’s bad**  
* Slow writes  
* High memory usage  
✅ **Index only**  
* frequently filtered columns  
* join columns  
* order-by columns  
  
## ❌ Anti-pattern 8: Trusting ORM blindly  
Hibernate will happily generate:  
```

SELECT * FROM employees;

```
or N+1 queries.  
✅ **Senior rule**  
ORMs are tools, not decision-makers.  
Always inspect SQL.  
  
##   
