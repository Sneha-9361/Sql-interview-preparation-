# Sql-interview-preparation-

1. What is the difference between a Primary Key and a Unique Key?
 * Answer:
   * Primary Key: Uniquely identifies each record in a table. It cannot accept NULL values, and a table can have only one primary key.
   * Unique Key: Ensures that all values in a column are distinct. It can accept NULL values (typically one NULL depending on the database engine), and a table can have multiple unique keys.
 * Example:
   CREATE TABLE Employees (
       emp_id INT PRIMARY KEY,         -- Primary Key: Cannot be NULL or duplicated
       email VARCHAR(100) UNIQUE       -- Unique Key: Must be unique, but can be NULL
   );

2. What is the difference between the WHERE and HAVING clauses?
 * Answer:
   * WHERE: Filters individual rows before any aggregation or grouping happens. It cannot be used directly with aggregate functions like COUNT(), SUM(), or AVG().
   * HAVING: Filters grouped records after the GROUP BY operation is performed. It is specifically used with aggregate functions.
 * Example:
   SELECT department_id, COUNT(*) AS total_employees
   FROM Employees
   WHERE salary > 30000                 -- Filters rows before grouping
   GROUP BY department_id
   HAVING COUNT(*) > 5;                 -- Filters aggregated groups

3. What are the common types of JOINs in SQL?
 * Answer:
   * INNER JOIN: Returns only rows where there is a match in both tables.
   * LEFT JOIN (or LEFT OUTER JOIN): Returns all rows from the left table and matched rows from the right table. Unmatched right-side values appear as NULL.
   * RIGHT JOIN: Returns all rows from the right table and matched rows from the left table.
   * FULL OUTER JOIN: Returns all records when there is a match in either left or right table.
 * Example:
   SELECT Employees.emp_name, Departments.dept_name
   FROM Employees
   INNER JOIN Departments 
       ON Employees.dept_id = Departments.dept_id;

4. What is the difference between DELETE, TRUNCATE, and DROP?
 * Answer:
   * DELETE: A DML (Data Manipulation Language) command used to remove specific rows based on a WHERE condition. Operations are logged row-by-row and can be rolled back in a transaction.
   * TRUNCATE: A DDL (Data Definition Language) command that removes all rows from a table at once while keeping the table structure intact. It is faster than DELETE and resets identity/auto-increment counters.
   * DROP: A DDL command that removes both the data and the entire table structure permanently from the database schema.
 * Comparison Summary:
   * DELETE FROM Employees WHERE emp_id = 101; -- deletes selected rows
   * TRUNCATE TABLE Employees;                 -- empties all rows, keeps schema
   * DROP TABLE Employees;                     -- removes table completely

5. How do you find the second-highest salary in an employee table?
 * Answer:
   This is a classic query question used to test subqueries, aggregate functions, and ranking concepts.
 * Approach 1: Using a Subquery with MAX()
   Find the maximum salary that is strictly less than the overall maximum salary:
   SELECT MAX(salary) AS second_highest_salary
   FROM Employees
   WHERE salary < (SELECT MAX(salary) FROM Employees);

 * Approach 2: Using DENSE_RANK() (Window Function)
   Assigns ranks without skipping numbers when salaries are tied:
   WITH RankedSalaries AS (
       SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
       FROM Employees
   )
   SELECT salary AS second_highest_salary
   FROM RankedSalaries
   WHERE salary_rank = 2
   LIMIT 1;

6. What is the difference between UNION and UNION ALL?
 * Answer:
   * UNION: Combines the result of two queries and removes all duplicate rows. It is slower because it sorts and filters data to make rows distinct.
   * UNION ALL: Combines the result of two queries and keeps every row, including duplicates. It is faster because it does not check for duplicates.
 * Example:
   -- Table_A has (10, 20) and Table_B has (20, 30)

   -- Returns: 10, 20, 30
   SELECT id FROM Table_A
   UNION
   SELECT id FROM Table_B;

   -- Returns: 10, 20, 20, 30
   SELECT id FROM Table_A
   UNION ALL
   SELECT id FROM Table_B;

