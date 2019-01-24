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

### 2.3小节：VARCHAR(25)--or not

#### 建议的最大长度

首先，我先声明，有些存储着基本是十六进制或限于ASCII码的字符串，你最好还是设置成```CHARACTER SET ascii```（当然latin1也可以），这样可以节省空间，不至于浪费，示例如下：

```sql
UUID CHAR(36) CHARACTER SET ascii -- or pack into BINARY(16)
country_code CHAR(2) CHARACTER SET ascii
ip_address CHAR(39) CHARACTER SET ascii -- or pack into BINARY(16)
phone VARCHAR(20) CHARACTER SET ascii -- probably enough to handle extension
postal_code VARCHAR(20) CHARACTER SET ascii -- (not 'zip_code') (don't know the max
city VARCHAR(100) -- This Russian town needs 91:
Poselok Uchebnogo Khozyaystva Srednego Professionalno-Tekhnicheskoye Uchilishche Nomer Odin
country VARCHAR(50) -- probably enough
name VARCHAR(64) -- probably adequate; more than some government agencies allow
```

#### 为什么不直接用VARCHAR(255)

这里有两个原因说明为什么不在任意场景下都直接使用（255）。

* 当一个复杂的**SELECT**语句需要创建临时表时（比如在子查询里，又是**UNION**，又是**GROUP BY**等等），这时候优先的做法是使用**MEMORY**引擎，也就是把数据放到内存RAM里。但是在这个过程中，**VARCHAR**类型会被转化成**CHAR**类型，这就导致```VARCHAR(255) CHARACTER SET utf8mb4```会占用1020字节（255 * 4 = 1020），这就导致这个过程会使用磁盘，磁盘肯定比内存慢得多了。

* 在某些场景下，InnoDB会在建表时候，计算表里列的大小，如果判断结果觉得这个数太大了，就会中断建表流程。

**VARCHAR**对比**TEXT**

以下是一些对TEXT、CHAR和VARCHAR类型的使用提示和最佳实践：

* 不要使用**TINYTEXT**
* 基本上不要用**CHAR**，因为他是固定长度，每个字符都占用了```CHARACTER SET```的最大长度（比如对utf8mb4来说，一个字符占用4个字节）。
* 如果使用**CHAR**，如果你没有特别的需求，就使用```CHARACTER SET ascii```。
* **VARCHAR(n)**会在n个字符时候截断；**TEXT**会在某个字节大小时候截断。（用的时候考虑好，你是否能接受截断，哈哈哈）
* **TEXT**可能会减慢复杂**SELECT**语句的执行速度，这个就看中间创建的临时表具体是怎么操作的了。

### 2.4小节：**INT**用作**AUTO_INCREMENT**时

任何大小的**INT**都可用来自增**AUTO_INCREMENT**。这时，使用无符号的**UNSIGNED**是非常合适的。

但是要记住，有些操作会“烧掉”自增**AUTO_INCREMENT**的id，这会导致很多意想不到的坑。举个例子：**INSERT IGNORE**和**REPLACE**语句，他们将会在你还没意识到的情况下就事先分配一个id，在你的场景中这可能是根本不需要的。不过这是InnoDB引擎设计的一个特性，不是bug，不应该去想着禁止它的这种行为。

### 其他

我们已经准备了专门的章节来讲“FLOAT、DOUBLE和DECIMAL”还有“ENUM”。在数据类型这里专门放一页来说这些好像又不太实用，我建议这里叫“字段类型”，这里将是一个概述，然后会分成以下几个主题章节讲解：

* INTs
* FLOAT, DOUBLE和DECIMAL
* 字符串类型(CHARs, TEXT, 等等)
* BINARY和BLOB
* DATETIME, TIMESTAMP, 和其他时间格式
* ENUM和SET
* 空间型数据
* JSON类型 (MySQL 5.7.8+)
* 用什么样的数据类型去存储钱，如何去存储其他的一些常用的“类型”，这些我们都会在已有的数据类型中去找出一个合适的匹配项。

每个主题章节除了基本的语法和事例，我们还会在合适的时候包括以下几项:

* 当改表结构（ALTERing）时需要考虑的问题
* 大小 (字节)
* 和其他非MySQL数据库的区别 (低优先级)
* 当该类型用于主键或辅助键时需要考虑的问题
* 其他一些最佳实践
* 其他一些性能问题

### 2.6小节：隐式/自动转换

```sql
select '123' * 2;
```

为了完成这个乘法，MySQL自动将字符串123转换成了数字。
返回：
> 246

这种转换是从左到右进行的，如果转换不了，则返回0

```sql
select '123ABC' * 2
```

返回值：
> 246

```sql
select 'ABC123' * 2
```

返回：
> 0

### 2.7小节：数值型介绍

MySQL提供了多种不同的数值型类型，具体分为以下几组
|    组    | 类型 |
| ---------- | --- |
| 整数 |  INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT |
| 固定点数       |  DECIMAL, NUMERIC |
| 浮点数       |  FLOAT, DOUBLE |
| 位值       |  BIT |

### 2.8小节：整数

最小的无符号值为0。

