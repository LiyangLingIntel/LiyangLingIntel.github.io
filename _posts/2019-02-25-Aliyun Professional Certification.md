---
layout:     post
title:      "Alibaba ACP Big Data Certification"
subtitle:    Details on ACP Exam
date:       2019-02-25
author:     Lyon Ling
header-img: img/post-bg-cloudcomputing.jpg
catalog: true
tags:
    - BigData
    - Notes
    - Alibaba
---

## Data+|Big Data Basic Services

### MaxCompute

- MaxCompute saves data to the system, the **Column-Store** file format is used to reduce invalid disk read operations (similar to **Spark**)
- **Project** is the **basic organizational unit** of MaxCompute. It is similar to the concept of database or Scheme in traditional database, and is the main boundary of multi-user isolation and access control
- The **table** is the **data storage unit** of MaxCompute.

#### MAC (Mandatory Access Control)

* `Column` is the granularity of sensitive data can be controlled by **Label Security** 

#### UDF & UDT & UDJ

* **UDF**

  * **user defined scalar function**

    Default UDF, if not specified, udf is udsf

    input and output is one-to-one relation

  * **user defined table valued function**

    input and output is multi-to-multi

  * **user defined aggregation function**

    input and output is multi-to-one

* **UDT**

  User defined type

* **UDJ**

  User defined join

### E-MapReduce

#### Job

* To create a job --> to create a configuration on how to run a job
* Configuration includes:
  * Jar packages to be run
  * Input & output data addresses
  * Running parameters
* Job cannot be run dirrectly.

#### Execution plan 

* A bond that associates the job and the cluster.
* multiple jobs can be combined into a job sequence and prepare a running cluster for the job (or automatically create a temporary cluster or associate an existing cluster) 

### DataWorks

DataWorks, Platform as a Service from Aliyun, based on `MaxCompute` supporting `Data Integration`, `Data Development`, `Data Management`, `Data Quality Campaign`, `Data Sharing` services.

#### Scheduling

* Monthly scheduling: It supports multi days scheduling, while end of month is not supported, like `31th`, `30th`.

* Minutes scheduling: The minimum scheduling cycle is 5 minutes, and it only supports 5*n minutes as scheduling interval.
* It also supports weekly, daily, hourly scheduling.
* Scheduling workflow is a `DAG`, current workflow will wait until its parent workflow finishes, even if  sheduling time comes.

## Data+|Big Data Analysis & Visualization

### Data V

#### Widget

* We want to add to the canvas from the widget menu on toolbar, many kinds of widget are currently supported by DataV, also third-party widgets, like `E-Charts`.

### Quick BI

#### Workspace

Workspace is the place where team menber can collaborate. If number of team menber is over 10, Quick BI **pro version**, else the **basic version**.

* In basic version, workspace is named `personal workspace`, while `workspace` in pro version.

* Difference between `personal workspace` and `workspace`:

  * A personal workspace is created automatically after the first logon. A workspace is created manually by an administrator of the organization.

  - A personal workspace is unique to each Alibaba Cloud account or RAM user. It cannot be deleted.
  - You are not allowed to add other members to a personal workspace. Therefore, a personal workspace does not support collaboration or sharing.
  - For a personal workspace, data objects can be transferred to or shared with any Alibaba Cloud accounts. For a workspace, data objects can only be transferred to or shared with the members of the workspace.

#### Permission Management

* Permission management include data **object management** and **row-level permission management**.
  * Data objects include `data sources`, `datasets`, `workbooks`, `dashboards`, and `portals`.
* Data object management is classified based on the workspace types, which include the personal workspace and the workspace.
* Data object sharing
  * Only the **owner of the data object** and the **administrators of the workspaces** have permissions to share the data object.
