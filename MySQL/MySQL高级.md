# 1、Linux中MySQL的安装与使用

## 1.1、安装前说明

### 1.1.1、Linux系统及工具准备

### 1.1.2、查看是否安装过MySQL

使用rpm命令查看

![image-20230322173752861](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322173752861.png)



### 1.1.3、MySQL卸载

关闭MySQL服务

![image-20230322173901111](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322173901111.png)

查看当前MySQL安装状况

![image-20230322173946584](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322173946584.png)

![image-20230322174009164](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322174009164.png)

卸载上述命令查询出的已安装程序

![image-20230322174044073](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322174044073.png)

- 一定要确认卸载彻底

删除MySQL相关文件

- 查找相关文件目录

![image-20230322174118309](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322174118309.png)

删除上述命令找出的文件

删除my.cnf





# 2、MySQL的数据目录

## 2.1、MySQL8的主要目录结构

![image-20230322174411132](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322174411132.png)



### 2.1.1、数据库文件的存放路径

MySQL数据库文件的存放路径：/var/lib/mysql/

![image-20230322174721430](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322174721430.png)

### 2.1.2、相关命令目录

相关命令目录：/usr/bin（mysqladmin、mysqlbinlog、mysqldump等命令）和/usr/sbin



### 2.1.3、配置文件目录

配置文件目录：/usr/share/mysql-8.0（命令及配置文件），/etc/mysql（如my.cnf）





## 2.2、数据库和文件系统的关系

### 2.2.1、查看默认数据库

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image(10).png)

可以看到有4个数据库是属于MySQL自带的系统数据库：

- mysql

- - MySQL 系统自带的核心数据库，它存储了MySQL的用户账户和权限信息，一些存储过程、事件的定义信息，一些运行过程中产生的日志信息，一些帮助信息以及时区信息等。

- information_schema

- - MySQL 系统自带的数据库，这个数据库保存着MySQL服务器维护的所有其他数据库的信息 ，比如有哪些表、哪些视图、哪些触发器、哪些列、哪些索引。这些信息并不是真实的用户数据，而是一些描述性信息，有时候也称之为元数据 。在系统数据库 information_schema 中提供了一些以 innodb_sys 开头的表，用于表示内部系统表。

- performance_schema

- - MySQL 系统自带的数据库，这个数据库里主要保存MySQL服务器运行过程中的一些状态信息，可以用来监控 MySQL 服务的各类性能指标 。包括统计最近执行了哪些语句，在执行过程的每个阶段都花费了多长时间，内存的使用情况等信息。

- sys

- - MySQL 系统自带的数据库，这个数据库主要是通过视图的形式把 information_schema 和 performance_schema 结合起来，帮助系统管理员和开发人员监控 MySQL 的技术性能。



### 2.2.2、数据库在文件系统中的表示

![image-20230322175249665](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322175249665.png)

这个数据目录下的文件和子目录比较多，除了 information_schema 这个系统数据库外，其他的数据库在数据目录下都有对应的子目录。

以我的 temp 数据库为例，在MySQL5.7 中打开：

![image-20230322175405024](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322175405024.png)

在MySQL8中打开：

![image-20230322175448862](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322175448862.png)





### 2.2.3、表在文件系统中的表示

#### 2.2.3.1、InnoDB存储引擎模式

##### 1、表结构

为了保存表结构， InnoDB 在数据目录下对应的数据库子目录下创建了一个专门用于描述表结构的文件 ，文件名是这样：**表名.frm**。

比方说我们在temp数据库下创建一个名为 test 的表，那么数据库temp对应的子目录下就会创建一个名为 test.frm 的用于描述表结构的文件。.frm文件的格式在不同的平台上都是相同的。这个后缀名为.frm是以二进制格式存储的，我们直接打开是乱码。

##### 2、表中数据和索引

系统表空间（system tablespace）

- 默认情况下，InnoDB会在数据目录下创建一个名为 ibdata1 、大小为 12M 的文件，这个文件就是对应的系统表空间在文件系统上的表示。怎么才12M？注意这个文件是自扩展文件 ，当不够用的时候它会自己增加文件大小。 当然，如果你想让系统表空间对应文件系统上多个实际文件，或者仅仅觉得原来的 ibdata1 这个文件名难听，那可以在MySQL启动时配置对应的文件路径以及它们的大小，比如我们这样修改一下my.cnf 配置文件：

```properties
[server] 
innodb_data_file_path=data1:512M;data2:512M:autoextend
```

![image-20230322175733420](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322175733420.png)

独立表空间（file-per-table tablespace）

- 在MySQL5.6.6以及之后的版本中，InnoDB并不会默认的把各个表的数据存储到系统表空间中，而是为每一个表建立一个独立表空间 ，也就是说我们创建了多少个表，就有多少个独立表空间。使用独立表空间来存储表数据的话，会在该表所属数据库对应的子目录下创建一个表示该独立表空间的文件，文件名和表名相同，只不过添加了一个 .ibd 的扩展名而已，所以完整的文件名称长这样： **表名.ibd**。
- 比如：我们使用了独立表空间去存储 temp 数据库下的 test 表的话，那么在该表所在数据库对应的temp目录下会为 test 表创建这两个文件： test.frm、test.ibd。
- MySQL8.0将.frm.ibd统一为.ibd了。
- 其中 test.ibd 文件就用来存储 test 表中的数据和索引。



系统表空间与独立表空间的设置

- 我们可以自己指定使用系统表空间还是独立表空间来存储数据，这个功能由启动参数 innodb_file_per_table 控制，比如说我们想刻意将表数据都存储到系统表空间时，可以在启动 MySQL服务器的时候这样配置：

```properties
[server] 
innodb_file_per_table=0 # 0：代表使用系统表空间； 1：代表使用独立表空间
```

- 默认情况

![image-20230322180003845](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322180003845.png)

其他类型的表空间

