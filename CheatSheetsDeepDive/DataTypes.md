# MySQL Data Types Cheatsheet

## Overview

MySQL supports various data types for storing different kinds of information. This cheatsheet covers the main categories of MySQL data types:

1. Numeric Types
2. Date and Time Types
3. String Types
4. Spatial Data Types
5. JSON Data Type

## 1. Numeric Types

### Integer Types

| Data Type | Storage (Bytes) | Minimum Value (Signed) | Maximum Value (Signed) |
|-----------|-----------------|------------------------|------------------------|
| TINYINT   | 1               | -128                   | 127                    |
| SMALLINT  | 2               | -32,768                | 32,767                 |
| MEDIUMINT | 3               | -8,388,608             | 8,388,607              |
| INT       | 4               | -2,147,483,648         | 2,147,483,647          |
| BIGINT    | 8               | -2^63                  | 2^63-1                 |

Note: Unsigned versions are available for all integer types, doubling the maximum positive value.

### Fixed-Point Types

| Data Type    | Storage (Bytes)    |
|--------------|---------------------|
| DECIMAL(M,D) | Varies              |

M is the maximum number of digits (precision), and D is the number of digits after the decimal point (scale).

### Floating-Point Types

| Data Type | Storage (Bytes) | Precision        |
|-----------|-----------------|-------------------|
| FLOAT     | 4               | ~7 decimal digits |
| DOUBLE    | 8               | ~15 decimal digits|

### Examples

```sql
CREATE TABLE numeric_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    tiny_val TINYINT,
    small_val SMALLINT UNSIGNED,
    normal_val INT,
    big_val BIGINT,
    decimal_val DECIMAL(10,2),
    float_val FLOAT,
    double_val DOUBLE
);

INSERT INTO numeric_examples 
(tiny_val, small_val, normal_val, big_val, decimal_val, float_val, double_val)
VALUES 
(127, 65535, 1000000, 9223372036854775807, 1234.56, 3.14159, 3.141592653589793);
```

## 2. Date and Time Types

| Data Type  | Storage (Bytes) | Format                | Range                                    |
|------------|-----------------|----------------------|--------------------------------------------|
| DATE       | 3               | YYYY-MM-DD           | '1000-01-01' to '9999-12-31'               |
| TIME       | 3               | HH:MM:SS             | '-838:59:59' to '838:59:59'                |
| DATETIME   | 8               | YYYY-MM-DD HH:MM:SS  | '1000-01-01 00:00:00' to '9999-12-31 23:59:59' |
| TIMESTAMP  | 4               | YYYY-MM-DD HH:MM:SS  | '1970-01-01 00:00:01' to '2038-01-19 03:14:07' |
| YEAR       | 1               | YYYY                 | 1901 to 2155                               |

### Examples

```sql
CREATE TABLE datetime_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    date_val DATE,
    time_val TIME,
    datetime_val DATETIME,
    timestamp_val TIMESTAMP,
    year_val YEAR
);

INSERT INTO datetime_examples 
(date_val, time_val, datetime_val, timestamp_val, year_val)
VALUES 
('2023-05-15', '14:30:00', '2023-05-15 14:30:00', '2023-05-15 14:30:00', 2023);
```

## 3. String Types

### Character String Types

| Data Type     | Maximum Length          | Storage                          |
|---------------|-------------------------|----------------------------------|
| CHAR(M)       | 255 characters          | Fixed-length, M bytes            |
| VARCHAR(M)    | 65,535 characters       | Variable-length, actual length + 1 or 2 bytes |

### Text Types

| Data Type | Maximum Length | Storage |
|-----------|----------------|---------|
| TINYTEXT  | 255 bytes      | L + 1 bytes, where L < 2^8 |
| TEXT      | 65,535 bytes   | L + 2 bytes, where L < 2^16 |
| MEDIUMTEXT| 16,777,215 bytes | L + 3 bytes, where L < 2^24 |
| LONGTEXT  | 4,294,967,295 bytes | L + 4 bytes, where L < 2^32 |

