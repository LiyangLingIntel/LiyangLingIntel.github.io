---
layout:     post
title:      "Alibaba Cloud Training (Day2)"
subtitle:    Notes on Alibaba Cloud Training
date:       2019-01-25
author:     Lyon Ling
header-img: img/post-bg-cloudcomputing.jpg
catalog: true
tags:
    - Alibaba
    - Notes
    - CloudComputing
    - DataScience
---



## Course Content

##### Alibaba Cloud Big Data Architechture

Here I drew the structure of whole AliCloud Big Data Platform.<img src="https://ws4.sinaimg.cn/large/006tNc79gy1fzmdk5anunj30u00xdaeh.jpg" />

##### MaxCompute provides two data import and export methods:

1. Tunnel Operation on the console
2. Tunnel written with Java  

##### MaxCompute pricing

* MaxCompute takes Project as a charge unit.

* Three factors affect the price
  * Usage of storage
  * Computing resource
  * Data download

## Certification Test Contents

### MaxCompute:

* **Use case of MaxCompute**

  Data Warehouse, Social networking analysis, User Profile

  This is **NOT**: Order management, transacrtion prcessing, Fast real-time response, ad-hoc queries by end user, high concurrnet user requests

* **MaxCompute Command & SQL:**

  ```
  > show tables	// if you want to view all tables in a project, you can execute;
  > desc table_a 	// show table schema and the size of space taken by the table;
  > datediff  	// calculate the difference between 2 time stamps
  ```

  **SQL** is based on **CBO**(Cost Based Optimizer)

* **MaxCompute Pricing:**

  MaxCompute: Data Download / Computing / Storage. (no data upload)

* **MaxCompute Client:**

  Tunnel: administration

  IntelliJ Idea: Development (Lower thresholds)

  Dataworks: configure workflow and scheduling (lower thresholds)

  *Lower thresholds means: the upper operating file size is limited*

* **MaxCompute Security steps: (`set ProjectProtestion=true;` has the first priority)**

  **accessKey pair: Access Key ID / Access Key Secret**

  	use prj1;
		
  	    add user [aliyun$alice@aliyun.com](mailto:aliyun$alice@aliyun.com);
		
  	    grant List, CreateTable, CreateInstance on project prj1 to user aliyun$alice@aliyun;

  ACL Objects: Project, Resource, Procedure

* **MaxCompute Resource:**

  Files, Tables, Jar package, Archive

### DataWorks:

* DataWorks can be used to create all types of tasks and configure scheduling cycles as needed. The supported granularity levels of scheduling cycles include days, weeks, months, hours, 5 minutes

* In DataWorks workflow, Inner nodes of a flow task can **NOT** be depended on by other flow or node tasks.

* Phase of the scheduling process: Not running, Running, Run successfully

* Work node Type: Data Synchronization, SHELL, MaxCompute SQL,  MaxCompute MR

**Operation & Maintainance:**

	Alert policies: Email, Text message

As an administrator of a project in MaxCompute. The project involves a large volume of sensitive data such as user IDs and shopping records, to be specific, project users can only access the data within the project, all data flows only within the project. 

What operation should you do? 
Enable the data protection mechanism in the project.

### DataV:

* **Data Portal:** 

  When a DataV screen is ready, it can embed works to the existing portal of the enterprise through URL after the release.

* **DataV data source:** 

  Alibaba Cloud' s AnalyticDB, ApsaraDB Static data in CSV and JSON formats Oracle Database / DataV can NOT make full use of MaxCompute for data process.

* **DataV Visual screens types:** 

  Presentation type / Analysis type / Monitoring type

### QuickBI:

* **Data Source:** 

  MaxCompute / Local Excel files / MySQL RDS

* **Data Storage:** 

  Data Will be stored in Exploration space built in Quick BI

* **Security:**

  Different users can view different data in a same report in Quick BI by Set a row-level permission Only Quick BI Pro provides the row-level permission function 

### E-MapReduce:

* **DTS:** 

  Migrate their data with virtually no downtime

* **Streaming:** 

  Flume + Kafka + Spark Streaming(storm, f(B)link ) + HDFS(Redis, HBase…)

It supports the Pay-As-You-Go payment method, which means that the cost of each task is measured according to ECS.

### **Pay attention the traps:**

*Wrong desctiptions*

- DataWorks provides a convenient way to analyze and process big data for the user. The user is able to analyze big data without concerning details of distributed computing.

* Deployment personnel or Operation & Management (O&M) personnel can generate release packages based on the latest development results

* MaxCompute SQL is 100% equivalent to Hive SQL

* MaxCompute SQL can complete the query in minutes even seconds, and it can be able to return the result in millisecond

* Tunnel command Parameters **Purge**: Clears the table directory. By default, use this command to clear information of the last three days.

* MaxCompute can identify the RAM account system, it can also identify the RAM permission system. 

* MaxCompute partition only supports string type and the conversion of any other types is not allowed

* The table name and column name are both case sensitive.

* DataWorks provides a convenient way to analyze and process big data for the user. The user is able to analyze big data without concerning details of distributed computing.

* Deployment personnel or Operation & Management (O&M) personnel can generate release packages based on the latest development results

* In DataWorks: Inner nodes of a flow task can be depended on by other flow or node tasks.

* If are Using DataWorks to Build an enterprise-level data warehouse, and process the history data of the enterprise.

