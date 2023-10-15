## 1、`Zookeeper`入门

### 1.1、概述

- `Zookeeper`是一个开源的分布式的，为分布式框架提供协调服务的`Apache`项目。
- `Zookeeper`工作机制
  - `Zookeeper`从设计模式角度来理解：是一个基于观察者模式设计的分布式服务管理框架，它负责存储和管理大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生变化，`Zookeeper`就将负责通知已经在`Zookeeper`上注册的那些观察者做出相应的反应。（观察者相当于各种微服务）

![image-20230926193525093](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926193525093.png)



### 1.2、特点

![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926193628746.png)

- `Zookeeper`：一个领导者（`Leader`），多个跟随者（`Follower`）组成的集群。
- 集群中只要有半数以上节点存活，`Zookeeper`集群就能正常服务。所 以`Zookeeper`适合安装奇数台服务器。
- 全局数据一致：每个`Server`保存一份相同的数据副本，`Client`无论连接到哪个`Server`，数据都是一致的。
- 更新请求顺序执行，来自同一个`Client`的更新请求按其发送顺序依次执行。
- 数据更新原子性，一次数据更新要么成功，要么失败。
- 实时性，在一定时间范围内，`Client`能读到最新数据。



### 1.3、数据结构

- ZooKeeper 数据模型的结构与 Unix 文件系统很类似，整体上可以看作是一棵树，每个节点称做一个 ZNode。每一个 ZNode 默认能够存储 1MB 的数据，每个 ZNode 都可以通过其路径唯一标识。

![image-20230926194642915](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926194642915.png)



### 1.4、应用场景

- 提供的服务包括：统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等。



#### 1.4.1、统一命名服务

- 在分布式环境下，经常需要对应用/服务进行统一命名，便于识别。
- 例如：IP不容易记住，而域名容易记住。

![image-20230926195545970](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926195545970.png)



#### 1.4.2、统一配置管理

- 分布式环境下，配置文件同步非常常见。
  - 一般要求一个集群中，所有节点的配置信息是一致的，比如 Kafka 集群。
  - 对配置文件修改后，希望能够快速同步到各个节点上。
- 配置管理可交由`ZooKeeper`实现。
  - 可将配置信息写入`ZooKeeper`上的一个`Znode`。
  - 各个客户端服务器监听这个`Znode`。
  - 一 旦`Znode`中的数据被修改，`ZooKeeper`将通知各个客户端服务器。

![image-20230926195647776](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926195647776.png)



#### 1.4.3、统一集群管理

- 分布式环境中，实时掌握每个节点的状态是必要的。
  - 可根据节点实时状态做出一些调整。
- `ZooKeeper`可以实现实时监控节点状态变化。
  - 可将节点信息写入`ZooKeeper`上的一个`ZNode`。
  - 监听这个`ZNode`可获取它的实时状态变化。

![image-20230926195813390](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926195813390.png)





#### 1.4.4、服务器动态上下线

![image-20230926195846782](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926195846782.png)





#### 1.4.5、软负载均衡

- 在`Zookeeper`中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求。

![image-20230926195913349](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926195913349.png)





### 1.5、下载地址

- 官网首页：`https://zookeeper.apache.org/`



## 2、`Zookeeper` 本地安装

### 2.1、安装

- 安装`JDK`
- 将`Zookeeper`的 bin.tar.gz 安装包上传到Linux系统 /usr/local/zookeeper 目录下，并解压

~~~shell
# 解压到指定目录下
tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz -C /usr/local/zookeeper
~~~

- 修改名称

~~~shell
mv apache-zookeeper-3.5.7-bin zookeeper-3.5.7
~~~

- 修改 /conf 目录下 `zoo_sample.cfg`文件的名字为 `zoo.cfg`
- 在安装目录下新建存放数据的文件`zkData`
- 修改`zoo.cfg`文件中`dataDir`路径

~~~properties
dataDir=/usr/local/zookeeper/zookeeper-3.5.7/zkData
~~~



### 2.2、操作 Zookeeper

- 启动`Zookeeper`

~~~shell
bin/zkServer.sh start
~~~

- 查看进程是否启动

~~~shell
jps -l
~~~

- 查看状态

~~~shell
bin/zkServer.sh status
~~~

- 启动客户端

~~~shell
bin/zkCli.sh
~~~

- 退出客户端

~~~shell
quit
~~~

- 停止 Zookeeper

~~~shell
bin/zkServer.sh stop
~~~





### 2.3、配置参数解读

- `Zookeeper`中的配置文件 `zoo.cfg`中参数含义解读如下

