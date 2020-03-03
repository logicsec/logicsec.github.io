---
layout: post
title: "Alternatives to Extract Tables and Columns from MySQL and MariaDB"
custom_page_description: "Alternatives to Extract Tables and Columns from MySQL and MariaDB"
categories: [mysql, web application]
post_image: "/assets/images/articles/mysql.png"
author: Nicholas Knight
author_image: "/assets/images/authors/5937.jpg"
author_bio: " An infosec junkie who likes to rant"
excerpt_separator: <!--more-->
comments: true
---

These are all the databases and tables I found where we can extract table names apart from ‘information_schema.tables’. I have tested the following in 5.7.29 MySQL and 10.3.18 MariaDB. There are 39 queries in total.

## Sys
These views were added in MySQL 5.7.9.


```mysql
mysql> SELECT object_name FROM `sys`.`x$innodb_buffer_stats_by_table` WHERE object_schema = DATABASE();
+-------------+
| object_name |
+-------------+
| emails      |
| flag        |
| referers    |
| uagents     |
| users       |
+-------------+
5 rows in set (0.04 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`x$schema_flattened_keys` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
5 rows in set (0.01 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`x$ps_schema_table_statistics_io` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| db         |
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
6 rows in set (0.04 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`x$schema_index_statistics` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| table_name |
+------------+
| users      |
| emails     |
| referers   |
| uagents    |
| flag       |
+------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`x$schema_table_statistics` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| emails     |
| users      |
| flag       |
| referers   |
| uagents    |
+------------+
5 rows in set (0.03 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`x$schema_table_statistics_with_buffer` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| referers   |
| uagents    |
| emails     |
| users      |
| flag       |
+------------+
5 rows in set (0.07 sec)
```

```mysql
mysql> SELECT object_name FROM `sys`.`innodb_buffer_stats_by_table` WHERE object_schema = DATABASE();
+-------------+
| object_name |
+-------------+
| emails      |
| flag        |
| referers    |
| uagents     |
| users       |
+-------------+
5 rows in set (0.05 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`schema_auto_increment_columns` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| table_name |
+------------+
| referers   |
| flag       |
| emails     |
| users      |
| uagents    |
+------------+
5 rows in set (0.14 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`schema_index_statistics` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| table_name |
+------------+
| users      |
| emails     |
| referers   |
| uagents    |
| flag       |
+------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`schema_table_statistics` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| users      |
| emails     |
| referers   |
| uagents    |
| flag       |
+------------+
5 rows in set (0.04 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `sys`.`schema_table_statistics_with_buffer` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| users      |
| emails     |
| flag       |
| referers   |
| uagents    |
+------------+
5 rows in set (0.09 sec)
```

Using these queries, you can get the table file paths stored locally on disk, along with it we can extract the table names.


```mysql
mysql> SELECT FILE FROM `sys`.`io_global_by_file_by_bytes` WHERE FILE REGEXP DATABASE();
+---------------------------------+
| file                            |
+---------------------------------+
| @@datadir\security\emails.ibd   |
| @@datadir\security\flag.ibd     |
| @@datadir\security\referers.ibd |
| @@datadir\security\uagents.ibd  |
| @@datadir\security\users.ibd    |
| @@datadir\security\uagents.frm  |
| @@datadir\security\referers.frm |
| @@datadir\security\users.frm    |
| @@datadir\security\emails.frm   |
| @@datadir\security\flag.frm     |
| @@datadir\security\db.opt       |
+---------------------------------+
11 rows in set (0.22 sec)
```

```mysql
mysql> SELECT FILE FROM `sys`.`io_global_by_file_by_latency` WHERE FILE REGEXP DATABASE();
+---------------------------------+
| file                            |
+---------------------------------+
| @@datadir\security\flag.ibd     |
| @@datadir\security\uagents.ibd  |
| @@datadir\security\flag.frm     |
| @@datadir\security\emails.frm   |
| @@datadir\security\emails.ibd   |
| @@datadir\security\referers.ibd |
| @@datadir\security\referers.frm |
| @@datadir\security\users.frm    |
| @@datadir\security\users.ibd    |
| @@datadir\security\uagents.frm  |
| @@datadir\security\db.opt       |
+---------------------------------+
```

```mysql
mysql> SELECT FILE FROM `sys`.`x$io_global_by_file_by_bytes` WHERE FILE REGEXP DATABASE();
+-----------------------------------------------------------------------------+
| file                                                                        |
+-----------------------------------------------------------------------------+
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.ibd   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.ibd     |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.ibd |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.ibd  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.ibd    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.frm  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.frm |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.frm    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.frm   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.frm     |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\db.opt       |
+-----------------------------------------------------------------------------+
```

```mysql
mysql> SELECT FILE FROM `sys`.`x$io_global_by_file_by_latency` WHERE FILE REGEXP DATABASE();
+-----------------------------------------------------------------------------+
| file                                                                        |
+-----------------------------------------------------------------------------+
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.ibd     |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.ibd  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.frm     |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.frm   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.ibd   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.ibd |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.frm |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.frm    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.ibd    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.frm  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\db.opt       |
+-----------------------------------------------------------------------------+
11 rows in set (0.00 sec)
```

