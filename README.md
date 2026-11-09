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
 * Example:
   SELECT
