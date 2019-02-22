---
layout:     post
title:      "Priority of SQL Clauses"
subtitle:    SQL语句的优先级
date:       2019-02-22
author:     Lyon Ling
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Database
    - Foundations
---

>最近在学Spark SQL的时候顺便复习了一下SQL语句，其中遇到一些问题就顺便把网上查到的结论记下来。
>
>在写复杂的SQL语句的时候如果不注意其中的工作原理，很容易就会写出效率低，出错率高的SQL语句，这里列举了一些SQL关键字的执行优先级。



* FROM字句:执行顺序为从后往前、从右到左。数据量较大的表尽量放在后面。

* WHERE字句：执行顺序为自下而上、从右到左。将能过滤掉最大数量记录的条件写在WHERE字句的最右。

* GROUP BY：执行顺序从右往左分组，最好在GROUP BY前使用WHERE将不需要的记录在GROUP BY之前过滤掉

* HAVING字句：消耗资源。尽量避免使用，HAVING会在**检索出所有记录之后**才对结果进行过滤，需要排序等操作。

* SELECT字句：少用\*号，尽量使用字段名称，oracle在解析的过程中，通过查询数据字典将\*号依次转换成所有列名，消耗时间。

* ORDER BY字句：执行顺序从左到右，消耗资源


原文：https://blog.csdn.net/qigc_0529/article/details/52096466 
