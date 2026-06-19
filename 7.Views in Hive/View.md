# Hive Views – Complete Conceptual Notes

Views are one of the most important concepts in Hive and SQL. They help us:

✅ Restrict access to sensitive data

✅ Simplify complex queries

✅ Reuse query logic

✅ Improve readability

---

# What is a View?

A View is a **virtual table** created from the result of a SQL query.

Unlike a real table:

* View does not store data separately.
* It stores only the SQL query definition.
* Data is fetched from the underlying table whenever the view is queried.

---

## Real Table vs View

### Employee Table

| emp_id | name  | salary | department |
| ------ | ----- | ------ | ---------- |
| 101    | Ram   | 50000  | IT         |
| 102    | Ravi  | 60000  | HR         |
| 103    | Priya | 70000  | Finance    |

---

### View

Suppose users only need:

| emp_id | name  |
| ------ | ----- |
| 101    | Ram   |
| 102    | Ravi  |
| 103    | Priya |

Create a view:

```sql
CREATE VIEW employee_view AS
SELECT emp_id,name
FROM employees;
```

---

# Why Use Views?

## 1. Security

Hide sensitive columns.

Employee Table:

```text
emp_id
name
salary
bank_account
```

Create View:

```sql
CREATE VIEW employee_public AS
SELECT emp_id,name
FROM employees;
```

Users can see:

```text
emp_id
name
```

but cannot see:

```text
salary
bank_account
```

---

## 2. Simplify Queries

Complex Query:

```sql
SELECT *
FROM employees
WHERE salary >
(
SELECT AVG(salary)
FROM employees
);
```

Instead of writing repeatedly:

```sql
CREATE VIEW avg_salary_view AS
SELECT *
FROM employees
WHERE salary >
(
SELECT AVG(salary)
FROM employees
);
```

Now:

```sql
SELECT COUNT(*)
FROM avg_salary_view;
```

Much simpler.

---

# How View Works Internally

```text
User Query
     ↓
View
     ↓
Stored SQL Query
     ↓
Base Table
     ↓
Result
```

---

# Creating a Simple View

Employee Table:

| emp_id | emp_name | salary |
| ------ | -------- | ------ |
| 101    | Ram      | 50000  |
| 102    | Ravi     | 60000  |

Create View:

```sql
CREATE VIEW employee_view AS
SELECT emp_id,emp_name
FROM employees;
```

---

## Query View

```sql
SELECT *
FROM employee_view;
```

Output:

| emp_id | emp_name |
| ------ | -------- |
| 101    | Ram      |
| 102    | Ravi     |

---

# View on Complex Data Types

Suppose employee table contains a STRUCT column.

Example:

```sql
job STRUCT<
salary:INT,
hiredate:DATE,
departmentnumber:INT
>
```

Data:

```text
job.salary
job.hiredate
job.departmentnumber
```

can be accessed using:

```sql
job.salary
job.hiredate
job.departmentnumber
```

---

# Creating View Using STRUCT Fields

```sql
CREATE VIEW job_view_2 AS
SELECT
employee_id,
job.hiredate,
job.departmentnumber
FROM employees;
```

---

## Query View

```sql
SELECT *
FROM job_view_2;
```

Output:

| employee_id | hiredate   | departmentnumber |
| ----------- | ---------- | ---------------- |
| 101         | 2001-05-10 | 20               |
| 102         | 1998-08-12 | 30               |

---

# Running Queries on Views

Example:

Find employees hired after year 2000.

```sql
SELECT *
FROM job_view_2
WHERE YEAR(hiredate) >= 2000;
```

Output:

```text
Only employees hired after 2000
```

---

# Checking View Details

```sql
DESCRIBE FORMATTED job_view_2;
```

Output contains:

```text
Table Type:
VIRTUAL_VIEW
```

This tells us:

```text
Not a physical table
```

It is a View.

---

# View Metadata

Hive stores:

```text
View Name

View Definition

Base Table

Selected Columns
```

Example:

```sql
SELECT employee_id,
job.hiredate,
job.departmentnumber
FROM employees;
```