#### 1、tickTime = 2000

- 通信心跳时间，`Zookeeper`服务器与客户端心跳时间，单位毫秒。

![image-20230926225259138](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926225259138.png)



#### 2、initLimit = 10

- LF 初始通信时限

![image-20230926225414198](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926225414198.png)

- Leader和Follower初始连接时能容忍的最多心跳数（tickTime的数量）。



#### 3、syncLimit = 5

- LF 同步通信时限

![image-20230926225505048](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926225505048.png)

- Leader和Follower之间通信时间如果超过syncLimit * tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。



#### 4、dataDir

- 保存Zookeeper中的数据
- 注意：默认的tmp目录，容易被Linux系统定期删除，所以一般不用默认的tmp目录。



#### 5、clientPort = 2181

- 客户端连接端口，通常不做修改



## 3、`Zookeeper` 集群操作

### 3.1、集群操作

#### 3.1.1、集群安装

##### 1、集群规划

- 在 110、111、112 三个节点上都部署 Zookeeper



##### 2、解压安装

- 在 111 解压 Zookeeper 安装到`/usr/local/zookeeper`目录下
- 修改`apache-zookeeper-3.5.7-bin`名称为 `zookeeper-3.5.7`



##### 3、配置服务器编号

- 在`/usr/local/zookeeper/zookeeper-3.5.7`这个目录下创建 `zkData`

~~~shell
[root@zcwcentos7 zookeeper-3.5.7]# mkdir zkData
~~~

- 在`/usr/local/zookeeper/zookeeper-3.5.7/zkData`目录下创建一个`myid`的文件，在文件中添加与 server 对应的编号（注意：上下不要有空行，左右不要有空格，添加 myid 文件，一定要在 Linux 里面创建，在 notepad++里面很可能乱码）

~~~shell 
[root@zcwcentos7 zkData]# vi myid
~~~

- 拷贝配置好的 zookeeper 到其他机器上，并分别在 111、112 上修改 myid 文件中内容为 3、4

~~~shell
[root@zcwcentos7 zkData]# xsync zookeeper-3.5.7
~~~



##### 4、配置`zoo.cfg`文件

- 增加如下配置

~~~properties
#######################cluster##########################
server.2=zcwcentos7:2888:3888
server.3=zcwcentos7c1:2888:3888
server.4=zcwcentos7c2:2888:3888
~~~

- 配置参数解读

~~~shell
server.A=B:C:D

# A 是一个数字，表示这个是第几号服务器。集群模式下配置一个文件 myid，这个文件在 dataDir 目录下，这个文件里面有一个数据就是 A 的值，Zookeeper 启动时读取此文件，拿到里面的数据与 zoo.cfg 里面的配置信息比较从而判断到底是哪个 server。
#B 是这个服务器的地址。
#C 是这个服务器 Follower 与集群中的 Leader 服务器交换信息的端口。
#D 是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。
~~~

- 同步 `zoo.cfg`配置文件，其他服务器都需要修改



##### 5、全部启动



#### 3.1.2、选举机制（重点）

##### 1、第一次启动（初始化时）

![image-20230927121900412](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927121900412.png)

- `服务器1`启动，发起一次选举，`服务器1`投自己一票，此时`服务器1`票数一票，不够半数以上（3票），选举无法成功，`服务器1`状态保持为`LOOKING`；
- `服务器2`启动，再发起一次选举，`服务器1`和`服务器2`分别投自己一票并交换选票信息。此时`服务器1`发现`服务器2`的`myid`比自己目前投票推举的（服务器1）大，更改选票为推举`服务器2`。此时`服务器1`票数0票，`服务器2`票数2票，没有半数以上结果，选举无法完成，`服务器1、2`状态保持`LOOKING`；
- `服务器3`启动，发起一次选举。此时`服务器1`和`服务器2`都会更改选票为`服务器3`。此次投票结果：`服务器1`为0票，`服务器2`为0票，`服务器3`为3票，此时`服务器3`的票数已经超过半数，`服务器3`当选`Leader`。`服务器1、2`更改状态为`FOLLOWING`，`服务器3`更改状态为`LEADING`；
- `服务器4`启动，发动一次选举。此时`服务器1、2、3`已经不是`LOOKING`状态，不会更改选票信息。交换选票信息结果：`服务器3`为3票，`服务器4`为1票。此时`服务器4`服从多数，更改选票信息为服务器3，并更改状态为`FOLLOWING`；
- `服务器5`启动，同4一样当小弟。



##### 2、非第一启动

![image-20230927121900412](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927121900412.png)

