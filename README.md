# SQL Interview Preparation

Simple SQL interview questions and answers with practical examples for Data Analyst and Data Engineer roles.

**Technologies:** SQL, MySQL, PostgreSQL, SQL Server

---

## Table of Contents

1. Primary Key vs Unique Key
2. WHERE vs HAVING
3. Types of JOINs
4. DELETE vs TRUNCATE vs DROP
5. Finding the Second-Highest Salary
6. UNION vs UNION ALL
7. ROW_NUMBER() vs RANK() vs DENSE_RANK()
8. Finding Duplicate Records
9. Common Table Expression (CTE)
10. LAG() vs LEAD()
11. CHAR vs VARCHAR
12. Subquery vs CTE
13. COUNT(*) vs COUNT(column) vs COUNT(DISTINCT column)

---

# Part 1: SQL Fundamentals

## 1. What is the difference between Primary Key and Unique Key?

**Definition:**

Both Primary Key and Unique Key are used to maintain uniqueness in a table.

- **Primary Key:** Uniquely identifies each record in a table. It does not allow NULL values, and a table can have only one primary key constraint.
- **Unique Key:** Ensures that values in a column or combination of columns are unique. A table can have multiple unique constraints. NULL handling depends on the database.

**Example:**

```sql
CREATE TABLE Employees (
    emp_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);
```

Here, emp_id uniquely identifies each employee, while email prevents duplicate email addresses.

## 2. What is the difference between WHERE and HAVING?

**Definition:**

Both are used to filter data, but they work at different stages of a query.

- **WHERE:** Filters individual rows before grouping.
- **HAVING:** Filters grouped results after GROUP BY.

**Example:**

```sql
SELECT department_id, COUNT(*) AS total_employees
FROM Employees
WHERE salary > 30000
GROUP BY department_id
HAVING COUNT(*) > 5;
```

**Explanation:**

- WHERE filters employees whose salary is above 30,000.
- GROUP BY groups employees by department.
- HAVING displays only departments with more than five qualifying employees.

## 3. What are the common types of JOINs in SQL?

**Definition:**

JOINs are used to combine data from two or more tables based on a related column.

**Types of JOINs:**

- **INNER JOIN:** Returns only matching records from both tables.
- **LEFT JOIN:** Returns all records from the left table and matching records from the right table.
- **RIGHT JOIN:** Returns all records from the right table and matching records from the left table.
- **FULL OUTER JOIN:** Returns matching and non-matching records from both tables.

**Example:**

```sql
SELECT
    Employees.emp_name,
    Departments.dept_name
FROM Employees
INNER JOIN Departments
ON Employees.dept_id = Departments.dept_id;
```

**Real-Time Example:**

Suppose we have an Employees table and a Departments table. We can use INNER JOIN to retrieve employee names along with their department names.

## 4. What is the difference between DELETE, TRUNCATE, and DROP?

**Definition:**

These commands are used to remove data or database objects.

- **DELETE:** Removes selected rows from a table. It supports the WHERE condition.
- **TRUNCATE:** Removes all rows from a table but keeps the table structure.
- **DROP:** Removes the entire table, including its structure and data.

**Examples:**

```sql
-- Delete selected records
DELETE FROM Employees
WHERE emp_id = 101;

-- Remove all records
TRUNCATE TABLE Employees;

-- Remove the entire table
DROP TABLE Employees;
```

**Difference:**

| DELETE | TRUNCATE | DROP |
|---|---|---|
| Removes selected rows | Removes all rows | Removes the entire table |
| Supports WHERE | Does not support WHERE | Removes table structure |
| Keeps table structure | Keeps table structure | Removes table structure |

**Interview Tip:** Transaction and rollback behavior can differ depending on the database system.

## 5. How do you find the second-highest salary in SQL?

**Definition:**

We can find the second-highest salary using a subquery with MAX() or a window function such as DENSE_RANK().

**Approach 1: Using MAX()**

```sql
SELECT MAX(salary) AS second_highest_salary
FROM Employees
WHERE salary < (
    SELECT MAX(salary)
    FROM Employees
);
```

**Explanation:**

The inner query finds the highest salary. The outer query finds the maximum salary below that value.

**Approach 2: Using DENSE_RANK()**

```sql
WITH RankedSalaries AS (
    SELECT
        salary,
        DENSE_RANK() OVER (
            ORDER BY salary DESC
        ) AS salary_rank
    FROM Employees
)
SELECT DISTINCT salary
FROM RankedSalaries
WHERE salary_rank = 2;
```

**Real-Time Example:**

In a company, we can use this query to find the employee salary level that ranks second highest.

## 6. What is the difference between UNION and UNION ALL?

**Definition:**

Both are used to combine the results of multiple SELECT statements.

- **UNION:** Combines results and removes duplicate rows.
- **UNION ALL:** Combines results and keeps duplicate rows.

**Example:**

```sql
-- Removes duplicates
SELECT id FROM Table_A
UNION
SELECT id FROM Table_B;

-- Keeps duplicates
SELECT id FROM Table_A
UNION ALL
SELECT id FROM Table_B;
```

**Example Data:**

Table A: 10, 20

Table B: 20, 30

