---
layout: page
title: SQL基本命令大全
subtitle: 专注数据库，一百年不放松
use-site-title: true
---

## What is SQL?

- SQL stands for Structured Query Language
- SQL lets you access and manipulate databases
- SQL became a standard of the American National Standards Institute (ANSI) in 1986, and of the International Organization for Standardization (ISO) in 1987

## What can SQL do ?

- SQL can execute queries against a database
- SQL can retrieve data from a database
- SQL can insert records in a database
- SQL can update records in a database
- SQL can delete records from a database
- SQL can create new databases
- SQL can create new tables in a database
- SQL can create stored procedures in a database
- SQL can create views in a database
- SQL can set permissions on tables, procedures, and views

## SQL Syntax

### SELECT

```
SELECT column_1, column_2,... 
FROM table_name;
```

```
SELECT DISTINCT column_1, column_2,...
FROM table_name;
```

### WHERE

```
SELECT column_1, column_2,... 
FROM table_name
WHERE condition;
```

```
SELECT column_1, column_2,... 
FROM table_name
WHERE condition1 AND condition2 AND condition3;
```

```
SELECT column_1, column_2,... 
FROM table_name
WHERE condition1 OR condition2 OR condition3;
```

```
SELECT column_1, column_2,... 
FROM table_name
WHERE NOT condition;
```

```
SELECT TOP number | percent column_name(s)
FROM table_name
WHERE condition;
```

```
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number;
```

```
SELECT MIN(column_name)
FROM table_name
WHERE condition;
```

```
SELECT MAX(column_name)
FROM table_name
WHERE condition;
```

```
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

```
SELECT AVG(column_names)
FROM table_name
WHERE condition;
```

```
SELECT SUM(column_names)
FROM table_name
WHERE condition;
```

### ORDER BY

```
SELECT column_1, column_2,... 
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;
```

### INSERT INTO

```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

```
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```

### NULL VALUES

```
SELECT column_names
FROM table_name
WHERE column_name IS (NOT) NULL;
```

### UPDATE

```
UPDATE table_name 
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

### DELETE

```
DELETE FROM table_name
WHERE condition;
```

### LIKE

```
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern;
```
(patten: 'a%' starts with a; '%a' ends with a; '_a%' second is a; '_%_%_' at least three characters)

### IN

```
SELECT column1, column2, ...
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

```
SELECT column1, column2, ...
FROM table_name
WHERE column_name IN (SELECT statement);
```

### BETWEEN

```
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

### AS

```
SELECT column_name AS column_alias_name
FROM table_name;
```

```
SELECT column_name
FROM table_name AS table_alias_name;
```

### JOIN

```
SELECT column_name(s)
FROM table1
INNER JOIN table2 ON table1.column_name = table2.column_name;
```

(the interleaving of table1 and table2)

```
SELECT column_name(s)
FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name;
```

(all of table1 + the interleaving)

```
SELECT column_name(s)
FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;
```

(all of table2 + the interleaving)

```
SELECT column_name(s)
FROM table1
FULL JOIN table2 ON table1.column_name = table2.column_name;
```

(all of table1 + all of table2)

```
SELECT column_name(s)
FROM table1 T1, table1 T2
WHERE condition;
```

(self join)

### UNION


```
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

```
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

(UNION ALL allows duplicate values)

### GROUP BY

```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s);
```

### HAVING


```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);
```
(The HAVING clause was added to SQL because the WHERE keyword could not be used with aggregate functions.)

### EXISTS

```
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition);
```

### ANY & ALL

```
SELECT column_name(s)
FROM table_name
WHERE column_name operator ANY
(SELECT column_name FROM table_name WHERE condition);
```

```
SELECT column_name(s)
FROM table_name
WHERE column_name operator ALL
(SELECT column_name FROM table_name WHERE condition);
```

### SELECT INTO

```
SELECT column1, column2, ...
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition;
```

(The SELECT INTO statement copies data from one table into a new table.)



### INSERT INTO SELECT

```
INSERT INTO table2
SELECT * FROM table1
WHERE condition;
```

(The INSERT INTO SELECT statement copies data from one table and inserts it into another table)

### Store Procedures

```
CREATE PROCEDURE procedure_name
AS
sql_statement
GO;

EXECUTE procedure_name;
```

```
CREATE PROCEDURE SelectAllCustomers @City nvarchar(30), @PostalCode nvarchar(10)
SELECT * FROM Customers WHERE City = @City AND PostalCode = @PostalCode
GO;

EXECUTE SelectAllCustomers City = "London", PostalCode = "WA1 1DP";
```

### Comments

```
--Select all:
SELECT * FROM Customers;
```

```
/*Select all the columns
of all the records
in the Customers table:*/
SELECT * FROM Customers;
```

### CREATE

```
CREATE DATABASE database_name;
```

```
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);
```

### DROP

```
DROP DATABASE database_name
```

```
DROP TABLE table_name
```

```
TRUNCATE TABLE table_name
```

(The TRUNCATE TABLE statement is used to delete the data inside a table, but not the table itself.)

### ALTER

```
ALTER TABLE table_name
ADD column_name datatype;
```

```
ALTER TABLE table_name
DROP COLUMN column_name;
```