- 当Zookeeper集群中的一台服务器出现以下两种情况之一时，就会开始进入`Leader`选举：
  - 服务器初始化启动。
  - 服务器运行期间无法和`Leader`保持连接。
- 而当一台机器进入`Leader`选举流程时，当前集群也可能会处于以下两种状态：
  - 集群中本来就已经存在一个`Leader`。对于这种情况，其他机器如果试图去选举`Leader`时，会被告知当前服务器的`Leader`信息，对于该机器来说，仅仅需要和`Leader`机器建立连接，并进行状态同步即可。
  - 集群中确实不存在`Leader`。假设ZooKeeper由5台服务器组成，`SID`分别为1、2、3、4、5，`ZXID`分别为8、8、8、7、7，并且此时`SID`为3的服务器是`Leader`。某一时刻，3和5服务器出现故障，因此开始进行`Leader`选举。

![image-20230927125001127](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927125001127.png)

> 注：
>
> - ZXID：事务ID，表示写操作的处理次数



#### 3.1.3、ZK 集群启动停止脚本

- 在`zcwcentos7`的`/root/bin`目录下创建脚本

~~~shell
[root@zcwcentos7 bin]# vim zk.sh
~~~

- 在脚本中编写如下内容：

~~~shell
#!/bin/bash
case $1 in
"start"){
	for i in zcwcentos7 zcwcentos7c1 zcwcentos7c2
	do
		echo ---------- zookeeper $i 启动 ------------
		ssh $i "/usr/local/zookeeper/zookeeper-3.5.7/bin/zkServer.sh start"	
	done
};;
"stop"){
	for i in zcwcentos7 zcwcentos7c1 zcwcentos7c2
	do
		echo ---------- zookeeper $i 停止 ------------ 
		ssh $i "/usr/local/zookeeper/zookeeper-3.5.7/bin/zkServer.sh stop"
	done
};;
"status"){
	for i in zcwcentos7 zcwcentos7c1 zcwcentos7c2
	do
		echo ---------- zookeeper $i 状态 ------------ 
		ssh $i "/usr/local/zookeeper/zookeeper-3.5.7/bin/zkServer.sh status"
	done
};;
esac
~~~

- 增加脚本执行权限

~~~shell
[root@zcwcentos7 bin]# chmod 777 zk.sh 
~~~

- `Zookeeper`集群启动脚本

~~~shell
[root@zcwcentos7 bin]# zk.sh start
~~~

- `Zookeeper`集群停止脚本

~~~shell
[root@zcwcentos7 bin]# zk.sh stop
~~~

> 注：
>
> 启动脚本执行后报错：Error: JAVA_HOME is not set and java could not be found in PATH.
>
> 解决方法：
>
> - 修改 zookeeper 目录下 `/bin/zkServer.sh`文件，在文件开始部分加上自己的java路径，export JAVA_HOME=/usr/local/jdk/jdk1.8.0_381
>
> ![image-20230927133651723](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927133651723.png)



#### 3.1.4、ssh 免密登录

- 找到`.ssh`目录

![image-20230927141708318](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927141708318.png)

- 输入命令

~~~shell
[root@zcwcentos7 .ssh]# ssh-keygen -t rsa
~~~

![image-20230927141851076](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927141851076.png)

- 拷贝公钥到其他服务器

~~~shell
[root@zcwcentos7 .ssh]# ssh-copy-id zcwcentos7c1
~~~





### 3.2、客户端命令行操作

#### 3.2.1、命令行语法

| **命令基本语法** |                         **功能描述**                         |
| :--------------: | :----------------------------------------------------------: |
|       help       |                       显示所以操作命令                       |
|    ls (path)     | 使用 ls 命令来查看当前`znode`的子节点【可监听】-w：监听子节点变化  -s：附加次级信息 |
|      create      |      普通创建 -s：含有序列  -e 临时（重启或者超时消失）      |
|     get path     | 获得节点的值【可监听】 -w：监听节点内容变化  -s：附加次级信息 |
|       set        |                       设置节点的具体值                       |
|       stat       |                         查看节点状态                         |
|      delete      |                           删除节点                           |
|    deleteall     |                         递归删除节点                         |

- 启动客户端


~~~shell
[root@zcwcentos7 zookeeper-3.5.7]# bin/zkCli.sh -server zcwcentos7:2181
~~~

- 显示所有操作命令

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 0] help
~~~



#### 3.2.2、znode 节点数据信息

- 查看当前`znode`中所包含的内容

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 3] ls /
[zookeeper]
~~~