- UNION Result: 10, 20, 30
- UNION ALL Result: 10, 20, 20, 30

**Interview Tip:** UNION ALL is generally faster because it does not remove duplicate records.

## 7. What is the difference between ROW_NUMBER(), RANK(), and DENSE_RANK()?

**Definition:**

These are SQL window functions used to assign rankings to rows.

- **ROW_NUMBER():** Assigns a unique sequential number to every row.
- **RANK():** Assigns the same rank to tied values but skips the next rank.
- **DENSE_RANK():** Assigns the same rank to tied values without skipping the next rank.

**Example:**

```sql
SELECT
    emp_name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
    RANK() OVER (ORDER BY salary DESC) AS salary_rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM Employees;
```

**Sample Result:**

| Salary | ROW_NUMBER | RANK | DENSE_RANK |
|---:|---:|---:|---:|
| 50000 | 1 | 1 | 1 |
| 40000 | 2 | 2 | 2 |
| 40000 | 3 | 2 | 2 |
| 30000 | 4 | 4 | 3 |

**Interview Tip:** Remember the ranking difference: ROW_NUMBER gives unique numbers, RANK skips numbers after ties, and DENSE_RANK does not skip numbers.

## 8. How do you find duplicate records in SQL?

**Definition:**

Duplicate records are repeated values in a table. We can identify them using GROUP BY and HAVING COUNT().

**Example:**

```sql
SELECT
    email,
    COUNT(*) AS total_count
FROM Employees
GROUP BY email
HAVING COUNT(*) > 1;
```

**Explanation:**

- GROUP BY groups records based on email.
- COUNT(*) counts the records in each group.
- HAVING COUNT(*) > 1 displays only duplicate email values.

**Real-Time Example:**

We can identify duplicate customer email addresses to maintain data quality.

## 9. What is a CTE (Common Table Expression)?

**Definition:**

A CTE is a temporary named result set created using the WITH keyword. It helps make complex SQL queries easier to read and maintain.

**Example:**

```sql
WITH HighSalaryEmployees AS (
    SELECT
        emp_id,
        emp_name,
        department_id,
        salary
    FROM Employees
    WHERE salary > 50000
)
SELECT
    department_id,
    COUNT(*) AS total_employees
FROM HighSalaryEmployees
GROUP BY department_id;
```

**Explanation:**

First, the CTE retrieves employees earning more than 50,000. Then the main query counts those employees department-wise.

## 10. What is the difference between LAG() and LEAD()?

**Definition:**

Both are SQL window functions used to access values from other rows without using a self-join.

- **LAG():** Retrieves a value from a previous row.
- **LEAD():** Retrieves a value from a following row.

**Example:**

```sql
SELECT
    emp_name,
    salary,
    LAG(salary, 1) OVER (
        ORDER BY salary
    ) AS previous_salary,
    LEAD(salary, 1) OVER (
        ORDER BY salary
    ) AS next_salary
FROM Employees;
```

**Real-Time Example:**

We can use LAG() to compare an employee's current salary with the previous salary in a sorted result.

---

# Part 2: Intermediate SQL Concepts

## 11. What is the difference between CHAR and VARCHAR?

**Definition:**

Both are data types used to store character values.

- **CHAR:** Fixed-length character data type.
- **VARCHAR:** Variable-length character data type.

**Example:**

```sql
CREATE TABLE Employees (
    emp_code CHAR(10),
    emp_name VARCHAR(100)
);
```

If we store 'John':

- CHAR(10) reserves a fixed length of 10 characters.
- VARCHAR(100) stores the actual text length plus storage overhead.

**Interview Tip:** Use CHAR for fixed-length values and VARCHAR for values with varying lengths.

## 12. What is the difference between a Subquery and a CTE?

**Definition:**

Both are used to break down complex SQL queries.

- **Subquery:** A query written inside another SQL query.
- **CTE:** A temporary named result set defined using WITH.

**Example – Subquery:**

```sql
SELECT *
FROM Employees
WHERE salary > (
    SELECT AVG(salary)
    FROM Employees
);
```

**Example – CTE:**

```sql
WITH AverageSalary AS (
    SELECT AVG(salary) AS avg_salary
    FROM Employees
)
SELECT *
FROM Employees
WHERE salary > (
    SELECT avg_salary
    FROM AverageSalary
);
```

**Difference:**

| Subquery | CTE |
|---|---|
| Written inside another query | Defined using WITH |
| Can be nested | Can improve query readability |
| Useful for smaller query operations | Useful for organizing complex queries |

**Interview Tip:** CTEs improve readability, but they are not automatically faster than subqueries.

## 13. What is the difference between COUNT(*), COUNT(column), and COUNT(DISTINCT column)?

**Definition:**

These functions are used to count records and values in SQL.

- **COUNT(*):** Counts all rows, including rows containing NULL values.
- **COUNT(column):** Counts only non-NULL values in the specified column.
- **COUNT(DISTINCT column):** Counts unique, non-NULL values in the specified column.

**Example:**

```sql
SELECT
    COUNT(*) AS total_rows,
    COUNT(email) AS non_null_emails,
    COUNT(DISTINCT email) AS unique_emails
FROM Employees;
```


