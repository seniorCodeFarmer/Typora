## 1、消息队列

### 1.1、MQ的相关概念

#### 1.1.1、什么是`MQ`

- `MQ(message queue)`，从字面意思上看，本质是个队列，`FIFO`先入先出，只不过队列中存放的内容是`message`而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，`MQ`是一种非常常见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了`MQ`之后，消息发送上游只需要依赖`MQ`，不用依赖其他服务。




#### 1.1.2、为什么要用`MQ`

##### 1、流量消峰

- 举个例子，如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以克服这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好。




##### 2、应用解耦

- 以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间里，物流系统要处理的消息被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，下单用户感受不到物流系统的故障，提升系统的可用性。


![image-20230903183231580](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903183231580.png)



##### 3、异步处理

- 有些服务间调用是异步的，例如`A`调用`B`，`B`需要花费很长时间执行，但是`A`需要知道`B`什么时候可以执行完，以前一般有两种方式，`A`过一段时间去调用`B`的查询`api`查询。或者`A`提供一个`callback api`，B 执行完之后调用`api`通知`A`服务。这两种方式都不是很优雅，使用消息总线，可以很方便解决这个问题，`A `调用`B`服务后，只需要监听`B`处理完成的消息，当`B`处理完成后，会发送一条消息给`MQ`，`MQ`会将此消息转发给`A`服务。这样`A`服务既不用循环调用`B`的查询`api`，也不用提供`callback api`。同样`B`服务也不用做这些操作。`A`服务还能及时的得到异步处理成功的消息。


![image-20230903183327420](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903183327420.png)



#### 1.1.3、`MQ`的分类

##### 1、`ActiveMQ`

- 优点：单机吞吐量万级，时效性`ms`级，可用性高，基于主从架构实现高可用性，消息可靠性，较低的概率丢失数据
- 缺点：官方社区现在对`ActiveMQ 5.x`维护越来越少，高吞吐量场景较少使用。



##### 2、Kafka

- 大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开`Kafka`，这款为大数据而生的消息中间件，以其百万级`TPS`的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被`LinkedIn，Uber, Twitter, Netflix`等大公司所采纳。

  - 优点：性能卓越，单机写入`TPS`约在百万条/秒，最大的优点，就是吞吐量高。时效性`ms`级可用性非常高，`kafka`是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用，消费者采用`Pull`方式获取消息，消息有序，通过控制能够保证所有消息被消费且仅被消费一次；有优秀的第三方`Kafka Web`管理界面 `Kafka-Manager`；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的`MQ`功能，在大数据领域的实时计算以及日志采集被大规模使用

  - 缺点：`Kafka`单机超过 64 个队列/分区，`Load`会发生明显的飙高现象，队列越多，`load`越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢；




##### 3、`RocketMQ`

- `RocketMQ`出自阿里巴巴的开源产品，用`Java`语言实现，在设计时参考了`Kafka`，并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，`binglog`分发等场景。
- 优点：单机吞吐量十万级，可用性非常高，分布式架构，消息可以做到`0`丢失，`MQ`功能较为完善，还是分布式的，扩展性好，支持10亿级别的消息堆积，不会因为堆积导致性能下降，源码是`java`我们可以自己阅读源码，定制自己公司的`MQ`。
  
- 缺点：支持的客户端语言不多，目前是`java`及`c++`，其中`c++`不成熟；社区活跃度一般，没有在`MQ`核心中去实现`JMS`等接口，有些系统要迁移需要修改大量代码。




##### 4、RabbitMQ

- 2007 年发布，是一个在`AMQP`（高级消息队列协议）基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。
- 优点：由于`erlang`语言的高并发特性，性能较好；吞吐量到万级，MQ 功能比较完备,健壮、稳定、易用、跨平台、支持多种语言如：`Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP`等，支持`AJAX`文档齐全；开源提供的管理界面非常棒，用起来很好用，社区活跃度高；更新频率相当高`https://www.rabbitmq.com/news.html`。
  
- 缺点：商业版需要收费，学习成本较高。




