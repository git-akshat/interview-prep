# ⚙️ MySQL Queries  
  
  
Execution Order  
```
1. FROM – get rows
2. WHERE – filter rows
3. GROUP BY – form groups
4. HAVING – filter groups
5. SELECT
6. ORDER BY

```
  
```
    SELECT
    FROM
    ORDER BY
    WHERE
    GROUP BY
    HAVING

```
  
```
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    department VARCHAR(50),
    salary INT,
    manager_id INT,
    created_at DATE
);

```
  
**Questions**  
  
1. Fetch the names and salaries of employees who earn more than 50,000, sorted by salary descending.  
```
    SELECT names, salary
    FROM employees
    WHERE salary>50000
    ORDER BY salary DESC;

```
  
2. Fetch the **top 3 highest-paid employees**  
```
	 SELECT name, salary
    FROM employees
    ORDER BY salary DESC
    LIMIT 3; 

```
  
3. Fetch only the names of employees from the **IT department** earning **more than 60,000**  
```
    SELECT name,
    FROM employees
    WHERE department="IT" AND salary>60000;

```
  
4. Find the **average salary per department**.  
```
	 SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department;

```
  
5. Find departments whose **average salary is greater than 60,000**  
```
	 SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
    HAVING AVG(salary)>60000;

#Note: WHERE filters rows, HAVING filters groups.

```
  
6. Count how many employees are in each department.  
```
	 SELECT department, COUNT(*) AS no_of_employees
    FROM employees
    GROUP BY department

```
Important nuance:  
    * ****COUNT****(*) counts rows  
    * ****COUNT****(column) skips ****NULL**** values  
  
7. Count employees **per department**, but only include employees earning more than 40,000.  
```
    SELECT department, COUNT(*) AS no_of_employees
    FROM employees
    WHERE salary>40000
    GROUP BY department

```
  
8. Find departments that have **more than 5 employees**  
```
	 SELECT department, COUNT(*) AS no_of_employees
    FROM employees
    GROUP BY department
    HAVING COUNT(*)>5;

```
  
```
# wrong 
    SELECT department, COUNT(*) AS no_of_employees
    FROM employees
    GROUP BY department
    HAVING no_of_employees>5;


```
```
Aliases are created in SELECT, and HAVING runs before SELECT, so the alias isn’t guaranteed to exist.

Can aliases be used in ORDER BY?
Yes, because ORDER BY is evaluated after SELECT

```
  
  
```
CREATE TABLE department (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    department_id INT,
    salary INT NOT NULL,
    manager_id INT,
    created_at DATE,
    CONSTRAINT fk_employee_department
        FOREIGN KEY (department_id) REFERENCES departments(id),
    CONSTRAINT fk_employee_manager
        FOREIGN KEY (manager_id) REFERENCES employees(id)
);

```
  
9. Fetch **employee name and department name** for all employees.  
```
	 SELECT e.name AS employee_name,
           d.name AS department_name
    FROM employees e
    LEFT JOIN department d 
        ON e.department_id = d.id

```
  
9b. How would you find employees without a department?  
```
	 SELECT e.name AS employee_name,
           d.department AS department_name
    FROM employees e
    LEFT JOIN department d 
        ON e.department_id = d.id
    WHERE d.id IS NULL

```
  
Why LEFT JOIN matters  
* LEFT JOIN keeps all rows from employees  
* If no matching department exists, d.name becomes NULL  
* This preserves employees with missing or unassigned departments  
  
INNER JOIN removes non-matching rows. LEFT JOIN preserves them.  
  
10. Fetch **employee name** and their **manager’s name**  
```
	 SELECT e.name AS employee_name,
           m.name AS manager_name
    FROM employees e
    LEFT JOIN employees m 
        ON e.manager_id = m.id

```
  
11. Fetch the **second highest salary** from the employees table  
```
Solution 1:	 

```
```

# If the highest salary appears multiple times, this query returns the same salary again, not the second distinct salary.


```
```
    SELECT salary

```
```
    FROM employees
    ORDER BY salary DESC
    LIMIT 1 OFFSET 1; 


Solution 2: Second distinct highest salary
    SELECT DISTINCT salary
    FROM employees
    ORDER BY salary DESC
    LIMIT 1 OFFSET 1;

Solution 3: MAX subquery
    SELECT MAX(salary) as second_highest_salary
    FROM employees
    WHERE salary < (
            SELECT MAX(salary)
            FROM employees
    );

```
  
