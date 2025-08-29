
## [[Creating and Connecting to a Database]]

```sql
-- Create a database
CREATE DATABASE db_name;

-- Connect to a database (in psql)
\c db_name;
```

⚠️ **Very Dangerous Command:**

```sql
DROP DATABASE db_name;
```

This deletes the entire database instantly. Be careful.

---

## [[Creating Tables in PostgreSQL]]

### ✅ Basic Syntax

```sql
CREATE TABLE table_name (
  column_name data_type [constraints],
  ...
);
```

### ✅ Example:

```sql
CREATE TABLE person (
  id INT,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  gender VARCHAR(6),
  date_of_birth TIMESTAMP
);
```

🔗 For all datatypes: [PostgreSQL Datatypes](https://www.postgresql.org/docs/current/datatype.html)

➡️ Most commonly used datatypes: See [[Datatypes]]

---

## [[\d Command – Inspecting Tables]]

| Command          | Description                                       |
| ---------------- | ------------------------------------------------- |
| `\d`             | List all tables, views, sequences in the schema   |
| `\dt`            | List only tables                                  |
| `\d table_name`  | Describe structure of a specific table            |
| `\di`            | List all indexes                                  |
| `\ds`            | List all sequences                                |
| `\dv`            | List all views                                    |
| `\d+`            | Show detailed list of all relations               |
| `\d+ table_name` | Show detailed table structure (with storage info) |

📸 ![[Pasted image 20250618115034.png]]

---

## [[Safe vs Unsafe Table Design]]

### ✅ Safe Table:

```sql
CREATE TABLE person (
  id BIGSERIAL NOT NULL PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  gender VARCHAR(6) NOT NULL,
  date_of_birth DATE NOT NULL,
  email VARCHAR(50),
  address VARCHAR(50)
);
```

### ❌ Unsafe Table:

```sql
CREATE TABLE person (
  id INT,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  gender VARCHAR(6),
  date_of_birth TIMESTAMP
);
```

### 🔍 Differences:

|Aspect|Safe Table|Unsafe Table|
|---|---|---|
|ID|BIGSERIAL PRIMARY KEY (auto-increments)|INT (manual insert, no uniqueness)|
|Primary Key|✅ Yes|❌ No|
|NOT NULL Fields|✅ Yes|❌ No|
|Date Field|Uses `DATE` (accurate)|Uses `TIMESTAMP` (overkill)|
|Data Integrity|✅ Strong|❌ Weak|

---

## [[Inserting Data Using SQL Files]]

### ✅ Use `\i` to Run SQL Files:

```sql
\i 'C:/psql/person.sql'
```

### ⚠️ Common Issues:

- Use `/` instead of `\` in paths.
    
- Quote the file path.
    
- Avoid protected folders like Downloads.
    
- Run terminal as **Administrator** if needed.
    

---

## [[SELECT Operations]]

### ✅ Basic Queries

```sql
SELECT * FROM person;
SELECT first_name, last_name FROM person;
SELECT * FROM person WHERE gender = 'Male';
SELECT * FROM person ORDER BY first_name ASC;
SELECT * FROM person LIMIT 5;
```

---

## [[Query Filters and Pagination]]

```sql
SELECT * FROM users LIMIT 5;
SELECT * FROM users OFFSET 5 LIMIT 5;
SELECT * FROM users FETCH FIRST 5 ROWS ONLY;
SELECT * FROM users WHERE age BETWEEN 18 AND 25;
SELECT * FROM orders WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';
```

```sql
SELECT * FROM users WHERE name LIKE 'A%';
SELECT * FROM users WHERE name LIKE '%n';
SELECT * FROM users WHERE name LIKE '%ar%';
SELECT * FROM users WHERE name LIKE '_a%';
```

Pagination:

```sql
SELECT * FROM products LIMIT 10 OFFSET 0;
SELECT * FROM products LIMIT 10 OFFSET 10;
```

---

## [[ORDER BY Keyword]]

```sql
SELECT * FROM table_name ORDER BY column_name [ASC | DESC];
SELECT * FROM users ORDER BY age;
SELECT * FROM users ORDER BY name DESC;
SELECT * FROM cars ORDER BY make ASC, price DESC;
```

---

## [[HAVING Keyword]]

```sql
SELECT gender, COUNT(*)
FROM person
GROUP BY gender
HAVING COUNT(*) > 5;
```

🔁 `WHERE` filters rows **before** grouping. `HAVING` filters groups **after** aggregation.

---

## [[COALESCE Function]]

```sql
SELECT COALESCE(email, 'email not provided') FROM person;
SELECT name, COALESCE(phone, 'no phone') FROM contacts;
```

Returns the first non-null value in a list.

---

## [[SET CONSTRAINTS]]
add constraint example is missing the code is
```sql
ALTER TABLE person ADD CONSTRAINT unique_car_id UNIQUE (car_id);
```
The above command will set the constraint to the car_id.

```sql
BEGIN;
SET CONSTRAINTS ALL DEFERRED;
-- insert parent and child records
COMMIT;
```

Used inside transactions to delay constraint checking until COMMIT.

---

## [[DROP CONSTRAINT]]

```sql
ALTER TABLE table_name DROP CONSTRAINT constraint_name;
ALTER TABLE table_name ALTER COLUMN column_name DROP NOT NULL;
```

Use `\d table_name` to check constraint names.

---

## [[DISTINCT Keyword]]

```sql
SELECT DISTINCT email FROM person;
SELECT DISTINCT country_of_birth, gender FROM person;
SELECT COUNT(DISTINCT email) FROM person;
```

Returns only unique values.

---

## [[Comparison Operators]]

|Operator|Meaning|Example|
|---|---|---|
|`=`|Equal to|`WHERE age = 25`|
|`!=`|Not equal to|`WHERE age != 30`|
|`>`|Greater than|`WHERE salary > 50000`|
|`<`|Less than|`WHERE age < 18`|
|`>=`|Greater or equal|`WHERE marks >= 40`|
|`<=`|Less or equal|`WHERE salary <= 80000`|
|`BETWEEN`|In range|`BETWEEN 10 AND 20`|
|`LIKE`|Pattern match|`LIKE 'A%'`|

---


## [[Update command]]

Syntax:
```sql

UPDATE PERSON SET EMAIL= 'subarna@ask.com' WHERE last_name = 'Pestell';
```

Update command is used to update the existing value in the table.

---

## HOW TO DEAL WITH DUPLICATE KEY ERRORS OR EXCEPTION?

Let's go again and select the previous line where email = 'subarna@ask.com'



 SELECT * FROM person JOIN car ON person.car_id = car.id; join operations , foreign key, relational tables relations
foreign key and primary key

inner join

deleting records with foreign keys

# Deleting Records with Foreign Keys

In relational databases, **foreign keys** are used to ensure that related data stays consistent.  
But when deleting records, they can **prevent you from deleting a row** if other rows still depend on it.



![[postgresql-cheat-sheet-a4.pdf]]