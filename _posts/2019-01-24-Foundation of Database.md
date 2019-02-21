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



## RDBMS

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

## NoSQL

> There are three cornerstones of the existing of NoSQL, which is **CAP**, **BASE** and **Eventual Consistency**. While 5-min principle shows the theoretical basis of memory data. 

### CAP

* C: Consitency

* A: Availability

* P: Tolerent of network Partition

10年前，Eric Brewer教授指出了著名的CAP理论，后来Seth Gilbert 和 Nancy lynch两人证明了CAP理论的正确性。CAP理论告诉我们，一个分布式系统不可能满足一致性，可用性和分区容错性这三个需求，最多只能同时满足两个。 
熊掌与鱼不可兼得也。关注的是一致性，那么您就需要处理因为系统不可用而导致的写操作失败的情况，而如果您关注的是可用性，那么您应该知道系统的read操作可能不能精确的读取到write操作写入的最新值。因此系统的关注点不同，相应的采用的策略也是不一样的，只有真正的理解了系统的需求，才有可能利用好CAP理论。 
作为架构师，一般有两个方向来利用CAP理论 
key-value存储，如Amaze Dynamo等，可根据CAP三原则灵活选择不同倾向的数据库产品。 
领域模型 + 分布式缓存 + 存储 （Qi4j和NoSql运动），可根据CAP三原则结合自己项目定制灵活的分布式方案，难度高。 
而对大型网站，可用性与分区容忍性优先级要高于数据一致性，一般会尽量朝着 A、P 的方向设计，然后通过其它手段保证对于一致性的商务需求。架构设计师不要精力浪费在如何设计能满足三者的完美分布式系统，而是应该进行取舍。 
不同数据对于一致性的要求是不同的。举例来讲，用户评论对不一致是不敏感的，可以容忍相对较长时间的不一致，这种不一致并不会影响交易和用户体验。而产品价格数据则是非常敏感的，通常不能容忍超过10秒的价格不一致。

### Eventual Consistency

 一言蔽之：过程松，结果紧，最终结果必须保持一致性 
为了更好的描述客户端一致性，我们通过以下的场景来进行，这个场景中包括三个组成部分： 
存储系统 
存储系统可以理解为一个黑盒子，它为我们提供了可用性和持久性的保证。 
Process A, B, C, 相互独立， 同时实现对存储系统的write和read操作。 
下面以上面的场景来描述下不同程度的一致性：

* **强一致性**（即时一致性） 

  假如A先写入了一个值到存储系统，存储系统保证后续A,B,C的读取操作都将返回最新值 

* **弱一致性** 

  假如A先写入了一个值到存储系统，存储系统不能保证后续A,B,C的读取操作能读取到最新值。此种情况下有一个“不一致性窗口”的概念，它特指从A写入值，到后续操作A,B,C读取到最新值这一段时间。 

* **最终一致性** 
  最终一致性是弱一致性的一种特例。假如A首先write了一个值到存储系统，存储系统保证如果在A,B,C后续读取之前没有其它写操作更新同样的值的话，最终所有的读取操作都会读取到最A写入的最新值。此种情况下，如果没有失败发生的话，“不一致性窗口”的大小依赖于以下的几个因素：交互延迟，系统的负载，以及复制技术中replica的个数（这个可以理解为master/salve模式中，salve的个数），最终一致性方面最出名的系统可以说是DNS系统，当更新一个域名的IP以后，根据配置策略以及缓存控制策略的不同，最终所有的客户都会看到最新的值。

- **Causal consistency（因果一致性）**

  如果Process A通知Process B它已经更新了数据，那么Process B的后续读取操作则读取A写入的最新值，而与A没有因果关系的C则可以最终一致性。

- **Read-your-writes consistency**

  如果Process A写入了最新的值，那么Process A的后续操作都会读取到最新值。但是其它用户可能要过一会才可以看到。

- **Session consistency**

  此种一致性要求客户端和存储系统交互的整个会话阶段保证Read-your-writes consistency.Hibernate的session提供的一致性保证就属于此种一致性。

- **Monotonic read consistency**

  此种一致性要求如果Process A已经读取了对象的某个值，那么后续操作将不会读取到更早的值。

- **Monotonic write consistency**

  此种一致性保证系统会序列化执行一个Process中的所有写操作。

### BASE

- Basically Availble --基本可用
- Soft-state --软状态/柔性事务

​	"Soft state" 可以理解为"无连接"的, 而 "Hard state" 是"面向连接"的

- Eventual Consistency --最终一致性

> 最终一致性， 也是是 ACID 的最终目的。

BASE模型反ACID模型，完全不同ACID模型，牺牲高一致性，获得可用性或可靠性： Basically Available基本可用。支持分区失败(e.g. sharding碎片划分数据库) Soft state软状态 状态可以有一段时间不同步，异步。 Eventually consistent最终一致，最终数据是一致的就可以了，而不是时时一致。

BASE思想的主要实现有

1. 按功能划分数据库

2. sharding碎片 

BASE思想主要强调基本的可用性，如果你需要高可用性，也就是纯粹的高性能，那么就要以一致性或容错性为牺牲，BASE思想的方案在性能上还是有潜力可挖的。

### I/O的五分钟法则

在 1987 年，Jim Gray 与 Gianfranco Putzolu 发表了这个"**五分钟法则**"的观点，简而言之，如果一条记录频繁被访问，就应该放到内存里，否则的话就应该待在硬盘上按需要再访问。这个临界点就是五分钟。 看上去像一条经验性的法则，实际上五分钟的评估标准是根据投入成本判断的，根据当时的硬件发展水准，在内存中保持 1KB 的数据成本相当于硬盘中存据 400 秒的开销(接近五分钟)。这个法则在 1997 年左右的时候进行过一次回顾，证实了五分钟法则依然有效（硬盘、内存实际上没有质的飞跃)，而这次的回顾则是针对 SSD 这个"新的旧硬件"可能带来的影响。

随着闪存时代的来临，五分钟法则一分为二：是把 SSD 当成较慢的内存（extended buffer pool ）使用还是当成较快的硬盘（extended disk）使用。小内存页在内存和闪存之间的移动对比大内存页在闪存和磁盘之间的移动。在这个法则首次提出的 20 年之后，在闪存时代，5 分钟法则依然有效，只不过适合更大的内存页(适合 64KB 的页，这个页大小的变化恰恰体现了计算机硬件工艺的发展，以及带宽、延时)。