- 查看当前节点详细数据
  - czxid：创建节点的事务 zxid。每次修改 ZooKeeper 状态都会产生一个 ZooKeeper 事务 ID。事务 ID 是 ZooKeeper 中所有修改总的次序。每次修改都有唯一的 zxid，如果 zxid1 小于 zxid2，那么 zxid1 在 zxid2 之前发生。
  - ctime：znode 被创建的毫秒数（从 1970 年开始）。
  - mzxid：znode 最后更新的事务 zxid。
  - mtime：znode 最后修改的毫秒数（从 1970 年开始）。
  - pZxid：znode 最后更新的子节点 zxid。
  - cversion：znode 子节点变化号，znode 子节点修改次数。
  - dataversion：znode 数据变化号。
  - aclVersion：znode 访问控制列表的变化号。
  - ephemeralOwner：如果是临时节点，这个是 znode 拥有者的 session id。如果不是临时节点则是 0。
  - dataLength：znode 的数据长度。
  - numChildren：znode 子节点数量。

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 4] ls -s /
[zookeeper]cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x0
cversion = -1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
~~~

> 注：
>
> - 在 zookeeper 中节点可以被看作是 Linux 系统中的每一层文件目录，创建一个节点相当于创建了一层文件目录，在 zookeeper 中每个节点还可以存储数据，节点中还可以创建子节点。



#### 3.2.3、节点类型（持久/短暂/有序号/无序号）

![image-20230927154101191](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927154101191.png)



##### 1、创建两个普通节点（永久节点 + 不带序号）

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 7] create /sanguo "diaochan"
Created /sanguo

[zk: zcwcentos7:2181(CONNECTED) 9] create /sanguo/shuguo "liubei"
Created /sanguo/shuguo

#获取节点的值
[zk: zcwcentos7:2181(CONNECTED) 14] get -s /sanguo 
diaochan
cZxid = 0x500000002
ctime = Wed Sep 27 15:44:17 CST 2023
mZxid = 0x500000002
mtime = Wed Sep 27 15:44:17 CST 2023
pZxid = 0x500000003
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 8
numChildren = 1
[zk: zcwcentos7:2181(CONNECTED) 15] get -s /sanguo/shuguo
liubei
cZxid = 0x500000003
ctime = Wed Sep 27 15:45:19 CST 2023
mZxid = 0x500000003
mtime = Wed Sep 27 15:45:19 CST 2023
pZxid = 0x500000003
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 6
numChildren = 0
~~~

- 注意：创建节点时，需要赋值



##### 2、创建带序号的节点（永久节点 + 带序号）

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 20] create -s /sanguo/weiguo/zhangliao "zhangliao"
Created /sanguo/weiguo/zhangliao0000000000
[zk: zcwcentos7:2181(CONNECTED) 26] create -s /sanguo/weiguo/zhangliao "zhangliao"
Created /sanguo/weiguo/zhangliao0000000001
[zk: zcwcentos7:2181(CONNECTED) 27] ls /sanguo/weiguo
[zhangliao0000000000, zhangliao0000000001]
[zk: zcwcentos7:2181(CONNECTED) 28] create -s /sanguo/weiguo/zhangliao "zhangliao"
Created /sanguo/weiguo/zhangliao0000000002
[zk: zcwcentos7:2181(CONNECTED) 29] create -s /sanguo/weiguo/xuchu "xuchu"
Created /sanguo/weiguo/xuchu0000000003
~~~

- 如果原来没有序号节点，序号从 0 开始依次递增。如果原节点下已有 2 个节点，则再排序时从 2 开始，以此类推。



##### 3、创建短暂节点（短暂节点 + 不带序号 + 带序号）

- 创建短暂的不带序号的节点

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 30] create -e /sanguo/wuguo "zhouyu"
Created /sanguo/wuguo
[zk: zcwcentos7:2181(CONNECTED) 31] ls /sanguo
[shuguo, weiguo, wuguo]
~~~

- 创建短暂的带序号的节点

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 33] create -e -s /sanguo/wuguo "zhouyu"
Created /sanguo/wuguo0000000003
[zk: zcwcentos7:2181(CONNECTED) 34] ls /sanguo
[shuguo, weiguo, wuguo, wuguo0000000003]
~~~

- 在当前客户端是能查看到的

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 34] ls /sanguo
[shuguo, weiguo, wuguo, wuguo0000000003]
~~~

- 退出当前客户端然后再重启客户端，再次查看根目录下短暂节点已经删除

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 0] ls /sanguo
[shuguo, weiguo]
~~~