The following tables store the queries used before like a log. You can use regular expressions to find what you need.


```mysql
mysql> SELECT QUERY FROM sys.x$statement_analysis WHERE QUERY REGEXP DATABASE();
+-----------------------------------------------------------------------------------------------------------------------------------+
| query                                                                                                                             |
+-----------------------------------------------------------------------------------------------------------------------------------+
| SHOW TABLE STATUS FROM `security`                                                                                                 |
| SHOW CREATE TABLE `security` . `emails`                                                                                           |
| SHOW CREATE TABLE `security` . `users`                                                                                            |
| SHOW CREATE TABLE `security` . `referers`                                                                                         |
+-----------------------------------------------------------------------------------------------------------------------------------+
```

```mysql
mysql> SELECT QUERY FROM `sys`.`statement_analysis` where QUERY REGEXP DATABASE();
+-----------------------------------------------------------+
| query                                                     |
+-----------------------------------------------------------+
| SHOW TABLE STATUS FROM `security`                         |
| SHOW CREATE TABLE `security` . `emails`                   |
| SHOW CREATE TABLE `security` . `users`                    |
| SHOW CREATE TABLE `security` . `referers`                 |
| SELECT * FROM `security` . `users` LIMIT ?                |
| SHOW CREATE TABLE `security` . `uagents`                  |
| SHOW CREATE PROCEDURE `security` . `select_first_column`  |
| SHOW CREATE TABLE `security` . `users`                    |
| SHOW OPEN TABLES FROM `security` WHERE `in_use` != ?      |
| SHOW TRIGGERS FROM `security`                             |
| USE `security`                                            |
| USE `security`                                            |
+-----------------------------------------------------------+
12 rows in set (0.01 sec)
```

## Performance_Schema


```mysql
mysql> SELECT object_name FROM `performance_schema`.`objects_summary_global_by_type` WHERE object_schema = DATABASE();
+---------------------+
| object_name         |
+---------------------+
| emails              |
| referers            |
| uagents             |
| users               |
| flag                |
| select_first_column |
+---------------------+
6 rows in set (0.00 sec)
```

```mysql
mysql> SELECT object_name FROM `performance_schema`.`table_handles` WHERE object_schema = DATABASE();
+-------------+
| object_name |
+-------------+
| emails      |
| referers    |
| uagents     |
| users       |
| users       |
| users       |
| users       |
| users       |
| users       |
| users       |
| emails      |
| flag        |
| referers    |
| uagents     |
| users       |
| emails      |
| flag        |
| referers    |
| uagents     |
| users       |
+-------------+
20 rows in set (0.00 sec)
```

```mysql
mysql> SELECT object_name FROM `performance_schema`.`table_io_waits_summary_by_index_usage` WHERE object_schema = DATABASE();
+-------------+
| object_name |
+-------------+
| emails      |
| referers    |
| uagents     |
| users       |
| users       |
| flag        |
+-------------+
6 rows in set (0.00 sec)
```

```mysql
mysql> SELECT object_name FROM `performance_schema`.`table_io_waits_summary_by_table` WHERE object_schema = DATABASE();
+-------------+
| object_name |
+-------------+
| emails      |
| referers    |
| uagents     |
| users       |
| flag        |
+-------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT object_name FROM `performance_schema`.`table_lock_waits_summary_by_table` WHERE object_schema = DATABASE();
+-------------+
| object_name |
+-------------+
| emails      |
| referers    |
| uagents     |
| users       |
| flag        |
+-------------+
5 rows in set (0.00 sec)
```

As mentioned before the following contains the log of all typed SQL queries. Sometimes you might find table names. For simplicity, I have used regular expressions to match the current database name.

```mysql
mysql> SELECT digest_text FROM `performance_schema`.`events_statements_summary_by_digest` WHERE digest_text REGEXP DATABASE();
+-----------------------------------------------------------------------------------------------------------------------------------+
| digest_text                                                                                                                       |
+-----------------------------------------------------------------------------------------------------------------------------------+
| SHOW CREATE TABLE `security` . `emails`                                                                                           |
| SHOW CREATE TABLE `security` . `referers`                                                                                         |
| SHOW CREATE PROCEDURE `security` . `select_first_column`                                                                          |
| SHOW CREATE TABLE `security` . `uagents`                                                                                          |
+-----------------------------------------------------------------------------------------------------------------------------------+
17 rows in set (0.00 sec)
```

Like before we are fetching the local table file paths.


```mysql
mysql> SELECT file_name FROM `performance_schema`.`file_instances` WHERE file_name REGEXP DATABASE();
+-----------------------------------------------------------------------------+
| file_name                                                                   |
+-----------------------------------------------------------------------------+
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.ibd   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.ibd     |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.ibd |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.ibd  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.ibd    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.frm   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.frm |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\db.opt       |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.frm  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.frm    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.frm     |
+-----------------------------------------------------------------------------+
11 rows in set (0.00 sec)
```

