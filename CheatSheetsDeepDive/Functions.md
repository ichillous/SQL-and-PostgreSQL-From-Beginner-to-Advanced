# MySQL Functions Cheatsheet

## Overview

MySQL provides a wide array of built-in functions for data manipulation, calculation, and analysis. These functions can be categorized into:

1. String Functions
2. Numeric Functions
3. Date and Time Functions
4. Aggregate Functions
5. Control Flow Functions
6. Encryption and Compression Functions
7. Information Functions
8. JSON Functions (MySQL 5.7.8+)
9. Window Functions (MySQL 8.0+)

## 1. String Functions

### Basic String Functions

| Function | Description |
|----------|-------------|
| CONCAT() | Concatenate strings |
| LENGTH() | Return the length of a string |
| LOWER()  | Convert to lowercase |
| UPPER()  | Convert to uppercase |
| TRIM()   | Remove leading and trailing spaces |
| SUBSTRING() | Extract a substring |

#### Examples

```sql
-- Simple
SELECT CONCAT('Hello', ' ', 'World');  -- 'Hello World'
SELECT LENGTH('OpenAI');  -- 6

-- Intermediate
SELECT 
    product_name,
    LOWER(CONCAT(SUBSTRING(product_name, 1, 3), '_', category)) AS product_code
FROM products;

-- Advanced
SELECT 
    order_id,
    customer_name,
    CASE 
        WHEN LENGTH(shipping_address) > 50 THEN 
            CONCAT(LEFT(shipping_address, 47), '...')
        ELSE shipping_address
    END AS formatted_address
FROM orders;
```

### Advanced String Functions

| Function | Description |
|----------|-------------|
| REGEXP   | Pattern matching using regular expressions |
| REPLACE() | Replace occurrences of a substring |
| SOUNDEX() | Return a soundex string |

#### Examples

```sql
-- Simple
SELECT * FROM customers WHERE last_name REGEXP '^Mc';

-- Intermediate
SELECT 
    product_name,
    REPLACE(description, 'old', 'new') AS updated_description
FROM products
WHERE description LIKE '%old%';

-- Advanced
SELECT 
    c1.customer_id,
    c1.last_name,
    c2.customer_id AS potential_duplicate,
    c2.last_name AS duplicate_last_name
FROM customers c1
JOIN customers c2 ON 
    c1.customer_id < c2.customer_id AND
    SOUNDEX(c1.last_name) = SOUNDEX(c2.last_name);
```

## 2. Numeric Functions

| Function | Description |
|----------|-------------|
| ABS()    | Return the absolute value |
| ROUND()  | Round a number to a specified number of decimal places |
| CEIL()   | Return the smallest integer value not less than the argument |
| FLOOR()  | Return the largest integer value not greater than the argument |
| RAND()   | Return a random floating-point value |

### Examples

```sql
-- Simple
SELECT ABS(-15.7);  -- 15.7
SELECT ROUND(3.14159, 2);  -- 3.14

-- Intermediate
SELECT 
    product_name,
    price,
    ROUND(price * 1.1, 2) AS price_with_tax
FROM products;

-- Advanced
SELECT 
    order_id,
    total_amount,
    CASE 
        WHEN total_amount < 100 THEN CEIL(total_amount)
        WHEN total_amount < 1000 THEN ROUND(total_amount, 1)
        ELSE FLOOR(total_amount)
    END AS rounded_amount
FROM orders;
```

## 3. Date and Time Functions

| Function | Description |
|----------|-------------|
| NOW()    | Return the current date and time |
| CURDATE() | Return the current date |
| YEAR()   | Extract the year from a date |
| MONTH()  | Extract the month from a date |
| DATEDIFF() | Calculate the difference between two dates |
| DATE_FORMAT() | Format a date as specified |

### Examples

```sql
-- Simple
SELECT NOW();  -- Current date and time
SELECT YEAR('2023-05-15');  -- 2023

-- Intermediate
SELECT 
    order_id,
    order_date,
    DATEDIFF(shipped_date, order_date) AS days_to_ship
FROM orders
WHERE YEAR(order_date) = YEAR(CURDATE());

-- Advanced
SELECT 
    YEAR(order_date) AS order_year,
    MONTH(order_date) AS order_month,
    COUNT(*) AS total_orders,
    SUM(total_amount) AS total_sales,
    AVG(DATEDIFF(shipped_date, order_date)) AS avg_ship_time
FROM orders
WHERE order_date >= DATE_SUB(CURDATE(), INTERVAL 2 YEAR)
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY order_year, order_month;
```

