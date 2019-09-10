---
layout:     post
title:      "如何修改Pyspark Dataframe的列名"
subtitle:    从零开始学Spark
date:       2019-08-15
author:     Lyon Ling
header-img: img/post-bg-kkt.jpg
catalog: true
tags:
    - Spark
    - Programing 
    - BigData
	- Tutorial
	- 转载
---

在做Spark Dataframe操作的时候会遇到很多需求. 最近我遇到了一个问题, 就是通过`agg`方法传入多种聚合函数对多列进行操作, 然后后就生成了多列的聚合结果. 但是列名就是以 原列名+聚合函数的方法, 然后就产生需求 ---- 如何修改这些列名呢?

然后找到了以下方法:

* 使用`selectExpr`

  ```python
  data = sqlContext.createDataFrame([("Alberto", 2), ("Dakota", 2)], 
                                    ["Name", "askdaosdka"])
  data.show()
  data.printSchema()
  # Output
  #+-------+----------+
  #|   Name|askdaosdka|
  #+-------+----------+
  #|Alberto|         2|
  #| Dakota|         2|
  #+-------+----------+
  #root
  # |-- Name: string (nullable = true)
  # |-- askdaosdka: long (nullable = true)
  
  df = data.selectExpr("Name as name", "askdaosdka as age")
  df.show()
  df.printSchema()
  # Output
  #+-------+---+
  #|   name|age|
  #+-------+---+
  #|Alberto|  2|
  #| Dakota|  2|
  #+-------+---+
  #root
  # |-- name: string (nullable = true)
  # |-- age: long (nullable = true)
  ```

* 使用`withColumnRenamed`

  ```python
  oldColumns = data.schema.names
  newColumns = ["name", "age"]
  
  # 这里的reduce是python的reduce
  from functools import reduce
  df = reduce(lambda data, idx: data.withColumnRenamed(oldColumns[idx], newColumns[idx]), range(len(oldColumns)), data)
  df.printSchema()
  df.show()
  ```

  为了便于理解这里给出python `reduce`方法的官方解释.

  ```python
  functools.reduce(function, iterable[, initializer])
  '''
  Apply function of two arguments cumulatively to the items of sequence, from left to right, so as to reduce the sequence to a single value. 
  '''
  
  # Roughly equivalent to:
  def reduce(function, iterable, initializer=None):
      it = iter(iterable)
      if initializer is None:
          value = next(it)
      else:
          value = initializer
      for element in it:
          value = function(value, element)
      return value
  ```

  简单总结一下, 之前看到这种实现方式, 以为是`withColumnRenamed`有什么特别高级的实现, 现在结合两部分代买分析一下, 简单来说还是就是通过循环, 反复调用`withColumnRenamed`方法给df的column重命名......

* 使用`alias`

  ```python
  from pyspark.sql.functions import *
  
  data = data.select(col("Name").alias("name"), col("askdaosdka").alias("age"))
  data.show()
  
  # Output
  #+-------+---+
  #|   name|age|
  #+-------+---+
  #|Alberto|  2|
  #| Dakota|  2|
  #+-------+---+
  ```

* 使用`sqlContext.sql`

  ```python
  sqlContext.registerDataFrameAsTable(data, "myTable")
  df2 = sqlContext.sql("SELECT Name AS name, askdaosdka as age from myTable")
  
  df2.show()
  
  # Output
  #+-------+---+
  #|   name|age|
  #+-------+---+
  #|Alberto|  2|
  #| Dakota|  2|
  #+-------+---+
  ```

总的来说, Pyspark并没有能有一次性对整个column names进行替换的方法, 只能通过一些trick逐个列进行操作. 从个人喜好来说, 第一种和第二种实现方式感觉会相对简单.

主要方法转载自：[python – 如何更改pyspark中的数据框列名？](https://codeday.me/bug/20180407/150940.html) - [代码日志](https://codeday.me/)