12. Fetch employees who belong to a department named **"IT"**  
```
Solution 1: 
    SELECT name
    FROM employees
    WHERE department_id IN (
        SELECT id
        FROM department
        WHERE name = 'IT'
    );
    

```
```
Solution 2: finding only first match
    SELECT e.name

```
```
    FROM employees e
    WHERE EXISTS (
        SELECT 1
        FROM department d
        WHERE d.name = 'IT'
        AND d.id == e.department_id
    );

```
  
13. Fetch employees who **do not belong to any department**.  
```
	 SELECT name
    FROM employees
    WHERE department_id IS NULL;

```
  
  
## What is an index?  
An index is a **separate data structure** that lets MySQL avoid scanning every row.  
  
****Primary key index (this exists even if you never created one)****  
```
PRIMARY KEY (id)

```
  
# Secondary index (the common one)  
```
CREATE INDEX idx_emp_salary ON employees(salary);

```
This index stores:  
    * salary  
    * primary key (id)  
	Not the full row.  
  
So when you query by salary:  
1. MySQL finds matching ids via the index  
2. Then jumps back to the table to fetch full rows  
  
This extra step is called a **lookup** (or back-to-table).  
  
****Composite index****  
```
CREATE INDEX idx_dept_salary ON employees(department_id, salary);

```
  
This index is ordered like:   
```
(department_id, salary)

```
  
**This index helps:**  
```
WHERE department_id = 3;
WHERE department_id = 3 AND salary > 50000;

```
  
**This index does NOT help:**  
```
WHERE salary > 50000;

```
Why? Because indexes follow **leftmost prefix rule**  
  
  
## When indexes hurt performance  
They:  
* slow down INSERT  
* slow down UPDATE  
* slow down DELETE  
* take memory and disk  
  
Why not index every column?  
Because indexes optimize reads at the cost of writes and storage.  
  
  
## ⭐️ One more subtle rule  
Once a **range condition** is used (>, <, BETWEEN) on a column in an index, Columns **after it** cannot be used for further index filtering  
  
Example:  
```
Index: (department_id, salary, created_at)

✅ This works:
department_id = 2 AND salary > 50000

❌ But created_at won’t help in the index after that range.
department_id = 2 AND salary > 50000 AND created_at = 10

```
  
##   
##   
## Transaction  
A transaction is a **unit of work** that must be:  
* all applied  
* or not applied at all  
Nothing in between is allowed to leak out.  
  
  
## ACID Properties  
———————————  
**Atomicity**  
All or nothing. If the transaction fails halfway, everything rolls back.  
  
**Consistency**  
The database moves from one valid state to another. Constraints are not violated.  
  
**Isolation**  
Your transaction shouldn’t see half-finished work from others.  
This is where things get interesting.  
  
**Durability**  
Once committed, data survives crashes.  
  
  
## ⭐️ ==Isolation is the part interviewers care about==  
Because it explains:  
* weird bugs  
* missing rows  
* duplicate updates  
* “but it worked in dev”  
  
## ⭐️ The core problem: concurrent transactions  
Imagine two transactions running at the same time.  
```
# Transaction T1:
SELECT salary FROM employees WHERE id = 1;
-- gets 50000

# Transaction T2:
UPDATE employees SET salary = 60000 WHERE id = 1;
COMMIT;

# Now T1 continues:
UPDATE employees SET salary = salary + 5000 WHERE id = 1;
COMMIT;

# Final salary becomes 55000, not 65000.

```
This is called a **lost update**.  
Isolation levels exist to control this chaos.  
  
  
  
  
  
##  The four isolation levels  
  
**1. READ UNCOMMITTED**  
* Can read uncommitted data  
* Dirty reads possible  
* Rarely used  
```
# Timelines
# T2
UPDATE employees
SET salary = 100000
WHERE id = 1;
-- NOT committed yet

# T1
SELECT salary FROM employees WHERE id = 1;
-- sees 100000 ❌

# Now T2 crashes:
ROLLBACK;

# Final salary is still 50000, but T1 already saw 100000.
# This is a dirty read.
# T1 observed a state of the world that never officially existed.

```
  
