---
layout:     post
title:      "Foundation of Database"
subtitle:    数据库基础知识整理[持续更新]
date:       2019-01-25
author:     Lyon Ling
header-img: img/post-bg-dbfoundation.jpg
catalog: true
tags:
    - Database
    - Foundations
---

>These days, I met several scenarios that have some database principles and common concepts. While most of them I have already forgot. As a computer science background student, it really makes me ashamed.
>
>So, here I put them as more as I could consider. 



### Common Concepts

#### Transactions (事务)

A **transaction** symbolizes a unit of work performed within a **database management system** (or similar system) against a database, it's a single unit of logic or work, sometimes made up of multiple operations.

A database transaction, by definition, must follow **ACID** principle, witch is atomic, consistent, isolated and durable.

#### DML vs DDL vs DCL vs TCL

**DDL** is short name of **Data Definition Language**, which deals with database schemas and descriptions, of how the data should reside in the database.

- `CREATE` – to create database and its objects like (table, index, views, store procedure, function and triggers)
- `ALTER` – alters the structure of the existing database
- `DROP` – delete objects from the database
- `TRUNCATE` – remove all records from a table, including all spaces allocated for the records are removed
- `COMMENT` – add comments to the data dictionary
- `RENAME` – rename an object

**DML** is short name of **Data Manipulation Language** which deals with data manipulation, and includes most common SQL statements such SELECT, INSERT, UPDATE, DELETE etc, and it is used to store, modify, retrieve, delete and update data in database.

- `SELECT` – retrieve data from the a database
- `INSERT` – insert data into a table
- `UPDATE` – updates existing data within a table
- `DELETE` – Delete all records from a database table
- `MERGE` – UPSERT operation (insert or update)
- `CALL` – call a PL/SQL or Java subprogram
- `EXPLAIN PLAN` – interpretation of the data access path
- `LOCK TABLE` – concurrency Control

**DCL** is short name of **Data Control Language** which includes commands such as GRANT, and mostly concerned with rights, permissions and other controls of the database system.

- `GRANT` – allow users access privileges to database
- `REVOKE` – withdraw users access privileges given by using the GRANT command

**TCL** is short name of **Transaction Control Language** which deals with transaction within a database.

- `COMMIT` – commits a Transaction
- `ROLLBACK` – rollback a transaction in case of any error occurs
- `SAVEPOINT` – to rollback the transaction making points within groups
- `SET TRANSACTION` – specify characteristics for the transaction

#### Data Warehouse

> Concept 

##### ETL



### Principles

#### ACID vs BASE

> These two represent the two different types and directions of database management system, Like RDBMS and NoSQL.
>
> It's a bit complex, I will explain them latter, because there is a urgent exam tomorrow.



#### Normal Forms

##### 1NF

##### 2NF

##### 3NF

##### BCNF