- 随着MySQL的发展，除了上述两种老牌表空间之外，现在还新提出了一些不同类型的表空间，比如通用表空间（general tablespace）、临时表空间（temporary tablespace）等。



##### 3、疑问

.frm 在MySQL8中不存在了，那去哪里了呢？

- 这就需要解析ibd文件。Oracle官方将frm文件的信息及更多信息移动到叫做序列化字典信息（Serialized Dictionary Information，SDI），SDI被写在ibd文件内部。
- 为了从ibd文件中提取SDI信息，Oracle提供了一个应用程序ibd2sdi。
- 此工具MySQL8自带

查看表结构，到存储ibd文件的目录下，执行下面的命令

```properties
ibd2sdi --dump-file=xxx.txt xxx.ibd
```

结果如图

![image-20230322180158606](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322180158606.png)



#### 2.2.3.2、MyISAM存储引擎模式

##### 1、表结构

- 在存储表结构方面， MyISAM 和 InnoDB 一样，也是在数据目录下对应的数据库子目录下创建了一个专门用于描述表结构的文件：**表名.frm。**




##### 2、表中数据和索引

- MySQL5.7，在MyISAM中的索引全部都是二级索引 ，该存储引擎的数据和索引是分开存放的。所以在文件系统中也是使用不同的文件来存储数据文件和索引文件，同时表数据都存放在对应的数据库子目录下。假如 test 表使用MyISAM存储引擎的话，那么在它所在数据库对应的 temp 目录下会为 test 表创建这三个文件：


```
test.frm 存储表结构 
test.MYD 存储数据 (MYData) 
test.MYI 存储索引 (MYIndex)
```

- 举例：创建一个 MyISAM 表，使用 ENGINE 选项显式指定引擎。因为 InnoDB 是默认引擎。


```sql
CREATE TABLE `student_myisam` (
`id` bigint NOT NULL AUTO_INCREMENT,
`name` varchar(64) DEFAULT NULL,
`age` int DEFAULT NULL,
`sex` varchar(2) DEFAULT NULL,
PRIMARY KEY (`id`)
)ENGINE=MYISAM AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb3;
```

在MySQL8中：

![image-20230322183049164](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322183049164.png)



### 2.2.4、小结

举例： 数据库a ，表b 。

如果表b采用 InnoDB ，data\a中会产生1个或者2个文件：

- b.frm ：描述表结构文件，字段长度等
- 如果采用系统表空间模式的，数据信息和索引信息都存储在 ibdata1 中
- 如果采用独立表空间存储模式，data\a中还会产生 b.ibd 文件（存储数据信息和索引信息）

此外：

- ① MySQL5.7 中会在data/a的目录下生成 db.opt 文件用于保存数据库的相关配置。比如：字符集、比较规则。而MySQL8.0不再提供db.opt文件。
- ② MySQL8.0中不再单独提供b.frm，而是合并在b.ibd文件中。

如果表b采用 MyISAM ，data\a中会产生3个文件：

- MySQL5.7 中：b.frm ：描述表结构文件，字段长度等
- MySQL8.0 中 b.xxx.sdi ：描述表结构文件，字段长度等
- b.MYD (MYData)：数据信息文件，存储数据信息（如果采用独立表存储模式）
- b.MYI (MYIndex)：存放索引信息文件





### 2.2.5、视图在文件系统中的表示

我们知道MySQL中的视图其实是虚拟的表，也就是某个查询语句的一个别名而已，所以在存储视图的时候是不需要存储真实的数据的，只需要把它的结构存储起来就行了。和表一样，描述视图结构的文件也会被存储到所属数据库对应的子目录下边，只会存储一个视图名. frm的文件。如下图中的：emp_ details_view. frm

![image-20230322183314614](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230322183314614.png)



### 2.2.6、其他的文件

除了我们上边说的这些用户自己存储的数据以外，数据目录下还包括为了更好运行程序的一些额外文件，主要包括这几种类型的文件

- 服务器进程文件：我们知道每运行一个MySQL服务器程序，都意味着启动一个进程。MySQL 服务器会把自己的进程ID写入到一个文件中。
- 服务器日志文件：在服务器运行过程中，会产生各种各样的日志，比如常规的查询日志、错误日志、二进制日志、redo日志等。这些日志各有各的用途，后面讲解。
- 默认/自动生成的SSL和RSA证书和密钥文件：主要是为了客户端和服务器安全通信而创建的一些文件。





# 3、用户与权限管理





# 4、逻辑架构

## 4.1、逻辑架构剖析

### 4.1.1、服务器处理客户端请求

- 首先MySQL是典型的C/S架构，即Client/Server架构，服务器端程序使用的mysqld。
- 不论客户端进程和服务器进程是采用哪种方式进行通信，最后实现的效果都是：客户端进程向服务器进程发送一段文本（SQL语句），服务器进程处理后再向客户端进程发送一段文本（处理结果）。
- 那服务器进程对客户端进程发送的请求做了什么处理，才能产生最后的处理结果呢？这里以查询请求为例展示：

![image-20230924224830691](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924224830691.png)

![image-20230924230435697](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924230435697.png)



#### 1、Connectors（客户端）

- Connectors，指的是不同语言与SQL的交互。MySQL 首先是一个网络程序，在TCP之上定义了自己的应用层协议。所以要使用MySQL，我们可以编写代码，跟MySQL Server 建立TCP连接， 之后按照其定义好的协议进行交互，或者比较方便的办法是调用SDK，比如Native C API、JDBC、 PHP等各语言MySQL Connector，或者通过ODBC。但通过SDK来访问MySQL，本质上还是在TCP连接上通过MySQL协议跟MySQL进行交互。



#### 2、第一层：连接层（服务端）

- 系统（客户端）访问MySQL服务器前，做的第一件事就是建立TCP连接。
- 经过三次握手建立连接成功后，MySQL服务器对TCP传输过来的账号密码做身份认证、权限获取。
  - 用户名或密码不对，会收到一个Access denied for user错误，客户端程序结束执行。
  - 用户名密码认证通过，会从权限表查出账号拥有的权限与连接关联，之后的权限判断逻辑，都将依赖于此时读到的权限。
