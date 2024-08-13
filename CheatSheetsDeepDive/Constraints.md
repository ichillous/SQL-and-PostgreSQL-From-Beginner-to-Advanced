# MySQL Constraints Cheatsheet

## Overview

Constraints in MySQL are used to specify rules for the data in a table. They are used to limit the type of data that can go into a table, ensuring the accuracy and reliability of the data. MySQL supports the following constraints:

1. PRIMARY KEY
2. FOREIGN KEY
3. UNIQUE
4. NOT NULL
5. CHECK (fully supported in MySQL 8.0.16 and later)
6. DEFAULT

## 1. PRIMARY KEY Constraint

A PRIMARY KEY constraint uniquely identifies each record in a table. It must contain UNIQUE values and cannot contain NULL values.

### Syntax

```sql
-- During table creation
CREATE TABLE table_name (
    column1 datatype PRIMARY KEY,
    column2 datatype,
    ...
);

-- Or
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...,
    PRIMARY KEY (column1, column2)
);

-- After table creation
ALTER TABLE table_name
ADD PRIMARY KEY (column1);
```

### Examples

```sql
-- Simple PRIMARY KEY
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

-- Composite PRIMARY KEY
CREATE TABLE course_enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)
);

-- Adding PRIMARY KEY after table creation
CREATE TABLE products (
    product_id INT,
    name VARCHAR(100),
    price DECIMAL(10, 2)
);

ALTER TABLE products
ADD PRIMARY KEY (product_id);
```

## 2. FOREIGN KEY Constraint

A FOREIGN KEY constraint is used to prevent actions that would destroy links between tables. It references the PRIMARY KEY of another table.

### Syntax

```sql
-- During table creation
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...,
    FOREIGN KEY (column1) REFERENCES another_table(column_name)
);

-- After table creation
ALTER TABLE table_name
ADD FOREIGN KEY (column1) REFERENCES another_table(column_name);
```

### Examples

```sql
-- Creating tables with FOREIGN KEY
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(100)
);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(100),
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

-- Adding FOREIGN KEY after table creation
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE
);

ALTER TABLE orders
ADD FOREIGN KEY (customer_id) REFERENCES customers(customer_id);

-- FOREIGN KEY with ON DELETE and ON UPDATE actions
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id) 
        REFERENCES orders(order_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (product_id) 
        REFERENCES products(product_id)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
);
```

## 3. UNIQUE Constraint

A UNIQUE constraint ensures that all values in a column are different.

### Syntax

```sql
-- During table creation
CREATE TABLE table_name (
    column1 datatype UNIQUE,
    column2 datatype,
    ...
);

-- After table creation
ALTER TABLE table_name
ADD UNIQUE (column1);
```

### Examples

```sql
-- Creating a table with a UNIQUE constraint
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE
);

-- Adding a UNIQUE constraint after table creation
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_code VARCHAR(20),
    name VARCHAR(100)
);

ALTER TABLE products
ADD UNIQUE (product_code);

-- Composite UNIQUE constraint
CREATE TABLE employee_projects (
    employee_id INT,
    project_id INT,
    role VARCHAR(50),
    UNIQUE (employee_id, project_id)
);
```

## 4. NOT NULL Constraint

A NOT NULL constraint ensures that a column cannot have a NULL value.

### Syntax

```sql
-- During table creation
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    column2 datatype,
    ...
);

-- After table creation
ALTER TABLE table_name
MODIFY column1 datatype NOT NULL;
```

### Examples

```sql
-- Creating a table with NOT NULL constraints
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100)
);

-- Adding a NOT NULL constraint after table creation
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE
);

ALTER TABLE orders
MODIFY customer_id INT NOT NULL;
```

## 5. CHECK Constraint

A CHECK constraint specifies a condition that must be satisfied for each row in the table.

### Syntax

```sql
-- During table creation
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...,
    CHECK (condition)
);

-- After table creation
ALTER TABLE table_name
ADD CHECK (condition);
```

### Examples

```sql
-- Creating a table with a CHECK constraint
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10, 2) CHECK (price > 0)
);

-- Adding a CHECK constraint after table creation
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);

ALTER TABLE employees
ADD CHECK (age >= 18);

-- Complex CHECK constraint
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    ship_date DATE,
    CHECK (ship_date IS NULL OR ship_date >= order_date)
);
```

## 6. DEFAULT Constraint

A DEFAULT constraint provides a default value for a column when no value is specified.

### Syntax

```sql
-- During table creation
CREATE TABLE table_name (
    column1 datatype DEFAULT default_value,
    column2 datatype,
    ...
);

-- After table creation
ALTER TABLE table_name
ALTER column1 SET DEFAULT default_value;
```

### Examples

```sql
-- Creating a table with DEFAULT constraints
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10, 2),
    in_stock BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Adding a DEFAULT constraint after table creation
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    status VARCHAR(20)
);

ALTER TABLE orders
ALTER status SET DEFAULT 'Pending';
```

## Best Practices

1. Use PRIMARY KEY constraints to ensure each record can be uniquely identified.
2. Implement FOREIGN KEY constraints to maintain referential integrity between related tables.
3. Apply UNIQUE constraints on columns or combinations of columns that should contain only unique values.
4. Use NOT NULL constraints for columns that should always have a value.
5. Leverage CHECK constraints to enforce domain integrity and business rules.
6. Provide DEFAULT values for columns that often have a common value to simplify data entry.
7. Consider the impact on performance when adding constraints, especially on large tables.
8. Use naming conventions for constraints to make them easier to manage.
9. Be cautious with CASCADE actions on FOREIGN KEYs, as they can lead to unintended data loss.
10. Regularly review and update constraints as business rules and data requirements evolve.

Remember that while constraints are crucial for data integrity, they also have implications for performance and data operations. Always test thoroughly in a non-production environment before implementing constraints on large or critical tables.