### 1.2、`RabbitMQ`

#### 1.2.1、`RabbitMQ`的概念

- `RabbitMQ`是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑`RabbitMQ`其实是一个快递站，一个快递员帮你传递快件。`RabbitMQ`与快递站的主要区别在于，它不处理快件而是接收，存储和转发消息数据。



#### 1.2.2、四大核心概念

##### 1、生产者

- 产生数据发送消息的程序是生产者。

##### 2、交换机

- 交换机是`RabbitMQ`非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定。

##### 3、队列

- 队列是`RabbitMQ`内部使用的一种数据结构，尽管消息流经`RabbitMQ`和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式。

##### 4、消费者

- 消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。



#### 1.2.3、`RabbitMQ`核心部分

![image-20230903193152204](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903193152204.png)



#### 1.2.4、各个名词解释

![image-20230903193212297](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903193212297.png)

##### 1、`Broker`（代理）

- 接收和分发消息的应用，`RabbitMQ Server`就是`Message Broker`。

##### 2、`Virtual host`

- 出于多租户和安全因素设计的，把`AMQP`的基本组件划分到一个虚拟的分组中，类似于网络中的`namespace`概念。当多个不同的用户使用同一个`RabbitMQ server`提供的服务时，可以划分出多个`vhost`，每个用户在自己的`vhost`中创建`exchange／queue`等。

##### 3、`Connection`

- `publisher／consumer`和`broker`之间的`TCP`连接。

##### 4、`Channel`（信道）

- 如果每一次访问`RabbitMQ`都建立一个`Connection`，在消息量大的时候建立`TCP Connection`的开销将是巨大的，效率也较低。`Channel`是在`Connection`内部建立的逻辑连接，如果应用程序支持多线程，通常每个`thread`创建单独的 `Channel`进行通讯，`AMQP method`包含了`channel id`帮助客户端和`message broker`识别 channel，所以`channel`之间是完全隔离的。`Channel`作为轻量级的`Connection`极大减少了操作系统建立`TCP connection`的开销。

##### 5、`Exchange`

- `message`到达`broker`的第一站，根据分发规则，匹配查询表中的`routing key`，分发消息到`queue`中去。常用的类型有：`direct(point-to-point)、topic(publish-subscribe)、fanout(multicast)`。

##### 6、`Queue`

- 消息最终被送到这里等待`consumer`取走。

##### 7、`Binding`

- `exchange`和`queue`之间的虚拟连接，`binding`中可以包含`routing key`，`Binding`信息被保存到`exchange`中的查询表中，用于`message`的分发依据。



#### 1.2.5、安装

##### 1、官网地址

- `https://www.rabbitmq.com/download.html`

##### 2、文件上传

- 上传到/opt 目录下


![image-20230903205310206](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903205310206.png)

##### 3、安装文件（分别按照以下顺序安装）

- `rpm -ivh erlang-21.3-1.el7.x86_64.rpm`
- `yum install socat -y`
- `rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm`

##### 4、常用命令（按照以下顺序执行）

- 添加开机启动`RabbitMQ`服务


~~~powershell
chkconfig rabbitmq-server on
~~~

- 启动`RabbitMQ`服务


~~~shell
/sbin/service rabbitmq-server start
~~~

- 查看服务状态


~~~shell
/sbin/service rabbitmq-server status
~~~

- 停止服务（选择执行）


~~~shell
/sbin/service rabbitmq-server stop
~~~

- 开启 web 管理插件


~~~shell
rabbitmq-plugins enable rabbitmq_management
~~~

- 用默认账号密码(guest)访问地址`http://192.168.13.110:15672/`出现权限问题




##### 5、添加一个新的帐户

- 创建账号


~~~shell
rabbitmqctl add_user zcwRabbit 19981025、
~~~

- 设置用户角色


~~~shell
rabbitmqctl set_user_tags zcwRabbit administrator
~~~

- 设置用户权限
  - 用户`user_admin`具有`/vhost1`这个`virtual host`中所有资源的配置、写、读权限

