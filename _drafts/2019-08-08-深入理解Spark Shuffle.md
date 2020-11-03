---
layout:     post
title:      "深入理解Spark Shuffle"
subtitle:    从零开始学Spark
date:       2019-08-08
author:     Lyon Ling
header-img: img/post-bg-macosaudio.jpg
catalog: true
tags:
    - BigData
    - Spark
---

[TOC]

## Shuffle带来的问题

在Spark大规模数据处理中，因为shuffle后出现OOM问题非常常见, 深入理解shuffle前先从实际问题出发.

### 报错提示

#### SparkSQL shuffle操作带来的报错

```shell
org.apache.spark.shuffle.MetadataFetchFailedException: 
Missing an output location for shuffle 0
```

```shell
org.apache.spark.shuffle.FetchFailedException:
Failed to connect to hostname/192.168.xx.xxx:50268
```

#### RDD的shuffle操作带来的报错

```
WARN TaskSetManager: Lost task 17.1 in stage 4.1 (TID 1386, spark050013): java.io.FileNotFoundException: /data04/spark/tmp/blockmgr-817d372f-c359-4a00-96dd-8f6554aa19cd/2f/temp_shuffle_e22e013a-5392-4edb-9874-a196a1dad97c (没有那个文件或目录)
```

```
FetchFailed(BlockManagerId(6083b277-119a-49e8-8a49-3539690a2a3f-S155, spark050013, 8533), shuffleId=1, mapId=143, reduceId=3, message=
org.apache.spark.shuffle.FetchFailedException: Error in opening FileSegmentManagedBuffer{file=/data04/spark/tmp/blockmgr-817d372f-c359-4a00-96dd-8f6554aa19cd/0e/shuffle_1_143_0.data, offset=997061, length=112503}
```

### 原因

shuffle分为`shuffle write`和`shuffle read`两部分。 

`shuffle write`的分区数由上一阶段的RDD分区数控制，`shuffle read`的分区数则是由Spark提供的一些参数控制。

`shuffle write`可以简单理解为类似于`saveAsLocalDiskFile`的操作，将计算的中间结果按某种规则临时放到各个executor所在的本地磁盘上。

`shuffle read`的时候数据的分区数则是由spark提供的一些参数控制。可以想到的是，如果这个参数值设置的很小，同时`shuffle read`的量很大，那么将会导致一个task需要处理的数据非常大。结果导致`JVM crash`，从而导致取shuffle数据失败，同时executor也丢失了，看到`Failed to connect to host`的错误，也就是executor lost的意思。有时候即使不会导致`JVM crash`也会造成长时间的`gc`。

### 解决办法

知道原因后问题就好解决了，主要从shuffle的数据量和处理shuffle数据的分区数两个角度入手。

1. 减少shuffle数据

   思考是否可以使用`map side join`或是`broadcast join`来规避shuffle的产生。

   将不必要的数据在shuffle前进行过滤，比如原始数据有20个字段，只要选取需要的字段进行处理即可，将会减少一定的shuffle数据。

2. SparkSQL和DataFrame的`join`, `group by`等操作

   通过`spark.sql.shuffle.partitions`控制分区数，默认为200，根据shuffle的量以及计算的复杂度提高这个值。

3. Rdd的`join`, `groupBy`, `reduceByKey`等操作

   通过`spark.default.parallelism`控制`shuffle read`与`reduce`处理的分区数，默认为运行任务的core的总数（mesos细粒度模式为8个，local模式为本地的core总数），官方建议为设置成运行任务的core的2-3倍。

4. 提高executor的内存

   通过`spark.executor.memory`适当提高executor的memory值。

5. 是否存在数据倾斜的问题

   空值是否已经过滤？异常数据（某个key数据特别大）是否可以单独处理？考虑改变数据分区规则。

原文链接：https://blog.csdn.net/lsshlsw/article/details/51213610



## Spark Shuffle的基本原理与特性

