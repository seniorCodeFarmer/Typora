# 1、Linux中MySQL的安装与使用

## 1.1、安装前说明

### 1.1.1、Linux系统及工具准备

### 1.1.2、查看是否安装过MySQL

使用rpm命令查看

![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)

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

在存储表结构方面， MyISAM 和 InnoDB 一样，也是在数据目录下对应的数据库子目录下创建了一个专门用于描述表结构的文件：**表名.frm。**



##### 2、表中数据和索引

MySQL5.7，在MyISAM中的索引全部都是二级索引 ，该存储引擎的数据和索引是分开存放的。所以在文件系统中也是使用不同的文件来存储数据文件和索引文件，同时表数据都存放在对应的数据库子目录下。假如 test 表使用MyISAM存储引擎的话，那么在它所在数据库对应的 temp 目录下会为 test 表创建这三个文件：

```
test.frm 存储表结构 
test.MYD 存储数据 (MYData) 
test.MYI 存储索引 (MYIndex)
```

举例：创建一个 MyISAM 表，使用 ENGINE 选项显式指定引擎。因为 InnoDB 是默认引擎。

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





## 3、用户与权限管理





## 4、逻辑架构





























































