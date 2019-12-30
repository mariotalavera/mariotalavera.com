---
layout: post
title:  "NiFi 00 - Environment Configuration"
date:   2019-09-01 00:01:00 -0500
categories: nifi
published: false
---

## Summary 

## Starting MySQL Locally
1. docker start mysql
1. docker inspect mysql \| grep IPAddress

## Prerequisites
1. [MySQL Server](https://dev.mysql.com/downloads/mysql/5.5.html?os=src&version=5.1).
1. [MySQL JDBC Driver](https://dev.mysql.com/downloads/connector/j/).
1. [Apache NiFi](https://nifi.apache.org/).

## Configure NiFi to use MySQL JDBC Driver.
Download the MySQL JDBC driver and place in NiFi's lib directory.  
For my setup, I placed JAR in **/nifi-1.9.2/lib**. 

## Starting NiFi Locally
1. servers/nifi-1.9.2/bin/nifi.sh start
1. servers/nifi-1/9.2/conf/nifi.properties nifi.web.http.port
1. Go to http://127.0.0.1:8686/nifi/

## Starting Jekyll Locally
1. bundle exec jekyll serve






---

http://127.0.0.1:4000/nifi/2019/09/08/NiFi-01-Copying-Records-Between-Tables.html#QueryDatabaseTable
http://localhost:8686/nifi/
https://www.batchiq.com/database-extract-with-nifi.html
https://www.batchiq.com/database-ingest-with-nifi.html
https://gist.github.com/ijokarumawak/b37db141b4d04c2da124c1a6d922f81f
https://towardsdatascience.com/big-data-managing-the-flow-of-data-with-apache-nifi-and-apache-kafka-af674cd8f926
https://blog.pythian.com/database-migration-using-apache-nifi/

for more parts
https://docs.microsoft.com/en-us/sql/connect/jdbc/system-requirements-for-the-jdbc-driver?view=sql-server-2017
https://www.mkyong.com/maven/how-to-add-oracle-jdbc-driver-in-your-maven-local-repository/
https://blogs.oracle.com/datawarehousing/connecting-apache-zeppelin-to-your-oracle-data-warehouse

https://www.markdownguide.org/extended-syntax
https://kramdown.gettalong.org/quickref.html