**2. READ COMMITTED**  
* Can only read committed data  
* Dirty reads prevented  
* Non-repeatable reads possible  
```
# T1
SELECT salary FROM employees WHERE id = 1;
-- sees 50000

# T2
UPDATE employees
SET salary = 60000
WHERE id = 1;
COMMIT;

# T1 (same transaction)
SELECT salary FROM employees WHERE id = 1;
-- now sees 60000 ❌

# This is a non-repeatable read.
# The same query, in the same transaction, returned different results.

```
  
**3. REPEATABLE READ (MySQL default)⭐️**  
* Rows you read won’t change during the transaction  
* Prevents dirty and non-repeatable reads  
* Phantom rows are *mostly* handled via MVCC  
  
When a transaction starts, MySQL gives it a **snapshot** of the database. That snapshot does not change. This is done using **MVCC** (Multi-Version Concurrency Control).  
  
```
# T1 starts
SELECT salary FROM employees WHERE id = 1;
-- sees 50000

# T2
UPDATE employees
SET salary = 60000
WHERE id = 1;
COMMIT;

# T1 again
SELECT salary FROM employees WHERE id = 1;
-- still sees 50000 ✅

```
  
**Why this works**  
* MySQL keeps multiple versions of the row  
* T1 keeps reading the old version  
* T2 writes a new version  
No locks. No blocking. Just different snapshots.  
  
Reads don’t block writes, and writes don’t block reads.  
  
**4. SERIALIZABLE**  
* Transactions behave as if they ran one after another  
* Slow, heavy locking  
* Maximum safety  
```
# T1
SELECT * FROM employees WHERE department_id = 2;

# T2
INSERT INTO employees VALUES (4, 'D', 2, 80000);
-- blocked until T1 commits

```
```


```
  
**Why does MySQL use REPEATABLE READ by default?**  
Because it gives the **best tradeoff**:  
* Prevents dirty reads ✅  
* Prevents non-repeatable reads ✅  
* Mostly prevents phantom reads ✅  
* High concurrency via MVCC ✅  
* Minimal locking for reads ✅  
  
⭐️To fix Phantom reads in REPEATABLE READ  
```
SELECT … FOR UPDATE

```
  
**❌ Deadlock scenario**  
**T1**  
```
START TRANSACTION;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;

```
  
**T2**  
```

START TRANSACTION;
SELECT * FROM accounts WHERE id = 2 FOR UPDATE;

```
  
Now:  
**T1**  
```
SELECT * FROM accounts WHERE id = 2 FOR UPDATE;
-- waits

```
  
**T2**  
```

SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
-- waits

```
  
  
# How you design to avoid deadlocks  
**Rule 1: Lock in consistent order**  
Always lock rows in the same order.  
```
-- Always lock smaller id first
SELECT * FROM accounts WHERE id IN (1, 2) ORDER BY id FOR UPDATE;

```
This single habit prevents most deadlocks.  
  
**Rule 2: Keep transactions short**  
* Don’t call external services inside transactions  
* Don’t wait for user input  
* Lock → update → commit  
The shorter the lock duration, the safer the system.  
  
  
**⭐️ CRUD**  
  
**CREATE (INSERT)**  
```
INSERT INTO employees (id, name, department_id, salary)
VALUES (1, 'A', 2, 50000);

```
  
****Insert without specifying all columns****  
```
INSERT INTO employees (name, salary)
VALUES ('B', 60000);

```
  
**INSERT … SELECT (very important)**  
```
INSERT INTO employees_backup (id, name, salary)
SELECT id, name, salary
FROM employees
WHERE department_id = 2;

```
  
**CASE (business logic in queries)**  
```
name    | salary
-----------------
Amit    | 90000
Neha    | 75000
Ravi    | 50000
Priya   | 42000

SELECT name,
       salary,
       CASE
           WHEN salary >= 80000 THEN 'HIGH'
           WHEN salary >= 50000 THEN 'MEDIUM'
           ELSE 'LOW'
       END AS salary_band
FROM employees;


# Output

name   | salary | salary_band
-----------------------------
Amit   | 90000  | HIGH
Neha   | 75000  | MEDIUM
Ravi   | 50000  | MEDIUM
Priya  | 42000  | LOW

```
  
**EXISTS**  
```
SELECT name
FROM employees e
WHERE EXISTS (
    SELECT 1
    FROM employees x
    WHERE x.id = e.department_id
);

name
A
B
C

```
  