- 接着我们来思考一个问题：一个系统只会和MySQL服务器建立一个连接吗? 只能有一个系统和MySQL服务器建立连接吗?
  - 当然不是，多个系统都可以和MySQL服务器建立连接，每个系统建立的连接肯定不止一个。所以，为了解决TCP无限创建与TCP频繁创建销毁带来的资源耗尽、性能下降问题。MySQL 服务器里有专门的TCP连接池限制连接数，采用长连接模式复用TCP连接，来解决上述问题。
- TCP连接收到请求后，必须要分配一个线程专门与这个客户端的交互。 所以还会有个线程池，去走后面的流程。每一个连接从线程池中获取线程，省去了创建和销毁线程的开销。这些内容我们都归纳到MySQL的连接管理组件中。所以连接管理的职责是负责认证、管理连接、获取权限信息。



#### 3、第二层：服务层

- 第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如存储过程、函数等。

- 在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化：如确定查询表的顺序，是否利用索引等，最后生成相应的执行操作。

- 如果是SELECT语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

- SQL Interface：SQL接口

  - 接收用户的SQL命令，并且返回用户需要查询的结果。比如SELECT .. FROM就是调用SQL Interface。
  - MySQL支持DML （数据操作语言）、DDL （数据定义语言）、存储过程、 视图、触发器、自定义函数等多种SQL语言接口。

- Parser：解析器

  - 在解析器中对SQL语句进行语法分析、语义分析。将SQL语句分解成数据结构，并将这个结构传递到后续步骤，以后SQL语句的传递和处理就是基于这个结构的。如果在分解构成中遇到错误，那么就说明这个SQL语句是不合理的。
  - 在SQL命令传递到解析器的时候会被解析器验证和解析，并为其创建语法树，并根据数据字典丰富查询语法树，会验证该客户端是否具有执行该查询的权限。创建好语法树后，MySQL还会对SQL查询进行语法上的优化，进行查询重写。

- Optimizer：查询优化器

  - SQL语句在语法解析之后、查询之前会使用查询优化器确定SQL语句的执行路径，生成一个执行计划。

  - 这个执行计划表明应该使用哪些索引进行查询（全表检索还是使用索引检索），表之间的连接顺序如何，最后会按照执行计划中的步骤调用存储引擎提供的方法来真正的执行查询，并将查询结果返回给用户。

  - 它使用`选取-投影-连接`策略进行查询。例如：
    - `SELECT id, name FROM student WHERE gender ='女'`
  
  - 这个SELECT查询先根据WHERE语句进行选取，而不是将表全部查询出来以后再进行gender过滤。
  
  - 这个SELECT查询先根据id和name进行属性投影，而不是将属性全部取出以后再进行过滤，将这两个查询条件连接起来生成最终查询结果。
  
- Caches & Buffers：查询缓存组件

  - MySQL内部维持着一些Cache和Buffer，比如Query Cache用来缓存一条SELECT语句的执行结果， 如果能够在其中找到对应的查询结果，那么就不必再进行查询解析、优化和执行的整个过程了，直接将结果反馈给客户端。
  - 这个缓存机制是由一系列小缓存组成的。比如表缓存，记录缓存，key缓存，权限缓存等。
  - 这个查询缓存可以在不同客户端之间共享。
  - 从MySQL 5.7.20开始，不推荐使用查询缓存，并在MySQL8. 0中删除。



#### 4、第三层：引擎层

- 和其它数据库相比，MySQL有点与众不同，它的架构可以在多种不同场景中应用并发挥良好作用，主要体现在存储引擎的架构上，插件式的存储引擎架构将查询处理和其它的系统任务以及数据的存储提取相分离。这种架构可以根据业务的需求和实际需要选择合适的存储引擎。同时开源的MySQL还允许开发人员设置自己的存储引擊。
- 这种高效的模块化架构为那些希望专针对特定应用程序需求（例如数据仓库、事务处理或高可用性情况）的人提供了巨大的好处，同时享受使用一组独立于任何接口和服务的优势存储引擎。
- 插件式存储引擎层（Storage Engines），真正的负责了MySQL中数据的存储和提取，对物理服务器级别维护的底层数据执行操作，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取。

![image-20230924234854271](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924234854271.png)



#### 5、存储层

- 所有的数据，数据库、表的定义，表的每一行的内容，索引，都是存在文件系统上，以文件的方式存在的，并完成与存储引擎的交互。当然有些存储引擎比如InnpDB，也支持不使用文件系统直接管理裸设备，但现代文件系统的实现使得这样做没有必要了。在文件系统之下，可以使用本地磁盘，可以使用DAS、NAS、SAN等各种存储系统。



#### 6、小结

- MySQL 简化架构图：

![image-20230924235105307](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924235105307.png)



- 简化为三层结构：

- - 连接层：客户端和服务器端建立连接，客户端发送SQL至服务器端；
  - SQL层（服务层）：对SQL语句进行查询处理；与数据库文件的存储方式无关；
  - 存储引擎层：与数据库文件打交道，负责数据的存储和读取。



## 4.2、SQL执行流程

### 4.2.1、MySQL中的SQL执行流程

![image-20230924235244556](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924235244556.png)



#### 1、查询缓存

