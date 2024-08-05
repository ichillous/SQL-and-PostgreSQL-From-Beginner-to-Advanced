# Advanced SQL Queries in PostgreSQL 🚀

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

Welcome to the world of advanced SQL queries in PostgreSQL! This guide will take your SQL skills to the next level, introducing you to powerful techniques and PostgreSQL-specific features for complex data manipulation and analysis.

## Table of Contents
- [Common Table Expressions (CTEs)](#common-table-expressions-ctes)
- [Window Functions](#window-functions)
- [Advanced Joins](#advanced-joins)
- [Subqueries and Derived Tables](#subqueries-and-derived-tables)
- [Set Operations](#set-operations)
- [Recursive Queries](#recursive-queries)
- [Full-Text Search](#full-text-search)
- [JSON and JSONB Operations](#json-and-jsonb-operations)
- [Array Operations](#array-operations)
- [Pivot and Unpivot Operations](#pivot-and-unpivot-operations)
- [Advanced Aggregations](#advanced-aggregations)
- [Performance Optimization](#performance-optimization)
- [Exercises](#exercises)

## Common Table Expressions (CTEs) 📊

CTEs provide a way to write auxiliary statements in larger queries, similar to temporary views.

```sql
WITH regional_sales AS (
    SELECT region, SUM(amount) AS total_sales
    FROM orders
    GROUP BY region
), top_regions AS (
    SELECT region
    FROM regional_sales
    WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales)
)
SELECT region,
       product,
       SUM(quantity) AS product_units,
       SUM(amount) AS product_sales
FROM orders
WHERE region IN (SELECT region FROM top_regions)
GROUP BY region, product;
```

## Window Functions 🪟

Window functions perform calculations across a set of rows that are related to the current row.

```sql
SELECT 
    department,
    employee_name,
    salary,
    AVG(salary) OVER (PARTITION BY department) AS dept_avg,
    salary - AVG(salary) OVER (PARTITION BY department) AS diff_from_avg
FROM employees;
```

Common window functions:
- `ROW_NUMBER()`: Assigns a unique number to each row
- `RANK()`: Assigns a rank with gaps
- `DENSE_RANK()`: Assigns a rank without gaps
- `LAG()` and `LEAD()`: Access data from previous or subsequent rows

## Advanced Joins 🔗

### Self Join
Used to join a table to itself:

```sql
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.employee_id;
```

### FULL OUTER JOIN
Returns all rows when there is a match in either left or right table:

```sql
SELECT customers.name, orders.order_id
FROM customers
FULL OUTER JOIN orders ON customers.customer_id = orders.customer_id;
```

### CROSS JOIN
Produces a Cartesian product of two tables:

```sql
SELECT products.name, colors.color
FROM products
CROSS JOIN colors;
```

## Subqueries and Derived Tables 🔍

Subqueries can be used in various parts of a SQL statement:

```sql
SELECT employee_name, department, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
) AND department IN (
    SELECT department
    FROM departments
    WHERE location = 'New York'
);
```

Derived tables (subquery in the FROM clause):

```sql
SELECT dt.department, dt.avg_salary
FROM (
    SELECT department, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
) AS dt
WHERE dt.avg_salary > 50000;
```

## Set Operations 🔢

Set operations combine the results of two or more SELECT statements:

### UNION
Combines results and removes duplicates:

```sql
SELECT product_id FROM products
UNION
SELECT product_id FROM discontinued_products;
```

### INTERSECT
Returns only rows that appear in both result sets:

```sql
SELECT customer_id FROM active_customers
INTERSECT
SELECT customer_id FROM customers_with_orders;
```

### EXCEPT
Returns rows from the first query that do not appear in the second query:

```sql
SELECT employee_id FROM all_employees
EXCEPT
SELECT employee_id FROM terminated_employees;
```

## Recursive Queries 🔄

Recursive CTEs are useful for hierarchical or tree-structured data:

```sql
WITH RECURSIVE subordinates AS (
    SELECT employee_id, manager_id, name
    FROM employees
    WHERE name = 'Jane Doe'
    UNION ALL
    SELECT e.employee_id, e.manager_id, e.name
    FROM employees e
    INNER JOIN subordinates s ON s.employee_id = e.manager_id
)
SELECT * FROM subordinates;
```

## Full-Text Search 🔎

PostgreSQL provides powerful full-text search capabilities:

```sql
CREATE INDEX articles_idx ON articles USING GIN (to_tsvector('english', title || ' ' || body));

SELECT title, ts_rank(to_tsvector('english', title || ' ' || body), to_tsquery('english', 'postgresql & database')) AS rank
FROM articles
WHERE to_tsvector('english', title || ' ' || body) @@ to_tsquery('english', 'postgresql & database')
ORDER BY rank DESC;
```

## JSON and JSONB Operations 📦

PostgreSQL offers robust support for JSON data:

```sql
CREATE TABLE events (
    id SERIAL PRIMARY KEY,
    data JSONB
);

INSERT INTO events (data) VALUES 
('{"user": "johndoe", "action": "login", "timestamp": "2023-05-15T14:30:00Z"}');

SELECT * FROM events WHERE data @> '{"action": "login"}';

SELECT data->>'user' AS username, data->>'action' AS action
FROM events
WHERE (data->>'timestamp')::timestamp > '2023-05-01';
```

## Array Operations 📚

Working with array columns in PostgreSQL:

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT,
    tags TEXT[]
);

INSERT INTO products (name, tags) VALUES 
('Laptop', ARRAY['electronics', 'computers']),
('Coffee Maker', ARRAY['appliances', 'kitchen']);

SELECT * FROM products WHERE 'electronics' = ANY(tags);

SELECT name, unnest(tags) AS tag FROM products;
```

## Pivot and Unpivot Operations 🔄

### Pivot
Transform rows into columns:

```sql
SELECT *
FROM crosstab(
    'SELECT department, job_title, COUNT(*) 
     FROM employees 
     GROUP BY department, job_title 
     ORDER BY department, job_title',
    'SELECT DISTINCT job_title FROM employees ORDER BY job_title'
) AS ct (department TEXT, manager INT, developer INT, designer INT);
```

### Unpivot
Transform columns into rows:

```sql
SELECT department, 'manager' AS job_title, manager AS count
FROM pivoted_data
UNION ALL
SELECT department, 'developer' AS job_title, developer AS count
FROM pivoted_data
UNION ALL
SELECT department, 'designer' AS job_title, designer AS count
FROM pivoted_data;
```

## Advanced Aggregations 📊

### FILTER Clause
Allows you to apply a WHERE condition to an aggregate function:

```sql
SELECT 
    department,
    COUNT(*) AS total_employees,
    COUNT(*) FILTER (WHERE salary > 50000) AS high_paid_employees
FROM employees
GROUP BY department;
```

### GROUPING SETS
Allows specification of multiple groupings in a single query:

```sql
SELECT 
    COALESCE(department, 'All Departments') AS department,
    COALESCE(job_title, 'All Positions') AS job_title,
    COUNT(*) AS employee_count
FROM employees
GROUP BY GROUPING SETS ((department, job_title), (department), (job_title), ());
```

## Performance Optimization 🚀

1. Use `EXPLAIN ANALYZE` to understand query execution plans:

```sql
EXPLAIN ANALYZE
SELECT * FROM large_table WHERE id > 1000 AND category = 'A';
```

2. Create appropriate indexes:

```sql
CREATE INDEX idx_large_table_id_category ON large_table(id, category);
```

3. Use `VACUUM` and `ANALYZE` regularly to update statistics and reclaim space:

```sql
VACUUM ANALYZE large_table;
```

4. Consider partitioning large tables:

```sql
CREATE TABLE measurements (
    city_id         int not null,
    logdate         date not null,
    peaktemp        int,
    unitsales       int
) PARTITION BY RANGE (logdate);

CREATE TABLE measurements_y2020 PARTITION OF measurements
    FOR VALUES FROM ('2020-01-01') TO ('2021-01-01');
```

## Exercises 🏋️‍♀️

1. Create a CTE to find the top 5 customers by total purchase amount.
2. Use window functions to rank products within each category by their sales volume.
3. Write a recursive query to display an employee hierarchy.
4. Implement a full-text search on a table with product descriptions.
5. Create a query using JSON operations to find all events with a specific user action.
6. Write a query to pivot sales data by quarter and product category.
7. Use advanced aggregations to calculate running totals and moving averages of daily sales.
8. Optimize a slow-running query on a large table using EXPLAIN ANALYZE and appropriate indexing.

Remember, mastering these advanced SQL techniques takes practice and experimentation. Don't hesitate to try out these queries on your own PostgreSQL database! Happy querying! 🎉🐘
