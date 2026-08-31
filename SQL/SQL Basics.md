## Databases

A **database** is an organized collection of data.

A relational database stores data primarily in **tables**, where:

-   **Table** → collection of related data.
-   **Row / Record** → one individual entry.
-   **Column / Field** → one property of the data.
-   **Schema** → structure/definition of database objects such as tables.

Example table:

| id  | name  | email               | age |
| --- | ----- | ------------------- | --- |
| 1   | Alice | `alice@example.com` | 25  |
| 2   | Bob   | `bob@example.com`   | 30  |


Basic database commands:

``` sql
CREATE DATABASE appyflow;
```

``` sql
DROP DATABASE appyflow;
```

In PostgreSQL `psql`:

``` sql
\l
```

Lists databases.

``` sql
\c appyflow
```

Connects to a database.

``` sql
\dt
```

Lists tables in the current database.

``` sql
\d users
```

Shows the structure of a table.

------------------------------------------------------------------------

## DDL --- Data Definition Language

DDL commands define or modify the **structure** of the database.

Main commands:

-   `CREATE`
-   `ALTER`
-   `DROP`
-   `TRUNCATE`

### CREATE TABLE

``` sql
CREATE TABLE users (
    id INTEGER,
    name VARCHAR(100),
    email VARCHAR(255),
    age INTEGER
);
```

Common data types:

``` sql
INTEGER
BIGINT
DECIMAL(10, 2)
VARCHAR(100)
TEXT
BOOLEAN
DATE
TIME
TIMESTAMP
```

PostgreSQL also commonly uses:

``` sql
UUID
JSON
JSONB
SERIAL
BIGSERIAL
```

### Constraints

Constraints define rules for columns.

``` sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE,
    age INTEGER CHECK (age >= 18),
    active BOOLEAN DEFAULT TRUE
);
```

Common constraints:

-   `PRIMARY KEY` → uniquely identifies a row.
-   `FOREIGN KEY` → references another table.
-   `NOT NULL` → value must exist.
-   `UNIQUE` → duplicate values are not allowed.
-   `DEFAULT` → provides a default value.
-   `CHECK` → validates a condition.

### ALTER TABLE

Add a column:

``` sql
ALTER TABLE users
ADD COLUMN phone VARCHAR(20);
```

Remove a column:

``` sql
ALTER TABLE users
DROP COLUMN phone;
```

Rename a column:

``` sql
ALTER TABLE users
RENAME COLUMN name TO full_name;
```

Rename a table:

``` sql
ALTER TABLE users
RENAME TO app_users;
```

### DROP TABLE

Deletes the table and its data.

``` sql
DROP TABLE users;
```

Optionally:

``` sql
DROP TABLE IF EXISTS users;
```

### TRUNCATE

Deletes all rows while keeping the table itself.

``` sql
TRUNCATE TABLE users;
```

Difference:

``` text
DROP      → removes the table
TRUNCATE  → removes all rows
```

------------------------------------------------------------------------

## DML --- Data Manipulation Language

DML deals with the **data inside tables**.

Main operations:

-   `INSERT`
-   `SELECT`
-   `UPDATE`
-   `DELETE`

### INSERT

Insert one row:

``` sql
INSERT INTO users (id, name, email, age)
VALUES (1, 'Alice', 'alice@example.com', 25);
```

Insert multiple rows:

``` sql
INSERT INTO users (id, name, email, age)
VALUES
    (2, 'Bob', 'bob@example.com', 30),
    (3, 'Charlie', 'charlie@example.com', 28);
```

### SELECT

Retrieve all columns:

``` sql
SELECT *
FROM users;
```

Retrieve specific columns:

``` sql
SELECT name, email
FROM users;
```

Filter rows:

``` sql
SELECT *
FROM users
WHERE age >= 25;
```

Multiple conditions:

``` sql
SELECT *
FROM users
WHERE age >= 25 AND active = TRUE;
```

Common comparison operators:

``` text
=       equal
<>      not equal
!=      not equal
>       greater than
<       less than
>=      greater than or equal
<=      less than or equal
```

Common logical operators:

``` text
AND
OR
NOT
```

### IN

``` sql
SELECT *
FROM users
WHERE age IN (20, 25, 30);
```

Equivalent to checking several values with `OR`.

### BETWEEN

``` sql
SELECT *
FROM users
WHERE age BETWEEN 20 AND 30;
```

`BETWEEN` is inclusive.

### LIKE

Pattern matching:

``` sql
SELECT *
FROM users
WHERE name LIKE 'A%';
```

Common patterns:

``` text
'A%'    starts with A
'%A'    ends with A
'%A%'   contains A
'A_'    A followed by exactly one character
```

PostgreSQL provides `ILIKE` for case-insensitive matching:

``` sql
SELECT *
FROM users
WHERE name ILIKE 'alice%';
```

### NULL

`NULL` represents a missing or unknown value.

Do not use:

``` sql
WHERE email = NULL;
```

Use:

``` sql
WHERE email IS NULL;
```

Or:

``` sql
WHERE email IS NOT NULL;
```

### ORDER BY

Ascending:

``` sql
SELECT *
FROM users
ORDER BY age ASC;
```

Descending:

``` sql
SELECT *
FROM users
ORDER BY age DESC;
```

`ASC` is the default.

### LIMIT

``` sql
SELECT *
FROM users
LIMIT 10;
```