|类型|存储容量（单位字节）|最小值（有符号）|最大值（有符号）|最大值（无符号）|
|---|---|---|---|---|
|TINYINT|1|-128|127|255|
|SMALLINT|2|-32,768|32767|65535|
|MEDIUMINT|3|-8,388,608|8,388,607|16,777,215|
|INT|4|-2,147,483,648|2,147,483,647|4,294,967,295|
|BIGINT|8|-9,223,372,036,854,775,808|9,223,372,036,854,775,807|18,446,744,073,709,551,615|

### 2.9小节：固定点数

MySQL的**DECIMAL**和**NUMERIC**类型存储精确数值，所以建议用这类类型去存储需要精确度的东西，比如说钱。

#### Decimal

这类值以二进制格式存储。在这种两列的声明中，数据的精度和标度都必须指定，精度表示存储数字的有效数字的位数，标度表示小数点后面的位数。

```sql
salary DECIMAL(5,2)
```

5表示精度，2表示标度。在上面这个例子中，可以存储的数字的范围从```-999.99```到```999.99```。

如果后面那个标度参数省略的话，缺省值是0。

精度参数最大可以达到65，也就是这里最多可以存储65位有效数字的数。```DECIMAL(M,N)```所占用的空间大概是M/2个字节。

### 2.10小节：浮点数

**FLOAT**和**DOUBLE**代表着近似值类型。
|类型|存储空间|精度|范围|
|--|--|--|--|
|FLOAT|4字节|23位有效二进制位/7个数字|10^+/-38|
|DOUBLE|8字节|53位有效二进制位/16个数字|10^+/-38|

**REAL**是**FLOAT**的同义词。**DOUBLE PRECISION**是**DOUBLE**的同义词。

虽然MySQL支持(M,D)这种修饰符，但是*不要用它*。(M,D)的意思是可以存储最多M位十进制数，包括整数位和小数位一起，D代表小数位。整个数字会被取整两次或者截断；这绝对是弊大于利的。

由于浮点数是近似值不是精确值，如果想用它们去比较大小的话可能会出问题。特别注意一下，一个**FLOAT**值不等于一个**DOUBLE**值。

### 2.11小节：位值类型

**BIT**可以用来存储位值。```BIT(M)```可以存储最多M位的值，并且M的范围是1到64。
你还可以用位值标记法来表示一个值。

```sql
b'111' -> 7
b'10000000' -> 128
```

有时候我们用“移位”来构成位值也是非常方便的，比如说(1 << 7)表示的就是128。

在一个NDB表中，所有的位列加起来大小不能超过4096位。

## 第三章：SELECT语句

```SELECT```是用来从一个表或多个表里获取数据行的。

### 3.1小节：配合DISTINCT使用的SELECT语句

```SELECT```语句后面跟着```DISTINCT```，就可以从结果集中删除重复的行。

```sql
CREATE TABLE `car`
( `car_id` INT UNSIGNED NOT NULL PRIMARY KEY,
`name` VARCHAR(20),
`price` DECIMAL(8,2)
);
INSERT INTO CAR (`car_id`, `name`, `price`) VALUES (1, 'Audi A1', '20000');
INSERT INTO CAR (`car_id`, `name`, `price`) VALUES (2, 'Audi A1', '15000');
INSERT INTO CAR (`car_id`, `name`, `price`) VALUES (3, 'Audi A2', '40000');
INSERT INTO CAR (`car_id`, `name`, `price`) VALUES (4, 'Audi A2', '40000');
SELECT DISTINCT `name`, `price` FROM CAR;
+---------+----------+
| name    | price    |
+---------+----------+
| Audi A1 | 20000.00 |
| Audi A1 | 15000.00 |
| Audi A2 | 40000.00 |
+---------+----------+
```

```DISTINCT```会横跨所有列去做筛选，而不是某一个列。有些初学者可能会对此表示疑惑和懵逼。简单来说，这个去重是针对整个行级别的，而不是列级别的。大家可以看看上表里的“Audi A1”，就明白了。