~~~shell
set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
rabbitmqctl set_permissions -p "/" zcwRabbit ".*" ".*" ".*"
~~~

- 当前用户和角色


~~~shell
rabbitmqctl list_users
~~~



##### 6、重置命令

- 关闭应用的命令为


~~~shell
rabbitmqctl stop_app
~~~

- 清除的命令为


~~~shell
rabbitmqctl reset
~~~

- 重新启动命令为


~~~shell
rabbitmqctl start_app
~~~



## 2、`Hello World`

- 在本教程的这一部分中，我们将用 Java 编写两个程序。发送单个消息的生产者和接收消息并打印出来的消费者。我们将介绍 Java API 中的一些细节。


### 2.1、依赖

~~~xml
<!--指定 jdk 编译版本-->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>8</source>
                <target>8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
<dependencies>
    <!--rabbitmq 依赖客户端-->
    <dependency>
        <groupId>com.rabbitmq</groupId>
        <artifactId>amqp-client</artifactId>
        <version>5.8.0</version>
    </dependency>
    <!--操作文件流的一个依赖-->
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.6</version>
    </dependency>
</dependencies>
~~~



### 2.2、消息生产者

~~~java
public class Producer {
    private final static String QUEUE_NAME = "hello";
    public static void main(String[] args) throws Exception {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("182.92.234.71");
        factory.setUsername("admin");
        factory.setPassword("123");
        //channel 实现了自动 close 接口 自动关闭 不需要显示关闭
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        /**
 			* 生成一个队列
			* 1.队列名称
 			* 2.队列里面的消息是否持久化（磁盘）（默认消息存储在内存中）
 			* 3.该队列是否只供一个消费者进行消费 是否进行消息共享 true 可以多个消费者消费
 			* 4.是否自动删除 最后一个消费者端开连接以后 该队列是否自动删除 true 自动删除
 			* 5.其他参数
		*/
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        String message="hello world";
        /**
 			* 发送一个消息
			* 1.发送到那个交换机
 			* 2.路由的 key 是哪个
 			* 3.其他的参数信息
 			* 4.发送消息的消息体
 		*/
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
        System.out.println("消息发送完毕");
    }
}
~~~



### 2.3、消息消费者

~~~java
public class Consumer {
    private final static String QUEUE_NAME = "hello";
    public static void main(String[] args) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("182.92.234.71");
        factory.setUsername("admin");
        factory.setPassword("123");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        System.out.println("等待接收消息....");
        // 推送的消息如何进行消费的接口回调
        DeliverCallback deliverCallback = (consumerTag,delivery) -> {
            String message= new String(delivery.getBody());
            System.out.println(message);
        };
        //取消消费的一个回调接口 如在消费的时候队列被删除掉了
        CancelCallback cancelCallback = (consumerTag)->{
            System.out.println("消息消费被中断");
        };
        /**
         * 消费者消费消息
 		* 1.消费哪个队列
 		* 2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
 		* 3.消费者消费的回调
 		* 4.消费者取消消费的回调
 		*/
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}
~~~

~~~Java
@FunctionalInterface
public interface DeliverCallback {
    void handle(String var1, Delivery var2) throws IOException;
}
~~~

~~~Java
@FunctionalInterface
public interface CancelCallback {
    void handle(String var1) throws IOException;
}
~~~



## 3、`Work Queues`

- 工作队列（又称任务队列）的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。




### 3.1、轮训分发消息

- 在这个案例中我们会启动两个工作线程，一个消息发送线程，我们来看看他们两个工作线程是如何工作的。


#### 3.1.1、抽取工具类

~~~java
public class RabbitMqUtils {
    //得到一个连接的 channel
    public static Channel getChannel() throws Exception{
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("182.92.234.71");
        factory.setUsername("admin");
        factory.setPassword("123");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        return channel;
    }
}
~~~



#### 3.1.2、启动两个工作线程

