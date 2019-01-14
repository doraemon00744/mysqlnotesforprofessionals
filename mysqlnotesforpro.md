# MySQL Notes For Professionals

## 第一章：开始熟悉MySQL

各个版本发布日期

* 1.0   1995-05-23
* 3.19  1996-12-01
* 3.20  1997-01-01
* 3.21  1998-10-01
* 3.22  1999-10-01
* 3.23  2001-01-22
* 4.0   2003-03-01
* 4.1   2004-10-01
* 5.0   2005-10-01
* 5.1   2008-11-27
* 5.5   2010-11-01
* 5.6   2013-02-01
* 5.7   2015-10-01

### 1.1小节：开始

#### 在MySQL里创建一个库

```sql
CREATE DATABASE mydb;
```

返回值：
> Query OK, 1 row affected (0.05 sec)

**现在使用刚才创建的库**mydb

```sql
USE mydb;
```

返回值：
> Database Changed

#### 在MySQL里创建一个表

```sql
CREATE TABLE mytable
(
    id int unsigned NOT NULL auto_increment,
    username varchar(100) NOT NULL,
    email varchar(100) NOT NULL,
    PRIMARY KEY (id)
);
```
