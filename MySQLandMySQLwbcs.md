# MySQL and MySQL Workbench Cheat Sheet 📘🛠️

![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)

## Table of Contents
- [MySQL Commands](#mysql-commands)
  - [Database Operations](#database-operations)
  - [Table Operations](#table-operations)
  - [Data Manipulation](#data-manipulation)
  - [Querying Data](#querying-data)
  - [Joins](#joins)
  - [Aggregate Functions](#aggregate-functions)
  - [Indexing](#indexing)
  - [Transactions](#transactions)
  - [Views](#views)
  - [Stored Procedures and Functions](#stored-procedures-and-functions)
  - [Triggers](#triggers)
  - [User Management](#user-management)
  - [Data Types](#data-types)
  - [Constraints](#constraints)
  - [Utility Commands](#utility-commands)
- [MySQL Workbench](#mysql-workbench)
  - [Connection Management](#connection-management)
  - [Query Execution](#query-execution)
  - [Schema Management](#schema-management)
  - [Data Modeling](#data-modeling)
  - [Administration](#administration)
  - [Performance](#performance)

## MySQL Commands

### Database Operations

```sql
-- Create a new database
CREATE DATABASE database_name;

-- Create database with specific character set and collation
CREATE DATABASE database_name
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

-- Switch to a database
USE database_name;

-- List all databases
SHOW DATABASES;

-- Show current database
SELECT DATABASE();

-- Delete a database
DROP DATABASE database_name;

-- Rename a database (MySQL doesn't provide a direct way, you need to create a new one and move data)
-- 1. Create new database
CREATE DATABASE new_database_name;
-- 2. Export data from old database and import to new one
-- 3. Drop old database
```

### Table Operations

```sql
-- Create a new table
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
    ...
);

-- Example: Create users table
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Show all tables in the current database
SHOW TABLES;

-- Describe table structure
DESCRIBE table_name;
-- or
SHOW COLUMNS FROM table_name;

-- Show table creation SQL
SHOW CREATE TABLE table_name;

-- Modify table structure
ALTER TABLE table_name ADD column_name datatype;
ALTER TABLE table_name DROP COLUMN column_name;
ALTER TABLE table_name MODIFY COLUMN column_name datatype;
ALTER TABLE table_name CHANGE old_column_name new_column_name datatype;

-- Rename a table
RENAME TABLE old_table_name TO new_table_name;

-- Copy table structure
CREATE TABLE new_table LIKE existing_table;

-- Copy table structure and data
CREATE TABLE new_table AS SELECT * FROM existing_table;

-- Truncate a table (remove all data)
TRUNCATE TABLE table_name;

-- Delete a table
DROP TABLE table_name;
```

### Data Manipulation

```sql
-- Insert data
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);

-- Insert multiple rows
INSERT INTO table_name (column1, column2, column3, ...)
VALUES 
    (value1, value2, value3, ...),
    (value1, value2, value3, ...),
    (value1, value2, value3, ...);

-- Update data
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;

-- Delete data
DELETE FROM table_name WHERE condition;

-- Replace data (insert if not exists, update if exists)
REPLACE INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);

-- Upsert data (insert if not exists, update if exists)
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...)
ON DUPLICATE KEY UPDATE
    column1 = value1,
    column2 = value2;
```

### Querying Data

```sql
-- Select all columns
SELECT * FROM table_name;

-- Select specific columns
SELECT column1, column2 FROM table_name;

-- Select with condition
SELECT * FROM table_name WHERE condition;

-- Select distinct values
SELECT DISTINCT column_name FROM table_name;

-- Order results
SELECT * FROM table_name ORDER BY column_name [ASC|DESC];

-- Limit results
SELECT * FROM table_name LIMIT number;

-- Offset results
SELECT * FROM table_name LIMIT offset, count;
-- or
SELECT * FROM table_name LIMIT count OFFSET offset;

-- Using LIKE for pattern matching
SELECT * FROM table_name WHERE column_name LIKE 'pattern%';

-- Using IN to specify multiple values
SELECT * FROM table_name WHERE column_name IN (value1, value2, ...);

-- Using BETWEEN for range queries
SELECT * FROM table_name WHERE column_name BETWEEN value1 AND value2;

-- Using IS NULL to check for null values
SELECT * FROM table_name WHERE column_name IS NULL;

-- Combining conditions with AND, OR
SELECT * FROM table_name WHERE condition1 AND condition2 OR condition3;
```

### Joins

```sql
-- Inner Join
SELECT * FROM table1
INNER JOIN table2 ON table1.column_name = table2.column_name;

-- Left Join
SELECT * FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name;

-- Right Join
SELECT * FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;

-- Full Outer Join (MySQL doesn't support FULL OUTER JOIN directly)
SELECT * FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name
UNION
SELECT * FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;

-- Self Join
SELECT a.column_name, b.column_name
FROM table_name a, table_name b
WHERE a.column_name = b.column_name;

-- Cross Join
SELECT * FROM table1
CROSS JOIN table2;
```

### Aggregate Functions

```sql
-- Count rows
SELECT COUNT(*) FROM table_name;

-- Sum of column values
SELECT SUM(column_name) FROM table_name;

-- Average of column values
SELECT AVG(column_name) FROM table_name;

-- Minimum value
SELECT MIN(column_name) FROM table_name;

-- Maximum value
SELECT MAX(column_name) FROM table_name;

-- Group results
SELECT column_name, COUNT(*) FROM table_name
GROUP BY column_name;

-- Having clause (used with GROUP BY)
SELECT column_name, COUNT(*) FROM table_name
GROUP BY column_name
HAVING COUNT(*) > value;

-- Using multiple aggregate functions
SELECT 
    COUNT(*) as total_count,
    SUM(column_name) as total_sum,
    AVG(column_name) as average_value,
    MIN(column_name) as minimum_value,
    MAX(column_name) as maximum_value
FROM table_name;
```

### Indexing

```sql
-- Create index
CREATE INDEX index_name ON table_name (column_name);

-- Create unique index
CREATE UNIQUE INDEX index_name ON table_name (column_name);

-- Create composite index
CREATE INDEX index_name ON table_name (column1, column2);

-- Create fulltext index
CREATE FULLTEXT INDEX index_name ON table_name (column_name);

-- Show indexes
SHOW INDEX FROM table_name;

-- Drop index
DROP INDEX index_name ON table_name;
```

### Transactions

```sql
-- Start a transaction
START TRANSACTION;

-- Commit a transaction
COMMIT;

-- Rollback a transaction
ROLLBACK;

-- Set a savepoint
SAVEPOINT savepoint_name;

-- Rollback to a savepoint
ROLLBACK TO savepoint_name;

-- Example transaction
START TRANSACTION;
INSERT INTO table1 (column1, column2) VALUES (value1, value2);
INSERT INTO table2 (column1, column2) VALUES (value1, value2);
COMMIT;
```

### Views

```sql
-- Create a view
CREATE VIEW view_name AS
SELECT column1, column2
FROM table_name
WHERE condition;

-- Create or replace a view
CREATE OR REPLACE VIEW view_name AS
SELECT column1, column2
FROM table_name
WHERE condition;

-- Drop a view
DROP VIEW view_name;
```

### Stored Procedures and Functions

```sql
-- Create a stored procedure
DELIMITER //
CREATE PROCEDURE procedure_name(IN parameter1 INT, OUT parameter2 INT)
BEGIN
    -- Procedure body
    SET parameter2 = parameter1 * 2;
END //
DELIMITER ;

-- Call a stored procedure
CALL procedure_name(5, @result);
SELECT @result;

-- Create a function
DELIMITER //
CREATE FUNCTION function_name(parameter INT) 
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE result INT;
    -- Function body
    SET result = parameter * 2;
    RETURN result;
END //
DELIMITER ;

-- Use a function
SELECT function_name(5);

-- Drop a procedure
DROP PROCEDURE procedure_name;

-- Drop a function
DROP FUNCTION function_name;
```

### Triggers

```sql
-- Create a trigger
DELIMITER //
CREATE TRIGGER trigger_name
BEFORE INSERT ON table_name
FOR EACH ROW
BEGIN
    -- Trigger body
    SET NEW.column_name = UPPER(NEW.column_name);
END //
DELIMITER ;

-- Drop a trigger
DROP TRIGGER trigger_name;
```

### User Management

```sql
-- Create a new user
CREATE USER 'username'@'host' IDENTIFIED BY 'password';

-- Grant privileges
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'host';
GRANT SELECT, INSERT ON database_name.table_name TO 'username'@'host';

-- Show grants for a user
SHOW GRANTS FOR 'username'@'host';

-- Revoke privileges
REVOKE ALL PRIVILEGES ON database_name.* FROM 'username'@'host';

-- Change a user's password
ALTER USER 'username'@'host' IDENTIFIED BY 'new_password';

-- Delete a user
DROP USER 'username'@'host';
```

### Data Types

Common MySQL data types:

1. Numeric Types:
   - INT
   - TINYINT
   - SMALLINT
   - MEDIUMINT
   - BIGINT
   - FLOAT
   - DOUBLE
   - DECIMAL

2. String Types:
   - CHAR
   - VARCHAR
   - TINYTEXT
   - TEXT
   - MEDIUMTEXT
   - LONGTEXT

3. Date and Time Types:
   - DATE
   - TIME
   - DATETIME
   - TIMESTAMP
   - YEAR

4. Binary Types:
   - BINARY
   - VARBINARY
   - TINYBLOB
   - BLOB
   - MEDIUMBLOB
   - LONGBLOB

5. Other Types:
   - ENUM
   - SET
   - JSON (MySQL 5.7.8 and later)

### Constraints

```sql
-- Primary Key
CREATE TABLE table_name (
    id INT PRIMARY KEY,
    ...
);

-- Auto Increment
CREATE TABLE table_name (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ...
);

-- Foreign Key
CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

-- Unique
CREATE TABLE table_name (
    column_name datatype UNIQUE,
    ...
);

-- Not Null
CREATE TABLE table_name (
    column_name datatype NOT NULL,
    ...
);

-- Check
CREATE TABLE table_name (
    age INT,
    CHECK (age >= 18)
);

-- Default
CREATE TABLE table_name (
    column_name datatype DEFAULT default_value,
    ...
);
```

### Utility Commands

```sql
-- Show MySQL version
SELECT VERSION();

-- Show current date and time
SELECT NOW();

-- Show current user
SELECT USER();

-- Show MySQL status
SHOW STATUS;

-- Show MySQL variables
SHOW VARIABLES;

-- Show processlist
SHOW PROCESSLIST;

-- Kill a MySQL process
KILL process_id;

-- Show table status
SHOW TABLE STATUS;

-- Check and repair table
CHECK TABLE table_name;
REPAIR TABLE table_name;

-- Optimize table
OPTIMIZE TABLE table_name;

-- Flush privileges (reload grant tables)
FLUSH PRIVILEGES;
```

## MySQL Workbench

### Connection Management

- Create a new connection: File > New Connection
- Manage connections: Database > Manage Connections
- Connect to a database: Click on the connection in the home screen
- Edit connection: Right-click on a connection > Edit Connection
- Duplicate connection: Right-click on a connection > Copy Connection
- Export connections: File > Export > Connections
- Import connections: File > Import > Connections from File

Tips:
- Use SSH tunneling for secure remote connections
- Store connection passwords in vault for enhanced security
- Use connection groups to organize multiple connections

### Query Execution

- Open a new query tab: File > New Query Tab or Ctrl+T
- Execute a query: Click the lightning bolt icon or press Ctrl+Shift+Enter
- Execute selected query: Select the query and click the lightning bolt icon or press Ctrl+Enter
- Stop query execution: Click the stop icon or press Ctrl+Break
- Explain query plan: Click the 'Explain' button next to the query execution button
- Format query: Right-click in query editor > Format > Beautify Query
- Comment/Uncomment lines: Ctrl+/ or Ctrl+Shift+/
- Find and replace: Ctrl+F or Edit > Find and Replace

Tips:
- Use Query > Limit Rows to set a default LIMIT for SELECT statements
- Enable auto-completion: Edit > Preferences > SQL Editor > Enable Auto-Complete
- Use snippets for frequently used code: Tools > Snippets

### Schema Management

- Create a new schema: Right-click on the Schemas panel > Create Schema
- Create a new table: Right-click on Tables under a schema > Create Table
- Modify table structure: Double-click on a table > Table Editor
- Generate SQL scripts: Right-click on a schema > Send to SQL Editor > Create Statement
- Reverse engineer database: Database > Reverse Engineer
- Forward engineer database: Database > Forward Engineer

Tips:
- Use the visual Table Editor for easy table structure modifications
- Utilize the Foreign Key wizard for setting up relationships between tables
- Take advantage of the built-in Data Dictionary for documentation

### Data Modeling

- Create a new model: File > New Model
- Add a new diagram: Click on 'Add Diagram' in the model overview
- Add tables to diagram: Drag and drop from the left panel or use the toolbar
- Create relationships: Use the relationship tools in the vertical toolbar
- Validate model: Model > Validate All
- Synchronize model with database: Database > Synchronize Model

Tips:
- Use layers to organize complex diagrams
- Utilize notes and text objects to add documentation to your diagrams
- Export diagrams as images or PDF for documentation purposes

### Data Import/Export

- Import data: Server > Data Import
- Export data: Server > Data Export
- Use Table Data Import Wizard: Right-click on a table > Table Data Import Wizard
- Use Table Data Export Wizard: Right-click on a table > Table Data Export Wizard

Tips:
- Use CSV import/export for easy data transfer between applications
- Leverage the Advanced Options in export to customize the output

### Administration

- Server status: Navigate to Management > Status and System Variables
- User management: Navigate to Management > Users and Privileges
- Server logs: Navigate to Management > Server Logs
- Startup/Shutdown: Navigate to Management > Startup/Shutdown
- Data Export/Import: Navigate to Management > Data Export/Import

Tips:
- Use the Server Status page to monitor current activities and resource usage
- Regularly review Server Logs for potential issues or anomalies
- Set up custom roles for fine-grained access control

### Performance

- View query execution plan: Click on the 'Explain' button next to the query execution button
- Monitor performance: Navigate to Performance > Dashboard
- Analyze slow queries: Navigate to Performance > Performance Reports
- View current user sessions: Navigate to Performance > Client Connections

Tips:
- Use the Performance Dashboard for real-time monitoring of server metrics
- Analyze slow queries regularly to identify performance bottlenecks
- Set up Performance Schema for more detailed performance analysis

### Backup and Restore

- Create backup: Navigate to Management > Data Export
- Restore from backup: Navigate to Management > Data Import
- Schedule backups: Use the MySQL Enterprise Backup feature (requires commercial license)

Tips:
- Regular backups are crucial; set up a backup schedule
- Test your backups by performing regular restore operations
- Consider using binary log backups for point-in-time recovery

### Workbench Customization

- Change color scheme: Edit > Preferences > Appearance
- Customize keyboard shortcuts: Edit > Preferences > Keyboard Shortcuts
- Adjust SQL Editor settings: Edit > Preferences > SQL Editor

Tips:
- Customize the Workbench UI to match your workflow
- Set up custom snippets for frequently used code blocks
- Adjust the SQL Editor's behavior to match your coding style

### Version Control Integration

- Enable version control: Edit > Preferences > MySQL Workbench > Admin > Version Control
- Commit changes: File > Source Control > Commit
- Update from repository: File > Source Control > Update

Tips:
- Use version control for tracking changes to your database schemas
- Collaborate with team members using shared repositories
- Keep your local workspace in sync with the central repository

### Scripting and Automation

- Execute Python scripts: Scripting > Execute Script File
- Create custom plugins: Plugins > Install Plugin/Update
- Use the Workbench command-line interface: mysqlworkbench --script=<script_file>

Tips:
- Leverage Python scripting for automating repetitive tasks
- Create custom plugins to extend Workbench functionality
- Use the command-line interface for integrating Workbench into your workflows

### Troubleshooting

- Check error logs: Help > Locate Log Files
- Reset Workbench configuration: Delete the Workbench configuration folder
- Update Workbench: Help > Check for Updates

Tips:
- Keep Workbench updated to the latest version for bug fixes and new features
- Use the bug reporter (Help > Report a Bug) for issues you can't resolve
- Check the official MySQL Workbench documentation for detailed information

Remember, MySQL Workbench is a powerful tool with many features. Experiment with different functionalities to find the workflows that best suit your needs. Regular practice will help you become more proficient in using this versatile database management tool. Happy database management! 🚀🐬