##### 4、修改节点数据值

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 1] get /sanguo/weiguo
caocao
[zk: zcwcentos7:2181(CONNECTED) 2] set /sanguo/weiguo "simayi"
[zk: zcwcentos7:2181(CONNECTED) 3] get /sanguo/weiguo
simayi
~~~



#### 3.2.4、监听器原理

- 客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、节点删除、子目录节点增加删除）时，ZooKeeper 会通知客户端。监听机制保证 ZooKeeper 保存的任何的数据的任何改变都能快速的响应到监听了该节点的应用程序。

> 注：
>
> - Zookeeper 监听器机制能够生效的基本保证就是：Zookeeper 的全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。



##### 1、监听原理详解

- 首先要有一个main()线程
- 在main线程中创建 Zookeeper 客户端，这时就会创建两个线程，一个负责网络连接通信（connet），一个负责监听（listener）。
- 通过connect线程将注册的监听事件发送给Zookeeper。
- 在Zookeeper的注册监听器列表中将注册的监听事件添加到列表中。
- Zookeeper监听到有数据或路径变化，就会将这个消息发送给listener线程。
- listener线程内部调用了process()方法。

![image-20230927172808492](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927172808492.png)



##### 2、常见的监听

- 监听节点数据的变化：`get path [watch]`
- 监听子节点增减的变化：`ls path [watch]`



##### 3、具体操作

###### （1）节点的值变化监听

- 在 `zcwcentos7`主机上注册监听 `/sanguo`节点数据变化

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 5] get -w /sanguo
diaochan
~~~

- 在 `zcwcentos7c1`主机上修改 `/sanguo`节点的数据

~~~shell
[zk: localhost:2181(CONNECTED) 2] set /sanguo "huangyueying"
~~~

- 观察 `zcwcentos7`主机收到数据变化的监听

~~~shell
WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/sanguo
~~~

- 注意：在hadoop103再多次修改/sanguo的值，hadoop104上不会再收到监听。因为注册一次，只能监听一次。想再次监听，需要再次注册。



###### （2）节点的子节点变化监听（路径变化）

- 在`zcwcentos7`主机上注册监听`/sanguo`节点的子节点变化

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 1] ls -w /sanguo
[shuguo, weiguo]
~~~

- 在`zcwcentos7c1`主机`/sanguo`节点上创建子节点

~~~shell 
[zk: zcwcentos7c1:2181(CONNECTED) 1] create /sanguo/jin "simayi"
Created /sanguo/jin
~~~

- 观察`zcwcentos7`主机收到子节点变化的监听

~~~shell 
[zk: zcwcentos7:2181(CONNECTED) 2] 
WATCHER::

WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/sanguo
~~~

- 注意：节点的路径变化，也是注册一次，生效一次。想多次生效，就需要多次注册。



#### 3.2.5、节点的删除与查看

###### 1、删除节点

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 2] delete /sanguo/jin
~~~

###### 2、递归删除节点

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 5] deleteall /sanguo
[zk: zcwcentos7:2181(CONNECTED) 6] ls /
[zookeeper]
~~~

###### 3、查看节点状态

~~~shell
[zk: localhost:2181(CONNECTED) 17] stat /sanguo
cZxid = 0x100000003
ctime = Wed Aug 29 00:03:23 CST 2018
mZxid = 0x100000011
mtime = Wed Aug 29 00:21:23 CST 2018
pZxid = 0x100000014
cversion = 9
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 4
numChildren = 1
~~~



### 3.3、客户端API操作

#### 3.3.1、IDEA 环境搭建

- 创建一个Maven工程
- 添加POM文件

~~~xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.5.7</version>
    </dependency>
</dependencies>
~~~

- 拷贝log4j.properties文件到项目根目录
  - 需要在项目的 src/main/resources 目录下，新建一个文件，命名为“log4j.properties”，在文件中填入。

~~~properties
log4j.rootLogger=INFO, stdout 
log4j.appender.stdout=org.apache.log4j.ConsoleAppender 
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout 
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n 
log4j.appender.logfile=org.apache.log4j.FileAppender 
log4j.appender.logfile.File=target/spring.log 
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout 
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c]- %m%n
~~~



#### 3.3.2、创建 Zookeeper 客户端

~~~Java
public class zkClient {

    private String connectString = "zcwcentos7:2181,zcwcentos7c1:2181,zcwcentos7c2:2181";
    private int sessionTimeout = 2000;
    private ZooKeeper zkClient;

