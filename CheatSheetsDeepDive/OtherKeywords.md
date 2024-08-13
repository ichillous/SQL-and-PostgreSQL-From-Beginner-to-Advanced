# MySQL Other Keywords Cheatsheet

## Overview

This cheatsheet covers important MySQL keywords and clauses that are essential for writing efficient and powerful queries. These include:

1. DISTINCT
2. LIMIT and OFFSET
3. ORDER BY
4. GROUP BY and HAVING
5. UNION and UNION ALL
6. JOIN types
7. Subqueries
8. EXISTS and NOT EXISTS
9. CASE
10. WITH (Common Table Expressions)

## 1. DISTINCT

DISTINCT is used to return only unique values in the result set.

### Examples

```sql
-- Simple: Get unique categories
SELECT DISTINCT category FROM products;

-- Intermediate: Get unique combinations
SELECT DISTINCT city, state FROM customers;

-- Advanced: Count distinct values
SELECT COUNT(DISTINCT category) AS unique_categories FROM products;
```

## 2. LIMIT and OFFSET

LIMIT is used to restrict the number of rows returned. OFFSET is used to skip a number of rows.

### Examples

```sql
-- Simple: Get top 5 products
SELECT * FROM products ORDER BY price DESC LIMIT 5;

-- Intermediate: Pagination
SELECT * FROM products LIMIT 10 OFFSET 20; -- Get rows 21-30

-- Advanced: Get top 3 products per category
SELECT * FROM (
    SELECT *, 
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY price DESC) as row_num
    FROM products
) ranked
WHERE row_num <= 3;
```

## 3. ORDER BY

ORDER BY is used to sort the result set.

### Examples

```sql
-- Simple: Sort by a single column
SELECT * FROM products ORDER BY price DESC;

-- Intermediate: Sort by multiple columns
SELECT * FROM orders ORDER BY customer_id ASC, order_date DESC;

-- Advanced: Sort with expression
SELECT *, 
       (price * (1 - discount)) AS final_price
FROM products
ORDER BY final_price DESC;
```

## 4. GROUP BY and HAVING

GROUP BY is used to group rows that have the same values. HAVING is used to specify conditions for groups.

### Examples

```sql
-- Simple: Group and count
SELECT category, COUNT(*) AS product_count
FROM products
GROUP BY category;

-- Intermediate: Group with HAVING clause
SELECT category, AVG(price) AS avg_price
FROM products
GROUP BY category
HAVING avg_price > 100;

-- Advanced: Complex grouping and filtering
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    SUM(total_amount) AS total_sales,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM orders
WHERE status = 'Completed'
GROUP BY YEAR(order_date), MONTH(order_date)
HAVING total_sales > 10000 AND unique_customers > 100
ORDER BY year DESC, month DESC;
```

## 5. UNION and UNION ALL

UNION is used to combine result sets of multiple SELECT statements. UNION ALL does the same but includes duplicates.

### Examples

```sql
-- Simple: Combine two queries
SELECT 'Customer' AS type, name FROM customers
UNION
SELECT 'Employee' AS type, name FROM employees;

-- Intermediate: UNION ALL with condition
SELECT product_id, 'Low Stock' AS status FROM products WHERE stock < 10
UNION ALL
SELECT product_id, 'Out of Stock' AS status FROM products WHERE stock = 0;

-- Advanced: Complex UNION query
SELECT 
    'Best Selling' AS category,
    product_id,
    SUM(quantity) AS total_sold
FROM order_items
GROUP BY product_id
ORDER BY total_sold DESC
LIMIT 5

UNION ALL

SELECT 
    'Least Selling' AS category,
    product_id,
    SUM(quantity) AS total_sold
FROM order_items
GROUP BY product_id
ORDER BY total_sold ASC
LIMIT 5;
```

## 6. JOIN Types

JOINs are used to combine rows from two or more tables based on a related column between them.

### Examples

```sql
-- Simple: INNER JOIN
SELECT orders.id, customers.name
FROM orders
INNER JOIN customers ON orders.customer_id = customers.id;

-- Intermediate: LEFT JOIN with multiple conditions
SELECT p.name, c.name AS category, s.quantity AS stock
FROM products p
LEFT JOIN categories c ON p.category_id = c.id
LEFT JOIN stock s ON p.id = s.product_id AND s.location_id = 1;

-- Advanced: Multiple JOINs with subquery
SELECT 
    o.id AS order_id,
    c.name AS customer_name,
    p.name AS product_name,
    oi.quantity,
    oi.unit_price,
    (oi.quantity * oi.unit_price) AS total_price
FROM orders o
JOIN customers c ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
JOIN (
    SELECT order_id, SUM(quantity * unit_price) AS order_total
    FROM order_items
    GROUP BY order_id
    HAVING order_total > 1000
) high_value ON o.id = high_value.order_id
WHERE o.order_date >= DATE_SUB(CURDATE(), INTERVAL 30 DAY);
```

## 7. Subqueries

A subquery is a query nested inside another query.

### Examples

