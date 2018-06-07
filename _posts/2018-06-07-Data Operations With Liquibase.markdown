---
layout: post
title:  "Data Operations With Liquibase"
date:   2018-06-07 00:01:00 -0500
categories: ops 
published: true
---

## Description
This is a 'helper' project to assist in common but annoying database ops.  It uses [Liquibase](http://www.liquibase.org/index.html) and [Apache Ant](https://ant.apache.org).  Too often, we have to move schemas between db vendors or between servers with limited access or rights.  Using Liquibase, it is very simple to do 90% of these operations with very little pain.  Here I am sharing a simple project with the 90% set up already.  You can check it out at [**Github**](https://github.com/mariotalavera/liquibase_helper). 

Enjoy.

## Syntax

### Main tasks

<img src="/assets/liquibase/overview.png" alt="Main Tasks" width="50%" class="center">
1. **ant** (Move Database) - Moves the database from the *source.properties* db and puts it in the *destination.properties* db.
1. **ant getdb** (Get Database) - Extracts both schema and data from source database.
1. **ant putdb** (Put Database) - Creates and populated destination database.
1. **ant docdb** (Document Schema) - Reads the Liquibase Schema file stored in the "out" directory and creates Javadoc-style documentation in the *doc* directory.
1. **ant diffdb** (Diff) - Diffs source schema and reference schema.  Both of these are defined in the *source.properties* file.

### Other tasks
1. **ant getschema** (Extract Schema) - Extracts the schema defined in the *source.properties* file and stores it in the *out* directory.
1. **ant getdata** (Extract Data) - Extracts the schema defined in the *source.properties* file and stores it in the *out* directory.
1. **ant putschema** (Create Schema) - Reads the Liquibase Schema file stored in the "out" directory and creates this schema in the *destination.properties* schema.
1. **ant putdata** (Insert Data) - Reads the data file in the *out* directory and inserts it into the schema defined in the *destination.properties* file.

## Ant Tasks

|Target|Description|
|:--|:--|
|**cleandata**|Deletes the generated data file from the source database, if it exists.|
|**cleanschema**|Deletes the generated schema file from the source database, if it exists.|
|**diffdb**|Performs a diff between the source and the reference database.|
|**docdb**|Generates documentation for the source database.|
|**dropdb**|Drops all the database tables from the destination database.|
|**getdata**|Extract the data to Liquibase format from the source database.|
|**getdb**|Extracts both the database schema and data to Liquibase format from the source database.|
|**getschema**|Extract the schema to Liquibase format from the source database.|
|**main**|Extracts source database schema and data to Liquibase format and creates schema on destination database and inserts data.|
|**putdata**|Inserts the extracted data on the destination database from Liquibase extract.|
|**putdb**|Creates the database schema on the destination database and inserts the data from Liquibase extracts.|
|**putschema**|Creates the database schema on the destination database from Liquibase extract.|

## Project Directory Structure

``` properties
liquibase_helper/
|-- build.properties
|-- build.xml
|-- destination.properties
|-- source.properties
|-- readme.md (this file)
|-- lib/
	|-- ojdbc7.jar
	|-- mysql-connector-java-5.1.42-bin.jar
	|-- mssql-jdbc-6.2.2.jre8.jar
|-- out/
|-- doc/
```

### Issues
1. Unicode between db vendors.
2. data type incompatibilities between db vendors.
3. Binary data types (in Oracle at least).
4. **doc** column links do not resolve at data type level.  Incorrectly scoped?!

### Reference
[Liquibase Reference](https://www.liquibase.org/documentation/command_line.html)