    @Before
    public void init() throws IOException {
        zkClient = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            // 收到事件通知后的回调函数（用户的业务逻辑）
            @Override
            public void process(WatchedEvent watchedEvent) {
                /**
                 * 参数1：获取哪个节点下的子节点
                 * 参数2：true 使用初始化时的监听器方法
                 */
                List<String> children = null;
                try {
                    children = zkClient.getChildren("/", true);
                } catch (KeeperException e) {
                    e.printStackTrace();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                for (String child : children) {
                    System.out.println(child);
                }
            }
        });
    }
}
~~~



#### 3.3.3、创建子节点

~~~Java
/**
* 创建子节点
* 参数1：要创建的节点的路径
* 参数2：节点数据
* 参数3：节点权限
* 参数4：节点的类型
*/
@Test
public void create() throws InterruptedException, KeeperException {
    String nodeCreate = zkClient.create("/atguigu", "ss.avi".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
}
~~~

 

#### 3.3.4、获取子节点并监听节点变化

~~~Java
@Test
public void getChildren() throws InterruptedException, KeeperException {
    /**
     * 参数1：获取哪个节点下的子节点
     * 参数2：true 使用初始化时的监听器方法
     */
    List<String> children = zkClient.getChildren("/", true);

    for (String child : children) {
        System.out.println(child);
    }
	
    // 让程序不得终止
    while (true) {}
}
~~~



#### 3.3.5、判断 Znode 是否存在

~~~Java
@Test
public void exits() throws InterruptedException, KeeperException {
    Stat exists = zkClient.exists("/", false);

    System.out.println(exists==null ? "no exists" : "exists");
}
~~~



### 3.4、客户端向服务端写数据流程

#### 3.4.1、写流程之写入请求发送给`Leader`节点

![image-20230927193535386](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927193535386.png)

- 1、客户端向`Leader`节点发送写入请求
- 2、`Leader`节点自己先写一份，然后将请求发送到`Follower`
- 3、`Follower`写入后，发送应答到`Leader`
- 4、写入服务器数已经超过半数，可以向客户端发送应答
- 5、`Leader`继续向剩下的`Follower`发送写入请求
- 6、`Follower`写入后应答`Leader`



#### 3.4.2、写流程之写入请求发送给`Follower`节点

![image-20230927210451309](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927210451309.png)

- 1、客户端向`Follower`节点发送写入请求
- 2、`Follower`将写入请求发送给`Leader`
- 3、`Leader`节点自己先写一份，然后将请求发送到`Follower`
- 4、`Follower`写入后，发送应答到`Leader`
- 5、写入服务器已经超过半数，`Leader`向最先与客户端通信的`Follower`发送应答，要求其应答客户端
- 6、`Follower`应答客户端
- 7、`Leader`继续向未写入数据的`Follower`发送写入请求
- 8、`Follower`写入后应答`Leader`



## 4、服务器动态上下线监听案例

### 4.1、需求

- 某分布式系统中，主节点可以有多台，可以动态上下线，任意一台客户端都能实时感知到主节点服务器的上下线。

![image-20230927214856853](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927214856853.png)



### 4.2、具体实现

- 先在集群上创建`/servers`节点

~~~shell
[zk: zcwcentos7:2181(CONNECTED) 12] create /servers "servers"
~~~

- 代码如下：

~~~Java
// 服务端代码，向Zookeeper中注册

public class DistributeServer {
    private String connectString = "zcwcentos7:2181,zcwcentos7c1:2181,zcwcentos7c2:2181";
    private int sessionTimeout = 2000;
    private ZooKeeper zk;

    public static void main(String[] args) throws IOException, InterruptedException, KeeperException {

        DistributeServer distributeServer = new DistributeServer();

        // 1. 获取zk连接
        distributeServer.getConnect();

        // 2.注册服务器到zk集群
        distributeServer.register(args[0]);


        // 3.启动业务逻辑
        distributeServer.business();
    }

    private void business() throws InterruptedException {
        Thread.sleep(Long.MAX_VALUE);
    }

    /**
    * 注册服务器到zookeeper中，也就是在/servers节点下创建服务器信息节点
    */
    private void register(String hostName) throws InterruptedException, KeeperException {
	   /**
        * 创建子节点
        * 参数1：要创建的节点的路径
        * 参数2：节点数据
        * 参数3：节点权限
        * 参数4：节点的类型 —— 临时带序号
        */
        String create = zk.create("/servers", hostName.getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);

        System.out.println(hostName + "is online");
    }

    private void getConnect() throws IOException {
        zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {

            }
        });
    }
}
~~~

~~~Java
// 客户端监听Zookeeper中服务器的信息 ，也就是监听/servers节点的子节点的变化

public class DistributeClient {

