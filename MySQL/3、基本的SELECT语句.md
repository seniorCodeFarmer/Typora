## 3、基本的 SELECT 语句



## 1、SQL概述

### 1.1、SQL背景知识

1946年，世界上第一台电脑诞生，如今，借由这台电脑发展起来的互联网已经自成江湖。在这几十年里，无数的技术、产业在这片江湖里沉浮，有的方兴未艾，有的已经几幕兴衰。但在这片浩荡的波动里，有一门技术从未消失，甚至“老当益壮”，那就是SQL。

- 45年前，也就是1974年，IBM研究员发布了一篇揭开数据库技术的论文《SEQUEL：一门结构化的英语查询语言》，直到今天这门结构化的查询语言并没有太大的变化，相比于其他语言，SQL的半衰期可以说是非常长了。

不论是前端工程师，还是后端算法工程师，都一定会和数据打交道，都需要了解如何又快又准确地提取自己想要的数据。更别提数据分析师了，他们的工作就是和数据打交道，整理不同的报告，以便指导业务决策。

SQL（Structured Query Language，结构化查询语言）是使用关系模型的数据库应用语言， 与数据直接打交道 ，由IBM上世纪70年代开发出来。后由美国国家标准局（ANSI）开始着手制定SQL标准，先后有SQL-86 ， SQL-89 ， SQL-92 ， SQL-99 等标准。

- SQL有两个重要的标准，分别是 SQL92 和 SQL99，它们分别代表了 92 年和 99 年颁布的 SQL 标 准，我们今天使用的 SQL 语言依然遵循这些标准。

不同的数据库生产厂商都支持SQL语句，但都有特有内容。

![image-20230803010948831](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230803010948831.png)



### 1.2、SQL分类

SQL在功能上主要分为如下3大类：

- DDL（Data Definition Languages 数据定义语言）：这些语句定义了不同的数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和数据表的结构。主要的语句关键词包括：CREAT、DROP、ALTER等

- DML（Data Manipulation Language数据操作语句）：用于添加、删除、更新和查询数据库记录，并检查数据完整性，主要的语句关键字包括：INSERT、DELETE、UPDATE、SELECT等
- DCL（Data Control Language数据控制语言）：用于定义数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括：GRANT、REVOKE、COMMIT、ROLLBACK、SAVEPOINT等



## 2、SQL语言的规则与规范

### 2.1、基本规则

