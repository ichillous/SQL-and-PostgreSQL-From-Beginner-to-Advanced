# Advanced PostgreSQL Features 🚀

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

Welcome to the guide on Advanced PostgreSQL Features! This README will introduce you to some of the more sophisticated and powerful capabilities that PostgreSQL offers, setting it apart from other database management systems.

## Table of Contents
- [JSON and JSONB Data Types](#json-and-jsonb-data-types)
- [Full-Text Search](#full-text-search)
- [Window Functions](#window-functions)
- [Common Table Expressions (CTEs)](#common-table-expressions-ctes)
- [Materialized Views](#materialized-views)
- [Table Partitioning](#table-partitioning)
- [Inheritance](#inheritance)
- [Custom Data Types](#custom-data-types)
- [Foreign Data Wrappers](#foreign-data-wrappers)
- [Parallel Query Execution](#parallel-query-execution)
- [Logical Replication](#logical-replication)
- [Event Triggers](#event-triggers)
- [Extensions](#extensions)
- [Exercises](#exercises)

## JSON and JSONB Data Types 📦

PostgreSQL provides robust support for JSON data, with two types: JSON and JSONB (Binary JSON).

JSONB advantages:
- Faster to process
- Supports indexing

Creating a table with JSONB:
```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    info JSONB
);
```

Inserting JSONB data:
```sql
INSERT INTO orders (info) VALUES ('{"customer": "John Doe", "items": [{"product": "Phone", "quantity": 1}, {"product": "Laptop", "quantity": 1}]}');
```

Querying JSONB:
```sql
SELECT info->'customer' AS customer
FROM orders
WHERE info @> '{"items": [{"product": "Phone"}]}';
```

Creating a GIN index for JSONB:
```sql
CREATE INDEX idxgin ON orders USING GIN (info);
```

## Full-Text Search 🔍

PostgreSQL offers powerful full-text search capabilities.

Creating a tsvector column:
```sql
ALTER TABLE articles ADD COLUMN textsearchable_index_col tsvector
    GENERATED ALWAYS AS (to_tsvector('english', coalesce(title,'') || ' ' || coalesce(body,''))) STORED;
```

Creating a GIN index for full-text search:
```sql
CREATE INDEX textsearch_idx ON articles USING GIN (textsearchable_index_col);
```

Performing a full-text search:
```sql
SELECT title
FROM articles
WHERE textsearchable_index_col @@ to_tsquery('english', 'postgresql & database');
```

## Window Functions 🪟

Window functions perform calculations across a set of rows that are related to the current row.

Example of a window function:
```sql
SELECT 
    department,
    employee,
    salary,
    AVG(salary) OVER (PARTITION BY department) as dept_avg_salary
FROM employees;
```

## Common Table Expressions (CTEs) 🔄

CTEs provide a way to write auxiliary statements for use in a larger query.

Recursive CTE example:
```sql
WITH RECURSIVE subordinates AS (
    SELECT employee_id, manager_id, full_name
    FROM employees
    WHERE employee_id = 1
    UNION ALL
    SELECT e.employee_id, e.manager_id, e.full_name
    FROM employees e
    INNER JOIN subordinates s ON s.employee_id = e.manager_id
)
SELECT * FROM subordinates;
```

## Materialized Views 📊

Materialized views store the results of a query for faster access.

Creating a materialized view:
```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT 
    date_trunc('month', order_date) AS month,
    SUM(amount) AS total_sales
FROM orders
GROUP BY 1
WITH DATA;
```

Refreshing a materialized view:
```sql
REFRESH MATERIALIZED VIEW monthly_sales;
```

## Table Partitioning 📂

Partitioning can significantly improve query performance for very large tables.

Example of range partitioning:
```sql
CREATE TABLE measurements (
    logdate date not null,
    peaktemp int,
    unitsales int
) PARTITION BY RANGE (logdate);

CREATE TABLE measurements_y2020 PARTITION OF measurements
    FOR VALUES FROM ('2020-01-01') TO ('2021-01-01');

CREATE TABLE measurements_y2021 PARTITION OF measurements
    FOR VALUES FROM ('2021-01-01') TO ('2022-01-01');
```

## Inheritance 🧬

PostgreSQL supports table inheritance, allowing for a table to inherit from another table.

Example:
```sql
CREATE TABLE cities (
    name        text,
    population  float,
    elevation   int     -- in feet
);

CREATE TABLE capitals (
    state      char(2)
) INHERITS (cities);
```

## Custom Data Types 🎨

PostgreSQL allows you to create custom data types.

Creating a composite type:
```sql
CREATE TYPE inventory_item AS (
    name            text,
    supplier_id     integer,
    price           numeric
);

CREATE TABLE on_hand (
    item      inventory_item,
    count     integer
);
```

## Foreign Data Wrappers 🌐

Foreign Data Wrappers allow you to access data stored outside your PostgreSQL database.

Example using file_fdw:
```sql
CREATE EXTENSION file_fdw;

CREATE SERVER csv_server FOREIGN DATA WRAPPER file_fdw;

CREATE FOREIGN TABLE foreign_employees (
    id int,
    name text,
    department text
) SERVER csv_server
OPTIONS ( filename '/path/to/employees.csv', format 'csv' );
```

## Parallel Query Execution ⚡

PostgreSQL can utilize multiple CPU cores to answer queries faster.

Enabling parallel query execution:
```sql
SET max_parallel_workers_per_gather = 4;
```

## Logical Replication 📡

Logical replication allows more flexible replication scenarios.

Setting up a publication:
```sql
CREATE PUBLICATION mypub FOR TABLE users, orders;
```

Setting up a subscription:
```sql
CREATE SUBSCRIPTION mysub
CONNECTION 'host=192.168.1.50 port=5432 dbname=postgres'
PUBLICATION mypub;
```

## Event Triggers 🎣

Event triggers allow you to execute a procedure when certain database events occur.

Creating an event trigger:
```sql
CREATE OR REPLACE FUNCTION log_ddl_func() RETURNS event_trigger AS $$
BEGIN
    RAISE NOTICE 'DDL operation performed: % %', tg_tag, tg_event;
END;
$$ LANGUAGE plpgsql;

CREATE EVENT TRIGGER log_ddl ON ddl_command_start
    EXECUTE FUNCTION log_ddl_func();
```

## Extensions 🧩

PostgreSQL's functionality can be extended through extensions.

Installing an extension:
```sql
CREATE EXTENSION pg_stat_statements;
```

Using the extension:
```sql
SELECT query, calls, total_time, rows, 100.0 * shared_blks_hit /
               nullif(shared_blks_hit + shared_blks_read, 0) AS hit_percent
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 5;
```

## Exercises 🏋️‍♀️

1. Create a table with a JSONB column to store product details. Write queries to:
   a) Insert a new product
   b) Update a specific field in the JSONB data
   c) Find all products with a specific attribute

2. Implement full-text search on a table containing article titles and bodies. Create appropriate indexes and write a query to find articles related to a specific topic.

3. Use a window function to rank employees by their salary within each department.

4. Write a recursive CTE to generate a sequence of Fibonacci numbers up to the 20th number in the sequence.

5. Create a partitioned table for storing temperature readings by year. Insert some sample data and write queries to demonstrate the partition pruning in action.

6. Create a custom data type for representing a 2D point with x and y coordinates. Create a table using this data type and write a function to calculate the distance between two points.

7. Set up logical replication between two PostgreSQL databases for a specific set of tables.

Remember, these advanced features provide powerful tools for complex data management and processing tasks. Experimenting with these features will deepen your understanding of PostgreSQL's capabilities. Happy coding! 🚀🐘