    private ZooKeeper zooKeeper;
    private String connectString = "zcwcentos7:2181,zcwcentos7c1:2181,zcwcentos7c2:2181";
    private int sessionTimeout = 2000;

    public static void main(String[] args) throws Exception {

        DistributeClient distributeClient = new DistributeClient();

        // 1.获取zk连接
        distributeClient.getConnect();

        // 2.监听/server下面子节点的变化
        distributeClient.getServerList();

        // 3.业务逻辑
        distributeClient.business();
    }

    private void business() throws InterruptedException {

        Thread.sleep(Long.MAX_VALUE);
    }

    /**
    * 获取/servers节点的子节点
    */
    private void getServerList() throws Exception{
         /**
         * 参数1：获取哪个节点下的子节点
         * 参数2：true 使用初始化时的监听器方法
         */
        List<String> children = zooKeeper.getChildren("/servers", true);

        ArrayList<String> servers = new ArrayList<>();

        for (String child : children) {
            // 获取节点上的数据
            byte[] data = zooKeeper.getData("/servers/" + child, false, null);
            servers.add(new String(data));
        }

        System.out.println(servers);
    }

    private void getConnect() throws IOException {
        zooKeeper = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                try {
                    getServerList();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
    }
}
~~~



### 4.3、测试

- 在 Idea 上操作增加减少服务器
  - 启动 DistributeClient 客户端（如果已经启动过，不需要重启）
  - 启动 DistributeServer 服务

![image-20230927220826179](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927220826179.png)

![image-20230927220848225](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927220848225.png)





## 5、`Zookeeper `分布式锁案例

- 什么叫做分布式锁呢？
  - 比如说"进程 1"在使用该资源的时候，会先去获得锁，"进程 1"获得锁以后会对该资源保持独占，这样其他进程就无法访问该资源，"进程 1"用完该资源以后就将锁释放掉，让其他进程来获得锁，那么通过这个锁机制，我们就能保证了分布式系统中多个进程能够有序的访问该临界资源。那么我们把这个分布式环境下的这个锁叫作分布式锁。

![image-20230927231025573](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230927231025573.png)





### 5.1、原生 Zookeeper 实现分布式锁案例

#### 5.1.1、分布式锁实现

~~~Java
public class DistributeLock {

    private String connection = "zcwcentos7:2181,zcwcentos7c1:2181,zcwcentos7c2:2181";

    private int sessionTimeout = 2000;

    private ZooKeeper zooKeeper;

    private CountDownLatch countDownLatch = new CountDownLatch(1);

    private CountDownLatch latch = new CountDownLatch(1);

    // 前一个节点路径
    private String prefNode;

    // 当前节点路径
    private String currentMode;

    public DistributeLock() throws Exception {

        // 获取连接
        zooKeeper = new ZooKeeper(connection, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                if (watchedEvent.getState() == Event.KeeperState.SyncConnected) {
                    countDownLatch.countDown();
                }

                if (watchedEvent.getType() == Event.EventType.NodeDeleted && watchedEvent.getPath().equals(prefNode)) {
                    latch.countDown();
                }
            }
        });

        // 等待 zk 正常连接后，往下走程序
        countDownLatch.await();


        // 判断根节点 /locks 是否存在
        Stat exists = zooKeeper.exists("/locks", false);

        if (exists == null) {
            // 创建根节点
            zooKeeper.create("/locks", "locks".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        }
    }

