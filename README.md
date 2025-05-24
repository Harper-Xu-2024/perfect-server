# Perfect Server For MySQL 8.0

This is a MySQL development branch, which is based on MySQL 8.0.28 as published by Oracle on MySQL on Launchpad.

This repository is published in order to share code and information and is intended to be used directly for free commercial use. We provide no guarantees of bug fixes, ongoing maintenance, compatibility, or suitability for any user.

# Features in Perfect Server for MySQL 8.0

## Enhancements to mysqlbinlog:

* Support for transaction analysis mode

SQL cases:

```bash
mysql> show create table private.cluster_key\G
*************************** 1. row ***************************
       Table: cluster_key
Create Table: CREATE TABLE `cluster_key` (
  `id` int NOT NULL AUTO_INCREMENT,
  `col_1` varchar(45) DEFAULT NULL,
  `col_2` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=114690 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql> insert into cluster_key values(null, sleep(30), 0);
Query OK, 1 row affected (30.02 sec)

mysql> insert into cluster_key values(null, sleep(5), 'select (5)');
Query OK, 1 row affected (5.01 sec)

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> delete from cluster_key limit 1000;
Query OK, 1000 rows affected (0.07 sec)

mysql> select sleep(5);
+----------+
| sleep(5) |
+----------+
|        0 |
+----------+
1 row in set (5.00 sec)

mysql> insert into cluster_key values(null, 0, 0);
Query OK, 1 row affected (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.02 sec)

mysql> insert into cluster_key values(null, sleep(10), 'select sleep(10)');
Query OK, 1 row affected (10.02 sec)
```

List all of the sql statistics transactions by transactions:

```bash
[root@DESKTOP-87FJKA1 data01]# /usr/local/mysql/bin/mysqlbinlog --analysis-mode -v --base64-output='decode-rows' /data01/binlog.000014
'512948d0-122e-11f0-9824-aa7bb193c60d:26':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:12:38'
  'stop_time': '20250524 16:12:38'
  'exec_time': 30
  'sql_statistics':
    'insert':
      'private.cluster_key': 1
'512948d0-122e-11f0-9824-aa7bb193c60d:27':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:13:36'
  'stop_time': '20250524 16:13:36'
  'exec_time': 5
  'sql_statistics':
    'insert':
      'private.cluster_key': 1
'512948d0-122e-11f0-9824-aa7bb193c60d:28':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:14:15'
  'stop_time': '20250524 16:15:00'
  'exec_time': 0
  'sql_statistics':
    'delete':
      'private.cluster_key': 1000
    'insert':
      'private.cluster_key': 1
'512948d0-122e-11f0-9824-aa7bb193c60d:31':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:23:14'
  'stop_time': '20250524 16:23:14'
  'exec_time': 10
  'sql_statistics':
    'insert':
      'private.cluster_key': 1
```

List the sql statistics of top 30 transactions order by execution time:

```bash
[root@DESKTOP-87FJKA1 data01]# /usr/local/mysql/bin/mysqlbinlog --analysis-mode --analysis-sort -v --base64-output='decode-rows' /data01/binlog.000014
'512948d0-122e-11f0-9824-aa7bb193c60d:26':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:12:38'
  'stop_time': '20250524 16:12:38'
  'exec_time': 30
  'sql_statistics':
    'insert':
      'private.cluster_key': 1
'512948d0-122e-11f0-9824-aa7bb193c60d:31':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:23:14'
  'stop_time': '20250524 16:23:14'
  'exec_time': 10
  'sql_statistics':
    'insert':
      'private.cluster_key': 1
'512948d0-122e-11f0-9824-aa7bb193c60d:27':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:13:36'
  'stop_time': '20250524 16:13:36'
  'exec_time': 5
  'sql_statistics':
    'insert':
      'private.cluster_key': 1
'512948d0-122e-11f0-9824-aa7bb193c60d:28':
  'binlog_file': '/data01/binlog.000014'
  'start_time': '20250524 16:14:15'
  'stop_time': '20250524 16:15:00'
  'exec_time': 0
  'sql_statistics':
    'delete':
      'private.cluster_key': 1000
    'insert':
      'private.cluster_key': 1
```

* Support for transaction rollback mode

## Additional SQL throttling plugin:

* Added support for SQL requests limiting