**NULL handling**  
```
WHERE manager_id IS NULL

manager_id = NULL   -- ❌ always false

```
  
 **LIMIT (pagination base)**  
```
SELECT name, salary
FROM employees
ORDER BY salary DESC
LIMIT 2 OFFSET 1;

# Output
B | 60000
A | 50000

```
⭐️ OFFSET becomes slow at scale. Cursor pagination is preferred.  
  
  
**UPDATE**  
```
UPDATE employees
SET salary = 60000
WHERE id = 1

```
If WHERE is missing, you update **every row**.  
If affected rows = 0 → handle it in service layer. (For java dev)  
  
**Update using another table (advanced but real)**  
```
id | name   | salary | department_id
------------------------------------
1  | Amit   | 60000  | 1
2  | Neha   | 70000  | 2
3  | Ravi   | 55000  | 1
4  | Priya  | 80000  | 3
departments

id | name
---------
1  | IT
2  | HR
3  | Finance

UPDATE employees e
JOIN departments d
  ON e.department_id = d.id
SET e.salary = e.salary + 5000
WHERE d.name = 'IT';


id | name   | salary | department_id
------------------------------------
1  | Amit   | 65000  | 1
2  | Neha   | 70000  | 2
3  | Ravi   | 60000  | 1
4  | Priya  | 80000  | 3

```
This is *very* MySQL-specific and very real.  
  
**Update with subquery**  
```
UPDATE employees
SET salary = salary + 5000
WHERE department_id IN (
    SELECT id FROM departments WHERE name = 'IT'
);

```
  
  
****DELETE (danger zone)****  
  
**Delete specific row**  
```
DELETE FROM employees
WHERE id = 4;

```
  
**Delete with condition**  
```
DELETE FROM employees
WHERE department_id IS NULL;

```
Never run DELETE without WHERE in production. Ever.  
  
  
# Aggregate functions  
  
**COUNT**  
```
SELECT COUNT(*) FROM employees;

COUNT(*)
--------
4

```
  
**SUM / AVG / MIN / MAX**  
```
SELECT
  SUM(salary) AS total,
  AVG(salary) AS avg_salary,
  MIN(salary) AS min_salary,
  MAX(salary) AS max_salary
FROM employees;

total  | avg_salary | min_salary | max_salary
---------------------------------------------
265000 | 66250      | 55000      | 80000

```
  
## GROUP BY  
**Avg salary per department**  
```
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;

department_id | avg_salary
1             | 55000
2             | 70000
NULL          | 40000

```
  
**Filter groups (HAVING)**  
```
SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 60000;

department_id
2

```
#   
## String functions  
  
**CONCAT**  
```
SELECT CONCAT(name, '_EMP') FROM employees;

name | tag
A    | A_EMP
B    | B_EMP
C    | C_EMP
D    | D_EMP

```
  
**LENGTH**  
```
SELECT name, LENGTH(name) FROM employees;

```
  
  
## Numeric functions  
  
**ROUND**  
```
SELECT ROUND(AVG(salary), 2) FROM employees;

avg_salary
55000.00

```
  
**ABS**  
```
SELECT ABS(-100);

100

```
  
## ⭐️Date / Time functions  
  
**NOW vs CURDATE**  
  
NOW()         → current date **and** time  
CURDATE() → current date **only**  
  
```
SELECT NOW(), CURDATE();

NOW()               | CURDATE()
--------------------------------
2026-01-28 12:15:30 | 2026-01-28

```
  
**Date difference**  
  
**DATEDIFF**(date1, date2) = **date1 − date2 (in days)**  
```
id | name   | created_at
------------------------
1  | Amit   | 2026-01-20
2  | Neha   | 2026-01-25
3  | Ravi   | 2026-01-28

CURRDATE() -> 2026-01-28

SELECT DATEDIFF(CURDATE(), created_at) AS days_since_created
FROM employees;

days_since_created
------------------
8
3
0

```
  
**Extract year / month (index trap)**  
```
SELECT *
FROM employees
WHERE YEAR(created_at) = 2023;

```
  
Internally, the database has to do this:  
* read each row’s created_at  
* compute YEAR(created_at)  
* compare it to 2023  
  
  
⚠️ Breaks index usage. Better:  
```
WHERE created_at >= '2023-01-01'
AND created_at < '2024-01-01';

```
Now the database can:  
* jump straight to 2023-01-01 in the index  
* scan forward until just before 2024-01-01  
* stop  
  
  
## NULL-handling functions (huge source of bugs)  
  
