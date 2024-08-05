# Introduction to SQL 🗃️💻

![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=sql&logoColor=white)

Welcome to the fascinating world of SQL (Structured Query Language)! This guide will introduce you to the fundamental concepts of SQL, providing a solid foundation for your journey into database management and querying.

## Table of Contents
- [What is SQL?](#what-is-sql)
- [Why Learn SQL?](#why-learn-sql)
- [Basic SQL Concepts](#basic-sql-concepts)
- [SQL Data Types](#sql-data-types)
- [SQL Commands](#sql-commands)
- [Basic SQL Queries](#basic-sql-queries)
- [SQL Operators](#sql-operators)
- [SQL Functions](#sql-functions)
- [SQL Joins](#sql-joins)
- [Subqueries](#subqueries)
- [Best Practices](#best-practices)
- [Exercises](#exercises)

## What is SQL? 🤔

SQL (Structured Query Language) is a standardized language used for managing and manipulating relational databases. It allows you to:

- Create, modify, and delete database structures
- Insert, update, and delete data
- Retrieve information from databases

SQL is used by various database management systems (DBMS) like PostgreSQL, MySQL, Oracle, and Microsoft SQL Server.

## Why Learn SQL? 🎯

1. **Data-Driven Decision Making**: SQL allows you to extract valuable insights from large datasets.
2. **Career Opportunities**: SQL skills are in high demand across various industries.
3. **Data Analysis**: SQL is crucial for data analysis and business intelligence.
4. **Database Management**: SQL is essential for efficient database design and management.
5. **Integration**: Many applications and tools use SQL for data storage and retrieval.

## Basic SQL Concepts 📚

### 1. Database
A structured collection of data organized into tables.

### 2. Table
A collection of related data entries consisting of rows and columns.

### 3. Column (Field)
A vertical entity in a table that contains specific information about every record in the table.

### 4. Row (Record)
A horizontal entity in a table that represents a single, implicitly structured data item in a table.

### 5. Primary Key
A column (or a combination of columns) that uniquely identifies each row in a table.

### 6. Foreign Key
A column that creates a relationship between two tables.

## SQL Data Types 📊

SQL supports various data types. Here are some common ones:

| Data Type | Description | Example |
|-----------|-------------|---------|
| INTEGER   | Whole numbers | 42, -17, 0 |
| DECIMAL   | Decimal numbers | 3.14, 2.5, -0.1 |
| VARCHAR   | Variable-length character string | 'Hello', 'SQL' |
| DATE      | Date in YYYY-MM-DD format | '2023-05-15' |
| BOOLEAN   | True or False values | TRUE, FALSE |
| TIMESTAMP | Date and time | '2023-05-15 14:30:00' |

## SQL Commands 🛠️

SQL commands are divided into several categories:

1. **Data Definition Language (DDL)**
   - CREATE: Create database objects (tables, indexes, etc.)
   - ALTER: Modify the structure of database objects
   - DROP: Delete database objects
   - TRUNCATE: Remove all records from a table

2. **Data Manipulation Language (DML)**
   - SELECT: Retrieve data from one or more tables
   - INSERT: Add new records to a table
   - UPDATE: Modify existing records
   - DELETE: Remove records from a table

3. **Data Control Language (DCL)**
   - GRANT: Give privileges to users
   - REVOKE: Remove privileges from users

4. **Transaction Control Language (TCL)**
   - COMMIT: Save the changes
   - ROLLBACK: Undo the changes
   - SAVEPOINT: Create points within groups of transactions

## Basic SQL Queries 🔍

### SELECT Statement
The SELECT statement is used to retrieve data from one or more tables.

```sql
SELECT column1, column2
FROM table_name
WHERE condition;
```

Example:
```sql
SELECT first_name, last_name
FROM employees
WHERE department = 'Sales';
```

### INSERT Statement
The INSERT statement is used to add new records to a table.

```sql
INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);
```

Example:
```sql
INSERT INTO customers (customer_id, name, email)
VALUES (1, 'John Doe', 'john@example.com');
```

### UPDATE Statement
The UPDATE statement is used to modify existing records in a table.

```sql
UPDATE table_name
SET column1 = value1, column2 = value2
WHERE condition;
```

Example:
```sql
UPDATE employees
SET salary = 55000
WHERE employee_id = 1001;
```

### DELETE Statement
The DELETE statement is used to remove records from a table.

```sql
DELETE FROM table_name
WHERE condition;
```

Example:
```sql
DELETE FROM orders
WHERE order_date < '2022-01-01';
```

## SQL Operators 🧮

SQL supports various operators for performing operations and comparisons:

1. **Arithmetic Operators**: +, -, *, /, %
2. **Comparison Operators**: =, <>, <, >, <=, >=
3. **Logical Operators**: AND, OR, NOT
4. **LIKE Operator**: Used for pattern matching with wildcard characters
   - % (percent sign): Represents zero, one, or multiple characters
   - _ (underscore): Represents a single character

Example:
```sql
SELECT *
FROM products
WHERE product_name LIKE 'Apple%' AND price > 500;
```

## SQL Functions 🧰

SQL provides numerous built-in functions for data manipulation and calculation:

1. **Aggregate Functions**
   - COUNT(): Counts the number of rows
   - SUM(): Calculates the sum of a set of values
   - AVG(): Calculates the average of a set of values
   - MAX(): Returns the maximum value
   - MIN(): Returns the minimum value

2. **String Functions**
   - CONCAT(): Concatenates two or more strings
   - UPPER(): Converts a string to uppercase
   - LOWER(): Converts a string to lowercase
   - LENGTH(): Returns the length of a string

3. **Date Functions**
   - NOW(): Returns the current date and time
   - DATEADD(): Adds a time/date interval to a date
   - DATEDIFF(): Calculates the difference between two dates

Example:
```sql
SELECT department, COUNT(*) as employee_count, AVG(salary) as avg_salary
FROM employees
GROUP BY department;
```

## SQL Joins 🔗

SQL joins are used to combine rows from two or more tables based on a related column between them.

1. **INNER JOIN**: Returns records that have matching values in both tables
2. **LEFT JOIN**: Returns all records from the left table and matched records from the right table
3. **RIGHT JOIN**: Returns all records from the right table and matched records from the left table
4. **FULL OUTER JOIN**: Returns all records when there's a match in either left or right table

Example:
```sql
SELECT orders.order_id, customers.customer_name, orders.order_date
FROM orders
INNER JOIN customers ON orders.customer_id = customers.customer_id;
```

## Subqueries 🔍🔍

A subquery is a query nested inside another query. It can be used in various parts of an SQL statement.

Example:
```sql
SELECT employee_name, department
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE location = 'New York'
);
```

## Best Practices 🌟

1. Use meaningful table and column names
2. Use appropriate data types
3. Normalize your database to reduce redundancy
4. Use indexes for frequently queried columns
5. Write clear and readable queries
6. Use parameterized queries to prevent SQL injection
7. Regularly backup your database

## Exercises 🏋️‍♀️

To reinforce your learning, try these exercises:

1. Create a table named `books` with columns: `book_id`, `title`, `author`, `publication_year`, and `price`.
2. Insert at least 5 records into the `books` table.
3. Write a query to select all books published after 2000.
4. Update the price of a specific book.
5. Delete a book from the table.
6. Write a query to find the average price of books by each author.
7. Create a new table `authors` and establish a relationship with the `books` table.
8. Write a join query to display book titles along with their authors' names.

Remember, practice is key to mastering SQL. Happy querying! 🚀💽