7. What is the difference between ROW_NUMBER(), RANK(), and DENSE_RANK()?
 * Answer:
   * ROW_NUMBER(): Gives a unique, continuous number (1, 2, 3, 4...) to every row. Numbers never repeat, even when values are identical.
   * RANK(): Gives the same number to duplicate values, but skips the next rank numbers (e.g., 1, 2, 2, 4).
   * DENSE_RANK(): Gives the same number to duplicate values, but never skips any numbers (e.g., 1, 2, 2, 3).

  8. How do you find duplicate records in a table?
 * Answer:
   To find duplicate records, group the table by the column(s) that should be unique and use HAVING COUNT(*) > 1 to filter out unique rows.
 * Example:
   SELECT email, COUNT(*) AS total_count
   FROM Employees
   GROUP BY email
   HAVING COUNT(*) > 1;

9. What is a CTE (Common Table Expression) and why is it used?
 * Answer:
   * A CTE is a temporary named result set defined at the beginning of a query using the WITH keyword.
   * Why it is used: It makes long, complex queries much easier to read, organize, and debug compared to deeply nested subqueries.
 * Example:
   WITH HighSalaryEmployees AS (
       SELECT emp_id, emp_name, department_id, salary
       FROM Employees
       WHERE salary > 50000
   )
   SELECT department_id, COUNT(*) AS total_high_earners
   FROM HighSalaryEmployees
   GROUP BY department_id;

10. What is the difference between the LAG() and LEAD() window functions?
 * Answer:
   * LAG(): Accesses data from a previous row in the same result set without needing a self join.
   * LEAD(): Accesses data from a following (next) row in the same result set without needing a self join.
 * Example:
   SELECT 
       emp_name,
       salary,
       LAG(salary, 1)  OVER (ORDER BY salary) AS previous_salary,
       LEAD(salary, 1) OVER (ORDER BY salary) AS next_salary
   FROM Employees;

   ### SQL Interview Preparation – Next 3 Questions

11\. What is the difference between CHAR and VARCHAR in SQL?

* CHAR: Fixed-length character data type. It reserves the specified amount of storage even if the actual text is shorter.

* VARCHAR: Variable-length character data type. It stores only the required characters (plus some storage overhead).

Example:

```sql
CREATE TABLE Employees (
    emp_code CHAR(10),
    emp_name VARCHAR(100)
);
```

If we store `'John'`, CHAR(10) uses a fixed length of 10 characters, whereas VARCHAR(100) stores the actual text length plus overhead.

12\. What is the difference between a Subquery and a CTE in SQL?

* Subquery: A query written inside another SQL query. It is commonly used to filter or retrieve data.

* CTE (Common Table Expression): A temporary named result set created using the WITH keyword. It makes complex queries easier to read and maintain.

Example – Subquery:

```sql
SELECT *
FROM Employees
WHERE salary > (
    SELECT AVG(salary)
    FROM Employees
);
```

Example – CTE:

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

13\. What is the difference between COUNT(\*), COUNT(column), and COUNT(DISTINCT column)?

* COUNT(\*): Counts all rows in a table, including rows containing NULL values.

* COUNT(column): Counts only non-NULL values in the specified column.

* COUNT(DISTINCT column): Counts only unique, non-NULL values in the specified column.

Example:

```sql
SELECT
    COUNT(*) AS total_rows,
    COUNT(email) AS non_null_emails,
    COUNT(DISTINCT email) AS unique_emails
FROM Employees;
```

Interview tip: Remember that `COUNT(*)` includes NULL-containing rows, whereas `COUNT(column)` ignores NULL values.

We can continue with 3 SQL interview questions at a time, keeping the same format with simple explanations and practical examples.

