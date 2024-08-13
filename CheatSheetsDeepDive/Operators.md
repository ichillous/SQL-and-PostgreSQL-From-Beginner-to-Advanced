# MySQL Operators Cheatsheet

## Overview

MySQL supports a wide range of operators for performing various operations on data. These operators can be categorized into:

1. Arithmetic Operators
2. Comparison Operators
3. Logical Operators
4. Bitwise Operators
5. Assignment Operators
6. Other Operators

## 1. Arithmetic Operators

Used for performing mathematical operations.

| Operator | Description |
|----------|-------------|
| +        | Addition    |
| -        | Subtraction |
| *        | Multiplication |
| /        | Division    |
| %        | Modulo (remainder) |
| DIV      | Integer division |

### Examples

```sql
-- Simple
SELECT 10 + 5;  -- 15
SELECT 20 - 8;  -- 12

-- Intermediate
SELECT product_name, price, quantity, price * quantity AS total_value
FROM order_items;

-- Advanced
SELECT 
    order_id,
    total_amount,
    tax_rate,
    total_amount + (total_amount * tax_rate / 100) AS total_with_tax
FROM orders;
```

## 2. Comparison Operators

Used for comparing values.

| Operator | Description |
|----------|-------------|
| =        | Equal to    |
| <>, !=   | Not equal to |
| <        | Less than   |
| <=       | Less than or equal to |
| >        | Greater than |
| >=       | Greater than or equal to |
| BETWEEN  | Within a range |
| LIKE     | Pattern matching |
| IN       | Match any value in a list |
| IS NULL  | Is a null value |

### Examples

```sql
-- Simple
SELECT * FROM products WHERE price > 100;

-- Intermediate
SELECT * FROM employees 
WHERE hire_date BETWEEN '2020-01-01' AND '2020-12-31'
AND department IN ('Sales', 'Marketing');

-- Advanced
SELECT 
    product_name,
    price,
    CASE 
        WHEN price < 50 THEN 'Budget'
        WHEN price BETWEEN 50 AND 100 THEN 'Mid-range'
        ELSE 'Premium'
    END AS price_category
FROM products;
```

## 3. Logical Operators

Used for combining multiple conditions.

| Operator | Description |
|----------|-------------|
| AND      | True if all conditions are true |
| OR       | True if any condition is true |
| NOT      | Reverses the result of a condition |
| XOR      | True if one condition is true, but not both |

### Examples

```sql
-- Simple
SELECT * FROM orders 
WHERE status = 'Shipped' AND total_amount > 1000;

-- Intermediate
SELECT * FROM products 
WHERE (category = 'Electronics' AND price > 500) 
   OR (category = 'Books' AND price > 30);

-- Advanced
SELECT 
    customer_id,
    COUNT(*) AS order_count,
    AVG(total_amount) AS avg_order_value
FROM orders
WHERE status = 'Completed'
    AND order_date BETWEEN '2023-01-01' AND '2023-12-31'
    AND NOT EXISTS (
        SELECT 1 FROM order_issues 
        WHERE order_issues.order_id = orders.id
    )
GROUP BY customer_id
HAVING order_count > 5 AND avg_order_value > 200;
```

## 4. Bitwise Operators

Used for bit manipulation.

| Operator | Description |
|----------|-------------|
| &        | Bitwise AND |
| \|       | Bitwise OR  |
| ^        | Bitwise XOR |
| <<       | Left shift  |
| >>       | Right shift |
| ~        | Bitwise NOT |

### Examples

```sql
-- Simple
SELECT 5 & 3;  -- 1 (binary: 101 AND 011 = 001)

-- Intermediate
SELECT id, permissions
FROM user_roles
WHERE (permissions & 4) > 0;  -- Check if 'write' permission bit is set

-- Advanced
UPDATE user_roles
SET permissions = permissions | 8  -- Add 'delete' permission
WHERE role = 'moderator';
```

## 5. Assignment Operators

Used for assigning values.

| Operator | Description |
|----------|-------------|
| =        | Assign value |
| :=       | Assign value (can be used in SET statements) |

### Examples

```sql
-- Simple
SET @var1 = 10;

-- Intermediate
SELECT @row_num := @row_num + 1 AS row_num, id, name
FROM users, (SELECT @row_num := 0) AS init
ORDER BY name;

-- Advanced
UPDATE products
SET price = CASE
    WHEN stock > 100 THEN price * 0.9  -- 10% discount
    WHEN stock < 10 THEN price * 1.1   -- 10% markup
    ELSE price
END;
```

## 6. Other Operators

| Operator | Description |
|----------|-------------|
| LIKE     | Pattern matching |
| REGEXP   | Regular expression matching |
| IS       | Test a value against a boolean |
| IN       | Check if a value is in a list |
| BETWEEN  | Check if a value is within a range |

### Examples

```sql
-- Simple
SELECT * FROM customers WHERE name LIKE 'John%';

-- Intermediate
SELECT * FROM products WHERE description REGEXP 'water(proof|resistant)';

-- Advanced
WITH RECURSIVE category_tree AS (
    SELECT id, name, parent_id, 0 AS level
    FROM categories
    WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.name, c.parent_id, ct.level + 1
    FROM categories c
    JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree
WHERE level BETWEEN 1 AND 3
ORDER BY level, name;
```

## Best Practices

1. Use appropriate operators to make your queries more efficient.
2. Be cautious with NULL values when using comparison operators.
3. Use parentheses to make complex logical conditions more readable.
4. Optimize your queries by using appropriate indexes when using operators in WHERE clauses.
5. Be aware of operator precedence, especially when combining multiple operators.
6. Use EXPLAIN to understand how MySQL is executing queries with complex operators.
7. When using pattern matching (LIKE or REGEXP), be aware of performance implications for large datasets.
8. Consider using prepared statements when building dynamic queries with operators to prevent SQL injection.

Remember to test your queries thoroughly, especially when using complex combinations of operators, to ensure they produce the expected results and perform well with your dataset.
