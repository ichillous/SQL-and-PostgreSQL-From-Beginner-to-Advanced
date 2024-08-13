# MySQL Data Manipulation Language (DML) Cheatsheet

## Overview

Data Manipulation Language (DML) is used to retrieve, store, modify, delete, insert and update data in a database. MySQL supports the following DML statements:

- SELECT
- INSERT
- UPDATE
- DELETE
- MERGE (MySQL 8.0.31+)

## SELECT Statement

The SELECT statement is used to retrieve data from one or more tables.

### Basic Syntax:

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

### Key Clauses:

- **FROM**: Specifies the table(s) to query
- **WHERE**: Filters the results based on a condition
- **GROUP BY**: Groups rows that have the same values
- **HAVING**: Specifies a search condition for a group or an aggregate
- **ORDER BY**: Sorts the result set in ascending or descending order
- **LIMIT**: Specifies the maximum number of rows to return

### Examples:

```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns with a condition
SELECT first_name, last_name, salary 
FROM employees 
WHERE department = 'Sales';

-- Using GROUP BY and HAVING
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING avg_salary > 50000;

-- Using ORDER BY and LIMIT
SELECT first_name, last_name, hire_date
FROM employees
ORDER BY hire_date DESC
LIMIT 10;
```

## INSERT Statement

The INSERT statement is used to insert new records in a table.

### Basic Syntax:

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

### Examples:

```sql
-- Insert a single row
INSERT INTO employees (first_name, last_name, email, hire_date)
VALUES ('John', 'Doe', 'john.doe@example.com', '2023-01-15');

-- Insert multiple rows
INSERT INTO employees (first_name, last_name, email, hire_date)
VALUES 
('Jane', 'Smith', 'jane.smith@example.com', '2023-02-01'),
('Bob', 'Johnson', 'bob.johnson@example.com', '2023-02-15');

-- Insert data from another table
INSERT INTO archived_employees
SELECT * FROM employees
WHERE termination_date < '2023-01-01';
```

## UPDATE Statement

The UPDATE statement is used to modify existing records in a table.

### Basic Syntax:

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

### Examples:

```sql
-- Update a single column for all rows
UPDATE employees
SET salary = salary * 1.05;

-- Update multiple columns with a condition
UPDATE employees
SET salary = salary * 1.1, job_title = 'Senior Developer'
WHERE department = 'IT' AND years_of_service >= 5;

-- Update with subquery
UPDATE products
SET price = price * 1.1
WHERE category_id IN (SELECT id FROM categories WHERE name = 'Electronics');
```

## DELETE Statement

The DELETE statement is used to delete existing records in a table.

### Basic Syntax:

```sql
DELETE FROM table_name
WHERE condition;
```

### Examples:

```sql
-- Delete all records from a table
DELETE FROM temporary_logs;

-- Delete with a condition
DELETE FROM employees
WHERE termination_date < '2020-01-01';

-- Delete with a subquery
DELETE FROM orders
WHERE customer_id IN (SELECT id FROM customers WHERE last_purchase_date < '2022-01-01');
```

## MERGE Statement (MySQL 8.0.31+)

The MERGE statement (also known as UPSERT) allows you to INSERT new records or UPDATE existing ones in a single operation.

### Basic Syntax:

```sql
MERGE INTO target_table AS t
USING source_table AS s
ON (t.key_column = s.key_column)
WHEN MATCHED THEN
  UPDATE SET t.column1 = s.column1, t.column2 = s.column2
WHEN NOT MATCHED THEN
  INSERT (column1, column2) VALUES (s.column1, s.column2);
```

### Example:

```sql
MERGE INTO products AS t
USING product_updates AS s
ON (t.product_id = s.product_id)
WHEN MATCHED THEN
  UPDATE SET t.price = s.new_price, t.stock = t.stock + s.stock_change
WHEN NOT MATCHED THEN
  INSERT (product_id, product_name, price, stock)
  VALUES (s.product_id, s.product_name, s.new_price, s.stock_change);
```

## Best Practices

1. Always use WHERE clauses with UPDATE and DELETE to avoid unintended modifications.
2. Use transactions for complex operations to ensure data integrity.
3. Be cautious with large-scale updates or deletes; consider breaking them into smaller batches.
4. Use prepared statements or parameterized queries to prevent SQL injection.
5. Optimize your SELECT queries by only requesting the columns you need.
6. Use appropriate indexes to speed up your queries.

Remember to always test your DML statements in a safe environment before applying them to production databases.