~~~Java
public class Worker01 {
    private static final String QUEUE_NAME="hello";
    public static void main(String[] args) throws Exception {
        // 获取信道
        Channel channel = RabbitMqUtils.getChannel();

        // 消息的接收
        DeliverCallback deliverCallback = (consumerTag,delivery)->{
            String receivedMessage = new String(delivery.getBody());
            System.out.println("接收到消息:" + receivedMessage);
        };

        // 消息接收被取消时，执行下面的内容
        CancelCallback cancelCallback=(consumerTag)->{
            System.out.println(consumerTag+"消费者取消消费接口回调逻辑");
        };

        System.out.println("C2 消费者启动等待消费......");
        /**
         * 消费者消费消息
 		* 1.消费哪个队列
 		* 2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
 		* 3.消费者消费的回调
 		* 4.消费者取消消费的回调
 		*/
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}
~~~

![image-20230914153706734](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230914153706734.png)



#### 3.1.3、启动一个生产者线程

~~~Java
public class Task01 {
    private static final String QUEUE_NAME="hello";
    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        /**
        * 生成一个队列
        * 1.队列名称
        * 2.队列里面的消息是否持久化（磁盘）（默认消息存储在内存中）
        * 3.该队列是否只供一个消费者进行消费 是否进行消息共享 true 可以多个消费者消费
        * 4.是否自动删除 最后一个消费者端开连接以后 该队列是否自动删除 true 自动删除
        * 5.其他参数
		*/
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //从控制台当中接受信息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String message = scanner.next();
            // 发送消息
            channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
            System.out.println("发送消息完成:"+message);
        }
    }
}
~~~

~~~java 
public class Test1 {

    private static final String QUEUE_NAME="hello";

    public static void main(String[] args) throws Exception {
        ExecutorService executorService = Executors.newFixedThreadPool(3);

        Channel channel = RabbitMqUtils.getChannel();

        executorService.execute(() -> {
            // 接收成功后，消费者对消息进行处理的回调函数
            DeliverCallback deliverCallback = (var1, var2) -> {
                String s = new String(var2.getBody());
                System.out.println("C1接收到消息" + s);
            };

            CancelCallback cancelCallback = (consumerTag) -> {
                System.out.println(consumerTag);
            };

            System.out.println("C1等待消费");
            try {
                channel.basicConsume(QUEUE_NAME, false, deliverCallback, cancelCallback);
            } catch (IOException e) {
                e.printStackTrace();
            }
        });

        executorService.execute(() -> {
            DeliverCallback deliverCallback = (var1, var2) -> {
                String s = new String(var2.getBody());
                System.out.println("C2接收到消息" + s);

            };

            CancelCallback cancelCallback = (consumerTag) -> {
                System.out.println(consumerTag);
            };

            System.out.println("C2等待消费");
            try {
                channel.basicConsume(QUEUE_NAME, false, deliverCallback, cancelCallback);
            } catch (IOException e) {
                e.printStackTrace();
            }
        });

        executorService.execute(() -> {
            try {
                /**
                * 生成一个队列
                * 1.队列名称
                * 2.队列里面的消息是否持久化（磁盘）（默认消息存储在内存中）
                * 3.该队列是否只供一个消费者进行消费 是否进行消息共享 true 可以多个消费者消费
                * 4.是否自动删除 最后一个消费者端开连接以后 该队列是否自动删除 true 自动删除
                * 5.其他参数
                */
                channel.queueDeclare(QUEUE_NAME,false,false,false,null);
            } catch (IOException e) {
                e.printStackTrace();
            }

            Scanner scanner = new Scanner(System.in);
            while (scanner.hasNext()) {
                String next = scanner.next();
                try {
                    /**
                    * 发送一个消息
                    * 1.发送到那个交换机
                    * 2.路由的 key 是哪个，哪个队列
                    * 3.其他的参数信息
                    * 4.发送消息的消息体
                    */
                    channel.basicPublish("",QUEUE_NAME,null,next.getBytes());
                } catch (IOException e) {
                    e.printStackTrace();
                }
                System.out.println("发送消息成功：" + next);
            }
        });
    }
}
~~~

- 输出