**COALESCE (use this a lot)**  
```
SELECT name, COALESCE(department_id, 0) AS dept_id
FROM employees;

name | dept_id
A    | 1
B    | 1
C    | 2
D    | 0

```
  
**IFNULL (MySQL specific)**  
```
SELECT IFNULL(department_id, -1)
FROM employees;

```
  
  
  
## Window Functions ⭐️⭐️⭐️  
They let you **see aggregate or ranking information without collapsing rows**.  
No GROUP BY row loss.  
  
## Base table (reference)  
```
employees
-------------------------------------------------
id | name | department_id | salary | created_at
1  | A    | 1              | 50000  | 2023-01-10
2  | B    | 1              | 60000  | 2023-02-15
3  | C    | 1              | 60000  | 2023-03-01
4  | D    | 2              | 70000  | 2023-03-20
5  | E    | 2              | 80000  | 2023-04-01
6  | F    | 2              | 80000  | 2023-04-10

```
  
****1. ROW_NUMBER (absolute ranking, no ties)****  
Give each employee a unique rank by salary within department.  
```
SELECT
  department_id,
  name,
  salary,
  ROW_NUMBER() OVER (
      PARTITION BY department_id
      ORDER BY salary DESC
  ) AS row_num
FROM employees;

department_id | name | salary | row_num
1             | B    | 60000  | 1
1             | C    | 60000  | 2
1             | A    | 50000  | 3
2             | E    | 80000  | 1
2             | F    | 80000  | 2
2             | D    | 70000  | 3

```
🔹 Same salary still gets different numbers  
🔹 Always sequential  
🔹 Very common in pagination  
  
  
# 2. RANK (gaps allowed for ties)  
Rank employees by salary, but give same rank for same salary.  
```
SELECT
  department_id,
  name,
  salary,
  RANK() OVER (
      PARTITION BY department_id
      ORDER BY salary DESC
  ) AS salary_rank
FROM employees;

department_id | name | salary | salary_rank
1             | B    | 60000  | 1
1             | C    | 60000  | 1
1             | A    | 50000  | 3
2             | E    | 80000  | 1
2             | F    | 80000  | 1
2             | D    | 70000  | 3

```
🔹 Notice rank **skips 2** 🔹 This gap is intentional  
  
## 3. DENSE_RANK (no gaps)  
Rank salaries, but don’t skip numbers.  
```
SELECT
  department_id,
  name,
  salary,
  DENSE_RANK() OVER (
      PARTITION BY department_id
      ORDER BY salary DESC
  ) AS dense_rank
FROM employees;

department_id | name | salary | dense_rank
1             | B    | 60000  | 1
1             | C    | 60000  | 1
1             | A    | 50000  | 2
2             | E    | 80000  | 1
2             | F    | 80000  | 1
2             | D    | 70000  | 2

```
🔹 This is usually what business wants  
  
## 4. Top N per group (classic interview question)  
Get top 2 highest-paid employees per department.  
```
SELECT department_id, name, salary
FROM (
    SELECT
      department_id,
      name,
      salary,
      DENSE_RANK() OVER (
          PARTITION BY department_id
          ORDER BY salary DESC
      ) AS rnk
    FROM employees
) t
WHERE rnk <= 2;

department_id | name | salary
1             | B    | 60000
1             | C    | 60000
2             | E    | 80000
2             | F    | 80000
2             | D    | 70000

```
This **cannot be done cleanly** with plain GROUP BY.  
  
  
## 5. Running total (very common in reports)  
Show cumulative salary per department ordered by salary.  
```
SELECT
  department_id,
  name,
  salary,
  SUM(salary) OVER (
      PARTITION BY department_id
      ORDER BY salary
  ) AS running_total
FROM employees;

department_id | name | salary | running_total
1             | A    | 50000  | 50000
1             | B    | 60000  | 110000
1             | C    | 60000  | 170000
2             | D    | 70000  | 70000
2             | E    | 80000  | 150000
2             | F    | 80000  | 230000

```
  
## 6. Window AVG vs GROUP BY AVG (important contrast)  
  
