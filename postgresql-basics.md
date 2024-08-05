# PostgreSQL Basics 🐘

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

Welcome to PostgreSQL, a powerful, open-source object-relational database system! This guide will introduce you to the basics of PostgreSQL, building upon your foundational SQL knowledge.

## Table of Contents
- [What is PostgreSQL?](#what-is-postgresql)
- [PostgreSQL vs. Other Databases](#postgresql-vs-other-databases)
- [Installing PostgreSQL](#installing-postgresql)
- [Connecting to PostgreSQL](#connecting-to-postgresql)
- [PostgreSQL Data Types](#postgresql-data-types)
- [Creating and Managing Databases](#creating-and-managing-databases)
- [Table Operations](#table-operations)
- [Basic PostgreSQL Queries](#basic-postgresql-queries)
- [PostgreSQL Specific Features](#postgresql-specific-features)
- [Indexing in PostgreSQL](#indexing-in-postgresql)
- [PostgreSQL Configuration](#postgresql-configuration)
- [Backup and Restore](#backup-and-restore)
- [Best Practices](#best-practices)
- [Exercises](#exercises)

## What is PostgreSQL? 🤔

PostgreSQL, often simply "Postgres," is an advanced, open-source object-relational database management system (ORDBMS). It's known for its:

- Reliability
- Robustness
- Performance
- Extensibility
- SQL compliance
- Active community support

PostgreSQL supports both SQL (relational) and JSON (non-relational) querying, making it a versatile choice for various applications.

## PostgreSQL vs. Other Databases 📊

Here's a quick comparison of PostgreSQL with other popular databases:

| Feature | PostgreSQL | MySQL | Oracle | SQL Server |
|---------|------------|-------|--------|------------|
| Open Source | ✅ | ✅ | ❌ | ❌ |
| ACID Compliance | ✅ | ✅ | ✅ | ✅ |
| JSON Support | ✅ | ✅ | ✅ | ✅ |
| Full-Text Search | ✅ | ✅ | ✅ | ✅ |
| Materialized Views | ✅ | ❌ | ✅ | ✅ |
| Table Inheritance | ✅ | ❌ | ❌ | ❌ |
| Multi-Version Concurrency Control (MVCC) | ✅ | ✅ | ✅ | ✅ |
| Native Replication | ✅ | ✅ | ✅ | ✅ |

## Installing PostgreSQL 💻

To install PostgreSQL:

1. Visit the [official PostgreSQL download page](https://www.postgresql.org/download/)
2. Choose your operating system
3. Follow the installation instructions

For Ubuntu:
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

For macOS using Homebrew:
```bash
brew install postgresql
```

## Connecting to PostgreSQL 🔌

After installation, you can connect to PostgreSQL using the `psql` command-line tool:

```bash
psql -U postgres
```

You'll be prompted for the password you set during installation.

## PostgreSQL Data Types 📊

PostgreSQL supports all standard SQL data types and adds some of its own:

| Data Type | Description | Example |
|-----------|-------------|---------|
| INTEGER   | Whole number | 42, -17, 0 |
| NUMERIC   | Exact numeric with specified precision | 3.14159265359 |
| VARCHAR(n) | Variable-length character string | 'Hello', 'PostgreSQL' |
| TEXT      | Variable unlimited length | Large text content |
| DATE      | Calendar date (year, month, day) | '2023-05-15' |
| TIMESTAMP | Date and time | '2023-05-15 14:30:00' |
| BOOLEAN   | True or False values | TRUE, FALSE |
| UUID      | Universally Unique Identifier | 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11' |
| JSON      | JavaScript Object Notation data | '{"name": "John", "age": 30}' |
| ARRAY     | Array of other data types | '{1,2,3}', '{"a","b","c"}' |

## Creating and Managing Databases 🗃️

To create a new database:

```sql
CREATE DATABASE mydatabase;
```

To connect to a database:

```bash
\c mydatabase
```

To list all databases:

```bash
\l
```

To drop a database:

```sql
DROP DATABASE mydatabase;
```

## Table Operations 📋

Creating a table:

```sql
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Altering a table:

```sql
ALTER TABLE users ADD COLUMN last_login TIMESTAMP;
```

Dropping a table:

```sql
DROP TABLE users;
```

## Basic PostgreSQL Queries 🔍

Select all records:

```sql
SELECT * FROM users;
```

Insert a record:

```sql
INSERT INTO users (username, email) VALUES ('johndoe', 'john@example.com');
```

Update a record:

```sql
UPDATE users SET email = 'newemail@example.com' WHERE username = 'johndoe';
```

Delete a record:

```sql
DELETE FROM users WHERE username = 'johndoe';
```

## PostgreSQL Specific Features 🌟

### 1. Arrays

PostgreSQL supports array columns:

```sql
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    tags TEXT[]
);

INSERT INTO products (name, tags) VALUES ('Laptop', ARRAY['electronics', 'computer']);
```

Querying arrays:

```sql
SELECT * FROM products WHERE 'electronics' = ANY(tags);
```

### 2. JSON and JSONB

PostgreSQL offers robust support for JSON data:

```sql
CREATE TABLE events (
    id SERIAL PRIMARY KEY,
    data JSONB
);

INSERT INTO events (data) VALUES ('{"user": "johndoe", "action": "login"}');
```

Querying JSON:

```sql
SELECT * FROM events WHERE data->>'action' = 'login';
```

### 3. Full-Text Search

PostgreSQL provides powerful full-text search capabilities:

```sql
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    title TEXT,
    body TEXT
);

CREATE INDEX articles_idx ON articles USING GIN (to_tsvector('english', title || ' ' || body));
```

Performing a full-text search:

```sql
SELECT * FROM articles 
WHERE to_tsvector('english', title || ' ' || body) @@ to_tsquery('english', 'postgresql & database');
```

## Indexing in PostgreSQL 📇

Indexes improve query performance. PostgreSQL supports several types of indexes:

1. B-tree (default)
2. Hash
3. GiST
4. SP-GiST
5. GIN
6. BRIN

Creating an index:

```sql
CREATE INDEX idx_username ON users(username);
```

## PostgreSQL Configuration ⚙️

The main configuration file is `postgresql.conf`. Some important parameters:

- `max_connections`: Maximum number of concurrent connections
- `shared_buffers`: Amount of memory for shared memory buffers
- `effective_cache_size`: Planner's assumption about the total size of the data caches

You can view current settings with:

```sql
SHOW ALL;
```

## Backup and Restore 💾

To backup a database:

```bash
pg_dump dbname > dbname.sql
```

To restore a database:

```bash
psql dbname < dbname.sql
```

## Best Practices 🏆

1. Use appropriate data types
2. Create indexes for frequently queried columns
3. Regularly VACUUM and ANALYZE your database
4. Use connection pooling for better performance
5. Keep your PostgreSQL version up to date
6. Implement proper backup strategies
7. Use prepared statements to prevent SQL injection
8. Optimize your queries using EXPLAIN ANALYZE

## Exercises 🏋️‍♀️

1. Create a new database named `library`.
2. In the `library` database, create a `books` table with columns: `book_id`, `title`, `author`, `genres` (as an array), `publication_date`, and `price`.
3. Insert at least 5 books into the `books` table, ensuring that at least one book has multiple genres.
4. Write a query to select all books published after 2000 with 'Fiction' in their genres.
5. Create an index on the `title` column of the `books` table.
6. Create a new table `reviews` with columns: `review_id`, `book_id` (foreign key referencing `books`), `reviewer_name`, `rating`, and `review_text` (as JSON).
7. Insert some reviews and write a query to find the average rating for each book.
8. Implement a full-text search on the `title` and `author` columns of the `books` table.

Remember, hands-on practice is crucial for mastering PostgreSQL. Happy coding! 🚀🐘
