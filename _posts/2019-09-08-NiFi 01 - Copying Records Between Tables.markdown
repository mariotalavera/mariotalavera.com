---
layout: post
title:  "NiFi 01 - Copying Records Between Tables"
date:   2019-09-08 00:01:00 -0500
categories: nifi
published: true
---

## Summary 
This writeup explains how to copy the contents of one database table to another database  table using Apache NiFi.  Database vendor is irrelevant; only the JDBC driver is needed.  In this writeup, the data is moved from one MySQL table to another MySQL table in the same schema.  Only records that do not exist in the destination table are copied.

## Disclaimer
There must be one hundred ways of doing this.  This is a note to self so that I record how I do this and hoping it helps someone else. 

## Prerequisites
1. [MySQL Server](https://dev.mysql.com/downloads/mysql/5.5.html?os=src&version=5.1).
1. [MySQL JDBC Driver](https://dev.mysql.com/downloads/connector/j/).
1. [Apache NiFi](https://nifi.apache.org/).
1. The sample data used comes from [MySQL's Employees Sample Database](https://dev.mysql.com/doc/employee/en/).  Its freely available on their github page.  The file I used is called **load_employees.dump**.

## Configure NiFi to use MySQL JDBC Driver
Download the MySQL JDBC driver and place in NiFi's lib directory.  
For my setup, I placed JAR in **/nifi-1.9.2/lib**. 

## Setup the database schema

``` sql
-- Create the database schema.
DROP SCHEMA IF EXISTS nifi;
CREATE SCHEMA nifi;

-- Select newly created schema to put our tables in
USE nifi;

-- Create the source table.
CREATE TABLE employees_source (
	emp_no		int(11)		NOT NULL,
	birth_date	date		NOT NULL,
	first_name	varchar(14) 	NOT NULL,
	last_name	varchar(16) 	NOT NULL,
	gender		enum('M','F') 	NOT NULL,
	hire_date	date		NOT NULL,
	PRIMARY KEY (emp_no)
);

-- create the destination table.
CREATE TABLE employees_destination (
	emp_no		int(11)		NOT NULL,
	birth_date	date 		NOT NULL,
	first_name	varchar(14) 	NOT NULL,
	last_name	varchar(16) 	NOT NULL,
	gender		enum('M','F') 	NOT NULL,
	hire_date	date		NOT NULL,
	PRIMARY KEY (emp_no)
);
```

## NiFi Configuration

#### 1. QueryDatabaseTable Processor Configuration {#QueryDatabaseTable}
From the top menu in NiFi, click the processor icon ![processor icon]({{ "/assets/nifi/processor-icon.png" | absolute_url }})  and drag it to the main canvas.  
On the **Add Processor Screen**, search and add a **QueryDatabaseTable** processor.

<img src="/assets/nifi/add-processor-1.png" alt="Add Processor" width="80%">

On the processor window, rigth-click/configure then select **Properties** to configure this processor.

<img src="/assets/nifi/query-table-properties-1.png" alt="QueryDatabaseTable Processor" width="80%">

|Property| |
|--|--|
|**Database Connection Pooling Service** | Choose **Create new service** and press create.  Now back on the **Configure Processor** Window, click the arrow next to your newly created pooling service to configure [<sup>1</sup>](#pooling_service). | 
|**Database Type** | Select MySQL. | 
|**Table Name** | This is the source table where data will be copied from. | 
|**Columns to Return** | Column list for the source table. | 
|**Maximun-value Columns** | This column will be used to keep track of the maximun values as they are copied over.  Multiple columns can be defined.  Using the numeric primary key sufices for this writeup. | 


#### 2. SplitAvro Processor Configuration {#split_avro}
Go back to the top menu and drap another processor right under our **QueryDatabaseTable** processor just configured.  Search for **SplitAvro** to add this processor.  All the settings for this processor can be left to their default values for this writeup.


#### 3. PutDatabaseRecord Processor Configuration {#put_database_record}
One last time, add a processor to the canvas.  This time, search and add a **PutDatabaseRecord** processor to the canvas.  Just like with our **QueryDatabaseTable** processor, go to porperties to configure.

<img src="/assets/nifi/put-record-properties-1.png" alt="PutDatabaseRecord Processor" width="80%">


|Property| |
|--|--|
|**Record Reader** | Choose create new service from dropdown and press create.  Now back on the **Configure Processor** Window, click the arrow next to your newly created avro reader service to configure [<sup>2</sup>](#avro_reader_service). | 
|**Statement Type** | INSERT | 
|**Database Connection Pooling Service** | Since we are moving data from tables in the same schema, we select the pooling service previously created.   | 
|**Schema Name** | The name of your database schema. | 
|**Table Name** | The name of our destination table, employees_destination. | 


#### 4. Connecting the Processors
At this point, the only thing left is to connect our processors in order of execution. 
1. Mouse over our **QueryDatabaseTable** processor, then click and drag arrow to our **SplitAvro** processor.
1. Mouse over our **SplitAvro** processor, then click and drag arrow to our **PutDatabaseRecord** processor.  
  For this connection, make sure to select all relationships on the details tab as shown.

<img src="/assets/nifi/avro-to-putrecord-connection.png" alt="avro to put record connection" width="60%">

This is the last step in the NiFi configuration.  

## Nifi Data Flow
By now, your NiFi data flow should look like this:

<img src="/assets/nifi/nifi-canvas-complete.png" alt="nifi data flow" width="60%">

## Running NiFi Example
1. Right-clik and select **Play** on each of our three processors.
1. Check the recordcount of each of our tables.  It is zero for both.
1. Edit script **load_employees.dump** replacing all instances of **employees** with **employees_source** to match our source table name.
1. From the command prompt, load the employee records
``` bash
mysql -h172.17.0.3 -uroot -ppassword nifi < load_employees.dump
```
1. Check the recordcount of each of our tables.  It should be zero for employees destination and 300,024 for employees source.
1. Back on Nifi, note how the GUI represents the data flow from beginning to end.  It may take a bit to refresh.
1. Check the recordcount of each of our tables.  It is 300,024 for both.
1. You can press stop in each processor.

## Other things you can do in this workflow
1. **Resetting query state**  Right-click on the Query Table Processor and select state.  Note how Nifi saves the highest value of the primary key.  This is how NiFi knows what records to bring (or not).  Note that if your primary key is not numeric you would have to make different arragements to keep state in your data flow.  You could similarly use an int that is not your primary key.  Lastly, note that you can also clear the state if you wanted to move all the data again.
1. Between processors, you have configurations.  For each of these, you can right-click and **empty the queue** of records in-motion.

There are many other features for each processor and there is a mutitude of processors for all sorts of tasks.  Think of these a bit like ANT Tasks to get an idea of the toolset at hand.

## Resetting Exercise Fast
1. Clear NiFi queues and states.
1. Shut down NiFi.
1. Rerun SQL snippet from beginning of post to drop and recreate schema with tables.
1. Start NiFi

## Conclusion
This is the quickest Get-Up-And-Going for NiFi I could manage.
It provides a succinct guide to creating a simple workflow for moving records from one table to another.

Without much trouble, a second data source could be added to the configuration and we could as easily move data between databases.

NiFi workflows can be scheduled as well.  Double-click a processor and note the Scheduling Tab.

NiFi provides a very efficient way of vendor independent data flows.
I hope development continues and popularity increases.
Thanks for reading.



------

#### DbConnectionPool Controller Configuration {#pooling_service}

From the NiFi Flow Configuration popup, select the gear icon for the pool service and revise the settings to match the following screenshot.

<img src="/assets/nifi/dbcpservice-properties-1.png" alt="dbcpservice properties" width="80%">

|Property| |
|--|--|
|**Database Connection URL** | DBC connection string to your schema. | 
|**Database Driver Class Name** | db driver name. | 
|**Database User** | Database username. | 
|**Database Password** | Database user's password. | 
|**Valudation Query** | select/check sql statement. | 

[Back](#QueryDatabaseTable)


<a id="avro_reader_service"></a>
#### Avro Reader Service Configuration

From the NiFi Flow Configuration popup, select the gear icon for the avro reader service.  Ensure the properties tab looks like this.

<img src="/assets/nifi/split-avro-properties.png" alt="split avro controller properties" width="60%">

[Back](#put_database_record)