- SQL可以写在一行或者多行。为了提高可读性，各子句分行写，必要时使用缩进
- 每条命令以；或/g、/G结束
- 关键字不能被缩写也不能分行
- 关于标点符号
  - 字符串型和日期时间类型的数据可以使用单引号(')表示
  - 列的别名，尽量使用双引号(" ")，而且不建议省略as



### 2.2、**SQL大小写规范**

- MySQL在Windows环境下是大小写不敏感的

- MySQL在Linux环境下是大小写敏感的

- - 数据库名、表名、表的别名、变量名是严格区分大小写的
  - 关键字、函数名、列名（或字段名）、列的别名（字段的别名）是忽略大小写的

- 推荐采用统一的书写规范

- - 数据库名、表名、表别名、字段名、字段别名都用小写
  - SQL关键字、函数名、绑定变量等都大写



### 2.3、注释

### 2.4、命名规则

- 数据库、表名不得超过30个字符，变量名限制为29个
- 必须只能包含 A–Z, a–z, 0–9, _共63个字符
- 数据库名、表名、字段名等对象名中间不要包含空格
- 同一个MySQL软件中，数据库不能同名；同一个库中，表不能重名；同一个表中，字段不能重名
- 必须保证你的字段没有和保留字、数据库系统或常用方法冲突。如果坚持使用，请在SQL语句中使用`（着重号）引起来
- 保持字段名和类型的一致性，在命名字段并为其指定数据类型的时候一定要保证一致性。假如数据类型在一个表里是整数，那在另一个表里可就别变成字符型了



### 2.5、数据导入命令

```sql
mysql> source d:\mysqldb.sql
```



## 3、基本的SELECT语句

### 3.1、SELECT * FROM

- 一般情况下，除非需要使用表中的所有字段数据，最好不要使用通配符“*”。使用通配符虽然可以节省输入查询语句的时间，但是获取不需要的列数据通常会减低查询和所使用的应用程序的效率。通配符的优势是，当不知道所需要的列的名称时，可以通过它获取它们。
- 在生产环境下，不推荐你直接使用SELECT * 进行查询



### 3.2、列的别名

- AS（alias），可以省略，但不建议省略
- 紧跟列名，也可以在列名和别名之间加入关键字AS，别名使用双引号，以便在别名中包含空格或特殊的字符并区分大小写。
- 列的别名可以使用一对双引号，不要使用单引号。

```sql
SELECT last_name AS "name", commission_pct comm FROM employees;
```



### 3.3、去除重复行

在SELECT语句中使用关键字DISTINCT去除重复行

```sql
SELECT DISTINCT department_id FROM employees;
```

针对于下列代码有两点需要注意：

- DISTINCT 需要放到所有列名的前面，如果写成 SELECT salary, DISTINCT department_id FROM employees 会报错。
- DISTINCT 其实是对后面所有列名的组合进行去重，你能看到最后的结果是 74 条，因为这74个部门id不同，都有salary这个属性值。如果你想要看都有哪些不同的部门（department_id），只需要写 DISTINCT department_id 即可，后面不需要再加其他的列名了。

~~~sql
SELECT DISTINCT department_id,salary FROM employees;
~~~



### 3.5、空值参与运算

所有运算符或列值遇到null值，运算的结果都为null

~~~sql
SELECT employee_id,salary,commission_pct, 12 * salary * (1 + commission_pct) AS "annual_sal" FROM employees;
~~~

这里你一定要注意，在 MySQL 里面， 空值不等于空字符串。一个空字符串的长度是 0，而一个空值的长度是空。而且，在 MySQL 里面，空值是占用空间的。



### 3.6、着重号

错误的：

~~~sql
mysql> SELECT * FROM ORDER; 
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'ORDER' at line 1
~~~

正确的：

~~~sql
mysql> SELECT * FROM `ORDER`;
~~~

我们需要保证表中的字段、表名等没有保留字、数据库系统或常用方法名冲突。如果真的相同，请在 SQL语句中使用一对``（单引号）引起来。



### 3.7、查询常数

SELECT 查询还可以对常数进行查询。对的，就是在 SELECT 查询结果中增加一列固定的常数列。这列的取值是我们指定的，而不是从数据表中动态取出的。

你可能会问为什么我们还要对常数进行查询呢？

- SQL 中的 SELECT 语法的确提供了这个功能，一般来说我们只从一个表中查询数据，通常不需要增加一个固定的常数列，但如果我们想整合不同的数据源，用常数列作为这个表的标记，就需要查询常数。
- 比如说，我们想对 employees 数据表中的员工姓名进行查询，同时增加一列字段 corporation ，这个字段固定值为“尚硅谷”，可以这样写：

~~~sql 
SELECT '尚硅谷' AS corporation, last_name FROM employees;
~~~



## 4、显示表的结构

### 4.1、使用 DESCRIBE 或 DESC 命令，表示表结构

~~~sql
DESCRIBE employees;
或
DESC employees;
~~~



### 4.2、各字段含义

- Field：表示字段名称。
- Type：表示字段类型，这里 barcode、goodsname 是文本型的，price 是整数类型的。
- Null：表示该列是否可以存储 NULL值。
- Key：表示该列是否已编制索引。PRI表示该列是表主键的一部分；UNI表示该列是UNIQUE索引的一部分；MUL表示在列中某个给定值允许出现多次。
- Default：表示该列是否有默认值，如果有，那么值是多少。
- Extra：表示可以获取的与给定列有关的附加信息，例如AUTO_INCREMENT等。































