~~~
C2等待消费
C1等待消费
AA
发送消息成功：AA
C1接收到消息AA
BB
发送消息成功：BB
C2接收到消息BB
AA
发送消息成功：AA
C1接收到消息AA
BB
发送消息成功：BB
C2接收到消息BB
~~~



### 3.2、消息应答

#### 3.2.1、概念

- 消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。`RabbitMQ`一旦向消费者传递了一条消息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费者的消息，它都无法接收到。
- 为了保证消息在发送过程中不丢失，`Rabbitmq`引入消息应答机制，消息应答就是：消费者在接收到消息并且处理该消息之后，告诉`Rabbitmq`它已经处理了，`Rabbitmq`可以把该消息删除了。



#### 3.2.2、自动应答

- 消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡，因为这种模式如果消息在接收到之前，消费者那边出现连接问题或者`channel`关闭，那么消息就丢失了，当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。



#### 3.2.3、手动应答

- `channel.basicAck`（用于肯定确认）
  - `RabbitMQ`已知道该消息并且成功的处理消息，可以将其丢弃了
- `channel.basicNack`（用于否定确认）
- `channel.basicReject`（用于否定确认）
  - 与`channel.basicNack`相比少一个`multiple`参数
  - 不处理该消息了直接拒绝，可以将其丢弃了



#### 3.2.4、`Multiple`的解释

- 手动应答的好处是可以批量应答并且减少网络拥堵

~~~Java
                             // multiple
channal.basicAsk(deliveryTag,true);
~~~

- `multiple`的 `true`和`false`代表不同意思
  - `true`代表批量应答`channel`上未应答的消息
    - 比如说`channel`上有传送`tag`的消息`5、6、7、8`，当前`tag`是`8`那么此时`5~8`的这些还未应答的消息都会被确认收到消息应答。
  - `false`同上面相比
    - 只会应答`tag=8`的消息，`5、6、7`这三个消息依然不会被确认收到消息应答。

![image-20230914211249876](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230914211249876.png)





#### 3.2.5、消息自动重新入队

- 如果消费者由于某些原因失去连接（其通道已关闭，连接已关闭或`TCP`连接丢失），导致消息未发送`ACK`确认，`RabbitMQ`将了解到消息未完全处理，会将对其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

![image-20230914222025902](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230914222025902.png)



#### 3.2.6、消息手动应答代码

- 默认消息采用的是自动应答，所以我们要想实现消息消费过程中不丢失，需要把自动应答改为手动应答，消费者在上面代码的基础上增加下面画红色部分代码。消费者在处理完消息后，进行一次应答。

![image-20230914222153636](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230914222153636.png)

- 消息生产者

~~~Java
public class Task02 {
    private static final String TASK_QUEUE_NAME = "ack_queue";
    public static void main(String[] argv) throws Exception {
        try (Channel channel = RabbitMqUtils.getChannel()) {
            channel.queueDeclare(TASK_QUEUE_NAME, false, false, false, null);
            Scanner sc = new Scanner(System.in);
            System.out.println("请输入信息");
            while (sc.hasNext()) {
                String message = sc.nextLine();
                channel.basicPublish("", TASK_QUEUE_NAME, null, message.getBytes("UTF-8"));
                System.out.println("生产者发出消息" + message);
            }
        }
    }
}
~~~

- 消费者01

~~~Java
public class Work03 {
    private static final String ACK_QUEUE_NAME="ack_queue";
    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        System.out.println("C1 等待接收消息处理时间较短");
        //消息消费的时候如何处理消息
        DeliverCallback deliverCallback=(consumerTag,delivery)->{
            String message= new String(delivery.getBody());
            SleepUtils.sleep(1);
            System.out.println("接收到消息:"+message);
            /**
 			* 1.消息标记 tag
 			* 2.是否批量应答未应答消息
 			*/
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        };
        //采用手动应答
        boolean autoAck = false;
        channel.basicConsume(ACK_QUEUE_NAME,autoAck,deliverCallback,(consumerTag)->{
            System.out.println(consumerTag+"消费者取消消费接口回调逻辑");
        });
    }
}
~~~

