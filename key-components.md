# MySQL Key Components 📘

![MySQL Logo](https://img.shields.io/badge/mysql-%2300f.svg?style=for-the-badge&logo=mysql&logoColor=white)

A comprehensive guide to MySQL's key components, including statements, clauses, operators, functions, and data types. This cheatsheet serves as a quick reference for MySQL developers, from beginners to advanced users.

## 📋 Table of Contents

- [Data Manipulation Language (DML)](#data-manipulation-language-dml)
- [Data Definition Language (DDL)](#data-definition-language-ddl)
- [Data Control Language (DCL)](#data-control-language-dcl)
- [Transaction Control](#transaction-control)
- [Operators](#operators)
- [Functions](#functions)
- [Other Keywords](#other-keywords)
- [Data Types](#data-types)
- [Constraints](#constraints)
- [Index Types](#index-types)

## 🔄 Data Manipulation Language (DML)

### Statements

- `SELECT`
- `INSERT`
- `UPDATE`
- `DELETE`
- `MERGE` (MySQL 8.0.31+)

### Clauses

- `FROM`
- `WHERE`
- `GROUP BY`
- `HAVING`
- `ORDER BY`
- `LIMIT`
- `OFFSET`
- `JOIN` (INNER, LEFT, RIGHT, FULL)
- `UNION`
- `INTERSECT` (MySQL 8.0.31+)
- `EXCEPT` (MySQL 8.0.31+)

## 🏗 Data Definition Language (DDL)

### Statements

- `CREATE`
- `ALTER`
- `DROP`
- `TRUNCATE`
- `RENAME`

### Database Objects

- `TABLE`
- `VIEW`
- `INDEX`
- `DATABASE`
- `PROCEDURE`
- `FUNCTION`
- `TRIGGER`
- `EVENT`

## 🔐 Data Control Language (DCL)

- `GRANT`
- `REVOKE`

## 💼 Transaction Control

- `START TRANSACTION`
- `COMMIT`
- `ROLLBACK`
- `SAVEPOINT`

## 🧮 Operators

### Comparison Operators

- `=`, `<>`, `!=`, `<`, `<=`, `>`, `>=`
- `IS NULL`, `IS NOT NULL`
- `BETWEEN`
- `IN`
- `LIKE`
- `REGEXP`

### Logical Operators

- `AND`
- `OR`
- `NOT`
- `XOR`

### Arithmetic Operators

- `+`, `-`, `*`, `/`, `%`
- `DIV` (integer division)

## 🛠 Functions

### String Functions

- `CONCAT`, `SUBSTRING`, `TRIM`, `UPPER`, `LOWER`

### Numeric Functions

- `ABS`, `ROUND`, `CEIL`, `FLOOR`

### Date and Time Functions

- `NOW()`, `CURDATE()`, `YEAR()`, `MONTH()`, `DAY()`

### Aggregate Functions

- `COUNT`, `SUM`, `AVG`, `MAX`, `MIN`

### Window Functions

- `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`

## 🔑 Other Keywords

- `DISTINCT`
- `AS` (aliasing)
- `WITH` (Common Table Expressions)
- `CASE`, `WHEN`, `THEN`, `ELSE`, `END`
- `IF`, `IFNULL`, `COALESCE`
- `EXISTS`
- `ALL`, `ANY`, `SOME`

## 📊 Data Types

### Numeric Types

- `INT`, `TINYINT`, `SMALLINT`, `MEDIUMINT`, `BIGINT`
- `DECIMAL`, `FLOAT`, `DOUBLE`

### String Types

- `CHAR`, `VARCHAR`
- `TEXT`, `TINYTEXT`, `MEDIUMTEXT`, `LONGTEXT`

### Date and Time Types

- `DATE`, `TIME`, `DATETIME`, `TIMESTAMP`
- `YEAR`

### Binary Types

- `BINARY`, `VARBINARY`
- `BLOB`, `TINYBLOB`, `MEDIUMBLOB`, `LONGBLOB`

### Other Types

- `ENUM`
- `SET`
- `JSON` (MySQL 5.7.8+)

## 🔒 Constraints

- `PRIMARY KEY`
- `FOREIGN KEY`
- `UNIQUE`
- `NOT NULL`
- `CHECK` (fully supported in MySQL 8.0.16+)
- `DEFAULT`

## 📇 Index Types

- `BTREE`
- `HASH`
- `FULLTEXT`
- `SPATIAL`

## 📝 Note

This cheatsheet covers most common MySQL elements but may not be exhaustive. MySQL is regularly updated, and new features may be added in future versions. Always refer to the [official MySQL documentation](https://dev.mysql.com/doc/) for the most up-to-date and comprehensive information.

## 📚 Additional Resources

- [MySQL Official Website](https://www.mysql.com/)
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [MySQL Community Forums](https://forums.mysql.com/)

## 🤝 Contributing

Feel free to contribute to this cheatsheet by submitting pull requests or opening issues for any corrections or additions.

## 📄 License

This MySQL Cheatsheet is released under the [MIT License](https://opensource.org/licenses/MIT).

---

Happy querying! 🚀