- Server（服务端） 如果在查询缓存中发现了这条SQL语询，就会直接将结果返回给客户端；如果没有，就进入到解析器阶段。需要说明的是，因为查询缓存往往效率不高，所以在MySQL8.0之后就抛弃了这个功能。
- MySQL拿到一个查询请求后，会先到查询缓存看看，之前是不是执行过这条语句。之前执行过的语句及其结果可能会以key-value对的形式，被直接缓存在内存中。key 是查询的语句，value 是查询的结果。如果你的查询能够直接在这个缓存中找到key，那么这个value就会被直接返回给客户端。如果语句不在查询缓存中，就会继续后面的执行阶段。执行完成后， 执行结果会被存入查询缓存中。所以，如果查询命中缓存，MySQL 不需要执行后面的复杂操作，就可以直接返回结果，这个效率会很高。
- 大多数情况查询缓存就是个鸡肋，为什么呢？
  - 查询缓存是提前把查询结果缓存起来，这样下次不需要执行就可以直接拿到结果。需要说明的是，在MySQL中的查询缓存，不是缓存查询计划，而是查询对应的结果。这就意味着查询匹配的鲁棒性大大降低，`只有相同的查询操作才会命中查询缓存`。两个查询请求在任何字符上的不同（例如：空格、注释、大小写），都会导致缓存不会命中。因此MySQL的查询缓存命中率不高。
  - 同时，如果查询请求中包含某些系统函数、用户自定义变量和函数、一些系统表，如mysql、information_ schema、performance_schema数据库中的表，那这个请求就不会被缓存。以某些系统函数举例，可能同样的函数的两次调用会产生不一样的结果，比如函数NOW，每次调用都会产生最新的当前时间，如果在一个查询请求中调用了这个函数，那即使查询请求的文本信息都一样，那不同时间的两次查询也应该得到不同的结果，如果在第一查询时就缓存了，那第二次查询的时候直接使用第一次查询的结果就是错误的！
  - 此外，既然是缓存，那就有它缓存失效的时候。MySQL的缓存系统会监测涉及到的每张表，只要该表的结构或者数据被修改，如对该表使用了INSERT、UPDATE、 DELETE、 TRUNCATE TABLE、 ALTER TABLE、 DROP TABLE或DROP DATABASE 语句，那使用该表的所有高速缓存查询都将变为无效并从高速缓存中删除！对于更新压力大的数据库来说，查询缓存的命中率会非常低。



















# 13、事务基础知识

## 13.1、数据库事务概述

- 事务是数据库区别于文件系统的重要特征之一，当我们有了事务就会让数据库始终保持一致性，同时我们还能通过事务的机制恢复到某个时间点，这样可以保证已提交到数据库的修改不会因为系统崩溃而丢失。


### 13.1.1、存储引擎支持情况

- `SHOW ENGINES`命令来查看当前`MySQL`支持的存储引擎都有哪些，以及这些存储引擎是否支持事务。

![image-20231011115220618](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011115220618.png)



### 13.1.2、基本概念

- 事务：一组逻辑操作单元，使数据从一种状态变换到另一种状态。
- 事务处理的原则：保证所有事务都作为一个工作单元来执行，即使出现了故障，都不能改变这种执行方式。当在一个事务中执行多个操作时，要么所有的事务都被提交(`commit`)，那么这些修改就永久地保存下来；要么数据库管理系统将放弃所作的所有修改 ，整个事务回滚（`rollback`） 到最初状态。



### 13.1.3、事务的`ACID`特性

- 原子性（`atomicity`）

- - 原子性是指事务是不可分割的工作单位，要么全部提交，要么全部失败回滚。即要么转账成功，要么转账失败，是不存在中间的状态。如果无法保证原子性会怎么样？就会出现数据不一致的情形，`A`账户减去`100`元，而`B`账户增加`100`元操作失败，系统将无故丢失`100`元。

- 一致性（`consistency`）

- - 根据定义，一致性是指事务执行前后，数据从一个合法性状态变换到另外一个合法性状态 。这种状态是语义上的而不是语法上的，跟具体的业务有关。
  - 那什么是合法的数据状态呢？满足预定的约束的状态就叫做合法的状态。通俗一点，这状态是由你自己来定义的（比如满足现实世界中的约束）。满足这个状态，数据就是一致的，不满足这个状态，数据就是不一致的！如果事务中的某个操作失败了，系统就会自动撤销当前正在执行的事务，返回到事务操作之前的状态。
  - 举例1：A账户有200元，转账300元出去，此时A账户余额为-100元，你自然就发现了此时数据是不一致的，为什么呢？因为你定义了一个状态，账户余额必须>=0；
  - 举例2：A账户有200元，转账50元给B账户，A账户的钱扣了，但是B账户因为各种意外，余额并没有增加。你也知道此时数据是不一致的，为什么呢？因为你定义了一个状态，要求A+B的总余额必须不变。
  - 举例3：在数据表中我们将姓名字段设置为唯一性约束，这时当事务进行提交或事务发生回滚的时候，如果数据表中的姓名不唯一，就破坏了事务的一致性要求。

- 隔离性（`isolation`）

- - 事务的隔离性是指一个事务的执行不能被其他事务干扰 ，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

- 持久性（`durability`）

- - 持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的 ，接下来的其他操作和数据库故障不应该对其有任何影响。
  - 持久性是通过事务日志来保证的。日志包括了重做日志和回滚日志 。当我们通过事务对数据进行修改的时候，首先会将数据库的变化信息记录到重做日志中，然后再对数据库中对应的行进行修改。这样做的好处是，即使数据库系统崩溃，数据库重启后也能找到没有更新到数据库系统中的重做日志，重新执行，从而使事务具有持久性。

- 

- 总结
  - `ACID`是事务的四大特性，在这四个特性中，原子性是基础，隔离性是手段，一致性是约束条件，而持久性是我们的目的。
  - 数据库事务，其实就是数据库设计者为了方便起见，把需要保证原子性、隔离性、一致性和持久性的一个或多个数据库操作称为一个事务。





### 13.1.4、事务的状态

- 我们现在知道事务是一个抽象的概念，它其实对应着一个或多个数据库操作，MySQL根据这些操作所执行的不同阶段把 事务大致划分成几个状态：

- 活动的（`active`）

- - 事务对应的数据库操作正在执行过程中时，我们就说该事务处在活动的状态。

- 部分提交的（`partially committed`）

- - 当事务中的最后一个操作执行完成，但由于操作都在内存中执行，所造成的影响并没有刷新到磁盘时，我们就说该事务处在部分提交的状态。