    // 加锁
    public void zkLock() {
        // 创建对应的临时带序号节点
        try {
            currentMode = zooKeeper.create("/locks/" + "seq-", null, ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);

            // 判断创建的节点是否是最小的序号节点，如果是获取到锁，如果不是监听它序号前一个节点
            List<String> children = zooKeeper.getChildren("/locks", false);

            // 如果 children 只有一个值，那就直接获取锁；如果有多个节点，需要判断，谁最小
            if(children.size() == 1) {
                return;
            } else {
                // 将集合排好序
                Collections.sort(children);

                // 获取节点名称 seq-00000000
                String thisNode = currentMode.substring("/locks/".length());
                // 通过seq-00000000获取该节点在children集合中的位置
                int index = children.indexOf(thisNode);
                if (index == -1) {
                    throw new RuntimeException("数据异常");
                } else if (index == 0) {
                    return;
                } else {
                    // 需要监听前一个节点
                    prefNode = "/locks/" + children.get(index - 1);
                    zooKeeper.getData(prefNode,true,null);

                    // 等待监听
                    latch.await();

                    return;
                }
            }
        } catch (KeeperException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    // 解锁
    public void zkUnLock() {
        // 删除节点
        try {
            zooKeeper.delete(currentMode,-1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (KeeperException e) {
            e.printStackTrace();
        }
    }
}
~~~



#### 5.1.2、分布式锁测试

~~~Java
public class DistributeLockTest {
    public static void main(String[] args) throws Exception {
        final DistributeLock distributeLock1 = new DistributeLock();
        final DistributeLock distributeLock2 = new DistributeLock();

        new Thread(() -> {
            try {
                distributeLock1.zkLock();
                System.out.println("线程1启动，获取到锁");
                Thread.sleep(5000);

                distributeLock1.zkUnLock();
                System.out.println("线程1 释放锁");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();

        new Thread(() -> {
            try {
                distributeLock2.zkLock();
                System.out.println("线程2启动，获取到锁");
                Thread.sleep(5000);

                distributeLock2.zkUnLock();
                System.out.println("线程2 释放锁");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
~~~



### 5.2、`Curator `框架实现分布式锁案例

### 5.2.1、原生的 `Java API `开发存在的问题

- 会话连接是异步的，需要自己去处理。比如使用`CountDownLatch`。
- `Watch`需要重复注册，不然就不能生效。
- 开发的复杂性还是比较高的。
- 不支持多节点删除和创建。需要自己去递归。



### 5.2.2、Curator 是一个专门解决分布式锁的框架，解决了原生 Java API 开发分布式遇到的问题

- `https://curator.apache.org/index.html`




### 5.2.3、实操案例

##### 1、添加依赖

~~~xml
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>4.3.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.3.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-client</artifactId>
    <version>4.3.0</version>
</dependency>
~~~

##### 2、代码实现

~~~Java
public class CuratorLockTest {
    private String rootNode = "/locks";
    // zookeeper server 列表
    private String connectString = 
        "hadoop102:2181,hadoop103:2181,hadoop104:2181";
    // connection 超时时间
    private int connectionTimeout = 2000;
    // session 超时时间
    private int sessionTimeout = 2000;
    public static void main(String[] args) {
        new CuratorLockTest().test();
    }
    // 测试
    private void test() {
        // 创建分布式锁 1
        final InterProcessLock lock1 = new 
            InterProcessMutex(getCuratorFramework(), rootNode);
        // 创建分布式锁 2
        final InterProcessLock lock2 = new 
            InterProcessMutex(getCuratorFramework(), rootNode);
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 获取锁对象
                try {
                    lock1.acquire();
                    System.out.println("线程 1 获取锁");
                    // 测试锁重入
                    lock1.acquire();
                    System.out.println("线程 1 再次获取锁");
                    Thread.sleep(5 * 1000);
                    lock1.release();
                    System.out.println("线程 1 释放锁");
                    lock1.release();
                    System.out.println("线程 1 再次释放锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 获取锁对象
                try {
                    lock2.acquire();
                    System.out.println("线程 2 获取锁");
                    // 测试锁重入
                    lock2.acquire();
                    System.out.println("线程 2 再次获取锁");
                    Thread.sleep(5 * 1000);
                    lock2.release();
                    System.out.println("线程 2 释放锁");
                    lock2.release();
                    System.out.println("线程 2 再次释放锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
    // 分布式锁初始化
    public CuratorFramework getCuratorFramework (){
        //重试策略，初试时间 3 秒，重试 3 次
        RetryPolicy policy = new ExponentialBackoffRetry(3000, 3);
        //通过工厂创建 Curator
        CuratorFramework client = 
            CuratorFrameworkFactory.builder()
            .connectString(connectString)
            .connectionTimeoutMs(connectionTimeout)
            .sessionTimeoutMs(sessionTimeout)
            .retryPolicy(policy).build();
        //开启连接
        client.start();
        System.out.println("zookeeper 初始化完成...");
        return client;
    }
}
~~~



**6.1** **选举机制**

- 半数机制，超过半数的投票通过，即通过。
  - 第一次启动选举规则：投票过半数时，服务器 id 大的胜出
  - 第二次启动选举规则：
    - EPOCH 大的直接胜出
    - EPOCH 相同，事务 id 大的胜出
    - 事务 id 相同，服务器 id 大的胜出



**6.2** **生产集群安装多少** **zk** **合适？**

- 安装奇数台。

- 生产经验：

  - 10 台服务器：3 台 zk；

  - 20 台服务器：5 台 zk；

  - 100 台服务器：11 台 zk；

  - 200 台服务器：11 台 zk

- 服务器台数多：好处，提高可靠性；坏处：提高通信延时





















