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

`CREATE TABLE mytable` 将会创建一个名为**mytable**的新表。

`id int unsigned NOT NULL auto_increment`会创建名为id的列，这种类型的字段将会为表里的每一行记录分配一个唯一的数字ID（也就是说，不会有两列的id相同） ，MySQL将会自动为每条记录的id字段分配一个新的，独一无二的值，（一般是从1开始计数）。
返回值：
> Query OK, 0 rows affected (0.10 sec)

#### 在MySQl表里插入一行数据

```sql
INSERT INTO mytable ( username, email )
VALUES ( "myuser", "myuser@example.com" );
```

这个例子的返回值：
> Query OK, 1 row affected (0.06 sec)

varchar类型，也就是字符串的插入，也可以使用单引号：

```sql
INSERT INTO mytable ( username, email )
VALUES ( 'username', 'username@example.com' );
```

#### 在MySQL表里更新一行数据

```sql
UPDATE mytable SET username="myuser" WHERE id=8
```

例子返回值：
> Query OK, 1 row affected (0.06 sec)

在sql语句中，int类型值可以不加引号直接使用。但是字符串类型和日期类型值必须使用单引号'或者双引号"包裹起来才能使用。

#### 在MySQL表里删除一行数据

```sql
DELETE FROM mytable WHERE id=8
```

例子返回值：
> Query OK, 1 row affected (0.06 sec)

这句将会把id值为8的那一行删除。

#### 在MySQL里根据条件查询多行数据

```sql
SELECT * FROM mytable WHERE username = "myuser";
```

返回值：

```table
+----+----------+---------------------+
| id | username | email               |
+----+----------+---------------------+
|  1 | myuser   | myuser@example.com  |
+----+----------+---------------------+
```

> 1 row in set (0.00 sec)

#### 显示所有数据库列表

```sql
SHOW databases;
```

返回值：

```table
+-------------------+
| Databases         |
+-------------------+
| information_schema|
| mydb              |
+-------------------+
```

> 2 rows in set (0.00 sec)

你可以把“information_schema”当成是主数据库，它存储了一些数据库元数据。

#### 显示当前数据库的所有表

```sql
SHOW tables;
```

返回值：

```table
+----------------+
| Tables_in_mydb |
+----------------+
| mytable        |
+----------------+
```

> 1 row in set (0.00 sec)

#### 显示表中所有的字段

```sql
DESCRIBE databaseName.tableName;
```

或者，如果已经选择使用了一个数据库：

```sql
DESCRIBE tableName;
```

返回值：

```table
+-----------+----------------+--------+---------+-------------------+-------+
| Field     | Type           | Null   | Key     | Default           | Extra |
+-----------+----------------+--------+---------+-------------------+-------+
| fieldname | fieldvaluetype | NO/YES | keytype | defaultfieldvalue |       |
+-----------+----------------+--------+---------+-------------------+-------+
```

**Extra**字段将会包含`auto_increment`之类的值。





*****TO DO：第一章还没弄完，先弄弄第二章*****




## 第二章：数据类型

### 2.1小节：CHAR(n)

CHAR(n)是固定长度n个字符的字符串，如果它被设置成```CHARACTER SET utf8mb4```，那么表示，不管里面存的内容到底是什么，它也会占用4*n个字节容量。
CHAR(n)最常被使用的场景是存储包含英文字符的字符串时，在这个场景一般被设置成```CHARACTER SET ascii```。（当然latin1也是可以的。）

```sql
country_code CHAR(2) CHARACTER SET ascii,
postal_code CHAR(6) CHARACTER SET ascii,
uuid CHAR(39) CHARACTER SET ascii, -- more discussion elsewhere
```

### 2.2小节：DATE、DATETIME、TIMESTAMP、YEAR和TIME

DATE数据类型包含了日期，但是不包含时间。他的格式是'YYYY-MM-DD'，范围从'1000-01-01'到'9999-12-31'。

DATETIME类型则包含了时间，它的格式为'YYYY-MM-DD HH:MM:SS'。其范围从'1000-01-01 00:00:00'到'9999-12-31 23:59:59'.

TIMESTAMP类型是一种包含了日期和时间的整数类型，其有效范围从'1970-01-01 00:00:01'UTC到'2038-01-19 03:14:07' UTC。（UTC指的是Coordinated Universal Time－ 世界协调时间又称世界标准时间或世界统一时间）

YEAR类型表示年，范围从1901到2155。

TIME类型表示时间，他的格式是'HH:MM:SS'，范围从'-838:59:59'到'838:59:59'。

各种类型存储空间需求如下：

```table
|-----------|--------------------|----------------------------------------|
| 数据类型   | MySQL 5.6.4之前版本 | MySQL 5.6.4                           |
|-----------|--------------------|----------------------------------------|
| YEAR      | 1 byte             | 1 byte                                 |
| DATE      | 3 bytes            | 3 bytes                                |
| TIME      | 3 bytes            | 3 bytes + 小数秒存储                    |
| DATETIME  | 8 bytes            | 5 bytes + 小数秒存储                    |
| TIMESTAMP | 4 bytes            | 4 bytes + 小数秒存储                    |
|-----------|--------------------|----------------------------------------|
```

小数秒存储需求（根据5.6.4版）：

```table
|------------------------------|------------------|
| 小数秒精度                    | 所需存储空间      |
|------------------------------|------------------|
| 0                            | 0 bytes          |
| 1,2                          | 1 byte           |
| 3,4                          | 2 byte           |
| 5,6                          | 3 byte           |
|------------------------------|------------------|
```

有兴趣可以查看MySQL官方手册 [DATE, DATETIME, and TIMESTAMP Types](http://dev.mysql.com/doc/refman/5.7/en/datetime.html), [Data Type Storage Requirements](https://dev.mysql.com/doc/refman/5.7/en/storage-requirements.html)还有[Fractional Seconds in Time Values](https://dev.mysql.com/doc/refman/5.7/en/fractional-seconds.html).