**Window AVG (no row loss)**  
```
SELECT
  name,
  department_id,
  salary,
  AVG(salary) OVER (PARTITION BY department_id) AS dept_avg
FROM employees;

name | department_id | salary | dept_avg
A    | 1             | 50000  | 56666.67
B    | 1             | 60000  | 56666.67
C    | 1             | 60000  | 56666.67
D    | 2             | 70000  | 76666.67
E    | 2             | 80000  | 76666.67
F    | 2             | 80000  | 76666.67

```
  
**GROUP BY (row collapse)**  
```
SELECT department_id, AVG(salary) AS dept_avg
FROM employees
GROUP BY department_id;

department_id | dept_avg
1             | 56666.67
2             | 76666.67

```
  
  
## 7. LAG (compare with previous row)  
Show salary difference from previous employee in same department.  
```
SELECT
  department_id,
  name,
  salary,
  salary - LAG(salary) OVER (
      PARTITION BY department_id
      ORDER BY salary
  ) AS diff_from_prev
FROM employees;

department_id | name | salary | diff_from_prev
1             | A    | 50000  | NULL
1             | B    | 60000  | 10000
1             | C    | 60000  | 0
2             | D    | 70000  | NULL
2             | E    | 80000  | 10000
2             | F    | 80000  | 0

```
  
## 8. LEAD (look ahead)  
```
SELECT
  department_id,
  name,
  salary,
  LEAD(salary) OVER (
      PARTITION BY department_id
      ORDER BY salary
  ) AS next_salary
FROM employees;

department_id | name | salary | next_salary
1             | A    | 50000  | 60000
1             | B    | 60000  | 60000
1             | C    | 60000  | NULL
2             | D    | 70000  | 80000
2             | E    | 80000  | 80000
2             | F    | 80000  | NULL

```
  
## Stored Procedures  
  
## 1️⃣ What is a Stored Procedure (in one line)  
A **stored procedure** is a **named block of SQL logic stored in the database** that can:  
* accept parameters  
* contain conditions & loops  
* return result sets  
* execute multiple SQL statements atomically  
Think of it as **a backend function inside MySQL**.  
  
## Base table (reference)  
```

employees
-------------------------------------------------
id | name | department_id | salary | created_at
1  | A    | 1              | 50000  | 2023-01-10
2  | B    | 1              | 60000  | 2023-02-15
3  | C    | 2              | 70000  | 2023-03-01
4  | D    | NULL           | 40000  | 2023-03-20

```
  
## 2️⃣ Basic Stored Procedure (NO parameters)  
**Create procedure**  
```

DELIMITER //

CREATE PROCEDURE get_all_employees()
BEGIN
    SELECT * FROM employees;
END //

DELIMITER ;

```
**Call procedure**  
```

CALL get_all_employees();

```
**Output**  
```

id | name | department_id | salary | created_at
1  | A    | 1              | 50000  | 2023-01-10
2  | B    | 1              | 60000  | 2023-02-15
3  | C    | 2              | 70000  | 2023-03-01
4  | D    | NULL           | 40000  | 2023-03-20

```
  
## 3️⃣ Stored Procedure with INPUT parameter  
**Use case**  
“Get employees by department”  
```

DELIMITER //

CREATE PROCEDURE get_employees_by_dept(IN dept_id INT)
BEGIN
    SELECT id, name, salary
    FROM employees
    WHERE department_id = dept_id;
END //

DELIMITER ;

```
**Call**  
```

CALL get_employees_by_dept(1);

```
**Output**  
```

id | name | salary
1  | A    | 50000
2  | B    | 60000

```
  
## 4️⃣ Stored Procedure with MULTIPLE parameters  
**Use case**  
“Get employees earning more than X in a department”  
```

DELIMITER //

CREATE PROCEDURE get_high_earners(
    IN dept_id INT,
    IN min_salary INT
)
BEGIN
    SELECT name, salary
    FROM employees
    WHERE department_id = dept_id
      AND salary >= min_salary;
END //

DELIMITER ;

```
**Call**  
```

CALL get_high_earners(1, 55000);

```
**Output**  
```

name | salary
B    | 60000

```
  
## 5️⃣ Stored Procedure with OUTPUT parameter  
**Use case**  
“Return average salary of a department”  
```

DELIMITER //

CREATE PROCEDURE get_avg_salary(
    IN dept_id INT,
    OUT avg_sal DECIMAL(10,2)
)
BEGIN
    SELECT AVG(salary)
    INTO avg_sal
    FROM employees
    WHERE department_id = dept_id;
END //

DELIMITER ;

```
**Call**  
```

CALL get_avg_salary(1, @result);
SELECT @result AS avg_salary;

```
**Output**  
```

avg_salary
55000.00

```
  