- 失败的（`failed`）

- - 当事务处在活动的或者部分提交的状态时，可能遇到了某些错误（数据库自身的错误、操作系统错误或者直接断电等）而无法继续执行，或者人为的停止当前事务的执行，我们就说该事务处在失败的状态。

- 中止的（`aborted`）

- - 如果事务执行了一部分而变为失败的状态，那么就需要把已经修改的事务中的操作还原到事务执行前的状态。换句话说，就是要撤销失败事务对当前数据库造成的影响。我们把这个撤销的过程称之为回滚 。当回滚操作执行完毕时，也就是数据库恢复到了执行事务之前的状态，我们就说该事务处在了中止的状态。

- 提交的（`committed`）

- - 当一个处在部分提交的状态的事务将修改过的数据都同步到磁盘上之后，我们就可以说该事务处在了提交的状态。

![image-20231011120739251](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011120739251.png)

- 图中可见，只有当事务处于提交的或者中止的状态时，一个事务的生命周期才算是结束了。对于已经提交的事务来说，该事务对数据库所做的修改将永久生效，对于处于中止状态的事务，该事务对数据库所做的所有修改都会被回滚到没执行该事务之前的状态。




## 13.2、如何使用事务

### 13.2.1、显式事务

- 步骤一：`START TRANSACTION`或者`BEGIN`，作用是显式开启一个事务。

  - `START TRANSACTION`语句相较于`BEGIN`特别之处在于，后边能跟随几个修饰符：

  - - `READ ONLY`：标识当前事务是一个只读事务，也就是属于该事务的数据库操作只能读取数据，而不能修改数据。补充：只读事务中只是不允许修改那些其他事务也能访问到的表中的数据，对于临时表来说（我们使用`CREATE TMEPORARY TABLE`创建的表），由于它们只能在当前会话中可见，所以只读事务其实也是可以对临时表进行增、删、改操作的。
    - `READ WRITE`（默认）：标识当前事务是一个读写事务，也就是属于该事务的数据库操作即可以读取数据，也可以修改数据。
    - `WITH CONSISTENT SNAPSHOT`：启动一致性读。

  - 注意：

  - - `READ ONLY`和`READ WRITE`是用来设置所谓的事务访问模式的，就是以只读还是读写的方式来访问数据库中的数据，一个事务的访问模式不能同时即设置为只读的还设置为读写，所以不能同时把`READ ONLY`和`READ WRITE`放到`START TRANSACTION`语句后边。
    - 如果我们不显式指定事务的访问模式，那么该事务的访问模式就是读写模式。

- 步骤二：一系列事务中的操作（主要是`DML`，不含`DDL`）
- 步骤三：提交事务或中止事务（即回滚事务）

~~~sql
#提交事务。当事务提交后，对数据库的修改时永久性的
commit

#回滚事务，即撤销正在进行的所有没有提交的修改
ROLLBACK;

#将事务回滚到某个保存点
ROLLBACK TO [SAVEPOINT]
~~~

- 其中有关`SAVEPOINT`相关操作有：

~~~sql
#在事务中创建保存点，方便后续针对保存点进行回滚。一个事务中可以存在多个保存点。
SAVEPOINT 保存点名称;

#删除某个保存点
RELEASE SAVEPOINT 保存点名称;
~~~





### 13.2.2、隐式事务

- `MySQL`中有一个系统变量`autocommit：`

~~~
mysql> SHOW VARIABLES LIKE 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+
1 row in set (0.01 sec)
~~~

- 默认情况下，如果我们不显式的使用`START TRANSACTION`或者`BEGIN`语句开启一个事务，那么每一条语句都算是一个独立的事务，这种特性称之为事务的自动提交。也就是说，不以`START TRANSACTION`或者`BEGIN`语句显式的开启一个事务，那么下边这两条语句就相当于放到两个独立的事务中去执行：

~~~sql
UPDATE account SET balance = balance - 10 WHERE id = 1;

UPDATE account SET balance = balance + 10 WHERE id = 2;
~~~

- 当然，如果我们想关闭这种自动提交的功能，可以使用下边两种方法之一：

- - 显式的使用`START TRANSACTION`或者`BEGIN`语句开启一个事务。这样在本次事务提交或者回滚前会暂时关闭掉自动提交的功能。
  - 把系统变量`autocommit`的值设置为`OFF`，就想这样：

~~~sql
SET autocommit = OFF;
或
SET autocommit = 0;
~~~

- 这样的话，我们写入的多条语句就算是属于同一个事务了，直到我们显式的写出`COMMIT`语句来把这个事务提交掉，或者显式的写出`ROLLBACK`语句来把这个事务回滚掉。补充：`Oracle`默认不自动提交，需要手写`COMMIT`命令，而`MySQL`默认自动提交。



### 13.2.3、隐式提交数据的情况

- 数据定义语言（`Data definition language`，缩写：`DDL`）

- - 数据库对象，指的就是数据库、表、视图、存储过程等结构。当我们使用`CREATE、ALTER、DROP`等语句去修改数据库对象时，就会隐式的提交前边语句所属于的事务。

- 隐式使用或修改`Mysql`数据库中的表

- - 当我们使用`ALTER USER、CREATE USER、DROP USER、GRANT、RENAME USER、REVOKE、SET PASSWORD`等语句时也会隐式的提交前边语句所属于的事务。

- 事务控制或关于锁定的语句

- - 当我们在一个事务还没提交或者回滚时就又使用`START TRANSACTION`或者`BEGIN`语句开启了另一个事务时，会隐式的提交上一个事务。
  - 当前的`autocommit`系统变量的值为`OFF`，我们手动把它调为`ON`时，也会隐式的提交前边语句所属的事务。
  - 使用`LOCK TABLES、UNLOCK TABLES`等关于锁定的语句也会隐式的提交前边语句所属的事务。

- 加载数据的语句

