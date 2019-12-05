---
layout:     post
title:      "OLAP vs OLTP"
subtitle:   "联机事务处理和联机处理分析"
date:       2019-01-24
author:     Lyon Ling
header-img: img/post-bg-oltpolap.jpg
catalog: true
tags:
    - Database
    - DataMining
    - BigData
---

*To be briefly.*

- **OLTP** (On-line Transaction Processing) is involved in the operation of a particular system. OLTP is characterized by a large number of **short on-line transactions** (INSERT, UPDATE, DELETE). The main emphasis for OLTP systems is put on very **fast** query processing, maintaining data integrity in multi-access environments and an effectiveness measured by number of transactions per second. In OLTP database there is detailed and current data, and schema used to store transactional databases is the entity model (usually 3NF). It involves Queries accessing individual record like Update your Email in Company database.
- **OLAP** (On-line Analytical Processing) deals with **Historical Data or Archival Data**. OLAP is characterized by relatively low volume of transactions. <u>Queries are often very complex and involve aggregations</u>. For OLAP systems a response time is an effectiveness measure. OLAP applications are widely used by Data Mining techniques. In OLAP database there is aggregated, historical data, stored in multi-dimensional schemas (usually star schema). Sometime query need to access large amount of data in Management records like what was the profit of your company in last year.