```mysql
mysql> SELECT file_name FROM `performance_schema`.`file_summary_by_instance` WHERE file_name REGEXP DATABASE();
+-----------------------------------------------------------------------------+
| file_name                                                                   |
+-----------------------------------------------------------------------------+
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.ibd   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.ibd     |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.ibd |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.ibd  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.ibd    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\emails.frm   |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\referers.frm |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\db.opt       |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\uagents.frm  |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\users.frm    |
| D:\MySQL\mysql-5.7.29-winx64\mysql-5.7.29-winx64\data\security\flag.frm     |
+-----------------------------------------------------------------------------+
11 rows in set (0.00 sec)
```

## MySQL

```mysql
mysql> SELECT table_name FROM `mysql`.`innodb_table_stats` WHERE database_name = DATABASE();
+------------+
| table_name |
+------------+
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT table_name FROM `mysql`.`innodb_index_stats` WHERE database_name = DATABASE();
+------------+
| table_name |
+------------+
| emails     |
| emails     |
| emails     |
| flag       |
| flag       |
| flag       |
| referers   |
| referers   |
| referers   |
| uagents    |
| uagents    |
| uagents    |
| users      |
| users      |
| users      |
+------------+
15 rows in set (0.00 sec)
```


##Information_Schema

```mysql
mysql> SELECT TABLE_NAME FROM `information_schema`.`KEY_COLUMN_USAGE` WHERE CONSTRAINT_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
5 rows in set (0.07 sec)
```

However, the first column value can be retrieved in this case.

```mysql
mysql> SELECT COLUMN_NAME FROM `information_schema`.`KEY_COLUMN_USAGE` WHERE table_schema = DATABASE();
+-------------+
| COLUMN_NAME |
+-------------+
| id          |
| id          |
| id          |
| id          |
| id          |
+-------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `information_schema`.`PARTITIONS` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
5 rows in set (0.01 sec)
```

In this table, you can also use the column ‘column_name’ to get the first column of all tables.

```mysql
mysql> SELECT TABLE_NAME FROM `information_schema`.`STATISTICS` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT TABLE_NAME FROM `information_schema`.`TABLE_CONSTRAINTS` WHERE TABLE_SCHEMA = DATABASE();
+------------+
| TABLE_NAME |
+------------+
| emails     |
| flag       |
| referers   |
| uagents    |
| users      |
+------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT file_name FROM `information_schema`.`FILES` where file_name regexp database();
+-------------------------+
| file_name               |
+-------------------------+
| .\security\emails.ibd   |
| .\security\flag.ibd     |
| .\security\referers.ibd |
| .\security\uagents.ibd  |
| .\security\users.ibd    |
+-------------------------+
5 rows in set (0.00 sec)
```

Starting from MySQL 5.6 InnoDB exists in Information_Schema.

```mysql
mysql> SELECT TABLE_NAME FROM `information_schema`.`INNODB_BUFFER_PAGE` WHERE TABLE_NAME REGEXP  DATABASE();
+-----------------------+
| TABLE_NAME            |
+-----------------------+
| `security`.`emails`   |
| `security`.`referers` |
| `security`.`uagents`  |
| `security`.`users`    |
| `security`.`flag`     |
+-----------------------+
```

```mysql
mysql> SELECT TABLE_NAME FROM `information_schema`.`INNODB_BUFFER_PAGE_LRU` WHERE TABLE_NAME REGEXP DATABASE();
+-----------------------+
| TABLE_NAME            |
+-----------------------+
| `security`.`emails`   |
| `security`.`referers` |
| `security`.`uagents`  |
| `security`.`users`    |
| `security`.`flag`     |
+-----------------------+
5 rows in set (0.06 sec)
```

```mysql
mysql> SELECT path FROM  `information_schema`.`INNODB_SYS_DATAFILES` WHERE path REGEXP DATABASE();
+-------------------------+
| path                    |
+-------------------------+
| .\security\users.ibd    |
| .\security\emails.ibd   |
| .\security\uagents.ibd  |
| .\security\referers.ibd |
| .\security\flag.ibd     |
+-------------------------+
5 rows in set (0.00 sec)
```

```mysql
mysql> SELECT NAME FROM `information_schema`.`INNODB_SYS_TABLESPACES` WHERE NAME REGEXP DATABASE();
+-------------------+
| NAME              |
+-------------------+
| security/users    |
| security/emails   |
| security/uagents  |
| security/referers |
| security/flag     |
+-------------------+
5 rows in set (0.04 sec)
```

```mysql
mysql> SELECT NAME FROM `information_schema`.`INNODB_SYS_TABLESTATS` WHERE NAME REGEXP DATABASE();
+-------------------+
| NAME              |
+-------------------+
| security/emails   |
| security/flag     |
| security/referers |
| security/uagents  |
| security/users    |
+-------------------+
5 rows in set (0.00 sec)
```


## Column Names
Most of the time people ask me if there’s any method to extract column names? You don’t need to know the column names really.

If you have the error displayed you can straightaway get the number of columns using the below first query which makes the query equals to 1 returning us the error. To determine the number of columns in a boolean blind injection scenario you can do this trick which will return 0 (since the values aren’t equal). After that use the below third query to extract data 🙂



I hope these might come handy in your next pentest