- 消费者02

~~~Java
public class Work04 {
    private static final String ACK_QUEUE_NAME="ack_queue";
    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        System.out.println("C2 等待接收消息处理时间较长");
        //消息消费的时候如何处理消息
        DeliverCallback deliverCallback=(consumerTag,delivery)->{
            String message= new String(delivery.getBody());
            SleepUtils.sleep(30);
            System.out.println("接收到消息:"+message);
            /**
 			* 1.消息标记 tag
			* 2.是否批量应答未应答消息
 			*/
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        };
        //采用手动应答
        boolean autoAck=false;
        channel.basicConsume(ACK_QUEUE_NAME,autoAck,deliverCallback,(consumerTag)->{
            System.out.println(consumerTag+"消费者取消消费接口回调逻辑");
        });
    }
}
~~~

- 睡眠工具类

~~~Java
public class SleepUtils {
    public static void sleep(int second){
        try {
            Thread.sleep(1000*second);
        } catch (InterruptedException _ignored) {
            Thread.currentThread().interrupt();
        }
    }
}
~~~



#### 3.2.7、手动应答效果演示

- 正常情况下消息发送方发送两个消息`C1`和`C2`分别接收到消息并进行处理。


![image-20230916104120487](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916104120487.png)

- 在发送者发送消息`dd`，发出消息之后的把`C2`消费者停掉，按理说该`C2`来处理该消息，但是由于它处理时间较长，在还未处理完，也就是说`C2`还没有执行 ack 代码的时候，`C2`被停掉了，此时会看到消息被`C1`接收到了，说明消息`dd`被重新入队，然后分配给能处理消息的`C1`处理了。

![image-20230916104154254](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916104154254.png)



### 3.3、`RabbitMQ`持久化

#### 3.3.1、概念

- 刚刚我们已经看到了如何处理任务不丢失的情况，但是如何保障当`RabbitMQ`服务停掉以后消息生产者发送过来的消息不丢失。默认情况下`RabbitMQ`退出或由于某种原因崩溃时，它会忽视队列和消息，除非告知它不要这样做。确保消息不会丢失需要做两件事：我们需要将队列和消息都标记为持久化。



#### 3.3.2、队列如何实现持久化

- 之前我们创建的队列都是非持久化的，`RabbitMq`如果重启的话，该队列就会被删除掉，如果要队列实现持久化需要在声明队列的时候把`durable`参数设置为持久化。

![image-20230916110151071](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916110151071.png)

- 但是需要注意的就是如果之前声明的队列不是持久化的，需要把原先队列先删除，或者重新创建一个持久化的队列，不然就会出现错误。

![image-20230916110225638](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916110225638.png)

- 以下为控制台中持久化与非持久化队列的 UI 显示区：


![image-20230916110243208](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916110243208.png)

- 这个时候即使重启 `Rabbitmq`队列也依然存在




#### 3.3.3、消息实现持久化

- 要想让消息实现持久化需要在消息生产者发送消息处修改代码，添加这个属性：`MessageProperties.PERSISTENT_TEXT_PLAIN`。

![image-20230916110341730](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916110341730.png)

- 将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉`RabbitMQ`将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候，但是还没有存储完，消息还在缓存的一个间隔点时，`RabbitMQ`宕机，此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经绰绰有余了。如果需要更强有力的持久化策略，参考后边课件发布确认章节。



#### 3.3.4、不公平分发

- 在最开始的时候我们学习到`RabbitMQ`分发消息采用的轮训分发，但是在某种场景下这种策略并不是很好，比方说有两个消费者在处理任务，其中有个消费者1处理任务的速度非常快，而另外一个消费者2处理速度却很慢，这个时候我们还是采用轮训分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是`RabbitMQ`并不知道这种情况，它依然很公平的进行分发。
- 为了避免这种情况，我们可以在消费者方设置参数`channel.basicQos(1)`。

![image-20230916111305394](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916111305394.png)

![image-20230916111435834](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916111435834.png)

