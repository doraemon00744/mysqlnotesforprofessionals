# MySQL Notes For Professionals

## :kissing_smiling_eyes:第一章：开始熟悉MySQL

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




## :kissing_smiling_eyes:第二章：数据类型

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

### 2.3小节：用VARCHAR(255)--还是不这么用

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

## :kissing_smiling_eyes:第三章：SELECT语句

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

#### 查询：

```sql
SELECT *
FROM Customers
ORDER BY CustomerID
LIMIT 3;
```

#### 结果：

|CustomerID|CustomerName|ContactName|Address|City|PostalCode|Country|
|----|----|----|----|----|----|----|
|1| Alfreds Futterkiste |Maria Anders |Obere Str. 57 |Berlin |12209| Germany|
|2| Ana Trujillo Emparedados yhelados |Ana Trujillo |Avda. de laConstitución 2222|México D.F. |05021 |Mexico|
|3 |Antonio Moreno Taquería |Antonio Moreno Mataderos 2312 |México D.F. |05023 |Mexico|

**最佳实践**用```LIMIT```时候总是使用```ORDER BY```排序，否则的话你将会得到不可预料的结果。

#### 查询：

```sql
SELECT *
FROM Customers
ORDER BY CustomerID
LIMIT 2,1;
```

#### 解释：

当```LIMIT```语句后面带两个参数时，我们可以理解为```LIMIT offseet,count```。所以事例中的结果就会跳过两条记录，并且返回一条。

#### 结果：

|CustomerID|CustomerName|ContactName|Address|City|PostalCode|Country|
|----|----|----|----|----|----|----|
|3 |Antonio Moreno Taquería |Antonio Moreno Mataderos 2312 |México D.F. |05023 |Mexico|

#### 注意：

LIMIT语句中的值必须是常数，而且不是列值。

### 3.8小节：带BETWEEN的SELECT语句

大家可以用BETWEEN语句来代替组合使用“比某一个值大且比某一个值小”这种场景。

#### 数据：

```table
+----+-----------+
| id | username  |
+----+-----------+
| 1  | admin     |
| 2  | root      |
| 3  | toor      |
| 4  | mysql     |
| 5  | thanks    |
| 6  | java      |
+----+-----------+
```

#### 使用操作符的查询语句：

```sql
SELECT * FROM stack WHERE id >= 2 and id <= 5;
```

#### 相同含义的使用了```BETWEEN```的查询语句

```sql
SELECT * FROM stack WHERE id BETWEEN 2 and 5;
```

#### 结果：

```table
+----+-----------+
| id | username  |
+----+-----------+
| 2  | root      |
| 3  | toor      |
| 4  | mysql     |
| 5  | thanks    |
+----+-----------+
4 rows in set (0.00 sec)
```

#### 注意：
> BETWEEN的含义是>=和<=，而不是>和<。

#### 使用```NOT BETWEEN```
如果你想查询互补结果，你可以使用```NOT```，比如说：

```sql
SELECT * FROM stack WHERE id NOT BETWEEN 2 and 5;
```

#### 结果：

```table
+----+-----------+
| id | username  |
+----+-----------+
| 1  | admin     |
| 6  | java      |
+----+-----------+
2 rows in set (0.00 sec)
```

#### 注意：

> ```NOT BETWEEN``` 使用的是```>```和```<```而不是```>=```和```<=```，也就是说```WHERE id NOT BETWEEN 2 and 5```和```WHERE (id < 2 OR id > 5)```是一样的意思

如果你在```BETWEEN```语句里查询的列设有索引，那么MySQL可以利用这个索引做一个范围扫描。

### 3.9小节：带有WHERE条件的SELECT语句

#### 查询：

```sql
SELECT * FROM stack WHERE username = "admin" AND password = "admin";
```

#### 结果：

```table
+------+----------+----------+
| id   | username | password |
+------+----------+----------+
| 1    | admin    | admin    |
+------+----------+----------+
1 row in set (0.00 sec)
```