## 4. Aggregate Functions

| Function | Description |
|----------|-------------|
| COUNT()  | Count the number of rows |
| SUM()    | Calculate the sum of a set of values |
| AVG()    | Calculate the average of a set of values |
| MAX()    | Return the maximum value |
| MIN()    | Return the minimum value |
| GROUP_CONCAT() | Return a concatenated string |

### Examples

```sql
-- Simple
SELECT COUNT(*) FROM orders;
SELECT AVG(price) FROM products;

-- Intermediate
SELECT 
    category,
    COUNT(*) AS product_count,
    AVG(price) AS avg_price,
    MIN(price) AS min_price,
    MAX(price) AS max_price
FROM products
GROUP BY category;

-- Advanced
SELECT 
    o.customer_id,
    COUNT(DISTINCT o.id) AS total_orders,
    SUM(oi.quantity * oi.unit_price) AS total_spent,
    GROUP_CONCAT(DISTINCT p.category ORDER BY p.category ASC SEPARATOR ', ') AS purchased_categories
FROM orders o
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE o.order_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY o.customer_id
HAVING total_orders > 5
ORDER BY total_spent DESC
LIMIT 10;
```

## 5. Control Flow Functions

| Function | Description |
|----------|-------------|
| IF()     | Return a value based on a condition |
| IFNULL() | Return a specified value if the expression is NULL |
| CASE     | Perform conditional processing |
| COALESCE() | Return the first non-NULL value in a list |

### Examples

```sql
-- Simple
SELECT IF(500 < 1000, 'Yes', 'No');  -- 'Yes'
SELECT IFNULL(NULL, 'Default Value');  -- 'Default Value'

-- Intermediate
SELECT 
    product_name,
    price,
    IF(stock > 0, 'In Stock', 'Out of Stock') AS stock_status
FROM products;

-- Advanced
SELECT 
    order_id,
    total_amount,
    CASE 
        WHEN total_amount < 100 THEN 'Small Order'
        WHEN total_amount BETWEEN 100 AND 1000 THEN 'Medium Order'
        WHEN total_amount > 1000 THEN 'Large Order'
        ELSE 'Unknown'
    END AS order_size,
    COALESCE(shipping_address, billing_address, 'Address Not Provided') AS delivery_address
FROM orders;
```

## 6. Window Functions (MySQL 8.0+)

| Function | Description |
|----------|-------------|
| ROW_NUMBER() | Assign a unique number to each row |
| RANK()   | Assign a rank to each row within its partition |
| DENSE_RANK() | Assign a rank to each row within its partition without gaps |
| LAG()    | Access data from a previous row in the result set |
| LEAD()   | Access data from a subsequent row in the result set |

### Examples

```sql
-- Simple
SELECT 
    product_name,
    category,
    price,
    ROW_NUMBER() OVER (ORDER BY price DESC) AS price_rank
FROM products;

-- Intermediate
SELECT 
    category,
    product_name,
    price,
    RANK() OVER (PARTITION BY category ORDER BY price DESC) AS category_price_rank
FROM products;

-- Advanced
SELECT 
    order_date,
    total_amount,
    LAG(total_amount) OVER (ORDER BY order_date) AS prev_day_amount,
    LEAD(total_amount) OVER (ORDER BY order_date) AS next_day_amount,
    total_amount - LAG(total_amount) OVER (ORDER BY order_date) AS day_over_day_change
FROM (
    SELECT 
        DATE(order_date) AS order_date,
        SUM(total_amount) AS total_amount
    FROM orders
    WHERE order_date >= DATE_SUB(CURDATE(), INTERVAL 30 DAY)
    GROUP BY DATE(order_date)
) daily_totals
ORDER BY order_date;
```

## Best Practices

1. Use appropriate functions to make your queries more efficient and readable.
2. Be aware of how NULL values are handled by different functions.
3. Use EXPLAIN to understand how MySQL is executing queries with complex function usage.
4. Consider indexing function results for frequently used complex calculations.
5. Be cautious with functions in WHERE clauses, as they can prevent the use of indexes.
6. Use window functions for complex analytical queries to improve performance and readability.
7. Always validate the results of your function-based queries, especially when dealing with date, time, or complex string manipulations.
8. Consider using stored functions for complex, frequently used calculations or business logic.

Remember to test your queries thoroughly, especially when using multiple or complex functions, to ensure they produce the expected results and perform well with your dataset.