## 6️⃣ IF–ELSE inside Stored Procedure  
**Use case**  
“Salary classification logic in DB”  
```

DELIMITER //

CREATE PROCEDURE salary_band(
    IN emp_id INT
)
BEGIN
    DECLARE sal INT;

    SELECT salary INTO sal
    FROM employees
    WHERE id = emp_id;

    IF sal >= 70000 THEN
        SELECT 'HIGH' AS band;
    ELSEIF sal >= 50000 THEN
        SELECT 'MEDIUM' AS band;
    ELSE
        SELECT 'LOW' AS band;
    END IF;
END //

DELIMITER ;

```
**Call**  
```

CALL salary_band(1);

```
**Output**  
```

band
MEDIUM

```
  
## 7️⃣ Stored Procedure with TRANSACTION (VERY IMPORTANT)  
**Use case**  
“Update salary safely”  
```

DELIMITER //

CREATE PROCEDURE update_salary_safe(
    IN emp_id INT,
    IN increment INT
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        SELECT 'FAILED' AS status;
    END;

    START TRANSACTION;

    UPDATE employees
    SET salary = salary + increment
    WHERE id = emp_id;

    COMMIT;
    SELECT 'SUCCESS' AS status;
END //

DELIMITER ;

```
**Call**  
```

CALL update_salary_safe(1, 5000);

```
**Output**  
```

status
SUCCESS

```
  
## 8️⃣ Loop inside Stored Procedure  
**Use case**  
“Give 10% hike to first N employees”  
```

DELIMITER //

CREATE PROCEDURE bulk_hike(IN limit_count INT)
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE emp_id INT;

    DECLARE emp_cursor CURSOR FOR
        SELECT id FROM employees LIMIT limit_count;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN emp_cursor;

    loop_label: LOOP
        FETCH emp_cursor INTO emp_id;
        IF done = 1 THEN
            LEAVE loop_label;
        END IF;

        UPDATE employees
        SET salary = salary * 1.10
        WHERE id = emp_id;
    END LOOP;

    CLOSE emp_cursor;
END //

DELIMITER ;

```
⚠️ Cursor-based procedures are **slow**. Know them, don’t overuse them.  
  
## 9️⃣ Drop Stored Procedure  
```

DROP PROCEDURE IF EXISTS get_all_employees;

```
  
## 1️⃣0️⃣ Interview-level questions (you must know answers)  
**❓ When should you use stored procedures?**  
✅ Data-heavy logic ✅ Bulk updates ✅ Transactions close to data ❌ NOT for business logic in modern microservices  
  
**❓ Why Java devs avoid heavy stored procedures?**  
* Hard to version control  
* Hard to test  
* Tightly coupled to DB  
* Hard to scale in microservices  
**Correct answer**:  
Use SPs for data integrity and performance, not business rules.  
  
## 1️⃣1️⃣ How Java calls Stored Procedures (JDBC)  
```

CallableStatement cs =
    conn.prepareCall("{call get_employees_by_dept(?)}");

cs.setInt(1, 1);
ResultSet rs = cs.executeQuery();

```
  
  
## Triggers  
A **trigger** is SQL logic that runs **automatically** when a table event happens:  
* INSERT  
* UPDATE  
* DELETE  
You **do not call** a trigger. The database calls it for you.  
Think of it as **implicit logic tied to data mutation**.  
  
## Base tables (reference)  
```
employees

id | name | department_id | salary | created_at
1  | A    | 1             | 50000  | 2023-01-10
2  | B    | 1             | 60000  | 2023-02-15
3  | C    | 2             | 70000  | 2023-03-01

```
****salary_audit (for auditing)****  
```

emp_id | old_salary | new_salary | changed_at

```
  
## 1️⃣ AFTER INSERT Trigger  
**Use case**  
Automatically log when a new employee is added.  
  
**Create trigger**  
```

DELIMITER //

CREATE TRIGGER after_employee_insert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO salary_audit (
        emp_id,
        old_salary,
        new_salary,
        changed_at
    )
    VALUES (
        NEW.id,
        NULL,
        NEW.salary,
        NOW()
    );
END //

DELIMITER ;

```
  
