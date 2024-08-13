# MySQL Index Types Cheatsheet

## Overview

Indexes in MySQL are used to speed up data retrieval operations on database tables. MySQL supports several types of indexes, each with its own use cases and benefits. The main index types in MySQL are:

1. B-Tree Indexes
2. Hash Indexes
3. Full-Text Indexes
4. Spatial Indexes
5. Multi-Valued Indexes (MySQL 8.0.17+)

## 1. B-Tree Indexes

B-Tree indexes are the default and most common type of index in MySQL. They are used for column comparisons in expressions that use the =, >, >=, <, <=, or BETWEEN operators.

### Syntax

```sql
CREATE INDEX index_name ON table_name (column1, column2, ...);

-- Or

ALTER TABLE table_name ADD INDEX index_name (column1, column2, ...);
```

### Examples

```sql
-- Simple B-Tree index
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    INDEX idx_username (username)
);

-- Composite B-Tree index
CREATE INDEX idx_name_email ON users (username, email);

-- Adding an index to an existing table
ALTER TABLE products ADD INDEX idx_price (price);
```

### Use Cases
- Equality comparisons
- Range queries
- Prefix matching (for strings)
- Sorting and grouping

## 2. Hash Indexes

Hash indexes use a hash table and are only available for MEMORY tables. They are useful only for exact lookups that use the entire key.

### Syntax

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    INDEX USING HASH (column1)
) ENGINE=MEMORY;
```

### Example

```sql
CREATE TABLE cache (
    key_id INT,
    value VARCHAR(100),
    INDEX USING HASH (key_id)
) ENGINE=MEMORY;
```

### Use Cases
- Exact lookups
- In-memory tables requiring fast access

## 3. Full-Text Indexes

Full-text indexes are used for full-text searches on text fields.

### Syntax

```sql
CREATE FULLTEXT INDEX index_name ON table_name (column1, column2, ...);
```

### Examples

```sql
-- Creating a table with a FULLTEXT index
CREATE TABLE articles (
    id INT PRIMARY KEY,
    title VARCHAR(200),
    content TEXT,
    FULLTEXT INDEX idx_content (content)
);

-- Adding a FULLTEXT index to an existing table
ALTER TABLE products 
ADD FULLTEXT INDEX idx_description (description);

-- Using a FULLTEXT index in a query
SELECT * FROM articles 
WHERE MATCH (content) AGAINST ('MySQL indexing' IN NATURAL LANGUAGE MODE);
```

### Use Cases
- Text search functionality
- Finding relevant documents based on word matches

## 4. Spatial Indexes

Spatial indexes are used for spatial data types like POINT, LINESTRING, POLYGON, etc.

### Syntax

```sql
CREATE SPATIAL INDEX index_name ON table_name (column_name);
```

### Example

```sql
CREATE TABLE locations (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    coordinates POINT,
    SPATIAL INDEX idx_coordinates (coordinates)
);

-- Inserting data
INSERT INTO locations (name, coordinates) 
VALUES ('Central Park', ST_GeomFromText('POINT(40.785091 -73.968285)'));

-- Using a spatial index in a query
SELECT name, ST_Distance(coordinates, ST_GeomFromText('POINT(40.7128 -74.0060)')) AS distance
FROM locations
WHERE ST_Distance(coordinates, ST_GeomFromText('POINT(40.7128 -74.0060)')) < 5000;
```

### Use Cases
- Geospatial queries
- Finding nearby locations
- Spatial analysis

## 5. Multi-Valued Indexes (MySQL 8.0.17+)

Multi-valued indexes are used for indexing JSON arrays.

### Syntax

```sql
CREATE INDEX index_name ON table_name ((CAST(column_name->'$.path' AS DATATYPE ARRAY)));
```

### Example

```sql
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    tags JSON
);

-- Creating a multi-valued index
CREATE INDEX idx_tags ON products ((CAST(tags->'$' AS CHAR(20) ARRAY)));

-- Inserting data
INSERT INTO products (name, tags) 
VALUES ('Laptop', '["electronics", "computer", "portable"]');

-- Using a multi-valued index in a query
SELECT * FROM products
WHERE JSON_OVERLAPS(tags, '["electronics", "smartphone"]');
```

### Use Cases
- Indexing JSON arrays
- Queries involving array containment or overlap

## Best Practices

1. Choose the appropriate index type based on your data and query patterns.
2. Use B-Tree indexes for most general-purpose indexing needs.
3. Consider HASH indexes for MEMORY tables with equality comparisons.
4. Utilize FULLTEXT indexes for text search functionality on large text fields.
5. Implement Spatial indexes for geographic data and location-based queries.
6. Use Multi-Valued indexes when working with JSON arrays that need to be searched efficiently.
7. Be mindful of the trade-off between query performance and write performance when adding indexes.
8. Regularly analyze and optimize your indexes using tools like EXPLAIN and ANALYZE.
9. Consider covering indexes (including all columns referenced in a query) for optimal performance.
10. Periodically review and remove unused indexes to maintain optimal write performance.

Remember that while indexes can significantly improve read performance, they come with a cost in terms of write performance and storage space. Always test the impact of indexes in a non-production environment before implementing them on large or critical tables.
