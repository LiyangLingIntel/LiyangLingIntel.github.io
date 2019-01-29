---
layout:     post
title:      "Alibaba Cloud Training (Day1)"
subtitle:    Notes on Alibaba Cloud Training
date:       2019-01-24
author:     Lyon Ling
header-img: img/post-bg-cloudcomputing2.jpg
catalog: true
tags:
    - Alibaba
    - Notes
    - Cloud Computing
    - Data Science
---

>Firstly, I am very honor to take part in the Alibaba Cloud Big Data Certification Course. Although it's only two days long, a lot of new concepts are given to me, like an iceberg waiting me to discover.
>
>In this course, basic idea of cloud computing and the way how Alibaba dealing with it are taught to us. I'm going to write down all I learnt here.
>
>Thanks Alibaba, BDT, lecturer Derek and our program director Prof Chen for providing this opportunity to us.

### Recommended Resources

If we want to get to know something about big data and cloud computing, there are three papers from Google are high recommended.

* ***The Google File System*** -- Google, 2003
* ***MapReduce: Simplified Data Processing on Large Clusters*** -- Goole, 2004
* ***Bigtable: A Distributed Storage System for Structured Data*** -- Google, 2006

### Notes on First Day's Course

At first, this is the brief architectures of `Hadoop` and `Alibaba Cloud`. Please forgive my ugly hand writing. ;)

<img src="https://ws3.sinaimg.cn/large/006tNc79gy1fzh2myj3psj31p10u0akl.jpg" />

*In this picture, there are many new words for me, I will collect them and give the brief explaination later.*  

### Common Concepts

#### Hadoop Ecosystem

1. **PROCESS, ANALYZE, SERVE**
   * **BATCH**:  Spark, Hive, Pig, MapReduce
   * **STREAM**: Spark
   * **SQL**: Impala
   * **SEARCH**: ElasticSearch
2. **UNIFIED SERVICES**
   * **RESOURCE MANAGEMENT**: YARN
   * **SECURIYTY**: Sentry, Kerberos, RecordService
3. **STORE**
   * **FILESYSTEM**: HDFS
   * **STRUCTURE**: Kudu
   * **NoSQL**: HBase
   * **OTHER**:Object Store
4. **INTEGRATE**
   * **BATCH**: Sqoop
   * **REAL-TIME**: Kafka, Flume

#### Data Processing - Single Instance to Scale up

|               | File System<br />(Linux Ext2/3/4) | RDBMS<br />(Oracle) | Distributed<br />File System | Distributed <br />Database<br />(Hive) |
| :-----------: | :-------------------------------: | :-----------------: | :--------------------------: | :------------------------------------: |
| **Data Unit** |         Block<br />1k/4k          |  Block<br />1k/16k  |     Block<br />64M/128M      |                  N/A                   |
| **Meta Data** |            INode Table            |     System.dbf      |           namenode           |                 MySQL                  |
|   **Data**    |            Data Block             |      User.dbf       |           datanode           |                  HDFS                  |

> Among my handwriting note, teacher gave us the comparison of Hadoop and Alibaba Cloud architecture. Below I will also separate those word by this two system.

#### Hadoop