- - 使用`LOAD DATA`语句来批量往数据库中导入数据时，也会隐式的提交前边语句所属的事务。

- 关于`MySQL`复制的一些语句

- - 使用`START SLAVE、STOP SLAVE、RESET SLAVE、CHANGE MASTER TO`等语句时会隐式的提交前边语句所属的事务。

- 其他一些语句

- - 使用`ANALYZE TABLE、CACHE INDEX、CHECK TABLE、FLUSH、LOAD INDEX INTO CACHE、OPTIMIZE TABLE、REPAIR TABLE、RESET`等语句也会隐式的提交前边语句所属的事务。



### 13.2.4、使用举例1：提交与回滚

- 情况`1`：

~~~sql
CREATE TABLE user(name varchar(20), PRIMARY KEY (name)) ENGINE=InnoDB;

BEGIN;
INSERT INTO user SELECT '张三';
COMMIT;

BEGIN;
INSERT INTO user SELECT '李四';
INSERT INTO user SELECT '李四';

ROLLBACK;

SELECT * FROM user;

mysql> commit;
Query OK, 0 rows affected (0.00 秒)

mysql> BEGIN;
Query OK, 0 rows affected (0.00 秒)

mysql> INSERT INTO user SELECT '李四';
Query OK, 1 rows affected (0.00 秒)

mysql> INSERT INTO user SELECT '李四';
Duplicate entry '李四' for key 'user.PRIMARY'

mysql> ROLLBACK;
Query OK, 0 rows affected (0.01 秒)

mysql> select * from user;
+--------+
| name |
+--------+
| 张三 |
+--------+
1 行于数据集 (0.01 秒)
~~~

- 情况`2`：

~~~sql
CREATE TABLE user (name varchar(20), PRIMARY KEY (name)) ENGINE=InnoDB;

BEGIN;
INSERT INTO user SELECT '张三';
COMMIT;

INSERT INTO user SELECT '李四';
INSERT INTO user SELECT '李四';

ROLLBACK;

mysql> SELECT * FROM user;
+--------+
| name |
+--------+
| 张三 |
| 李四 |
+--------+
2 行于数据集 (0.01 秒)
~~~

- 情况`3`：

~~~sql
CREATE TABLE user(name varchar(255), PRIMARY KEY (name)) ENGINE=InnoDB;

SET @@completion_type = 1;

BEGIN;
INSERT INTO user SELECT '张三';
COMMIT;

INSERT INTO user SELECT '李四';
INSERT INTO user SELECT '李四';

ROLLBACK;

SELECT * FROM user;

mysql> SELECT * FROM user;
+--------+
| name |
+--------+
| 张三 |
+--------+
1 行于数据集 (0.01 秒)
~~~

- 你能看到相同的`SQL`代码，只是在事务开始之前设置了`SET @@completion_type = 1`；结果就和我们第一次处理的一样，只有一个“张三”，这是为什么呢？

- 这里`MySQL`中`completion_type`参数的作用，实际上这个参数有3中可能：

- - `completion = 0`，这是默认情况。当我们执行`COMMIT`的时候会提交事务，在执行下一个事务时，还需要使用`START TRANSACTION`或者`BEGIN`来开启。
  - `completion = 1`，这种情况下，当我们提交事务后，相当于执行了`COMMIT AND CHAIN`，也就是开启一个链式事务，即当我们提交事务之后会开启一个相同隔离级别的事务。
  - `completion = 2`，这种情况下`COMMIT = COMMIT AND RELEASE`，也就是当我们提交后，会自动与服务器断开连接。



## 13.3、事务隔离级别

- `MySQL`是一个客户端/服务器架构的软件，对于同一个服务器来说，可以有若干个客户端与之连接，每个客户端与服务器连接上之后，就可以称为一个会话（`Session`）。每个客户端都可以在自己的会话中向服务器发出请求语句，一个请求语句可能是某个事务的一部分，也就是对于服务器来说可能同时处理多个事务。事务有隔离性的特性，理论上在某个事务对某个数据进行访问时，其他事务应该进行排队，当该事务提交之后，其他事务才可以继续访问这个数据。但是这样对性能影响太大，我们既想保持事务的隔离性，又想让服务器在处理访问同一数据的多个事务时性能尽量高些，那就看二者如何权衡取舍了。



### 13.3.1、数据准备

- 我们需要创建一个表


~~~sql
CREATE TABLE student (
    studentno INT,
    name VARCHAR(20),
    class varchar(20),
    PRIMARY KEY (studentno)
) Engine=InnoDB CHARSET=utf8;
~~~

- 然后向这个表里插入一条数据：

~~~sql
INSERT INTO student VALUES(1, '小谷', '1班');
~~~



### 13.3.2、数据并发问题

- 针对事务的隔离性和并发性，我们怎么做取舍呢？先看一下访问相同数据的事务在不保证串行执行（也就是执行完一个再执行另一个）的情况下可能会出现哪些问题：

#### 1、脏写（Dirty Writer）

- 对于两个事务`SessionA`、`SessionB`，如果事务`SessionA`修改了另一个未提交事务`SessionB`修改过的数据，那就意味着发生了脏写，示意图如下：

![image-20231011122215545](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011122215545.png)

- `Session A`和`Session B`各开启了一个事务，`Session B`中的事务先将`studentno`列为`1`的记录的`name`列更新为'李四'，然后`Session A`中的事务接着又把这条`studentno`列为`1`的记录的`name`列更新为'张三'。如果之后`Session B`中的事务进行了回滚，那么`Session A`中的更新也将不复存在，这种现象就称之为脏写。这时`Session A`中的事务就没有效果了，明明把数据更新了，最后也提交事务了，最后看到的数据什么变化也没有。这里大家对事务的隔离级比较了解的话，会发现默认隔离级别下，上面`SessionA`中的更新语句会处于等待状态，这里只是跟大家说明一下会出现这样现象。



#### 2、脏读（Dirty Read）