is stored as metadata.

---

# Complex Query Example

Suppose we need:

Employees whose salary is greater than average salary.

---

## Without View

```sql
SELECT *
FROM employees
WHERE salary >
(
SELECT AVG(salary)
FROM employees
);
```

---

### Flow

```text
Find AVG Salary
       ↓
Compare Salary
       ↓
Return Employees
```

---

# Creating View for This Query

```sql
CREATE VIEW average_salary_view AS

SELECT *
FROM employees
WHERE salary >
(
SELECT AVG(salary)
FROM employees
);
```

---

# Querying the View

Count employees:

```sql
SELECT COUNT(*)
FROM average_salary_view;
```

Instead of rewriting the subquery every time.

---

# Benefits

Imagine query contains:

```text
10 JOINS

5 Subqueries

20 Conditions
```

Writing repeatedly:

❌ Difficult

Using View:

✅ Easy

---

# Renaming a View

Suppose:

```text
average_salary_view
```

needs to become:

```text
average_sal
```

Use:

```sql
ALTER VIEW average_salary_view
RENAME TO average_sal;
```

---

## Verify

```sql
SHOW TABLES;
```

Output:

```text
average_sal
```

View renamed successfully.

---

# Dropping a View

Delete a View:

```sql
DROP VIEW average_sal;
```

---

## Verify

```sql
SHOW TABLES;
```

View no longer exists.

---

# DROP TABLE vs DROP VIEW

Table:

```sql
DROP TABLE employees;
```

Deletes table metadata and data (managed table).

---

View:

```sql
DROP VIEW employee_view;
```

Deletes only the view definition.

Base table remains intact.

---

# Table vs View

| Feature                    | Table | View |
| -------------------------- | ----- | ---- |
| Stores Data                | Yes   | No   |
| Physical Storage           | Yes   | No   |
| Query Definition Stored    | No    | Yes  |
| Can Restrict Columns       | No    | Yes  |
| Simplifies Complex Queries | No    | Yes  |
| Virtual Object             | No    | Yes  |

---

# Hive View Architecture

```text
Employees Table
│
├── employee_id
├── salary
├── hire_date
└── department

        ↓

CREATE VIEW

        ↓

job_view

        ↓

employee_id
hire_date
department

        ↓

User Queries
```

---

# Interview Questions

### Q1. What is a View in Hive?

A virtual table based on the result of a SQL query.

---

### Q2. Does a View store data?

No.

It stores only the query definition.

---

### Q3. How can you identify a View?

```sql
DESCRIBE FORMATTED view_name;
```

Look for:

```text
Table Type:
VIRTUAL_VIEW
```

---

### Q4. Why are Views used?

* Security
* Simplicity
* Reusability
* Abstraction

---

### Q5. Can we query a View like a table?

Yes.

```sql
SELECT * FROM view_name;
```

---

### Q6. How do you rename a View?

```sql
ALTER VIEW old_view
RENAME TO new_view;
```

---

### Q7. How do you delete a View?

```sql
DROP VIEW view_name;
```

---

### Q8. What happens if a View is dropped?

Only the view definition is deleted.

Underlying table remains unchanged.

---

# Quick Revision Sheet

```text
VIEW
=
Virtual Table

Stores
=
Query Definition

Does NOT Store
=
Actual Data

Create View

CREATE VIEW view_name AS
SELECT ...

Query View

SELECT * FROM view_name;

Rename View

ALTER VIEW old_view
RENAME TO new_view;

Drop View

DROP VIEW view_name;

Check View

DESCRIBE FORMATTED view_name;

Look For:
VIRTUAL_VIEW
```

# Visual Memory Trick

```text
Employees Table
       ↓

CREATE VIEW

       ↓

Virtual Table

       ↓

Stores Query Only

       ↓

Reads Data From Base Table
```

## One-Line Interview Answer

**A Hive View is a virtual table that stores a SQL query definition rather than actual data, allowing users to simplify complex queries, reuse logic, and restrict access to specific columns of a base table.**