与MapReduce计算框架一样，Spark的Shuffle实现大致如下图所示，在DAG阶段以shuffle为界，划分stage，上游stage做map task，每个map task将计算结果数据分成多份，每一份对应到下游stage的每个partition中，并将其临时写到磁盘，该过程叫做shuffle write；下游stage做reduce task，每个reduce task通过网络拉取上游stage中所有map task的指定分区结果数据，该过程叫做shuffle read，最后完成reduce的业务逻辑。举个栗子，假如上游stage有100个map task，下游stage有1000个reduce task，那么这100个map task中每个map task都会得到1000份数据，而1000个reduce task中的每个reduce task都会拉取上游100个map task对应的那份数据，即第一个reduce task会拉取所有map task结果数据的第一份，以此类推。

[![spark-shuffle-overview](http://sharkdtu.com/images/spark-shuffle-overview.png)](http://sharkdtu.com/images/spark-shuffle-overview.png)

在map阶段，除了map的业务逻辑外，还有shuffle write的过程，这个过程涉及到序列化、磁盘IO等耗时操作；在reduce阶段，除了reduce的业务逻辑外，还有前面shuffle read过程，这个过程涉及到网络IO、反序列化等耗时操作。所以整个shuffle过程是极其昂贵的，spark在shuffle的实现上也做了很多优化改进，随着版本的迭代发布，spark shuffle的实现也逐步得到改进。下面详细介绍spark shuffle的实现演进过程。

## Spark Shuffle实现演进

Spark在1.1以前的版本一直是采用Hash Shuffle的实现的方式，到1.1版本时参考Hadoop MapReduce的实现开始引入Sort Shuffle，在1.5版本时开始Tungsten钨丝计划，引入UnSafe Shuffle优化内存及CPU的使用，在1.6中将Tungsten统一到Sort Shuffle中，实现自我感知选择最佳Shuffle方式，到最近的2.0版本，Hash Shuffle已被删除，所有Shuffle方式全部统一到Sort Shuffle一个实现中。下图是spark shuffle实现的一个版本演进。

[![spark-shuffle-evolution](http://sharkdtu.com/images/spark-shuffle-evolution.png)](http://sharkdtu.com/images/spark-shuffle-evolution.png)

### Hash Shuffle v1

在spark-1.1版本以前，spark内部实现的是Hash Shuffle，其大致原理与前面基本原理介绍中提到的基本一样，如下图所示。

[![spark-shuffle-v1](http://sharkdtu.com/images/spark-shuffle-v1.png)](http://sharkdtu.com/images/spark-shuffle-v1.png)

在map阶段(shuffle write)，每个map都会为下游stage的每个partition写一个临时文件，假如下游stage有1000个partition，那么每个map都会生成1000个临时文件，一般来说一个executor上会运行多个map task，这样下来，一个executor上会有非常多的临时文件，假如一个executor上运行M个map task，下游stage有N个partition，那么一个executor上会生成M*N个文件。另一方面，如果一个executor上有K个core，那么executor同时可运行K个task，这样一来，就会同时申请K*N个文件描述符，一旦partition数较多，势必会耗尽executor上的文件描述符，同时生成K*N个write handler也会带来大量内存的消耗。

在reduce阶段(shuffle read)，每个reduce task都会拉取所有map对应的那部分partition数据，那么executor会打开所有临时文件准备网络传输，这里又涉及到大量文件描述符，另外，如果reduce阶段有combiner操作，那么它会把网络中拉到的数据保存在一个`HashMap`中进行合并操作，如果数据量较大，很容易引发OOM操作。

综上所述，Hash Shuffle实现简单但是特别naive，在小数据量下运行比较快，一旦数据量较大，基本就垮了。当然这个版本的shuffle也是在spark早期版本中，随着版本迭代的进行，shuffle的实现也越来越成熟。

### Hash Shuffle v2

在上一节讲到每个map task都要生成N个partition文件，为了减少文件数，后面引进了，目的是减少单个executor上的文件数。如下图所示，一个executor上所有的map task生成的分区文件只有一份，即将所有的map task相同的分区文件合并，这样每个executor上最多只生成N个分区文件。

[![spark-shuffle-v2](http://sharkdtu.com/images/spark-shuffle-v2.png)](http://sharkdtu.com/images/spark-shuffle-v2.png)

表面上看是减少了文件数，但是假如下游stage的分区数N很大，还是会在每个executor上生成N个文件，同样，如果一个executor上有K个core，还是会开K*N个writer handler，总体上来说基本没太解决问题。对于shuffle read阶段跟v1版一样没改进，仍然容易导致OOM。

### Sort Shuffle v1

针对上述Hash Shuffle的弊端，在spark 1.1.0版本中引入Sort Shuffle，它参考了Hadoop MapReduce中的shuffle实现，对记录进行排序来做shuffle，如下图所示。

[![spark-shuffle-v3](http://sharkdtu.com/images/spark-shuffle-v3.png)](http://sharkdtu.com/images/spark-shuffle-v3.png)

在map阶段(shuffle write)，会按照partition id以及key对记录进行排序，将所有partition的数据写在同一个文件中，该文件中的记录首先是按照partition id排序一个一个分区的顺序排列，每个partition内部是按照key进行排序存放，map task运行期间会顺序写每个partition的数据，并通过一个索引文件记录每个partition的大小和偏移量。这样一来，每个map task一次只开两个文件描述符，一个写数据，一个写索引，大大减轻了Hash Shuffle大量文件描述符的问题，即使一个executor有K个core，那么最多一次性开K*2个文件描述符。

在reduce阶段(shuffle read)，reduce task拉取数据做combine时不再是采用`HashMap`，而是采用`ExternalAppendOnlyMap`，该数据结构在做combine时，如果内存不足，会刷写磁盘，很大程度的保证了鲁棒性，避免大数据情况下的OOM。

总体上看来Sort Shuffle解决了Hash Shuffle的所有弊端，但是因为需要其shuffle过程需要对记录进行排序，所以在性能上有所损失。

### Unsafe Shuffle

从spark 1.5.0开始，spark开始了钨丝计划(Tungsten)，目的是优化内存和CPU的使用，进一步提升spark的性能。为此，引入Unsafe Shuffle，它的做法是将数据记录用二进制的方式存储，直接在序列化的二进制数据上sort而不是在java 对象上，这样一方面可以减少memory的使用和GC的开销，另一方面避免shuffle过程中频繁的序列化以及反序列化。在排序过程中，它提供cache-efficient sorter，使用一个8 bytes的指针，把排序转化成了一个指针数组的排序，极大的优化了排序性能。更多Tungsten详细介绍请移步[databricks博客](https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html)。

但是使用Unsafe Shuffle有几个限制，shuffle阶段不能有aggregate操作，分区数不能超过一定大小(224−1224−1，这是可编码的最大parition id)，所以像reduceByKey这类有aggregate操作的算子是不能使用Unsafe Shuffle，它会退化采用Sort Shuffle。

### Sort Shuffle v2

从spark-1.6.0开始，把Sort Shuffle和Unsafe Shuffle全部统一到Sort Shuffle中，如果检测到满足Unsafe Shuffle条件会自动采用Unsafe Shuffle，否则采用Sort Shuffle。从spark-2.0.0开始，spark把Hash Shuffle移除，可以说目前spark-2.0中只有一种Shuffle，即为Sort Shuffle。

## Spark Shuffle相关调优

从上述shuffle的原理介绍可以知道，shuffle是一个涉及到CPU(序列化反序列化)、网络IO(跨节点数据传输)以及磁盘IO(shuffle中间结果落地)的操作，用户在编写spark应用程序的时候应当尽可能考虑shuffle相关的优化，提升spark应用程序的性能。下面简单列举几点关于spark shuffle调优的参考。

- 尽量减少shuffle次数

```scala
// 两次shuffle
rdd.map(...).repartition(1000).reduceByKey(_ + _, 3000)

// 一次shuffle
rdd.map(...).repartition(3000).reduceByKey(_ + _)
```

- 必要时主动shuffle，通常用于改变并行度，提高后续分布式运行速度

```scala
rdd.repartiton(largerNumPartition).map(...)...
```

- 使用treeReduce & treeAggregate替换reduce & aggregate。数据量较大时，reduce & aggregate一次性聚合，shuffle量太大，而treeReduce & treeAggregate是分批聚合，更为保险。



### 什么时候需要 shuffle writer

假如我们有个 spark job 依赖关系如下

![img](https://oe9nbfytu.qnssl.com/c/75fd68334b09d27077b70388fa5a9e7b)

我们抽象出来其中的rdd和依赖关系，如果对这块不太清楚的可以参考我们之前的 [彻底搞懂spark stage 划分](http://mp.weixin.qq.com/s?__biz=MzI3MjY2MTYzMA==&mid=2247483846&idx=1&sn=ad5c6cb542d67e453972795743022298&scene=21#wechat_redirect)

```

```

对应的 划分后的RDD结构为：



![img](https://oe9nbfytu.qnssl.com/c/bfa9fb7e1c09bff43c86a729ca2b2622)



最终我们得到了整个执行过程：

![img](https://oe9nbfytu.qnssl.com/c/a6131c4afa406e5b06b930cdea92fcf4)

中间就涉及到shuffle 过程，前一个stage 的 ShuffleMapTask 进行 shuffle write， 把数据存储在 blockManager 上面， 并且把数据位置元信息上报到 driver 的 mapOutTrack 组件中， 下一个 stage 根据数据位置元信息， 进行 shuffle read， 拉取上个stage 的输出数据。

这边文章讲述的就是其中的 shuffle write 过程。

### spark shuffle 演进的历史

- Spark 0.8及以前 Hash Based Shuffle
- Spark 0.8.1 为Hash Based Shuffle引入File Consolidation机制
- Spark 0.9 引入ExternalAppendOnlyMap
- Spark 1.1 引入Sort Based Shuffle，但默认仍为Hash Based Shuffle
- Spark 1.2 默认的Shuffle方式改为Sort Based Shuffle
- Spark 1.4 引入Tungsten-Sort Based Shuffle
- Spark 1.6 Tungsten-sort并入Sort Based Shuffle
- Spark 2.0 Hash Based Shuffle退出历史舞台

总结一下， 就是最开始的时候使用的是 Hash Based Shuffle， 这时候每一个Mapper会根据Reducer的数量创建出相应的bucket，bucket的数量是M *R ，其中M是Map的个数，R是Reduce的个数。这样会产生大量的小文件，对文件系统压力很大，而且也不利于IO吞吐量。后面忍不了了就做了优化，把在统一core上运行的多个Mapper 输出的合并到同一个文件，这样文件数目就变成了 cores* R 个了，

举个例子：

本来是这样的，3个 map task， 3个 reducer， 会产生 9个小文件，

![img](https://oe9nbfytu.qnssl.com/c/2a9cddc8267eaf96e3d51822e75fe0b2)

是不是很恐怖， 后面改造之后

4个map task， 4个reducer， 如果不使用 Consolidation机制， 会产生 16个小文件。

但是但是现在这 4个 map task 分两批运行在 2个core上， 这样只会产生 8个小文件

![img](https://oe9nbfytu.qnssl.com/c/8a4d042d1c8a031a5091521c2c76d262)

在同一个 core 上先后运行的两个 map task的输出， 对应同一个文件的不同的 segment上， 称为一个 FileSegment， 形成一个 ShuffleBlockFile，

后面就引入了  Sort Based Shuffle， map端的任务会按照Partition id以及key对记录进行排序。同时将全部结果写到一个数据文件中，同时生成一个索引文件， 再后面就就引入了 Tungsten-Sort Based Shuffle， 这个是直接使用堆外内存和新的内存管理模型，节省了内存空间和大量的gc， 是为了提升性能。

现在2.1 分为三种writer， 分为 BypassMergeSortShuffleWriter， SortShuffleWriter 和 UnsafeShuffleWriter，顾名思义，大家应该可以对应上，我们本着过时不讲的原则， 本文中只描述这三种 writer 的实现细节， Hash Based Shuffle 已经退出历史舞台了，我就不讲了。

### 三种 writer 的分类

![img](https://oe9nbfytu.qnssl.com/c/8c6f0cae9d50f699cb3a39f4060ea2fe)

上面是使用哪种 writer 的判断依据， 是否开启 mapSideCombine 这个判断，是因为有些算子会在 map 端先进行一次 combine， 减少传输数据。 因为 BypassMergeSortShuffleWriter 会临时输出Reducer个（分区数目）小文件，所以分区数必须要小于一个阀值，默认是小于200。

UnsafeShuffleWriter需要Serializer支持relocation，Serializer支持relocation：原始数据首先被序列化处理，并且再也不需要反序列，在其对应的元数据被排序后，需要Serializer支持relocation，在指定位置读取对应数据。

### BypassMergeSortShuffleWriter 实现细节

BypassMergeSortShuffleWriter和Hash Shuffle中的HashShuffleWriter实现基本一致， 唯一的区别在于，map端的多个输出文件会被汇总为一个文件。 所有分区的数据会合并为同一个文件，会生成一个索引文件，是为了索引到每个分区的起始地址，可以随机 access 某个partition的所有数据。

![img](https://oe9nbfytu.qnssl.com/c/c695dc3a759a5133c38adbd989a03d8d)

但是需要注意的是，这种方式不宜有太多分区，因为过程中会并发打开所有分区对应的临时文件，会对文件系统造成很大的压力。

具体实现就是给每个分区分配一个临时文件，对每个 record的key 使用分区器（模式是hash，如果用户自定义就使用自定义的分区器）找到对应分区的输出文件句柄，直接写入文件，没有在内存中使用 buffer。 最后copyStream方法把所有的临时分区文件拷贝到最终的输出文件中，并且记录每个分区的文件起始写入位置，把这些位置数据写入索引文件中。

### SortShuffleWriter 实现细节

我们可以先考虑一个问题，假如我有 100亿条数据，但是我们的内存只有1M，但是我们磁盘很大， 我们现在要对这100亿条数据进行排序，是没法把所有的数据一次性的load进行内存进行排序的，这就涉及到一个外部排序的问题，我们的1M内存只能装进1亿条数据，每次都只能对这 1亿条数据进行排序，排好序后输出到磁盘，总共输出100个文件，最后怎么把这100个文件进行merge成一个全局有序的大文件。我们可以每个文件（有序的）都取一部分头部数据最为一个 buffer， 并且把这 100个 buffer放在一个堆里面，进行堆排序，比较方式就是对所有堆元素（buffer）的head元素进行比较大小， 然后不断的把每个堆顶的 buffer 的head 元素 pop 出来输出到最终文件中， 然后继续堆排序，继续输出。如果哪个buffer 空了，就去对应的文件中继续补充一部分数据。最终就得到一个全局有序的大文件。

如果你能想通我上面举的例子，就差不多搞清楚sortshufflewirter的实现原理了，因为解决的是同一个问题。

SortShuffleWriter 中的处理步骤就是

- 使用 PartitionedAppendOnlyMap 或者 PartitionedPairBuffer 在内存中进行排序，  排序的 K 是（partitionId， hash（key）） 这样一个元组。
- 如果超过内存 limit， 我 spill 到一个文件中，这个文件中元素也是有序的，首先是按照 partitionId的排序，如果 partitionId 相同， 再根据 hash（key）进行比较排序
- 如果需要输出全局有序的文件的时候，就需要对之前所有的输出文件 和 当前内存中的数据结构中的数据进行  merge sort， 进行全局排序

和我们开始提的那个问题基本类似，不同的地方在于，需要对 Key 相同的元素进行  aggregation， 就是使用定义的 func 进行聚合， 比如你的算子是 reduceByKey（*+*）, 这个func 就是加法运算， 如果两个key 相同， 就会先找到所有相同的key 进行 reduce(*+*) 操作，算出一个总结果 Result，然后输出数据（K，Result）元素。

SortShuffleWriter 中使用 ExternalSorter 来对内存中的数据进行排序，ExternalSorter内部维护了两个集合PartitionedAppendOnlyMap、PartitionedPairBuffer， 两者都是使用了 hash table 数据结构， 如果需要进行 aggregation， 就使用 PartitionedAppendOnlyMap（支持 lookup 某个Key，如果之前存储过相同key的K-V 元素，就需要进行 aggregation，然后再存入aggregation后的 K-V）， 否则使用 PartitionedPairBuffer（只进行添K-V 元素），

![img](https://oe9nbfytu.qnssl.com/c/e8987f6d52e24022dc500e46f7d4dc60)

我们可以看上图， PartitionedAppendOnlyMap 中的 K 是（PatitionId, K）的元组， 这样就是先按照partitionId进行排序，如果 partitionId 相同，再按照  hash（key）再进行排序。

![img](https://oe9nbfytu.qnssl.com/c/6d013affe760f663204e37a272c49718)

首先看下  AppendOnlyMap， 这个很简单就是个 hash table，其中的 K 是（PatitionId, hash（Key））的元组， 当要 put(K, V) 时，先 hash(K) 找存放位置，如果存放位置已经被占用，就使用 Quadratic probing 探测方法来找下一个空闲位置。对于图中的 K6 来说，第三次查找找到 K4 后面的空闲位置，放进去即可。get(K6) 的时候类似，找三次找到 K6，取出紧挨着的 V6，与先来的 value 做 func，结果重新放到 V6 的位置。

下面看下  ExternalAppendOnlyMap 结构， 这个就是内存中装不下所有元素，就涉及到外部排序，

![img](https://oe9nbfytu.qnssl.com/c/2e961b3cf14084b8e9228690ed431c18)

上图中可以看到整个原理图，逻辑也很简单， 内存不够的时候，先spill了四次，输出到文件中的元素都是有序的，读取的时候都是按序读取，最后跟内存剩余的数据进行 全局merge。

merge 过程就是 每个文件读取部分数据（StreamBuffer）放到 mergeHeap 里面， 当前内存中的 PartitionedAppendOnlyMap 也进行 sort，形成一个 sortedMap 放在 mergeHeap 里面，  这个 heap 是一个 优先队列 PriorityQueue， 并且自定义了排序方式，就是取出堆元素StreamBuffer的head元素进行比较大小，

```

```

这样的话，每次从堆顶的 StreamBuffer 中 pop 出的 head 元素就是全局最小的元素（记住是按照（partitionId，hash（Key））排序的）， 如果需要 aggregation， 就把这些key 相同的元素放在一个一个 mergeBuffers 中，  第一个被放入 mergeBuffers 的 StreamBuffer 被称为 minBuffer，那么 minKey 就是 minBuffer 中第一个 record 的 key。当 merge-combine 的时候，与 minKey 有相同的Key的records 被 aggregate 一起，然后输出。

如果不需要 aggregation， 那就简单了， 直接把 堆顶的 StreamBuffer 中 pop 出的 head 元素 返回就好了。

![img](https://oe9nbfytu.qnssl.com/c/6e5ff58672bcac22e8abbcbb26946ac5)

最终读取的时候，从整个 全局 merge 后的读取迭代器中读取的数据，就是按照 partitionId 从小到大排序的数据， 读取过程中使用再按照 分区分段， 并且记录每个分区的文件起始写入位置，把这些位置数据写入索引文件中。

## Shuffle相关参数配置

 Shuffle是Spark Core比较复杂的模块，它也是非常影响性能的操作之一。因此，在这里整理了会影响Shuffle性能的各项配置。

### 1．spark.shuffle.manager

Spark 1.2.0官方版本支持两种方式的Shuffle，即`Hash Based Shuffle`和`Sort Based Shuffle`。其中在Spark 1.0之前仅支持`Hash Based Shuffle`。Spark 1.1引入了`Sort Based Shuffle`。Spark 1.2的默认Shuffle机制从Hash变成了Sort。如果需要`Hash Based Shuffle`，只需将`spark.shuffle.manager`设置成“hash”即可。

* 配置方式

​    ① 进入spark安装目录的conf目录

​    ② `cp spark-defaults.conf.template spark-defaults.conf`

​    ③ `spark.shuffle.manager=hash`

* 应用场景

  当产生的临时文件不是很多时，性能可能会比sort shuffle要好。
  如果对性能有比较苛刻的要求，那么就要理解这两种不同的Shuffle机制的原理，结合具体的应用场景进行选择。
  对于不需要进行排序且Shuffle产生的文件数量不是特别多时，Hash Based Shuffle可能是更好的选择；因为Sort Based Shuffle会按照Reducer的Partition进行排序。
  而Sort Based Shuffle的优势就在于可扩展性，它的出现实际上很大程度上是解决Hash Based Shuffle的可扩展性的问题。由于Sort Based Shuffle还在不断地演进中，因此它的性能会得到不断改善。

对于选择哪种Shuffle，如果性能要求苛刻，最好还是通过实际测试后再做决定。不过选择默认的Sort，可以满足大部分的场景需要。

### 2．spark.shuffle.spill

这个参数的默认值是true，用于指定Shuffle过程中如果内存中的数据超过阈值（参考spark.shuffle.memoryFraction的设置）时是否需要将部分数据临时写入外部存储。如果设置为false，那么这个过程就会一直使用内存，会有内存溢出的风险。因此只有在确定内存足够使用时，才可以将这个选项设置为false。

### 3．spark.shuffle.memoryFraction

在启用`spark.shuffle.spill`的情况下，`spark.shuffle.memoryFraction`决定了当Shuffle过程中使用的<u>内存达到总内存多少比例</u>的时候开始spill。在Spark 1.2.0里，这个值是0.2。通过这个参数可以设置Shuffle过程占用内存的大小，它直接影响了写入到外部存储的频率和垃圾回收的频率。可以适当调大此值，可以减少磁盘I/O次数。

### 4．spark.shuffle.sort.bypassMergeThreshold

这个配置的默认值是200，用于设置在Reducer的Partition数目少于多少的时候，`Sort Based Shuffle`内部不使用**归并排序**的方式处理数据，而是直接将每个Partition写入单独的文件。这个方式和`Hash Based`的方式类似，区别就是在最后这些文件还是会合并成一个单独的文件，并通过一个Index索引文件来标记不同Partition的位置信息。

这个可以看作`Sort Based Shuffle`在Shuffle量比较小的时候对于`Hash Based Shuffle`的一种折中。当然了它和`Hash Based Shuffle`一样，也存在同时打开文件过多导致内存占用增加的问题。因此如果GC比较严重或者内存比较紧张，可以适当降低这个值。

### 5．spark.shuffle.blockTransferService

在Spark 1.2.0中这个配置的默认值是netty，而在之前的版本中是nio。它主要是用于在各个Executor之间传输Shuffle数据。netty的实现更加简洁，但实际上用户不用太关心这个选项。除非有特殊需求，否则采用默认配置即可。

### 6．spark.shuffle.consolidateFiles

这个配置的默认值是false。主要是为了解决在`Hash Based Shuffle`过程中产生过多文件的问题。如果配置选项为true，那么对于同一个Core上运行的Shuffle Map Task不会产生一个新的Shuffle文件而是重用原来的。

但是consolidateFiles的机制在Spark 0.8.1就引入了，到Spark 1.2.0还是没有稳定下来。从源码实现的角度看，实现源码是非常简单的，但是由于涉及本地文件系统等限制，这个策略可能会带来各种各样的问题。一般不建议开启。

### 7．spark.shuffle.compress和spark.shuffle.spill.compress

这两个参数的默认配置都是true。`spark.shuffle.compress`和`spark.shuffle.spill.compress`都是用来设置Shuffle过程中是否对Shuffle数据进行压缩。其中，前者针对最终写入本地文件系统的输出文件；后者针对在处理过程需要写入到外部存储的中间数据，即针对最终的shuffle输出文件。

* 设置`spark.shuffle.compress`

  需要评估压缩解压时间带来的时间消耗和因为数据压缩带来的时间节省。如果网络成为瓶颈，比如集群普遍使用的是千兆网络，那么将这个选项设置为true可能更合理；如果计算是CPU密集型的，那么将这个选项设置为false可能更好。

* 设置`spark.shuffle.spill.compress`

  如果设置为true，代表处理的中间结果在spill到本地硬盘时都会进行压缩，在将中间结果取回进行merge的时候，要进行解压。因此要综合考虑CPU由于引入压缩、解压的消耗时间和Disk IO因为压缩带来的节省时间的比较。在Disk IO成为瓶颈的场景下，设置为true可能比较合适；如果本地硬盘是SSD，那么设置为false可能比较合适。

### 8．spark.reducer.maxMbInFlight

这个参数用于限制一个Reducer Task向其他的Executor请求Shuffle数据时所占用的最大内存数，尤其是如果网卡是千兆和千兆以下的网卡时。默认值是 设置这个值需要综合考虑网卡带宽和内存。