- 意思就是如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，我目前只能处理一个任务，然后 `RabbitMq`就会把该任务分配给没有那么忙的那个空闲消费者，当然如果所有的消费者都没有完成手上任务，队列还在不停的添加新任务，队列有可能就会遇到被撑满的情况，这个时候就只能添加新的`worker`或者改变其他存储任务的策略。



#### 3.3.5、预取值

- 本身消息的发送就是异步发送的，所以在任何时候，`channel`上肯定不止只有一个消息，另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开发人员能限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。这个时候就可以通过使用`basic.qos`方法设置“预取计数”值来完成的。该值定义通道上允许的未确认消息的最大数量。一旦数量达到配置的数量，`RabbitMQ`将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认，例如，假设在通道上有未确认的消息5、6、7，8，并且通道的预取计数设置为 4，此时 `RabbitMQ`将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被`Ack`。比方说`tag=6`这个消息刚刚被确认`ACK`，`RabbitMQ`将会感知这个情况到并再发送一条消息。消息应答和`QoS`预取值对用户吞吐量有重大影响。通常，增加预取将提高向消费者传递消息的速度。虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的`RAM`消耗（随机存取存储器），应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同 100 到 300 范围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险。预取值为 1 是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中。对于大多数应用来说，稍微高一点的值将是最佳的。

~~~Java
channel.basicQos(4)
~~~



## 4、发布确认

### 4.1、发布确认原理

- 生产者将信道设置成`confirm`模式，一旦信道进入`confirm`模式，所有在该信道上面发布的消息都将会被指派一个唯一的 ID（从 1 开始），一旦消息被投递到所有匹配的队列之后，`broker`就会发送一个确认给生产者（包含消息的唯一 ID），这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，`broker`回传给生产者的确认消息中`delivery-tag`域包含了确认消息的序列号，此外`broker`也可以设置`basic.ack`的`multiple`域，表示到这个序列号之前的所有消息都已经得到了处理。
- `confirm`模式最大的好处在于它是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果`RabbitMQ`因为自身内部错误导致消息丢失，就会发送一条`nack`消息，生产者应用程序同样可以在回调方法中处理该`nack`消息。

> 注：消息应答与发布确认的区别？
>
> - 消息应答是指消费者在接收到消息并且处理该消息之后，告诉`Rabbitmq`它已经处理了，`Rabbitmq`可以把该消息删除了。（消费者处理之后）
> - 发布确认是指消息被投递到所有匹配的队列之后，`broker`就会发送一个确认给生产者，这就使得生产者知道消息已经正确到达目的队列了。（消息被投递到队列之后）



### 4.2、发布确认的策略

####  4.2.1、开启发布确认的方法

- 发布确认默认是没有开启的，如果要开启需要调用方法`confirmSelect`，每当你要想使用发布确认，都需要在 `channel`上调用该方法。

![image-20230916171029497](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916171029497.png)



#### 4.2.2、单个确认发布

- 这是一种简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布，`waitForConfirmsOrDie(long)`这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。
- 这种确认方式有一个最大的缺点就是：发布速度特别的慢，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了。

![image-20230916171201026](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916171201026.png)



#### 4.2.3、批量确认发布

- 上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

![image-20230916171540979](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916171540979.png)



#### 4.2.4、异步确认发布

- 异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说，它是利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功。

![image-20230916174102481](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230916174102481.png)