### Binary String Types

| Data Type     | Maximum Length          | Storage                          |
|---------------|-------------------------|----------------------------------|
| BINARY(M)     | 255 bytes               | Fixed-length, M bytes            |
| VARBINARY(M)  | 65,535 bytes            | Variable-length, actual length + 1 or 2 bytes |

### BLOB Types

| Data Type | Maximum Length | Storage |
|-----------|----------------|---------|
| TINYBLOB  | 255 bytes      | L + 1 bytes, where L < 2^8 |
| BLOB      | 65,535 bytes   | L + 2 bytes, where L < 2^16 |
| MEDIUMBLOB| 16,777,215 bytes | L + 3 bytes, where L < 2^24 |
| LONGBLOB  | 4,294,967,295 bytes | L + 4 bytes, where L < 2^32 |

### Examples

```sql
CREATE TABLE string_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    char_val CHAR(10),
    varchar_val VARCHAR(255),
    text_val TEXT,
    binary_val BINARY(10),
    varbinary_val VARBINARY(255),
    blob_val BLOB
);

INSERT INTO string_examples 
(char_val, varchar_val, text_val, binary_val, varbinary_val, blob_val)
VALUES 
('Fixed     ', 'Variable length', 'Long text content...', 
 0x42696E617279, 0x566172696162696E617279, 0x424C4F42);
```

## 4. Spatial Data Types

| Data Type    | Description                                      |
|--------------|--------------------------------------------------|
| GEOMETRY     | Can store any type of spatial data               |
| POINT        | A single point in space                          |
| LINESTRING   | A curve with linear interpolation between points |
| POLYGON      | A polygon                                        |
| MULTIPOINT   | A collection of points                           |
| MULTILINESTRING | A collection of curves                        |
| MULTIPOLYGON | A collection of polygons                         |
| GEOMETRYCOLLECTION | A collection of any spatial data types     |

### Example

```sql
CREATE TABLE spatial_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    location POINT,
    route LINESTRING,
    area POLYGON
);

INSERT INTO spatial_examples (location, route, area)
VALUES (
    ST_GeomFromText('POINT(40.7128 -74.0060)'),
    ST_GeomFromText('LINESTRING(0 0, 1 1, 2 2)'),
    ST_GeomFromText('POLYGON((0 0, 0 10, 10 10, 10 0, 0 0))')
);
```

## 5. JSON Data Type

MySQL supports a native JSON data type for storing JSON documents.

### Example

```sql
CREATE TABLE json_examples (
    id INT AUTO_INCREMENT PRIMARY KEY,
    data JSON
);

INSERT INTO json_examples (data)
VALUES 
(
    '{"name": "John Doe", "age": 30, "city": "New York", "skills": ["PHP", "MySQL", "JavaScript"]}'
);

-- Querying JSON data
SELECT id, JSON_EXTRACT(data, '$.name') AS name, JSON_EXTRACT(data, '$.age') AS age
FROM json_examples;

-- Updating JSON data
UPDATE json_examples
SET data = JSON_SET(data, '$.age', 31, '$.skills[3]', 'Python')
WHERE id = 1;
```

## Best Practices

1. Choose the smallest data type that can reliably store your data to optimize storage and performance.
2. Use UNSIGNED for integer types when you only need non-negative numbers.
3. Prefer CHAR for fixed-length strings and VARCHAR for variable-length strings.
4. Use TEXT types for storing large amounts of textual data, and BLOB types for binary data.
5. Consider using DECIMAL for precise decimal numbers, especially for financial calculations.
6. Use the appropriate spatial data type for geographic data to take advantage of spatial indexing and functions.
7. Leverage the JSON data type for flexible schema designs and to store semi-structured data.
8. Be mindful of the maximum length limitations when choosing string types.
9. Use TIMESTAMP for recording the last modification time of a record, as it updates automatically.
10. Remember that different storage engines may handle certain data types differently, especially regarding indexing and performance.

Remember to consider your specific use case, expected data volume, and query patterns when choosing data types for your database schema.