[Apache Hadoop](https://hadoop.apache.org/) software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. It is designed to scale up from single servers to thousands of machines, each offering local computation and storage. Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.

* **HDFS**

  Hadoop Distributed File System, a distributed file system designed to run on commodity hardware. 

* **YARN**

  **Resource management** and **job scheduling** technology, responsible for allocating system resources to the various applications running in a Hadoop cluster and scheduling tasks to be executed on different cluster nodes.

  **YARN Process:**

  <img src="https://ws1.sinaimg.cn/large/006tNc79gy1fznbrj2icqj31em0n20xd.jpg" />

* **Zookeeper**

  Centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services. 

* **Hive**

  **Data warehouse** software project built on top of **Apache Hadoop** for providing data query and analysis. It has two version implemented based on `MapReduce` and `Spark`.

* **Flume**

  Distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of log data. It has a simple and flexible architecture based on streaming data flows.

  <img src="https://ws2.sinaimg.cn/large/006tNc79gy1fzi76h6436j30co05bwes.jpg" />

* **Sqoop**

  Its name comes from SQL + Hadoop. 

  A **tool** designed for efficiently transferring **bulk data** between Apache Hadoop and structured datastores such as relational databases.

* **Kafka**

  Distributed streaming platform. 

  Kafka is generally used for two broad classes of applications:

  - Building real-time streaming data pipelines that reliably get data between systems or applications
  - Building real-time streaming applications that transform or react to the streams of data

  Often play as datahub to store data queue before data going through file system to storage.

* **Spark** could support both batch processing and streaming processing.

* **Flink**

  A framework and distributed processing engine for stateful computations over *unbounded and bounded* data streams. Flink has been designed to run in *all common cluster environments*, perform computations at *in-memory speed* and at *any scale*.

  <img src="https://ws4.sinaimg.cn/large/006tNc79gy1fzi7uqe9dvj32h80tadpm.jpg" />

* **HUE**

  The short name of **Hadoop User Experience**, an open-source Web interface that supports Apache Hadoop and its ecosystem. It acts as **Analytics Workbench** for browsing, querying and visualizing data.

#### Alibaba Cloud

Aliyun wants to make things easy. User put data into cloud service and get wanted outputs like reports or other business issues.

* **Pangu(盘古)** : Distributed file system

* **Fuxi(伏羲)**: For scheduling

* **Nvwa(女娲)**: For name space

* **Shennong(神农)**: For monitoring

* **Huatuo(华佗)**: For trouble shooting

* **Zhongkui(钟馗)**: For security

* **Apsara(飞天):** Alibaba cloud database system

* **MaxCompute**: Big data processing platform by Ali, acts as Data Warehouse like `Hive`. Mainly used for batch structural data storage and processing, which can provide massive data warehouse solution and big data modeling service.
  * **Project** is the basic **unit of operation** in MaxCompute, which set the boundary for MaxCompute multi-users isolation and access control.
  * **Table** is the **data storage unit** in MaxCompute and all data in MaxCompute is stored in tables. Table is a two -dimentianal data structure composed of rows and columns.
  * **Partition** is alternative to be specified when creating table in order to improve the processing efficiency.
  * **Resouce** is a particular concept of MaxCompute. It's needed if you want to use UDF(user-defined function) or MapReduce.
  * **LifeCycle** is counted from the last time the table(patition) data was updated. If that remains unchanged exceeding specified time, MaxCompute automatically recycles it.

* **Dataworks**: online IDE

* **OSS**: Alibaba's product to store unstructured data

* **PAI**: Machine learning platform on Alibaba cloud for AI. 

  * Rich algorithm components

  * All-in-one services

    All processing steps include **Data Preprocessing**, **Feature Engineering**, **Machine Learning Model Training**, **Model Evaluation**, **Learning | Offline/Online Services**

    <img src="https://ws2.sinaimg.cn/large/006tNc79gy1fzncf0zlltj30su0igtaa.jpg" width="600"/>

* **DTS / Data X**: (Offline Data Sync Tool) For migrate data from database to Alibaba cloud

* **Data V:** Visualization tools for real-time data

* **Quick BI:** Visualization toolkit for offline data

* **Blink**

  Blink is the kind of framework implemented by Alibaba based on Flink.

  In today's course, an interesting part is, the lecturer stressed on several times that Alibaba has bought `Apache Flink` and developed `Blink`, so now we can regard **Blink** and Flink as the same thing.

  For this story, I just want to show this picture.

<img src="https://ws4.sinaimg.cn/large/006tNc79gy1fzh02ipxerj30u00v746u.jpg" width="600" />