#### 在WHERE语句中嵌套SELECT语句

WHERE语句中可以包含任意合法的SELECT语句，以便构成更复杂的查询。这里有一个“嵌套”查询

#### 查询：

嵌套在里面的查询通常是用来返回一个单值，用来给外面查询语句做比较用的。

```sql
SELECT title FROM books WHERE author_id = (SELECT id FROM authors WHERE last_name = 'Bar' AND
first_name = 'Foo');
```

查询所有没有email地址的姓名

```sql
SELECT * FROM stack WHERE username IN (SELECT username FROM signups WHERE email IS NULL);
```

免责声明：当需要比较一整个结果集的时候，可以考虑用[joins](https://stackoverflow.com/questions/17946221/sql-join-and-different-types-of-joins)来提高性能。

### 3.10小节：使用LIKE(_)的SELECT语句

```LIKE```里面的_可以匹配一个字符

#### 查询：

```sql
SELECT username FROM users WHERE users LIKE 'admin_';
```

#### 结果：

```table
+----------+
| username |
+----------+
| admin1   |
| admin2   |
| admin-   |
| adminA   |
+----------+
```

### 3.11小节：查询日期范围的SELECT语句

```sql
SELECT ... WHERE dt >= '2017-02-01'
AND dt < '2017-02-01' + INTERVAL 1 MONTH
```

当然这个语句可以换成```BETWEEN```形式，并且带上23:59:59。不过上面这种形式还包含以下优点：

* 你不必重新计算后面那个日期（因为一般后面那个日期都与前面那个日期相隔一段具体的时间）
* You don't include both endpoints (as BETWEEN does), nor type '23:59:59' to avoid it.
* 对```DATE```, ```TIMESTAMP```, ```DATETIME```类型甚至包含微秒的```DATETIME(6)```都支持
* 它考虑了闰日和年末等情况
* 它是索引友好的（BETWEEN也是）

## 第四章：重音符的使用

### 4.1小节：使用重音符

有很多事例里面都在查询语句中使用了重音符，但是对于很多人来说还是不清楚何时何地应该使用重音符。

重音符的使用主要是为了避免一种叫做“MySQL保留字”的错误。当你在PHPmyAdmin中创建一个表时，经常会碰到警告或者报错，其中会告诉你，你正在使用“MySQL的保留字”。

举个例子，当你创建一个表时，将一列命名为“group”，你就会看到一个警告。这是因为你这么写sql了：

```sql
SELECT student_name, AVG(test_score) FROM student GROUP BY group
```

如果不想报错的话，你把语句改成如下这样：

```sql
SELECT student_name, AVG(test_score) FROM student GROUP BY `group`
```

#### 表

不光是列名，表名同样可以用重音符括起来。比如说当你需要```JOIN```多个表时。

```sql
SELECT `users`.`username`, `groups`.`group` FROM `users`
```

#### 方便阅读

大家可以发现，当用重音符把列名和表名括起来时，会使得我们的查询语句更加易读。
比如说当你习惯于用小写字母书写语句时：

```sql
select student_name, AVG(test_score) from student group by group
select `student_name`, AVG(`test_score`) from `student` group by `group`
```

请移步查看MySQL官方文档[Keywords and Reserved Words](https://dev.mysql.com/doc/refman/5.5/en/keywords.html)。带有(R)标记的那些就是保留字。其他的只是关键字。保留字需要大家特别注意。

## 第五章：NULL

### 5.1小节：NULL的用法

* 未知数据，比如end_date，rating等
* 可选数据，比如middle_initial（虽说这里用空字符串更好点）
* 0/0，某些计算的结果，比如说0除以0
* 别的什么？

### 5.2小节：检测是否NULL

* 可以用IS NULL或者IS NOT NULL，= NULL是没用的
* x <=> y是“NULL类型安全的”比较

在```LEFT JOIN```语句中用来得到在表a中且不在表b中数据：

```sql
SELECT ...
FROM a
LEFT JOIN b ON ...
WHERE b.id IS NULL
```

## 第六章：Limit和Offset

### 6.1小节：Limit和Offset的关系

考虑如下表数据：

|id|username|
|--|--|
|1|User1|
|2|User2|
|3|User3|
|4|User4|
|5|User5|

为了能对[SELECT查询](https://dev.mysql.com/doc/refman/5.7/en/select.html)查出来的数据条数进行限制，我们可以用带1个或2个正整数参数（包括0在内）的LIMIT语句。

#### 带一个参数的```LIMIT```语句

当只是用一个参数时，结果集就只是被这个参数限定，示例如下：

```sql
SELECT * FROM users ORDER BY id ASC LIMIT 2
```

|id|username|
|--|--|
|1|User1|
|2|User2|

如果把该参数设置成0，那么结果集返回为空。
注意ORDER BY语句的重要性，它的使用将会导致返回结果集的不同（当用其他列排序时）。

#### 带两个参数的```LIMIT```语句

当LIMIT语句带两个参数时：
* 第一个参数代表从哪一行开始展示结果集，这个数通常被叫做offset偏移量，因为它代表着被限定的结果集首行前面有多少行。这个数可以是0。
* 第二个参数代表返回结果集的最大行数（类似一个参数的情况）。

所以，如下语句：

```sql
SELECT * FROM users ORDER BY id ASC LIMIT 2, 3
```

将会返回：
|id|username|
|--|--|
|3|User3|
|4|User4|
|5|User5|

注意当offset偏移量为0时，等效于带1个参数的LIMIT语句。这就意味着如下两个查询语句：

```sql
SELECT * FROM users ORDER BY id ASC LIMIT 0, 2
SELECT * FROM users ORDER BY id ASC LIMIT 2
```

效果一样，都返回一样的数据：

|id|username|
|--|--|
|1|User1|
|2|User2|

#### OFFSET关键字：作为另一种选择的语法

还有一种带两个参数的LIMIT语句用法，就是使用OFFSET关键字，比如：

```sql
SELECT * FROM users ORDER BY id ASC LIMIT 2 OFFSET 3
```

结果如下：
将会返回：
|id|username|
|--|--|
|4|User4|
|5|User5|

注意，这种写法和之前那种写法相当于两个数字替换了位置：

* 第一个参数代表需要返回结果集的行数；
* 第二个参数代表offset偏移量。


## 第七章：创建数据库

## 第八章：使用变量

## 第九章：MySQL中的注释

## 第十章：INSERT语句

### 10.1小节：INSERT，ON DUPLICATE KEY UPDATE

```sql
INSERT INTO `table_name`
(`index_field`, `other_field_1`, `other_field_2`)
VALUES
('index_value', 'insert_value', 'other_value')
ON DUPLICATE KEY UPDATE
`other_field_1` = 'update_value',
`other_field_2` = VALUES(`other_field_2`);
```

这个语句将会在表table_name中插入指定数据，但是如果唯一键已经存在，它将会把列other_field_1更新为一个新值。
当你更新重复键时，有时更方便的做法是使用```VALUES()```语句，这样可以获取到传递给INSERT语句的原始值，而不是直接指定一个新值。用这种方式，你可以使用```INSERT```和```UPDATE```去设定一个新值。比如上面例子中，你可以使用```INSERT```将other_field_1列设置成insert_value或者用```UPDATE```将其更新为update_value，而other_field_2被设置成other_value。

特别注意下，INSERT ON DUPLICATE KEY UPDATE（IODKU）工作的前提是数据库中包含唯一键。这个唯一键可以是主键也可不是。它可以是单列上的唯一键，也可以是多列上的唯一键（组合键）。

### 10.2小节：插入多行

```sql
INSERT INTO `my_table` (`field_1`, `field_2`) VALUES
('data_1', 'data_2'),
('data_1', 'data_3'),
('data_4', 'data_5');
```
这是在一个INSERT语句中插入多行的简便做法。
这种“批量”插入比一个一个插入快得多。具体来说，在批量模式插入100条数据比一条一条插入这些数据要快10倍。

#### 忽略已经存在的行

当我们插入大量数据时，比较倾向的做法是把那些由于列限制比如重复主键导致插入语句失败的数据忽略掉。这种情况我们可以使用INSERT IGNORE。

考虑如下事例：

```sql
SELECT * FROM `people`;
--- Produces:
+----+------+
| id | name |
+----+------+
| 1 | john |
| 2 | anna |
+----+------+
INSERT IGNORE INTO `people` (`id`, `name`) VALUES
('2', 'anna'), --- Without the IGNORE keyword, this record would produce an error
('3', 'mike');
SELECT * FROM `people`;
--- Produces:
+----+--------+
| id | name |
+----+--------+
| 1 | john |
| 2 | anna |
| 3 | mike |
+----+--------+
```

要注意的一点是，```INSERT IGNORE```会把其他一些异常报错一起忽略掉，下面是MySQL官方的说法：
> 如果没有设置```IGNORE```，则那些会报错的数据转换会终止sql语句的执行。如果设置了```IGNORE```，则不合法的数据会被调整成最接近的合法数据并插入；会产生警告消息，但是不会终止语句执行。

注意：下面一部分内容是为了章节的完整性加入的，但是不作为最佳实践（如果我们的表多加了一列的话，这个语句是不能成功执行的）。

如果你在```INSERT```语句中设置了每一列的对应数据，那么可以省略描述列的列表书写，比如：

```sql
INSERT INTO `my_table` VALUES
('data_1', 'data_2'),
('data_1', 'data_3'),
('data_4', 'data_5');
```

### 10.3小节：基本的insert

```sql
INSERT INTO `table_name` (`field_one`, `field_two`) VALUES ('value_one', 'value_two');
```
在这个小例子中，table_name是我们要插入数据的表，field_one和fiel_two是我们要插入数据的列，value_one和value_two是我们分别要插入的数据。

编码时的最佳实践是把你要插入数据的列都写出来，这样当表结构有更改加入新的列后，如果你不列出的话，这个语句会报错。

### 10.4小节：带有AUTO_INCREMENT和LAST_INSERT_ID()的insert语句

当一个表里有自增主键时候，我们一般不会直接往那一列插入数据。取而代之的是，我们设置其他所有列的数据，然后获取到这个新数据id：

```sql
CREATE TABLE t (
id SMALLINT UNSIGNED AUTO_INCREMENT NOT NULL,
this ...,
that ...,
PRIMARY KEY(id) );
INSERT INTO t (this, that) VALUES (..., ...);
SELECT LAST_INSERT_ID() INTO @id;
INSERT INTO another_table (..., t_id, ...) VALUES (..., @id, ...);
```
需要注意的是LAST_INSERT_ID()是和会话绑定的，所以即使多个连接同时插入同一张表，每个连接获取的都是自己会话的id。

## 第十一章：DELETE语句

## 第十二章：UPDATE语句

## 第十三章：ORDER BY语句

## 第十四章：GROUP BY语句

## 第十五章：Error 1055: ONLY_FULL_GROUP_BY: 某某 is not in GROUP BY clause ...

## 第十六章：Join语句

## 第十七章：Join语句中：join三张表，都有叫id的列

## 第十八章：UNION语句

## 第十九章：算术

## 第二十章：字符串相关操作
|函数名|描述|
|----|----|
|ASCII()|返回字符串最左边的字符的ascii码数值|
|BIN()|返回一个数字的二进制表示字符串|
|BIT_LENGTH()|返回参数的二进制位数|
|CHAR()|返回整数所代表的字符|
|CHAR_LENGTH()|返回参数中有多少个字符|
|CHARACTER_LENGTH()|和CHAR_LENGTH()一个意思|
|CONCAT()|返回一个连接起来的字符串|
|CONCAT_WS()|返回带分隔符的连接起来的字符串|
|ELT()|返回指定索引上字符串列表中的的字符串|
|EXPORT_SET()|数字转成位后，1映射为On值，0映射为Off值，然后拼接起来|
|FIELD()|返回第一个参数在后续参数列表中的索引（位置）|
|FIND_IN_SET()|返回第一个参数在第二个参数中的索引（位置）|
|FORMAT()|返回一个格式化的数组，可以设置小数位数|
|FROM_BASE64()|将base64编码后的字符串解码并返回|
|HEX()|返回一个数字或字符串的16进制表示|
|INSERT()|插入一个子字符串，位置可以设定，最大字符数也可以设定|
|INSTR()|返回子串首次出现的索引|
|LCASE()|和LOWER()一样|
|LEFT()|返回从左边开始指定个数的字符|
|LENGTH()|返回给定字符串的字节数|
|LIKE|简单模式匹配|
|LOAD_FILE()|加载指定名称的文件|
|LOCATE()|返回子串首次出现的位置|
|LOWER()|返回参数的小写字母形式|
|LPAD()|返回在参数左边填充了指定字符串的新字符串|
|LTRIM()|去除开头的空格|
|MAKE_SET()|返回一个逗号分隔的字符串，是由数字所代表的二进制位决定|
|MATCH|用来执行全文搜索|
|MID()|返回从指定位置开始的子串|
|NOT LIKE|简单模式匹配的否定形式|
|NOT REGEXP|正则表达式的否定形式|
|OCT()|返回一个数字的8进制表示|
|OCTET_LENGTH()|和LENGTH()一样|
|ORD()|返回参数最左边字符的字符码|
|POSITION()|和LOCATE()一样|
|QUOTE()|转义参数，用于SQL语句中|
|REGEXP|用正则表达式的模式匹配|
|REPEAT()|将某字符串重复指定次数|
|REPLACE()|将指定字符串替换掉|
|REVERSE()|翻转字符串中的字符|
|RIGHT()|返回右边开始指定个数的字符|
|RLIKE|和REGEXP一样|
|RPAD()|在末尾追加指定次数字符串|
|RTRIM()|去除末尾的空格|
|SOUNDEX()|返回soundex的字符串，发音相似的|
|SOUNDS LIKE|比较发音|
|SPACE()|返回一个包含指定个数空格的字符串|
|STRCMP()|比较两个字符串|
|SUBSTR()|返回指定子串|
|SUBSTRING()|返回指定子串|
|SUBSTRING_INDEX()|返回一个截取到指定次数分隔符之前的子串|
|TO_BASE64()|返回一个将参数base64编码后的字符串|
|TRIM()|把前导和后置的空格全部去除|
|UCASE()|和UPPER()一样|
|UNHEX()|返回一个16进制代表的字符|
|UPPER()|转成大写|
|WEIGHT_STRING()|返回一个字符串的重量，这个重量是一个二进制串，代表字符串的排序和比较值|




## 第二十一章：操作日期和时间

## 第二十二章：处理时区

## 第二十三章：正则表达式

## 第二十四章：视图

## 第二十五章：建表

## 第二十六章：改表

## 第二十七章：删表

## 第二十八章：MySQL锁

### 28.1小节：行级锁

如果我们的表用的是InnoDB存储引擎，那么MySQL自动会使用行级锁，这样多个事务可以同时对同一张表进行读写操作，而不用相互之间进行等待。

如果两个事务要修改同一行且同时使用了行级锁，其中一个事务就必须等待直到另外一个事务完成。
也可以通过在想要更改的行上面使用```SELECT ... FOR UPDATE```来显示地使用行级锁。

下面使用两个连接来详细的说明行级锁：

连接1

```sql
START TRANSACTION;
SELECT ledgerAmount FROM accDetails WHERE id = 1 FOR UPDATE;
```

在以上连接当中，通过语句```SELECT ... FOR UPDATE```显示地获取了行级锁。

连接2

```sql
UPDATE accDetails SET ledgerAmount = ledgerAmount + 500 WHERE id=1;
```
此时，当我们在连接2中需要对同一行进行更新时，就必须等待连接1中的事务完成或者超时后抛异常，关于这个锁超时的设置，大家可以参考innodb_lock_wait_timeout属性，默认值是50秒。

```
Error Code: 1205. Lock wait timeout exceeded; try restarting transaction
```

```
---TRANSACTION 1973004, ACTIVE 7 sec updating
mysql tables in use 1, locked 1
LOCK WAIT 2 lock struct(s), heap size 360, 1 row lock(s)
MySQL thread id 4, OS thread handle 0x7f996beac700, query id 30 localhost root update
UPDATE accDetails SET ledgerAmount = ledgerAmount + 500 WHERE id=1
------- TRX HAS BEEN WAITING 7 SEC FOR THIS LOCK TO BE GRANTED:
```

连接2

```sql
UPDATE accDetails SET ledgerAmount = ledgerAmount + 250 WHERE id=2;
```

```sql
1 row(s) affected
```
从上面看到，如果在连接2中试图去更新另外一行的话会正常执行。

连接1

```sql
UPDATE accDetails SET ledgerAmount = ledgerAmount + 750 WHERE id=1;
COMMIT;
```

```sql
1 row(s) affected
```

此时行级锁就被释放了，因为连接1中的事务已经提交了。

连接2

```sql
UPDATE accDetails SET ledgerAmount = ledgerAmount + 500 WHERE id=1;
```

```sql
1 row(s) affected
```

当连接1中的事务完成后释放了行级锁，在连接2中的更新操作就可以正常执行完成了。

### 28.2小节：Mysql锁

表级锁在当你使用引擎是MyISAM时候是一个非常重要的工具，但是当你使用的引擎是InnoDB的时候并不是这样。如果你在使用InnoDB引擎的时候要使用表级锁，你就得重新思考一下你处理事务的方式了。

MySQL可以让客户端会话在与别的会话合作，需要访问数据表时候显示地获取表级锁；亦或是当一个会话需要排他性访问数据表时候，阻止其他会话在此时对数据表进行修改。一个会话只能由他自己来获取和释放锁。也就是说，一个会话不能帮另一个会话获取锁，同时也不能释放由其他会话所持有的锁。

锁命令可以用来模拟事务或者在更新数据表时提升速度。本章后续会对此进行更详细的解释。

命令：```LOCK TABLES table_name READ|WRITE;```

你只能为一个表指定锁类型；

示例（读锁）：

```sql
LOCK TABLES table_name READ;
```

示例（写锁）：

```sql
LOCK TABLES table_name WRITE;
```

查看是否使用了表锁，用下列命令

```sql
SHOW OPEN TABLES;
```

释放或者去除所有的表锁，用下列命令：

```sql
UNLOCK TABLES;
```

示例：

```sql
LOCK TABLES products WRITE:
INSERT INTO products(id,product_name) SELECT id,old_product_name FROM old_products;
UNLOCK TABLES;
```

在上面例子中，在释放了表products的表锁之前，其他任何连接会话都不能对这个表数据进行任何修改。

示例：

```sql
LOCK TABLES products READ:
UNLOCK TABLES;
```

在上面例子中，其他任何连接会话都可以读取到表products内容，不管本会话是否释放了表products的表锁。


## 第二十九章：错误码

## 第三十章：存储例程（过程和函数）

## 第三十一章：索引和键

### 31.1小节：创建索引

```sql
-- 在表my_table的列name上创建索引
CREATE INDEX idx_name ON my_table(name);
```

### 31.2小节：创建唯一索引

唯一索引可以防止在表中插入重复数据。在组成唯一索引的列中，可以插入NULL值（毕竟，从定义上来说，一个NULL值不同于任何值，包括另一个NULL值）

```sql
-- 在表my_table的列name上创建唯一索引
CREATE UNIQUE INDEX idx_name ON my_table(name);
```

### 31.3小节：自增键

```sql
CREATE TABLE (
id INT UNSIGNED NOT NULL AUTO_INCREMENT,
...
PRIMARY KEY(id),
... );
```

格外注意：

* 如果你没有在INSERT语句中特别指定，自增键会从1开始，并且自增1，或者你直接指定它为NULL。
* 这个id基本上都是互不相同，然而...
* 

不易察觉的注意点：

* 服务重启时，自增主键的“下一个”值是按照MAX(id) + 1来“计算”的。
* 如果在服务关闭或崩溃前的最后操作是删除了最大的id，此时这个id将被重复使用（这个特性是引擎相关的）。因此，不要相信自增会永久不唯一；他们只是在每个时刻不唯一。
* 对于多主或集群解决方案，注意auto_increment_offset和auto_increment_increment值。
* 完全可以将其他列设置为```主键```，或者直接在id上创建索引```INDEX(id)```。（在某些场景下，这是一种优化手段）


## 第三十二章：全文搜索

## 第三十三章：PREPARE STATEMENTS

## 第三十四章：JSON

## 第三十五章：从JSON类型数据中解析数据

## 第三十六章：MySQL admin

## 第三十七章：触发器

## 第三十八章：配置和调优

## 第三十九章：事件

## 第四十章：枚举

## 第四十一章：在Docker中安装MySQL容器

## 第四十二章：字符集和校对

## 第四十三章：MyISAM引擎

### 43.1小节：把引擎改为MyISAM

```sql
CREATE TABLE foo (
...
) ENGINE=MyISAM;
```

## 第四十四章：从MyISAM转到InnoDB

## 第四十五章：事务

### 45.1小节：开启事务

一个事务就是一组有序的SQL语句，比如select、insert、update或者delete，他们将会作为一整个工作单元来执行。

换句话说，除非语句组里的每个操作都执行成功，否则一个事务是不可能完成的。如果语句组中有任何一个操作失败的话，整个事务将会失败。

银行转账将会是最好的例子。假设有一个在两个账户之间的转账操作。为了完成整个流程，你将会下如下的SQL语句：

1. 检查第一个账户的要转账金额的可用性（是否有这么多钱可以转账）
2. 从第一个账户中减去转账金额
3. 存入第二个账户里

如果这个序列中任何一条失败的话，整个账户状态都应该回滚到最初的状态。

### ACID：事务的特性

事务有如下4个基本的特性：

* 原子性：保证工作组中的所有操作都成功完成；否则，整个事务将会失败，并且之前的所有操作将会回滚到它们原本的状态。
* 一致性：保证数据库在成功提交的事务的基础上正确的改变数据状态。
* 隔离性：事务之间的操作不是互相依赖的，是对对方透明的，不可见的。
* 持久性：保证一个提交的事务的结果和影响都持久化了，即使出现系统崩溃，修改的数据也不会丢失。

## 第四十六章：日志文件

## 第四十七章：集群

## 第四十八章：分区

## 第四十九章：复制

## 第五十章：用mysqldump备份

## 第五十一章：mysqlimport

## 第五十二章：LOAD DATA INFILE

### 52.1小节：通过使用```LOAD DATA INFILE```来将大量数据导入数据库

考虑如下情况，假设你要把一个分号分隔的CSV文件导入数据库中。

```csv
1;max;male;manager;12-7-1985
2;jack;male;executive;21-8-1990
...
1000000;marta;female;accountant;15-6-1992
```

创建需要导入数据的表。

```sql
CREATE TABLE `employee` ( `id` INT NOT NULL ,
`name` VARCHAR NOT NULL,
`sex` VARCHAR NOT NULL ,
`designation` VARCHAR NOT NULL ,
`dob` VARCHAR NOT NULL );
```

使用如下语句将数据导入这张表里：

```sql
LOAD DATA INFILE 'path of the file/file_name.txt'
INTO TABLE employee
FIELDS TERMINATED BY ';' //specify the delimiter separating the values
LINES TERMINATED BY '\r\n'
(id,name,sex,designation,dob)
```
考虑一下日期格式不标准的情况：

```csv
1;max;male;manager;17-Jan-1985
2;jack;male;executive;01-Feb-1992
...
1000000;marta;female;accountant;25-Apr-1993
```
在这种情况下，你可以在把数据插入到表里之前，对日期数据进行格式化，如下：

```sql
LOAD DATA INFILE 'path of the file/file_name.txt'
INTO TABLE employee
FIELDS TERMINATED BY ';' //specify the delimiter separating the values
LINES TERMINATED BY '\r\n'
(id,name,sex,designation,@dob)
SET date = STR_TO_DATE(@date, '%d-%b-%Y');
```

上面例子里并没有涉及到```LOAD DATA INFILE```所有的功能，想获取更详细的信息，大家可以参照官方文档[点我](https://dev.mysql.com/doc/refman/5.7/en/load-data.html)。

### 52.2有重复数据的LOAD DATA

如果你使用```LOAD DATA INFILE```命令往表里导入数据时候，表里已经存在你要导入的数据了，这时导入会因为重复数据而失败。为了解决这个问题，有如下几个方案。

#### LOAD DATA LOCAL

这个选项已经在你的服务端开启，用处是将存在于客户端而不是服务端的文件数据导入到数据库。其中的一个副作用就是，对于需要保持唯一性的重复数据，它会直接忽略掉。

```sql
LOAD DATA LOCAL INFILE 'path of the file/file_name.txt'
INTO TABLE employee
```

#### LOAD DATA INFILE 'fname' REPLACE

当我们使用replace关键字时，重复的唯一性数据或者主键将会有如下表现，之前存在于表里的数据将会被新的替换掉：

```sql
LOAD DATA INFILE 'path of the file/file_name.txt'
REPLACE INTO TABLE employee
```

#### LOAD DATA INFILE 'fname' IGNORE

这个正好和REPLACE关键字相反，会保留之前存在于表里的数据，把新的直接忽略。这个行为类似于前面我们讲的LOCAL的那种模式，不一样的就是这里的这个文件不能是存在客户端的。

```sql
LOAD DATA INFILE 'path of the file/file_name.txt'
IGNORE INTO TABLE employee
```

#### 通过使用中间表导入数据

有时候，不管是忽略还是替换都不是最佳方案。我们需要根据实际数据内容来作出决定，在这种情境下，最好的办法是把数据导入到中间表里，然后再从中间表转移数据。

```sql
INSERT INTO employee SELECT * FROM intermediary WHERE ...
```

### 52.3小节：将CSV文件数据导入到MySQL表里

下面的命令就是把CSV文件中数据导入到MySQL表里，此处的CSV文件数据有着相同的列，并且符合CSV的引用和转移规则。

```sql
load data infile '/tmp/file.csv'
into table my_table
fields terminated by ','
optionally enclosed by '"'
escaped by '"'
lines terminated by '\n'
ignore 1 lines; -- skip the header row
```

## 第五十三章：MySQL unions

## 第五十四章：MySQL客户端

## 第五十五章：临时表

## 第五十六章：个性化PS1

## 第五十七章：对稀疏数据和缺失数据的处理

## 第五十八章：在不同编程语言中用UTF-8连接MySQL

## 第五十九章：亚秒精度的时间

## 第六十章：一对多

## 第六十一章：服务器信息

## 第六十二章：SSL连接设置

## 第六十三章：创建一个新用户

## 第六十四章：通过GRANTs实现安全

## 第六十五章：修改密码

## 第六十六章：在MySQL 5.7+版本中恢复或重置默认root用户密码

## 第六十七章：root密码丢失后如何恢复

## 第六十八章：MySQL性能小贴士

## 第六十九章：性能调优

## 附录A：保留关键字

## 鸣谢

## 其他