~~~Java
public static void publishMessageAsync() throws Exception {
    try (Channel channel = RabbitMqUtils.getChannel()) {
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        //开启发布确认
        channel.confirmSelect();
        /**
         * 线程安全有序的一个哈希表，适用于高并发的情况
         * 1.轻松的将序号与消息进行关联
         * 2.轻松批量删除条目 只要给到序列号
         * 3.支持并发访问
         */
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new 
            ConcurrentSkipListMap<>();
        /**
         * 确认收到消息的一个回调
         * 1.消息序列号
         * 2.true 可以确认小于等于当前序列号的消息
         * false 确认当前序列号消息
         */
        ConfirmCallback ackCallback = (sequenceNumber, multiple) -> {
            if (multiple) {
                //返回的是小于等于当前序列号的未确认消息 是一个 map
                ConcurrentNavigableMap<Long, String> confirmed = 
                    outstandingConfirms.headMap(sequenceNumber, true);
                //清除该部分未确认消息
                confirmed.clear();
            }else{
                //只清除当前序列号的消息
                outstandingConfirms.remove(sequenceNumber);
            }
        };
        /**
        * 消息确认失败回调函数
        */
        ConfirmCallback nackCallback = (sequenceNumber, multiple) -> {
            String message = outstandingConfirms.get(sequenceNumber);
            System.out.println("发布的消息"+message+"未被确认，序列号"+sequenceNumber);
        };
        /**
         * 添加一个异步确认的监听器，监听哪些消息成功了，哪些消息失败了
         * 1.确认收到消息的回调
         * 2.未收到消息的回调
         */
        channel.addConfirmListener(ackCallback, null);
        long begin = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            /**
             * channel.getNextPublishSeqNo()获取下一个消息的序列号
             * 通过序列号与消息体进行一个关联
             * 全部都是未确认的消息体
             */
            outstandingConfirms.put(channel.getNextPublishSeqNo(), message);
            channel.basicPublish("", queueName, null, message.getBytes());
        }
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息,耗时" + (end - begin) + 
                           "ms");
    }
}
~~~



## 5、交换机

- 在上一节中，我们创建了一个工作队列。我们假设的是工作队列背后，每个任务都恰好交付给一个消费者（工作进程）。在这一部分中，我们将做一些完全不同的事情-我们将消息传达给多个消费者。这种模式称为 ”发布/订阅”。
- 为了说明这种模式，我们将构建一个简单的日志系统。它将由两个程序组成：第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘，另外一个消费者接收到消息后把消息打印在屏幕上，事实上第一个程序发出的日志消息将广播给所有消费者的。



### 5.1、`Exchanges`

#### 5.1.1、`Exchanges`概念

- `RabbitMQ`消息传递模型的核心思想是: 生产者生产的消息从不会直接发送到队列。实际上，通常生产者甚至都不知道这些消息传递传递到了哪些队列中。
- 相反，生产者只能将消息发送到交换机(exchange)，交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把他们到许多队列中还是说应该丢弃它们。这就的由交换机的类型来决定。



#### 5.1.2、`Exchanges`的类型

- 直接（direct)
- 主题（topic）
- 标题（headers）
- 扇出（fanout）



#### 5.1.3、无名`Exchange`

- 在本教程的前面部分我们对`Exchange`一无所知，但仍然能够将消息发送到队列。之前能实现的原因是因为我们使用的是默认交换，我们通过空字符串(“”)进行标识。

![image-20230929152407397](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929152407397.png)

- 第一个参数是交换机的名称。空字符串表示默认或无名称交换机：消息能路由发送到队列中其实是由 `routingKey(bindingkey)`绑定`key`指定的，如果它存在的话。



### 5.2、临时队列

- 之前的章节我们使用的是具有特定名称的队列（还记得`hello`和`ack_queue`吗？）。队列的名称我们来说至关重要，我们需要指定我们的消费者去消费哪个队列的消息。
- 每当我们连接到`Rabbit`时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。其次一旦我们断开了消费者的连接，队列将被自动删除。
- 创建临时队列的方式如下：

~~~java
String queueName = channel.queueDeclare().getQueue();
~~~

- 创建出来之后长成这样：

![image-20230929152650892](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929152650892.png)



### 5.3、绑定（`bindings`）

- 什么是`bingding`呢，`binding`其实是`exchange`和`queue`之间的桥梁，它告诉我们`exchange`和哪个队列进行了绑定关系。比如说下面这张图告诉我们的就是`X`与`Q1`和`Q2`进行了绑定。

![image-20230929152816763](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929152816763.png)



### 5.4、`Fanout`

### 5.4.1、`Fanout`介绍







































































































































































































































































































