Skip rows using `OFFSET`:

``` sql
SELECT *
FROM users
LIMIT 10 OFFSET 20;
```

### DISTINCT

Return unique values:

``` sql
SELECT DISTINCT age
FROM users;
```

### UPDATE

``` sql
UPDATE users
SET age = 26
WHERE id = 1;
```

Update multiple columns:

``` sql
UPDATE users
SET
    age = 26,
    active = TRUE
WHERE id = 1;
```

Without `WHERE`:

``` sql
UPDATE users
SET active = TRUE;
```

This updates **every row**.

### DELETE

``` sql
DELETE FROM users
WHERE id = 1;
```

Without `WHERE`:

``` sql
DELETE FROM users;
```

This deletes **every row**.

------------------------------------------------------------------------

## Primary and Foreign Keys

### Primary Key

A **primary key** uniquely identifies every row in a table.

``` sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100)
);
```

Primary keys:

-   Must be unique.
-   Cannot be `NULL`.
-   There is one primary key constraint per table.
-   Can contain one or multiple columns.

### Auto-Generated IDs

PostgreSQL supports identity columns:

``` sql
CREATE TABLE users (
    id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100)
);
```

Now:

``` sql
INSERT INTO users (name)
VALUES ('Alice');
```

PostgreSQL generates the ID automatically.

### Foreign Key

A foreign key creates a relationship between tables.

``` sql
CREATE TABLE users (
    id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE applications (
    id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    company VARCHAR(100),
    user_id INTEGER REFERENCES users(id)
);
```

`applications.user_id` is a foreign key referencing `users.id`.

The longer syntax is:

``` sql
CREATE TABLE applications (
    id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    company VARCHAR(100),
    user_id INTEGER,

    FOREIGN KEY (user_id)
        REFERENCES users(id)
);
```

------------------------------------------------------------------------

## Aggregate Functions

Aggregate functions calculate a value from multiple rows.

Common functions:

-   `COUNT()`
-   `SUM()`
-   `AVG()`
-   `MIN()`
-   `MAX()`

### COUNT

Count rows:

``` sql
SELECT COUNT(*)
FROM users;
```

Count non-NULL values:

``` sql
SELECT COUNT(email)
FROM users;
```

Count unique values:

``` sql
SELECT COUNT(DISTINCT age)
FROM users;
```

### SUM

``` sql
SELECT SUM(salary)
FROM employees;
```

### AVG

``` sql
SELECT AVG(salary)
FROM employees;
```

### MIN

``` sql
SELECT MIN(salary)
FROM employees;
```

### MAX

``` sql
SELECT MAX(salary)
FROM employees;
```

Aggregate functions can be combined with filtering:

``` sql
SELECT AVG(salary)
FROM employees
WHERE department = 'Engineering';
```

------------------------------------------------------------------------

## GROUP BY

`GROUP BY` groups rows with the same value before applying aggregate
functions.

``` sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department;
```

Another example:

``` sql
SELECT department, COUNT(*)
FROM employees
GROUP BY department;
```

General pattern:

``` sql
SELECT column, AGGREGATE_FUNCTION(column)
FROM table
GROUP BY column;
```

------------------------------------------------------------------------

## HAVING

`WHERE` filters **rows before grouping**.

`HAVING` filters **groups after grouping**.

``` sql
SELECT department, COUNT(*)
FROM employees
GROUP BY department
HAVING COUNT(*) >= 5;
```

You can use both:

``` sql
SELECT department, AVG(salary)
FROM employees
WHERE active = TRUE
GROUP BY department
HAVING AVG(salary) > 70000;
```

Think of the order roughly as:

``` text
FROM
 ↓
WHERE
 ↓
GROUP BY
 ↓
HAVING
 ↓
SELECT
 ↓
ORDER BY
 ↓
LIMIT
```

------------------------------------------------------------------------

## Useful Basic Functions

### String Functions

Convert to uppercase:

``` sql
SELECT UPPER(name)
FROM users;
```

Convert to lowercase:

``` sql
SELECT LOWER(name)
FROM users;
```

String length:

``` sql
SELECT LENGTH(name)
FROM users;
```

Concatenate strings:

``` sql
SELECT CONCAT(first_name, ' ', last_name)
FROM users;
```

PostgreSQL also supports:

``` sql
SELECT first_name || ' ' || last_name
FROM users;
```

Trim whitespace:

``` sql
SELECT TRIM(name)
FROM users;
```

### Numeric Functions

Round:

``` sql
SELECT ROUND(123.456, 2);
```

Absolute value:

``` sql
SELECT ABS(-50);
```

### NULL Functions

`COALESCE` returns the first non-NULL value:

``` sql
SELECT COALESCE(phone, 'No phone number')
FROM users;
```

------------------------------------------------------------------------

## Aliases

Aliases temporarily rename columns in query results.

``` sql
SELECT name AS user_name
FROM users;
```

Useful with functions:

``` sql
SELECT COUNT(*) AS total_users
FROM users;
```

``` sql
SELECT AVG(age) AS average_age
FROM users;
```

------------------------------------------------------------------------

## Basic SQL Query Template

A useful template to remember:

``` sql
SELECT column1, column2
FROM table_name
WHERE condition
GROUP BY column1
HAVING aggregate_condition
ORDER BY column1
LIMIT 10;
```