**Insert data**  
```

INSERT INTO employees (id, name, department_id, salary, created_at)
VALUES (4, 'D', 2, 80000, '2023-04-01');

```
  
**Check audit table**  
```

SELECT * FROM salary_audit;

emp_id | old_salary | new_salary | changed_at
4      | NULL       | 80000      | 2026-02-04 11:30:00

```
  
## Key concept  
* NEW.column → new inserted row  
* OLD.column → previous value (not available in INSERT)  
  
## 2️⃣ BEFORE UPDATE Trigger (most common)  
**Use case**  
Log salary changes.  
  
**Create trigger**  
```

DELIMITER //

CREATE TRIGGER before_salary_update
BEFORE UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary <> NEW.salary THEN
        INSERT INTO salary_audit (
            emp_id,
            old_salary,
            new_salary,
            changed_at
        )
        VALUES (
            OLD.id,
            OLD.salary,
            NEW.salary,
            NOW()
        );
    END IF;
END //

DELIMITER ;

```
  
**Update salary**  
```

UPDATE employees
SET salary = 65000
WHERE id = 1;

```
  
**Audit output**  
```

SELECT * FROM salary_audit;

emp_id | old_salary | new_salary | changed_at
4      | NULL       | 80000      | 2026-02-04 11:30:00
1      | 50000      | 65000      | 2026-02-04 11:32:10

```
  
## Important interview rule  
* **Use BEFORE UPDATE** if you want to **validate or block**  
* **Use AFTER UPDATE** if you want to **log**  
  
## 3️⃣ BEFORE UPDATE Trigger (data validation)  
**Use case**  
Prevent salary from going negative.  
  
```

DELIMITER //

CREATE TRIGGER prevent_negative_salary
BEFORE UPDATE ON employees
FOR EACH ROW
BEGIN
    IF NEW.salary < 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Salary cannot be negative';
    END IF;
END //

DELIMITER ;

```
  
**Invalid update**  
```

UPDATE employees
SET salary = -1000
WHERE id = 2;

```
  
**Result**  
```

ERROR: Salary cannot be negative

```
✔️ Database-level safety ✔️ Cannot be bypassed by any app  
  
## 4️⃣ AFTER DELETE Trigger  
**Use case**  
Log deleted employees.  
  
**Audit table**  
```

employee_delete_log
--------------------------------
emp_id | name | deleted_at

```
  
**Trigger**  
```

DELIMITER //

CREATE TRIGGER after_employee_delete
AFTER DELETE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_delete_log
    VALUES (OLD.id, OLD.name, NOW());
END //

DELIMITER ;

```
  
**Delete employee**  
```

DELETE FROM employees WHERE id = 3;

```
  
**Output**  
```

SELECT * FROM employee_delete_log;

emp_id | name | deleted_at
3      | C    | 2026-02-04 11:35:20

```
  
## 5️⃣ Trigger execution order (important)  
* One trigger per:  
    * BEFORE INSERT  
    * AFTER INSERT  
    * BEFORE UPDATE  
    * AFTER UPDATE  
    * BEFORE DELETE  
    * AFTER DELETE  
You **cannot control order** if multiple triggers exist. This is one reason teams avoid many triggers.  
  
## 6️⃣ What triggers CANNOT do (interview trap)  
❌ Call external APIs ❌ Commit / Rollback (inside trigger) ❌ Return result sets ❌ Be called manually  
Triggers run **inside the transaction** that caused them.  
  
## 7️⃣ Why Java teams are careful with triggers  
**Pros**  
✅ Enforces data integrity ✅ Centralized logic ✅ Cannot be bypassed  
**Cons (very important)**  
❌ Hidden side effects ❌ Hard to debug ❌ Hard to version control ❌ Surprise performance issues  
**Correct senior answer**  
Triggers are good for enforcing invariants and auditing, but business logic should stay in the application layer.  
  
## 8️⃣ Interview questions you WILL get  
**❓ Trigger vs Stored Procedure?**  
* Trigger → automatic  
* Procedure → manual  
**❓ BEFORE vs AFTER?**  
* BEFORE → validation / modification  
* AFTER → logging / side effects  
**❓ Can triggers cause deadlocks?**  
✅ Yes — because they run inside transactions  
  
## 9️⃣ Drop trigger  
```

DROP TRIGGER IF EXISTS before_salary_update;

```
