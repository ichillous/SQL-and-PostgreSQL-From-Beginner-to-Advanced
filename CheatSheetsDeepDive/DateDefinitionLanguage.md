# MySQL Data Definition Language (DDL) Cheatsheet

## Overview

Data Definition Language (DDL) is used to define and manage the structure of database objects. In MySQL, DDL statements are used to create, modify, and delete database objects such as tables, indexes, and constraints.

Key DDL statements in MySQL include:

- CREATE
- ALTER
- DROP
- TRUNCATE
- RENAME

## CREATE Statement

The CREATE statement is used to create new database objects.

### Create Database

```sql
-- Simple database creation
CREATE DATABASE my_app;

-- Advanced: Create database with specific character set and collation
CREATE DATABASE international_store
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

### Create Table

```sql
-- Simple table creation
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE
);

-- Intermediate: Table with foreign key
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Advanced: Table with check constraint, index, and table options
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    stock INT NOT NULL,
    category ENUM('Electronics', 'Clothing', 'Books', 'Other') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    CONSTRAINT chk_price CHECK (price > 0),
    CONSTRAINT chk_stock CHECK (stock >= 0),
    INDEX idx_category (category)
) ENGINE=InnoDB AUTO_INCREMENT=1000 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### Create Index

```sql
-- Simple index
CREATE INDEX idx_username ON users(username);

-- Composite index
CREATE INDEX idx_name_category ON products(name, category);

-- Advanced: Functional index (MySQL 8.0+)
CREATE INDEX idx_lower_email ON users((LOWER(email)));
```

### Create View

```sql
-- Simple view
CREATE VIEW active_users AS
SELECT id, username, email FROM users WHERE last_login > DATE_SUB(NOW(), INTERVAL 30 DAY);

-- Intermediate view with joins
CREATE VIEW order_details AS
SELECT o.id AS order_id, u.username, o.total_amount, o.order_date
FROM orders o
JOIN users u ON o.user_id = u.id;

-- Advanced: Updatable view with check option
CREATE VIEW high_value_products AS
SELECT id, name, price, stock
FROM products
WHERE price > 1000 WITH CHECK OPTION;
```

### Create Stored Procedure

```sql
-- Simple stored procedure
DELIMITER //
CREATE PROCEDURE get_user_orders(IN user_id INT)
BEGIN
    SELECT * FROM orders WHERE user_id = user_id;
END //
DELIMITER ;

-- Intermediate: Procedure with output parameter
DELIMITER //
CREATE PROCEDURE calculate_order_total(
    IN order_id INT,
    OUT total DECIMAL(10, 2)
)
BEGIN
    SELECT SUM(price * quantity) INTO total
    FROM order_items
    WHERE order_id = order_id;
END //
DELIMITER ;

-- Advanced: Procedure with error handling and dynamic SQL
DELIMITER //
CREATE PROCEDURE update_product_price(
    IN product_id INT,
    IN new_price DECIMAL(10, 2)
)
BEGIN
    DECLARE exit handler for sqlexception
    BEGIN
        ROLLBACK;
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'An error occurred while updating the product price.';
    END;

    START TRANSACTION;
    
    UPDATE products SET price = new_price WHERE id = product_id;
    
    IF ROW_COUNT() = 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Product not found.';
    END IF;

    COMMIT;
END //
DELIMITER ;
```

## ALTER Statement

The ALTER statement is used to modify existing database objects.

### Alter Table

```sql
-- Simple: Add column
ALTER TABLE users
ADD COLUMN phone_number VARCHAR(20);

-- Intermediate: Modify column and add constraint
ALTER TABLE products
MODIFY COLUMN price DECIMAL(12, 2) NOT NULL,
ADD CONSTRAINT chk_price CHECK (price > 0);

-- Advanced: Multiple operations including foreign key and index
ALTER TABLE orders
ADD COLUMN status ENUM('Pending', 'Shipped', 'Delivered') NOT NULL DEFAULT 'Pending',
ADD COLUMN shipping_address_id INT,
ADD FOREIGN KEY (shipping_address_id) REFERENCES addresses(id),
ADD INDEX idx_status_date (status, order_date);
```

### Alter Database

```sql
-- Change character set and collation
ALTER DATABASE international_store
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_520_ci;
```

## DROP Statement

The DROP statement is used to delete database objects.

```sql
-- Simple: Drop table
DROP TABLE IF EXISTS temporary_logs;

-- Intermediate: Drop multiple objects
DROP TABLE IF EXISTS order_items, order_history;
DROP VIEW IF EXISTS order_summary;

-- Advanced: Drop with cascading effect (be cautious!)
DROP DATABASE IF EXISTS test_database CASCADE;
```

## TRUNCATE Statement

The TRUNCATE statement is used to delete all data from a table quickly.

```sql
-- Simple truncate
TRUNCATE TABLE logs;

-- Advanced: Truncate with foreign key checks disabled
SET FOREIGN_KEY_CHECKS = 0;
TRUNCATE TABLE orders;
TRUNCATE TABLE order_items;
SET FOREIGN_KEY_CHECKS = 1;
```

## RENAME Statement

The RENAME statement is used to rename database objects.

```sql
-- Simple: Rename single table
RENAME TABLE users TO registered_users;

-- Advanced: Rename multiple tables in one statement
RENAME TABLE 
    old_products TO archived_products,
    current_products TO products,
    new_products TO upcoming_products;
```

## Best Practices

1. Always backup your database before performing DDL operations, especially in production environments.
2. Use appropriate naming conventions for database objects (e.g., lowercase with underscores for table names).
3. Plan your database schema carefully to minimize the need for frequent alterations.
4. Be cautious when using DROP or TRUNCATE as these operations can't be easily undone.
5. Use stored procedures and views to encapsulate complex logic and improve security.
6. Consider the impact on existing data and application code when modifying table structures.
7. Use appropriate character sets and collations for international data.
8. Implement proper constraints (PRIMARY KEY, FOREIGN KEY, UNIQUE, etc.) to maintain data integrity.
9. Create indexes judiciously to balance query performance and write performance.
10. Document your database schema and any significant changes made over time.

Remember to always test your DDL statements in a development or staging environment before applying them to production databases.