```sql
-- Simple: Subquery in WHERE clause
SELECT * FROM products
WHERE price > (SELECT AVG(price) FROM products);

-- Intermediate: Subquery in FROM clause
SELECT category, avg_price
FROM (
    SELECT category, AVG(price) AS avg_price
    FROM products
    GROUP BY category
) AS category_averages
WHERE avg_price > 100;

-- Advanced: Correlated subquery
SELECT o.id, o.order_date, o.total_amount,
    (SELECT COUNT(*) 
     FROM order_items oi 
     WHERE oi.order_id = o.id) AS item_count
FROM orders o
WHERE o.total_amount > 1000;
```

## 8. EXISTS and NOT EXISTS

EXISTS is used to test for the existence of rows that satisfy a subquery.

### Examples

```sql
-- Simple: EXISTS
SELECT * FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o
    WHERE o.customer_id = c.id
);

-- Intermediate: NOT EXISTS
SELECT * FROM products p
WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi
    WHERE oi.product_id = p.id
);

-- Advanced: Complex EXISTS query
SELECT d.name AS department, e.name AS employee
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id
WHERE EXISTS (
    SELECT 1
    FROM projects p
    JOIN employee_projects ep ON p.id = ep.project_id
    WHERE ep.employee_id = e.id
    GROUP BY ep.employee_id
    HAVING COUNT(DISTINCT p.id) > 2
);
```

## 9. CASE

CASE is used for conditional processing in a SELECT statement.

### Examples

```sql
-- Simple: Basic CASE
SELECT 
    product_name,
    price,
    CASE 
        WHEN price < 50 THEN 'Low'
        WHEN price BETWEEN 50 AND 100 THEN 'Medium'
        ELSE 'High'
    END AS price_category
FROM products;

-- Intermediate: CASE with aggregation
SELECT 
    category,
    SUM(CASE WHEN stock > 0 THEN 1 ELSE 0 END) AS in_stock,
    SUM(CASE WHEN stock = 0 THEN 1 ELSE 0 END) AS out_of_stock
FROM products
GROUP BY category;

-- Advanced: Complex CASE in a JOIN
SELECT 
    o.id AS order_id,
    c.name AS customer_name,
    SUM(oi.quantity * oi.unit_price) AS total_amount,
    CASE 
        WHEN SUM(oi.quantity * oi.unit_price) < 100 THEN 'Small Order'
        WHEN SUM(oi.quantity * oi.unit_price) BETWEEN 100 AND 1000 THEN 'Medium Order'
        ELSE 'Large Order'
    END AS order_size,
    CASE 
        WHEN c.total_orders > 10 THEN 'Loyal Customer'
        WHEN c.total_orders > 5 THEN 'Regular Customer'
        ELSE 'New Customer'
    END AS customer_status
FROM orders o
JOIN customers c ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
GROUP BY o.id, c.name, c.total_orders;
```

## 10. WITH (Common Table Expressions)

WITH clause, also known as Common Table Expressions (CTE), allows you to name a subquery and reference it later in the main query.

### Examples

```sql
-- Simple: Basic CTE
WITH high_value_products AS (
    SELECT * FROM products WHERE price > 1000
)
SELECT * FROM high_value_products WHERE stock > 0;

-- Intermediate: Multiple CTEs
WITH 
    monthly_sales AS (
        SELECT 
            DATE_FORMAT(order_date, '%Y-%m') AS month,
            SUM(total_amount) AS total_sales
        FROM orders
        GROUP BY DATE_FORMAT(order_date, '%Y-%m')
    ),
    avg_monthly_sales AS (
        SELECT AVG(total_sales) AS avg_sales
        FROM monthly_sales
    )
SELECT 
    ms.month,
    ms.total_sales,
    ams.avg_sales,
    ms.total_sales - ams.avg_sales AS sales_difference
FROM monthly_sales ms, avg_monthly_sales ams
ORDER BY ms.month;

-- Advanced: Recursive CTE
WITH RECURSIVE category_tree AS (
    SELECT id, name, parent_id, 0 AS level
    FROM categories
    WHERE parent_id IS NULL
    
    UNION ALL
    
    SELECT c.id, c.name, c.parent_id, ct.level + 1
    FROM categories c
    JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT 
    CONCAT(REPEAT('  ', level), name) AS category_hierarchy,
    level
FROM category_tree
ORDER BY level, name;
```

## Best Practices

1. Use DISTINCT judiciously, as it can impact query performance.
2. Utilize LIMIT for pagination and to restrict large result sets.
3. Optimize ORDER BY clauses by creating appropriate indexes.
4. Be cautious with GROUP BY and HAVING on large datasets; consider indexing grouped columns.
5. Prefer UNION ALL over UNION when duplicates are acceptable, for better performance.
6. Choose the appropriate JOIN type based on your data and query requirements.
7. Use subqueries sparingly and consider alternatives like JOINs for better performance.
8. Leverage EXISTS for efficiency when checking for the presence of related data.
9. Use CASE statements to simplify complex conditional logic in your queries.
10. Employ Common Table Expressions (WITH clause) to improve the readability and maintainability of complex queries.

Remember to analyze and optimize your queries using EXPLAIN, and always test thoroughly with representative data volumes to ensure good performance.