在后面版本的MySQL中，**DISTINCT has implications with its use alongside ORDER BY.**TO DO 这句不太好理解。关于```ONLY_FULL_GROUP_BY```这个参数的设置可以在MySQL的官方手册中的[MySQL Handling of
GROUP BY](http://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html)找到。

### 3.2小节：SELECT所有列(*)

语句如下：

```sql
SELECT * FROM stack;
```

结果：

```table
+------+----------+----------+
| id   | username | password |
+------+----------+----------+
| 1    | admin    | admin    |
| 2    | stack    | stack    |
+------+----------+----------+
2 rows in set (0.00 sec)
```

你可以在join语句中获取其中一个表的所有列：

```sql
SELECT stack.* FROM stack JOIN Overflow ON stack.id = Overflow.id;
```

最佳实践：除非你正在debug或是将数据行导入到关联数组中，不然不要用*，否则当schema变化时（ADD、DROP或者重新调整列）都会导致很恶心的问题。而且，当你置顶了你需要的列时，MySQL有时还能优化查询。

#### 优点：

1. 如果你真的用了```SELECT *```，那么在你增加或删除列时，sql语句不需要作出改变
2. 写起来很短
3. 也能查出结果来，所以说使用```SELECT *```到底合理么？

#### 缺点：

1. 你获取了比你所需的还要多的数据。假设你增加了一个```VARBINARY```类型的列，每行数据大概是200K。你可能只在某一个场景下需要查询这个数据，然而你要是用了SELECT *的话，你就会获得很多无用数据，每10条数据大概2MB
2. 对使用的数据明确
3. 如果指定了列的话，当一个列被移除时，你的查询就会报错
4. 查询处理器需要做额外的工作去判断哪些列是存在于表里的
5. 你可以更容易找到一个列在哪里被使用了
6. 如果你在join语句用```SELECT *```，那么你将获得所有列
7. 你没法安全的使用ordinal referencing（虽说在列上使用ordinal referencing本身就不太好）

### 3.3小节：通过列名使用SELECT

```sql
CREATE TABLE stack(
id INT,
username VARCHAR(30) NOT NULL,
password VARCHAR(30) NOT NULL
);
INSERT INTO stack (`id`, `username`, `password`) VALUES (1, 'Foo', 'hiddenGem');
INSERT INTO stack (`id`, `username`, `password`) VALUES (2, 'Baa', 'verySecret');
```

查询：

```sql
SELECT id FROM stack;
```

结果：
```table
+------+
| id   |
+------+
| 1    |
| 2    |
+------+
```

### 3.4小节：SELECT LIKE(%)语句

```sql
CREATE TABLE stack
( id int AUTO_INCREMENT PRIMARY KEY,
username VARCHAR(100) NOT NULL
);
INSERT stack(username) VALUES
('admin'),('k admin'),('adm'),('a adm b'),('b XadmY c'), ('adm now'), ('not here');
```

现在查询所有带着“adm”的数据：

```sql
SELECT * FROM stack WHERE username LIKE "%adm%";
+----+-----------+
| id | username  |
+----+-----------+
| 1  | admin     |
| 2  | k admin   |
| 3  | adm       |
| 4  | a adm b   |
| 5  | b XadmY c |
| 6  | adm now   |
+----+-----------+
```

查询所有以“adm”开头的：

```sql
SELECT * FROM stack WHERE username LIKE "adm%";
+----+----------+
| id | username |
+----+----------+
| 1  | admin    |
| 3  | adm      |
| 6  | adm now  |
+----+----------+ 
```

查询所有以“adm”结尾的：

```sql
SELECT * FROM stack WHERE username LIKE "%adm";
+----+----------+
| id | username |
+----+----------+
| 3  | adm      |
+----+----------+
```

```LIKE```语句中的```%```可以匹配任意数量的字符，而```_```只匹配一个任意字符，比如说：

```sql
SELECT * FROM stack WHERE username LIKE "adm_n";
+----+----------+
| id | username |
+----+----------+
| 1  | admin    |
+----+----------+
```

**关于性能方面的一些提示**如果```username```字段上面建立了索引的话，则：

* 执行```LIKE 'adm'```会和你写```='adm'```是一样的
* 执行```LIKE 'adm%'```会变成一个范围，有点类似```BETWEEN..AND..```，可以很好地利用索引
* 执行```LIKE '%adm'```（或者其他的以通配符开始的类似查询）不能使用索引，所以速度非常慢，如果表里有很多列的话，整个查询会非常慢以至于一无是处。
* ```RLICE(REGEXP)```比LIKE还慢，不过它具有更多的功能。
* 虽然**MySQL**在各种类型表和列提供了全文索引功能，但是这些功能不是用来满足类似```LIKE```这种查询的

### 3.5小节：带有CASE和IF的SELECT语句

#### 查询：

```sql
SELECT st.name,
st.percentage,
CASE WHEN st.percentage >= 35 THEN 'Pass' ELSE 'Fail' END AS `Remark`
FROM student AS st ;
```

#### 结果：

```table
+--------------------------------+
| name  | percentage | Remark    |
+--------------------------------+
| Isha  | 67         | Pass      |
| Rucha | 28         | Fail      |
| Het   | 35         | Pass      |
| Ansh  | 92         | Pass      |
+--------------------------------+
```

#### 或者使用IF语句：

```sql
SELECT st.name,
st.percentage,
IF(st.percentage >= 35, 'Pass', 'Fail') AS `Remark`
FROM student AS st ;
```

#### 注意：

```sql
IF(st.percentage >= 35, 'Pass', 'Fail')
```

> 这个意思是，当```st.percentage >= 35```时，返回’Pass‘，否则返回’Fail‘

### 3.6小节：带别名的SELECT语句

SQL中的别名是用来临时给一个表或者列取名的，一般用来提高可读性。

#### 查询：

```sql
SELECT username AS val FROM stack;
SELECT username val FROM stack;
```

（注意：```AS```是可以省略的）

#### 结果：

```table
+-------+
| val   |
+-------+
| admin |
| stack |
+-------+
2 rows in set (0.00 sec)
```

### 3.7小节：带LIMIT的SELECT语句