- 对于两个事务`SessionA、SessionB，SessionA`读取了已经被`SessionB`更新但还没被提交的字段。之后若`SessionB`回滚，`SessionA`读取的内容就是临时且无效的。

![image-20231011122400496](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011122400496.png)

- `Session A`和`Session B`各开启了一个事务，`Session B`中的事务先将`studentno`列为`1`的记录的`name`列更新为'张三'，然后`Session A`中的事务再去查询这条`studentno`为`1`的记录，如果读到列`name`的值为'张三'，而`Session B`中的事务稍后进行了回滚，那么`Session A`中的事务相当于读到了一个不存在的数据，这种现象就称之为脏读。



#### 3、不可重复度（`Non-Repeatable Read`）

- 对于两个事务`SessionA、SessionB，SessionA`读取了一个字段，然后`SessionB`更新了该字段并提交了。之后`SessionA`再次读取同一个字段，值就不同了。那就意味着发生了不可重复读。


![image-20231011124232235](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011124232235.png)

- 我们在`SessionB`中提交了几个隐式事务（注意是隐式事务，意味着语句结束事务就提交了），这些事务都修改了`studentno`列为`1`的记录的列`name`的值，每次事务提交之后，如果`SessionA`中的事务都可以查看到最新的值，这种现象也被称之为不可重复读。




#### 4、幻读（`Phantom`）

- 对于两个事务`SessionA、SessionB，SessionA`从一个表中读取了一个字段，然后`SessionB`在该表中插入了一些新的行。之后，如果`SessionA`再次读取同一个表，就会多出几行，那就意味着发生了幻读。


![image-20231011124417948](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011124417948.png)

- `Session A`中的事务先根据条件`studentno > 0`这个条件查询表`student`，得到了`name`列值为'张三'的记录；之后`Session B`中提交了一个隐式事务 ，该事务向表`student`中插入了一条新记录；之后`Session A`中的事务再根据相同的条件`studentno > 0`查询表`student`，得到的结果集中包含`Session B`中的事务新插入的那条记录，这种现象也被称之为幻读 。我们把新插入的那些记录称之为幻影记录 。

> 注：
>
> - 有的同学会有疑问，那如果`SessionB`中删除了一些符合`studentno > 0`的记录而不是插入新记录，那`SessionA`之后再根据`studentno > 0`的条件读取的记录变少了，这种现象算不算幻读呢？这种现象不属于幻读，幻读强调的是一个事务按照某个相同条件多次读取记录时，后读取时读到了之前没有读到的记录。
> - 那对于先前已经读到的记录，之后又读取不到的这种情况，算什么？这相当于对于每一条记录都发生了不可重复读的现象。幻读只是重点强调了读取到了之前读取没有获取到的记录。



### 13.3.3、`SQL`中的四种隔离级别

- 上面介绍了几种并发事务执行过程中可能遇到的一些问题，这些问题有轻重缓急之分，我们给这些问题按照严重性来排一下序：脏写 > 脏读 > 不可重复读 > 幻读

- 我们愿意舍弃一部分隔离性来换取一部分性能在这里就体现在：设立一些隔离级别，隔离级别越低，并发问题发生的就越多。`SQL`标准中设立了`4`个隔离级别：

- - `READ UNCOMMITTED`：读未提交，在该隔离级别，所有事务都可以看到其他未提交事务的执行结果。不能避免脏读、不可重复读、幻读。
  - `READ COMMITTED`：读已提交，它满足了隔离的简单定义：一个事务只能看见已经提交事务所做的改变。这是大多数数据库系统的默认隔离级别（但不是`MySQL`默认的）。可以避免脏读，但不可重复读、幻读问题仍然存在。
  - `REPEATABLE READ`：可重复读，事务`A`在读到一条数据之后，此时事务`B`对该数据进行了修改并提交，那么事务`A`再读该数据，读到的还是原来的内容。可以避免脏读、不可重复读，但幻读问题仍然存在。这是`MySQL`的默认隔离级别。
  - `SERIALIZABLE`：可串行化，确保事务可以从一个表中读取相同的行。在这个事务持续期间，禁止其他事务对该表执行插入、更新和删除操作。所有的并发问题都可以避免，但性能十分低下。能避免脏读、不可重复读和幻读。

- `SQL`标准中规定，针对不同的隔离级别，并发事务可以发生不同严重程度的问题，具体情况如下：

![image-20231011124804558](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011124804558.png)

- 脏写怎么没有涉及到？因为脏写这个问题太严重了，不论是哪种隔离级别，都不允许脏写的情况发生。
- 不同的隔离级别有不同的现象，并有不同的锁和并发机制，隔离级别越高，数据库的并发性能就越差，四种事务隔离级别与并发性能的关系如下：

![image-20231011124908992](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011124908992.png)



### 13.3.4、`MySQL`支持的四种隔离级别

- 不同的数据库厂商对`SQL`标准中规定的四种隔离级别支持不一样。比如，`Oracle`就只支持`READ COMMITTED`（默认隔离级别）和`SERIALIZABLE`隔离级别。`MySQL`虽然支持`4`种隔离级别，但与`SQL`标准中所规定的各级隔离级别允许发生的问题却有些出入，`MySQL`在`REPEATABLE READ`隔离级别下，是可以禁止幻读问题的发生的，禁止幻读的原因我们在第16章讲解。
- `MySOL`的默认隔离级别为`REPEATABLE READ`，我们可以手动修改一下事务的隔离级别。

~~~sql
# 查看隔离级别，MySQL 5.7.20的版本之前：
mysql> SHOW VARIABLES LIKE 'tx_isolation';
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| tx_isolation  | REPEATABLE-READ |
+---------------+-----------------+
1 row in set (0.00 sec)

# MySQL 5.7.20版本之后，引入transaction_isolation来替换tx_isolation

# 查看隔离级别，MySQL 5.7.20的版本及之后：
mysql> SHOW VARIABLES LIKE 'transaction_isolation';
+-----------------------+-----------------+
| Variable_name         | Value           |      
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.02 sec)

