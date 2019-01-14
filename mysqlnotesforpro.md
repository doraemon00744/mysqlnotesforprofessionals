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