#或者不同MySQL版本中都可以使用的：
SELECT @@transaction_isolation;
~~~



### 13.3.5、如何设置事务的隔离级别

##### 1、通过下面的语句修改事务的隔离级别

~~~sql
SET [GLOBAL|SESSION] TRANSACTION ISOLATION LEVEL 隔离级别;
#其中，隔离级别格式：
> READ UNCOMMITTED
> READ COMMITTED
> REPEATABLE READ
> SERIALIZABLE
~~~

- 或者

~~~sql
SET [GLOBAL|SESSION] TRANSACTION_ISOLATION = '隔离级别'
#其中，隔离级别格式：
> READ-UNCOMMITTED
> READ-COMMITTED
> REPEATABLE-READ
> SERIALIZABLE
~~~



##### 2、关于设置时使用`GLOBAL`或`SESSION`的影响：

- 使用`GLOBAL`关键字（在全局范围影响）：
  - 当前已经存在的会话无效
  - 只对执行完该语句之后产生的会话起作用
  - 重启`MySQL`服务后会失效

~~~sql
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
#或
SET GLOBAL TRANSACTION_ISOLATION = 'SERIALIZABLE'
~~~

- 使用`SESSION`关键字（在会话范围影响）：
  - 对当前会话的所有后续的事务有效
  - 如果在事务之间执行，则对后续的事务有效
  - 该语句可以在已经开启的事务中间执行，但不会影响当前正在执行的事务

~~~sql
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
#或
SET SESSION TRANSACTION_ISOLATION = 'SERIALIZABLE';
~~~

- 如果在服务器启动时想改变事务的默认隔离级别，可以修改启动参数`transaction_ isolation`的值。比如，在启动服务器时指定了`transaction_ isolation=SERIALIZABLE`，那么事务的默认隔离级别就从原来的`REPEATABLE- READ`变成了`SERIALIZABLE`。

- 小结：

- - 数据库规定了多种事务隔离级别，不同隔离级别对应不同的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱。



## 13.4、事务的常见分类

- 从事务理论的角度来看，可以把事务分为以下几种类型：
  - 扁平事务（Flat Transactions）
  - 带有保存点的扁平事务（Flat Transactions with Savepoints）
  - 链事务（Chained Transactions）
  - 嵌套事务（Nested Transactions）
  - 分布式事务（Distributed Transactions）

### 13.4.1、扁平事务

- 扁平事务是事务类型中最简单的一种，但是在实际生产环境中，这可能是使用最频繁的事务，在扁平事务中，所有操作都处于同一层次，其由`BEGIN WORK`开始，由`COMMIT WORK`或`ROLLBACK WORK`结束，其间的操作是原子的，要么都执行，要么都回滚，因此扁平事务是应用程序成为原子操作的基本组成模块。扁平事务虽然简单，但是在实际环境中使用最为频繁，也正因为其简单，使用频繁，故每个数据库系统都实现了对扁平事务的支持。扁平事务的主要限制是不能提交或者回滚事务的一部分，或分为几个步骤提价。
- 扁平事务一般有三种不同的结果：①事务成功完成。在平常应用中约占所有事务的96%。②应用程序要求停止事务。比如应用程序在捕获到异常时会回滚事务，约占事务的3%。③外界因素强制终止事务。如连接超时或连接断开，约占所有事务的1%。



### 13.4.2、带有保存点的扁平事务

- 除了支持扁平事务支持的操作外，还允许在事务执行过程中回滚到同一事务中较早的一个状态。这是因为某些事务可能在执行过程中出现的错误并不会导致所有的操作都无效，放弃整个事务不合乎要求，开销太大。
- 保存点（Savepoint）用来通知事务系统应该记住事务当前的状态，以便当之后发生错误时，事务能回到保存点当时的状态。对于扁平的事务来说，隐式的设置了一个保存点， 然而在整个事务中，只有这一个保存点， 因此，回滚只能会滚到事务开始时的状态。



### 13.4.3、链事务

- 是指一个事务由多个子事务链式组成,它可以被视为保存点模式的一个变种。带有保存点的扁平事务，当发生系统崩溃时，所有的保存点都将消失，这意味着当进行恢复时，事务需要从开始处重新执行，而不能从最近的一个保存点继续执行。链事务的思想是：在提交一个事务时， 释放不需要的数据对象，将必要的处理上下文隐式地传给下一个要开始的事务，前一个子事务的提交操作和下一个子事务的开始操作合并成一个原子操作，这意味着下一个事务将看到上一个事务的结果，就好像在一个事务中进行一 样。这样，在提交子事务时就可以释放不需要的数据对象，而不必等到整个事务完成后才释放。工作方式如下:

![image-20231011125402723](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231011125402723.png)

- 链事务与带有保存点的扁平事务的不同之处体现在:

- - 带有保存点的扁平事务能回滚到任意正确的保存点，而链事务中的回滚仅限于当前事务，即只能恢复到最近的一个保存点。
  - 对于锁的处理，两者也不相同，链事务在执行COMMIT后即释放 了当前所持有的锁，而带有保存点的扁平事务不影响迄今为止所持有的锁。



### 13.4.4、嵌套事务

- 是一个层次结构框架，由一个顶层事务（Top-Level Transaction）控制着各个层次的事务，顶层事务之下嵌套的事务被称为子事务（Subtransaction），其控制着每一个局部的变换， 子事务本身也可以是嵌套事务。因此，嵌套事务的层次结构可以看成是一棵树。



### 13.4.5、分布式事务

- 通常是在一个分布式环境下运行的扁平事务，因此，需要根据数据所在位置访问网络中不同节点的数据库资源。例如，一个银行用户从招商银行的账户向工商银行的账户转账`1000`元，这里需要用到分布式事务，因为不能仅调用某一家银行的数据库就完成任务。





# 14、MySQL 的事务日志



























































