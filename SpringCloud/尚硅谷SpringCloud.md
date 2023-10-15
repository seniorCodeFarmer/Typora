## 1、微服务与`SpringCloud`

### 1.1、微服务

- 强调的是服务的大小，它关注的是某一个点，是具体解决某一个问题和提供落地对应服务的一个服务应用。
- 狭义的看，可以看作`Eclipse`里面的一个个微服务工程或者`Module`。



#### 1.1.1、微服务架构

- 微服务架构是⼀种架构模式，它提倡将单⼀应⽤程序划分成⼀组⼩的服务，服务之间互相协调、互相配合，为⽤户提供最终价值。每个服务运⾏在其独⽴的进程中，服务与服务间采⽤轻量级的通信机制互相协作（通常是基于`HTTP`协议的`RESTful API`）。每个服务都围绕着具体业务进⾏构建，并且能够被独⽴的部署到⽣产环境、类⽣产环境等。另外，应当尽量避免统⼀的、集中式的服务管理机制，对具体的⼀个服务⽽⾔，应根据业务上下⽂，选择合适的语⾔、⼯具对其进⾏构建。
- `Dubbo`的通信机制是基于`RPC`远程过程调用。



#### 1.1.2、技术维度理解

- 微服务化的核心就是将传统的一站式应用，根据业务拆分成一个一个的服务，彻底地去耦合，每一个微服务提供单个业务功能的服务，一个服务做一件事，从技术角度看就是一种小而独立的处理过程，类似进程概念，能够自行单独启动或销毁，拥有自己独立的数据库。

- `https://martinfowler.com/articles/microservices.html`论文网址

![image-20230918133839274](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918133839274.png)



#### 1.1.3、微服务技术栈

![image-20230918134241179](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918134241179.png)





### 1.2、`SpringCloud`介绍

#### 1.2.1、概念

- `SpringCloud`基于`SpringBoot`提供了一套微服务解决方案，包括服务注册与发现，配置中心，全链路监控，服务网关，负载均衡，熔断器等组件，除了基于`NetFlix`的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。
- `SpringCloud`利用`SpringBoot`的开发便利性巧妙地简化了分布式系统基础设施的开发，`SpringCloud`为开发人员提供了快速构建分布式系统的一些工具，包括配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等，它们都可以用`SpringBoot`的开发风格做到一键启动和部署。
- `SpringBoot`并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过`SpringBoot`风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。
- 分布式微服务架构下的一站式解决方案，是各个微服务架构落地技术的集合体，俗称微服务全家桶。





#### 1.2.2、`SpringCloud`和`SpringBoot`是什么关系

- `SpringBoot`专注于快速方便的开发单个个体微服务。
- `SpringCloud`是关注全局的微服务协调整理治理框架，它将`SpringBoot`开发的一个个单体微服务整合并管理起来，为各个微服务之间提供，配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。
- `SpringBoot`可以离开`SpringCloud`独立使用开发项目，但是`SpringCloud`离不开`SpringBoot`，属于依赖的关系。
- `SpringBoot`专注于快速、方便的开发单个微服务个体，`SpringCloud`关注全局的服务治理框架。



#### 1.2.3、`SpringCloud`和`Dubbo`

![image-20230918174102648](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918174102648.png)

- 最大区别：`SpringCloud`抛弃了`Dubbo`的`RPC`通信，采用的是基于`HTTP`的`REST`方式。
- 严格来说，这两种方式各有优劣。虽然从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原生`RPC`带来的问题。而且`REST`相比`RPC`更为灵活，服务提供方和调用方的依赖只依靠一纸契约，不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适。
- 品牌机与组装机的区别
  - 很明显，`SpringCloud`的功能比`Dubbo`更加强大，涵盖面更广，而且作为`Spring`的拳头项目，它也能够与`Spring Framework、Spring Boot、Spring Data、Spring Batch`等其他`Spring`项目完美融合，这些对于微服务而言是至关重要的。使用`Dubbo`构建的微服务架构就像组装电脑，各环节我们的选择自由度很高，但是最终结果很有可能因为一条内存质量不行就点不亮了，总是让人不怎么放心，但是如果你是一名高手，那这些都不是问题；而`Spring Cloud`就像品牌机，在`Spring Source`的整合下，做了大量的兼容性测试，保证了机器拥有更高的稳定性，但是如果要在使用非原装组件外的东西，就需要对其基础有足够的了解。
- 社区支持与更新力度
  - 最为重要的是，`Dubbo`停止了`5`年左右的更新，虽然`2017.7`重启了。对于技术发展的新需求，需要由开发者自行拓展升级（比如当当网弄出了`DubboX`），这对于很多想要采用微服务架构的中小软件组织，显然是不太合适的，中小公司没有这么强大的技术能力去修改`Dubbo`源码+周边的一整套解决方案，并不是每一个公司都有阿里的大牛+真实的线上生产环境测试过。





### 1.3、技术选型

#### 1.3.1、`SpringBoot`

- `SpringBoot`源码 git 地址：https://github.com/spring-projects/spring-boot/releases/
- `SpringBoot2.0`新特性：https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes



#### 1.3.2、`SpringCloud`

- `SpringCloud`源码 gig 地址：https://github.com/spring-projects/spring-cloud
- `SpringCloud`官网：https://spring.io/projects/spring-cloud



#### 1.3.3、`Springcloud`和`Springboot`之间的依赖关系

- `https://spring.io/projects/spring-cloud#overview`
- 更详细的版本对应查看方法：`https://start.spring.io/actuator/inf`
  - 查看`JSON`串返回结果：

![image-20230918180056482](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918180056482.png)

![image-20230918175853731](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918175853731.png)





### 1.4、关于`Cloud`各种组件的停更/升级/替换

![image-20230918182045118](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918182045118.png)





## 2、微服务架构编码构建

### 2.1、`IDEA`新建`Project`工作空间

#### 2.1.1、微服务`cloud`整体聚合父工程`Project`

- `New Project`

![image-20230918225441899](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225441899.png)

- 聚合总父工程名字


![image-20230918225513629](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225513629.png)

- Maven选版本


![image-20230918225528855](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225528855.png)

- 工程名字


![image-20230918225538586](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225538586.png)

- 字符编码


![image-20230918225641246](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225641246.png)

- 注解生效激活


![image-20230918225652839](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225652839.png)

- `java`编译版本选`8`


![image-20230918225700965](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225700965.png)

- `File Type`过滤


![image-20230918225711152](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225711152.png)





### 2.2、父工程`POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.springcloud</groupId>
    <artifactId>cloud2020</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- 统一管理jar包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.18.18</lombok.version>
        <mysql.version>5.1.47</mysql.version>
        <druid.version>1.1.17</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>

    <!-- 子模块继承之后，提供作用：锁定版本+子modlue不用写groupId和version  -->
    <dependencyManagement>
        <dependencies>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
~~~



### 2.3、`Maven `工程落地细节复习

- `dependencyManagement`
  - `Maven`使用`dependencyManagement`元素来提供了一种管理依赖版本号的方式。
  - 通常会在一个组织或者项目的最顶层的父 POM 中看到`dependencyManagement`元素。

- 使用`pom.xml`中的`dependencyManagement`元素能让所有在子项目中引用一个依赖而不用显式的列出版本号。`Maven `会沿着父子层次向上走，直到找到一个拥有`dependencyManagement`元素的项目，然后它就会使用这个`dependencyManagement`元素中指定的版本号。


![image-20230918225949062](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918225949062.png)

- 这样做的好处就是：如果有多个子项目都引用同一样依赖，则可以避免在每个使用的子项目里都声明一个版本号，这样当想升级或切换到另一个版本时，只需要在顶层父容器里更新，而不需要一个一个子项目的修改 ；另外如果某个子项目需要另外的一个版本，只需要声明`version`就可。
- `dependencyManagement`里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。
- 如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且`version`和`scope`都读取自父`pom`。
- 如果子项目中指定了版本号，那么会使用子项目中指定的`jar`版本。



### 2.4、`Maven`中跳过单元测试

![image-20230918230046061](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918230046061.png)





### 2.5、`MySQL`驱动说明

~~~
com.mysql.jdbc.Driver和mysql-connector-java 5一起用。
com.mysql.cj.jdbc.Driver和mysql-connector-java 6 一起用。
~~~

- `com.mysql.cj.jdbc.Driver`是`mysql-connector-java 6`中的特性，相比`mysql-connector-java 5`多了一个时区：`serverTimezone`，把数据源配置的驱动改一下就好了
- `org.gjt.mm.mysql.Driver`是当时最好的`MySQL JDBC`，但不是`MySQL`公司的，然后`MySQL`将`MM`的`JDBC`驱动收为官方的`JDBC`驱动，所以将驱动的`package`也改了，但还保留了`org.gjt.mm.mysql.Driver`这个路径的引用，也就是你使用新版的`JDBC`驱动时还可以通过这个来引用，打开下载的新版`JDBC`驱动的`jar`文件可以看到，只有一个文件的目录是`org.gjt.mm.mysql`，就是为了兼容而设计的。





### 2.6、微服务提供者支付`Module`模块

- 新建`cloud-provider-payment8001`
- 改`POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8001</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
~~~

- 写`YML`

~~~yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver              # mysql驱动包 com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456


mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities    # 所有Entity别名类所在包
~~~

- 主启动类

~~~Java
@SpringBootApplication
public class PaymentMain8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
~~~

- 业务类-见`IDEA`



### 2.7、热部署

- `Adding devtools to your project`

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
~~~



- `Adding plugin to your pom.xml`

~~~xml
下段配置我们粘贴进聚合父类总工程的pom.xml里

<build>
    <finalName>你自己的工程名字</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

- `Enabling automatic build`

![image-20230919093220067](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919093220067.png)

![image-20230919093223463](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919093223463.png)



- `Update the value of`


![image-20230919093306741](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919093306741.png)

![image-20230919093311014](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919093311014.png)

- 重启`IDEA`



### 2.8、微服务消费者订单`Module`模块

- 新建`cloud-consumer-order80`
- 改`POM`

~~~xml
 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud190805</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-order80</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
~~~

- 写YML

~~~yaml
server:
  port: 80 
~~~

- 主启动类

~~~Java
@SpringBootApplication
public class MainApp80
{
    public static void main(String[] args)
    {
        SpringApplication.run(MainApp80.class,args);
    }
}
~~~

- 业务类-见`IDEA`
- `config` 配置类

~~~Java
@Configuration
public class ApplicationContextConfig
{
    @Bean
    public RestTemplate restTemplate()
    {
        return new RestTemplate();
    }
}
~~~



### 2.9、`RestTemplate`

#### 2.9.1、是什么

- 提供了多种便捷访问远程`Http`服务的方法， 是一种简单便捷的访问`restful`服务模板类，是`Spring`提供的用于访问`Rest`服务的客户端模板工具集。



#### 2.9.2、官网及使用

- `https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html`。

- 使用`restTemplate`访问`restful`接口非常的简单粗暴无脑。(`url, requestMap, ResponseBean.class`)这三个参数分别代表`REST`请求地址、请求参数、`HTTP`响应转换被转换成的对象类型。



### 2.10、工程重构

- 新建`cloud-api-commons`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-api-commons</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.1.0</version>
        </dependency>
    </dependencies>

</project>
~~~

- `entities`
- `Maven`命令 `clean install`
- 订单`80`和支付`8001`分别改造
  - 删除各自的原先有过的`entities`文件夹
  - 各自粘贴`POM`内容

~~~xml
<dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
    <groupId>com.atguigu.springcloud</groupId>
    <artifactId>cloud-api-commons</artifactId>
    <version>${project.version}</version>
</dependency>
~~~



## 3、`Eureka`服务注册与发现

### 3.1、`Eureka`基础知识

#### 3.1.1、什么是服务治理

- `Spring Cloud`封装了`Netflix`公司开发的`Eureka`模块来实现服务治理。
- 在传统的`Rpc`远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。



#### 3.1.2、`Eureka`是什么

- `Eureka`是`Netflix`的一个子模块，也是核心模块之一。`Eureka`是一个基于`REST`的服务，用于定位服务，以实现云端中间层服务发现和故障转移。
- 服务注册与发现对于微服务架构来说是非常重要的，有了服务发现与注册，只需要使用服务的标识符，就可以访问到服务，而不需要修改服务调用的配置文件了。功能类似于`Dubbo`的注册中心，比如`Zookeeper`。



#### 3.1.3、什么是服务注册与发现

- `Eureka`采用了`C-S`的设计架构。`Eureka Server`作为服务注册功能的服务器，它是服务注册中心。

- 而系统中的其他微服务，使用`Eureka`的客户端连接到`Eureka Server`并维持心跳连接。这样系统的维护人员就可以通过`Eureka Server`来监控系统中各个微服务是否正常运行。`SpringCloud`的一些其他模块（比如`Zuul`）就可以通过 `Eureka Server`来发现系统中的其他微服务，并执行相关的逻辑。



#### 3.1.4、`Eureka`两个组件

- `Eureka`包含两个组件：`Eureka Server`和`Eureka Client`：
  - `Eureka Server`提供服务注册服务，各个节点启动后，会在`EurekaServer`中进行注册，这样`EurekaServer`中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。
  - `EurekaClient`是一个`Java`客户端，用于简化`Eureka Server`的交互，客户端同时也具备一个内置的、使用轮询`(round-robin)`负载算法的负载均衡器。在应用启动后，将会向`Eureka Server`发送心跳（默认周期为`30`秒）。如果`Eureka Server`在多个心跳周期内没有接收到某个节点的心跳，`EurekaServer`将会从服务注册表中把这个服务节点移除（默认`90`秒）。


![image-20230919113750544](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919113750544.png)

![image-20230919113758249](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919113758249.png)



#### 3.1.5、为什么需要`Eureka`

- 假如我们的服务提供者`user-service`部署了多个实例，如图：


![image-20210713214925388](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20210713214925388.png)

- 思考几个问题：

  - `order-service`在发起远程调用的时候，该如何得知`user-service`实例的`ip`地址和端口？

  - 有多个`user-service`实例地址，`order-service`调用时该如何选择？

  - `order-service`如何得知某个`user-service`实例是否依然健康，是不是已经宕机？




#### 3.1.6、`Eureka`的结构和作用

- 这些问题都需要利用`SpringCloud`中的注册中心来解决，其中最广为人知的注册中心就是`Eureka`，其结构如下：


![image-20210713220104956](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20210713220104956.png)

- 问题`1`：`order-service`如何得知`user-service`实例地址？获取地址信息的流程如下：

  - `user-service`服务实例启动后，将自己的信息注册到`eureka-server`（`Eureka`服务端）。这个叫服务注册。

  - `eureka-server`保存服务名称和服务实例地址列表的映射关系。

  - `order-service`根据服务名称，拉取实例地址列表。这个叫服务发现或服务拉取。




- 问题`2`：`order-service`如何从多个`user-service`实例中选择具体的实例？

  - `order-service`从实例列表中利用负载均衡算法选中一个实例地址。

  - 向该实例地址发起远程调用。


​	

- 问题`3`：`order-service`如何得知某个`user-service`实例是否依然健康，是不是已经宕机？

  - `user-service`会每隔一段时间（默认`30`秒）向`eureka-server`发起请求，报告自己状态，称为心跳。

  - 当超过一定时间没有发送心跳时，`eureka-server`会认为微服务实例故障，将该实例从服务列表中剔除。

  - `order-service`拉取服务时，就能将故障实例排除了。


> 注：
>
> - 一个微服务，既可以是服务提供者，又可以是服务消费者，因此`eureka`将服务注册、服务发现等功能统一封装到了`eureka-client`端。



### 3.2、单机`Eureka`构建步骤

#### 3.2.1、`EurekaServer`端服务注册中心

- 新建`Module - cloud-eureka-server7001`

- 引入依赖

~~~xml
<!--eureka-server-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
~~~

- 写`YML`

~~~yaml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
    #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
    #defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
~~~

- 改主启动类

~~~Java
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001
{
    public static void main(String[] args)
    {
        SpringApplication.run(EurekaMain7001.class,args);
    }
}
~~~



#### 3.2.2、将`Eureka`客户端注册进服务中心

##### 1、`cloud-provider-payment8001`

- 改`POM`

~~~xml
<!--eureka-client-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
~~~

- 写`YML`

~~~yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver              # mysql驱动包 com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://localhost:7001/eureka

mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities    # 所有Entity别名类所在包
~~~

- 主启动

~~~Java
@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}

~~~

- 访问`http://localhost:7001/`

![image-20230924093103410](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924093103410.png)



##### 2、`cloud-consumer-order80`

- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-order80</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
~~~

- `YML`

~~~yaml
server:
	port: 80


spring:
    application:
        name: cloud-order-service

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://localhost:7001/eureka
~~~

- 主启动类

~~~Java
@SpringBootApplication
@EnableEurekaClient
public class OrderMain80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderMain80.class,args);
    }
}
~~~



![image-20230924093319926](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924093319926.png)

![image-20230924093329986](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924093329986.png)





### 3.3、集群`Eureka`构建步骤

#### 3.3.1、说明

![image-20230924093418013](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230924093418013.png)

- 问题：微服务`RPC`远程服务调用最核心的是什么?
  - 高可用，试想你的注册中心只有一个`only one`， 它出故障了那就呵呵了，会导致整个为服务环境不可用。
  - 解决办法：搭建`Eureka`注册中心集群 ，实现负载均衡`+`故障容错。



#### 3.3.2、`EurekaServer`集群环境构建步骤

##### 1、`Eureka`服务端集群配置

- 参考`cloud-eureka-server7001`，新建`cloud-eureka-server7002`，并修改`POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-eureka-server7002</artifactId>


    <dependencies>
        <!--eureka-server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--boot web actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>

</project>
~~~

- 修改映射配置
  - 找到`C:\Windows\System32\drivers\etc`路径下的`hosts`文件
  - 修改映射配置添加进`hosts`文件

~~~
127.0.0.1  eureka7001.com
127.0.0.1  eureka7002.com
~~~

- 修改`YML`

~~~yaml
#单机时的Yaml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
    #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ 
~~~

~~~yaml
#集群时的Yaml
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7002.com:7002/eureka/
~~~

~~~yaml
server:
  port: 7002

eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
~~~

- 主启动类

~~~Java
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7002
{
    public static void main(String[] args)
    {
        SpringApplication.run(EurekaMain7002.class,args);
    }
}
~~~



##### 2、注册`Eureka`客户端

- 将支付服务`8001`微服务发布到上面`2`台`Eureka`集群配置中
  - `YML`

~~~Yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  # 集群版
~~~

- 将订单服务`80`微服务发布到上面`2`台`Eureka`集群配置中
  - `YML`

~~~Yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  # 集群版
~~~

- 支付服务提供者`8001`集群环境构建
  - 新建`cloud-provider-payment8002`
  - 改`POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8002</artifactId>

    <dependencies>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- 写`YML`

~~~Yaml
server:
  port: 8002

spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver              # mysql驱动包
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  # 集群版
      #defaultZone: http://localhost:7001/eureka  # 单机版

mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities    # 所有Entity别名类所在包
~~~

- 主启动类

~~~Java
@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8002
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentMain8002.class,args);
    }
}
~~~

- 业务类



#### 3.3.3、负载均衡

- 订单支付服务访问地址不能写死。

~~~Java
//public static final String PAYMENT_SRV = "http://localhost:8001";
 
// 通过在eureka上注册过的微服务名称调用
@RestController
@Slf4j
public class OrderController {

    private static final String PAYMENT_SRV = "http://CLOUD-PAYMENT-SERVICE";

    @Resource
    private RestTemplate restTemplate;

    @PostMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment) {
        return restTemplate.postForObject(PAYMENT_SRV + "/payment/create",payment,CommonResult.class);
    }

    @GetMapping(value = "/consumer/payment/get/{id}")
    public CommonResult get(@PathVariable("id") Long id) {
        return restTemplate.getForObject(PAYMENT_SRV + "/payment/get/" + id , CommonResult.class);
    }

}
~~~

- 使用`@LoadBalanced`注解赋予`RestTemplate`负载均衡的能力。

~~~Java
@Configuration
public class ApplicationContextBean
{
    @Bean
    @LoadBalanced //使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
~~~

- 服务提供者交替被调用



#### 3.3.4、`actuator`微服务信息完善

##### 1、主机名称：服务名称修改

- 当前问题：含有主机名称

![image-20230928205815597](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928205815597.png)

- 修改`cloud-provider-payment8001`的`Yaml`文件

~~~Yaml
eureka:
  instance:
    instance-id: payment8001 #定义实例名
~~~

![image-20230928210431760](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928210431760.png)



##### 2、访问信息有`IP`信息提示

- 当前问题：没有`IP`提示
- 修改`cloud-provider-payment8001`的`Yaml`文件

~~~yaml
eureka:
  instance:
    instance-id: payment8001
    prefer-ip-address: true     #访问路径可以显示IP地址
~~~

![image-20230928211559543](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928211559543.png)



### 3.4、服务发现

- 对于注册进`eureka`里面的微服务，可以通过服务发现来获得该服务的信息。
- `@EnableDiscoveryClient`注解的作用：`@EnableDiscoveryClient`与`@EnableEurekaClient`类似，都是将一个微服务注册到`Eureka Server`（或其他服务发现组件，例如`Zookeeper、Consul`等）。
  - 共同点就是：都是能够让注册中心能够发现，扫描到该服务。
  - 不同点：`@EnableEurekaClient`只适用于`Eureka`作为注册中心，`@EnableDiscoveryClient`可以是其他注册中心。

- 从`Spring Cloud Edgware`开始，`@EnableDiscoveryClient`或`@EnableEurekaClient`可省略。只需加上相关依赖，并进行相应配置，即可将微服务注册到服务发现组件上。


~~~java
@RestController
@Slf4j
public class PaymentController
{
    @Value("${server.port}")
    private String serverPort;

    @Resource
    private PaymentService paymentService;

    @Resource
    private DiscoveryClient discoveryClient;

    @GetMapping(value = "/payment/discovery")
    public Object discovery()
    {
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            System.out.println(element);
        }

        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance element : instances) {
            System.out.println(element.getServiceId() + "\t" + element.getHost() + "\t" + element.getPort() + "\t" + element.getUri());
        }
        return this.discoveryClient;
    }
}

~~~

~~~java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient //服务发现
public class PaymentMain8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
~~~



### 3.5、`Eureka`自我保护

#### 3.5.1、故障现象

- 保护模式主要用于一组客户端和`Eureka Server`之间存在网络分区场景下的保护。一旦进入保护模式，`Eureka Server`将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。
- 如果在`Eureka Server`的首页看到以下这段提示，则说明`Eureka`进入了保护模式：

~~~
EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. 
RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE 
~~~

![image-20230928212800528](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928212800528.png)

- 属于`CAP`里面的`AP`分支（可用与分区容错性)




#### 3.5.2、导致原因

##### 1、为什么会产生`Eureka`自我保护机制？

- 为了防止`EurekaClient`可以正常运行，但是与`EurekaServer`网络不通的情况，`EurekaServer`不会立刻将`EurekaClient`服务剔除。



##### 2、什么是自我保护模式

- 默认情况下，如果`EurekaServer`在一定时间内没有接收到某个微服务实例的心跳，`EurekaServer`将会注销该实例（默认90秒）。但是当网络分区故障发生（延时、卡顿、拥挤）时，微服务与`EurekaServer`之间无法正常通信，以上行为可能变得非常危险了——因为微服务本身其实是健康的，此时本不应该注销这个微服务。`Eureka`通过“自我保护模式”来解决这个问题——当`EurekaServer`节点在短时间内丢失过多客户端时（可能发生了网络分区故障），那么这个节点就会进入自我保护模式。


![image-20230928213147925](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928213147925.png)

- 在自我保护模式中，`Eureka Server`会保护服务注册表中的信息，不再注销任何服务实例。

- 它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。一句话讲解：好死不如赖活着。

- 综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留）也不盲目注销任何健康的微服务。使用自我保护模式，可以让`Eureka`集群更加的健壮、稳定。

   

   

#### 3.5.3、禁止自我保护

##### 1、`Eureka`服务端`7001`

- 自我保护机制是默认行为

~~~properties
eureka.server.enable-self-preservation=true
#使用eureka.server.enable-self-preservation = false 可以禁用自我保护模式
~~~

![image-20230928223853565](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928223853565.png)



##### 2、`Eureka`客户端`8001`

- 默认

~~~properties
eureka.instance.lease-renewal-interval-in-seconds=30

eureka.instance.lease-expiration-duration-in-seconds=90
~~~

- 修改客户端`yaml`文件

~~~yaml
eureka:
#心跳检测与续约时间
#开发时设置小些，保证服务关闭后注册中心能及时剔除服务
  instance:
  #Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
  #Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
~~~

- 如果此时客户端服务实例停止运行，则该微服务信息会被`Eureka`在`2`秒后剔除。



## 4、`Zookeeper`服务注册与发现

- `zookeeper`是一个分布式协调工具，可以实现注册中心功能。
- `zookeeper`服务器取代`Eureka`服务器，`zk`作为服务注册中心。



###  7.1、服务提供者

#### 7.1.1、新建工程

- 新建`cloud-provider-payment8004`
- `POM`文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8004</artifactId>

    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!-- SpringBoot整合zookeeper客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`文件

~~~yaml
#8004表示注册到zookeeper服务器的支付服务提供者端口号
server:
  port: 8004
#服务别名----注册zookeeper到注册中心名称
spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: 192.168.13.110:2181
~~~

- 主启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
public class PaymentMain8004
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentMain8004.class,args);
    }
}
~~~

- `Controller`

~~~java
@RestController
public class PaymentController
{
    @Value("${server.port}")
    private String serverPort;

    @RequestMapping(value = "/payment/zk")
    public String paymentzk()
    {
        return "springcloud with zookeeper: "+serverPort+"\t"+ UUID.randomUUID().toString();
    }
}
~~~



#### 7.1.2、启动问题

- 启动`8004`注册进`zookeeper`

![image-20230928225437901](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928225437901.png)

- 解决`Zookeeper`版本`jar`包冲突问题


![image-20230928225514753](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928225514753.png)

- 排除`zk`冲突后的新`POM`

~~~xml
<!-- SpringBoot整合zookeeper客户端 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    <!--先排除自带的zookeeper3.5.3-->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!--添加zookeeper3.4.9版本-->
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.9</version>
</dependency>
~~~

- 思考：服务节点是临时节点还是持久节点
  - 很明显是临时节点

![image-20230928230044824](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928230044824.png)

- 验证测试：`http://localhost:8004/payment/zk`




### 7.2、服务消费者

#### 7.2.1、新建工程

- 新建`cloud-consumerzk-order80`
- `POM`文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumerzk-order81</artifactId>


    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- SpringBoot整合zookeeper客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`文件

~~~yaml
server:
  port: 80

spring:
  application:
    name: cloud-consumer-order
  cloud:
  #注册到zookeeper地址
    zookeeper:
      connect-string: 192.168.13.110:2181
~~~

- 主启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient
public class OrderZK80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderZK80.class,args);
    }
}
~~~

- 业务类

~~~java
@Configuration
public class ApplicationContextBean
{
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}

@RestController
public class OrderZKController
{
    public static final String INVOKE_URL = "http://cloud-provider-payment";

    @Autowired
    private RestTemplate restTemplate;

    @RequestMapping(value = "/consumer/payment/zk")
    public String paymentInfo()
    {
        String result = restTemplate.getForObject(INVOKE_URL+"/payment/zk", String.class);
        System.out.println("消费者调用支付服务(zookeeper)--->result:" + result);
        return result;
    }
}
~~~

![image-20230928235443250](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230928235443250.png)

- 验证测试`http://localhost/consumer/payment/zk`




## 5、`Consul`服务注册与发现

### 5.1、`Consul`简介

#### 5.1.1、`Consul`是什么

- `Consul`是一套开源的分布式服务发现和配置管理系统，由`HashiCorp`公司用`Go`语言开发。
- 提供了微服务系统中的服务治理、配置中心、控制总线等功能。这些功能中的每一个都可以根据需要单独使用，也可以一起使用以构建全方位的服务网格，总之`Consul`提供了一种完整的服务网格解决方案。
- 它具有很多优点。包括： 基于`raft`协议，比较简洁； 支持健康检查，同时支持`HTTP`和`DNS`协议支持跨数据中心的 `WAN`集群提供图形界面跨平台，支持`Linux、Mac、Windows`。



#### 5.1.2、`Consul`能干什么

- 服务发现：提供`HTTP`和`DNS`两种发现方式。
- 健康监测：支持多种方式，`HTTP、TCP、Docker、Shell`脚本定制化监控
- `KV`存储：`Key、Value`的存储方式
- 多数据中心：`Consul`支持多数据中心
- 可视化`Web`界面：



#### 5.1.3、下载

- `https://www.consul.io/downloads.html`



#### 5.1.4、中文文档

- `https://www.springcloud.cc/spring-cloud-consul.html`



### 8.2、安装并运行`Consul`

- 官网安装说明：`https://learn.hashicorp.com/consul/getting-started/install.html`
- 下载完成后只有一个`consul.exe`文件，硬盘路径下双击运行，查看版本号信息。
- 使用开发者模式启动：
  - `consul agent -dev`
  - 通过以下地址可以访问`Consul`的首页：`http://localhost:8500`



### 8.3、服务提供者

#### 8.3.1、新建工程

- 新建`cloud-providerconsul-payment8006`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-providerconsul-payment8006</artifactId>

    <dependencies>
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`

~~~yaml
###consul服务端口号
server:
  port: 8006

spring:
  application:
    name: consul-provider-payment
####consul注册中心地址
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
~~~

- 主启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain8006
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentMain8006.class,args);
    }
}
~~~

- 业务类

~~~java
@RestController
public class PaymentController
{
    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/consul")
    public String paymentInfo()
    {
        return "springcloud with consul: "+serverPort+"\t\t"+ UUID.randomUUID().toString();
    }
}
~~~

- 验证测试：`http://localhost:8006/payment/consul`

![image-20230929003954570](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929003954570.png)



### 8.4、服务消费者

#### 8.4.1、新建工程

- 新建`cloud-consumerconsul-order80`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumerconsul-order82</artifactId>

    <dependencies>
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`

~~~yaml
###consul服务端口号
server:
  port: 80

spring:
  application:
    name: cloud-consumer-order
####consul注册中心地址
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
~~~

- 主启动类

~~~Java
@SpringBootApplication
@EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
public class OrderConsulMain80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderConsulMain80.class,args);
    }
}
~~~

- 配置`Bean`

~~~java 
@Configuration
public class ApplicationContextBean
{
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
~~~

- 业务类

~~~Java
@RestController
public class OrderConsulController {

    public static final String INVOKE_URL = "http://consul-provider-payment";

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping(value = "/consumer/payment/consul")
    public String paymentInfo()
    {
        String result = restTemplate.getForObject(INVOKE_URL+"/payment/consul", String.class);
        System.out.println("消费者调用支付服务(consule)--->result:" + result);
        return result;
    }
}
~~~

- 验证测试：`http://localhost/consumer/payment/consul`



### 8.5、三个注册中心异同点

![image-20230929011133522](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929011133522.png)

![image-20230929011225928](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929011225928.png)

- 最多只能同时较好的满足两个。
  - `C:Consistency`（强一致性）
  - `A:Availability`（可用性）
  - `P:Partition tolerance`（分区容错性）
- `CAP`理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，因此，根据 `CAP`原理将`NoSQL`数据库分成了满足`CA`原则、满足`CP`原则和满足`AP`原则三大类：
  - `CA `- 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。
  - `CP`- 满足一致性，分区容错的系统，通常性能不是特别高。
  - `AP` - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。
- `AP(Eureka)`
  - 当网络分区出现后，为了保证可用性，系统`B`可以返回旧值，保证系统的可用性
  - 结论：违背了一致性`C`的要求，只满足可用性和分区容错，即`AP`

![image-20230929011617260](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929011617260.png)

- `CP(Zookeeper/Consul)`
  - 当网络分区出现后，为了保证一致性，就必须拒接请求，否则无法保证一致性
  - 结论：违背了可用性`A`的要求，只满足一致性和分区容错，即`CP`

![image-20230929011643538](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929011643538.png)



> 注：作为服务注册中心，`Eureka`比`Zookeeper`好在哪里？
>
> - 著名的`CAP`理论指出，一个分布式系统不可能同时满足`C(一致性)`、`A(可用性)`和`P(分区容错性)`。由于分区容错性`P`是在分布式系统中必须保证的，因此我们只能在`A`和`C`之间进行权衡。
> - `Zookeeper`保证的是`CP`
>   - 当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接宕机不可用。也就是说，服务注册功能对可用性的要求要高于一致性。 但是`zk`会出现这样一种情况，当`master`节点因为网络故障与其他节点失去联系时，剩余节点会重新进行`leader`选举。问题在于，选举`lealder`的时间太长，`30 ~ 120s`，且选举期间整个`zk`集群都是不可用的，这就导致在选举期间注册服务瘫痪。在云部署的环境下，因网络问题使得`zk`集群失去`master`节点是较大概率会发生的事，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。
> - `Eureka`保证的是`AP`
>   - `Eureka`看明白了这一点，因此在设计时就优先保证可用性。`Eureka`各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而`Eureka`的客户端在向某个`Eureka`注册时如果发现连接失败，则会自动切换至其它节点，只要有一台`Eureka`还在，就能保证注册服务可用（保证可用性），只不过查到的信息可能不是最新的（不保证强一致性）。 除此之外，`Eureka`还有一种自我保护机制，如果在`15`分钟内超过`85%`的节点都没有正常的心跳，那么`Eureka`就认为客户端与注册中心出现了网络故障，此时会出现以下几种情况：
>     - `Eureka`不再从注册列表中移除因为长时间没收到心跳而应该过期的服务。
>     - `Eureka`仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上（即保证当前节点依然可用）。
>     - 当网络稳定时，当前实例新的注册信息会被同步到其它节点中。
> - 因此，`Eureka`可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像`zookeeper`那样使整个注册服务瘫痪。





## 6、`Ribbon`负载均衡服务调用

### 9.1、概述

#### 9.1.1、是什么

- `Spring Cloud Ribbon`是基于`Netflix Ribbon`实现的一套客户端负载均衡的工具。
- 简单的说，`Ribbon`是`Netflix`发布的开源项目，主要功能是提供客户端的软件负载均衡算法和服务调用。`Ribbon`客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出`Load Balancer`（简称`LB`）后面所有的机器，`Ribbon`会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们很容易使用`Ribbon`实现自定义的负载均衡算法。



#### 9.1.2、官网资料

- `https://github.com/Netflix/ribbon/wiki/Getting-Started`

 

####  9.1.3、`Ribbon`能做什么

#####  1、`LB`负载均衡

-  简单的说就是将用户的请求平摊的分配到多个服务上，从而达到系统的`HA`（高可用）。
- 常见的负载均衡有软件`Nginx、LVS、硬件 F5`等。

######  （1）集中式`LB`

- 即在服务的消费方和提供方之间使用独立的`LB`设施（可以是硬件，如`F5`，也可以是软件，如`nginx`），由该设施负责把访问请求通过某种策略转发至服务的提供方。



###### （2）进程内`LB`

- 将`LB`逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选择出一个合适的服务器。
- `Ribbon`就属于进程内`LB`，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址。

 

### 9.2、`Ribbon`负载均衡演示

#### 9.2.1、架构说明

![image-20230929163327338](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929163327338.png)

- `Ribbon`在工作时分成两步：
  - 第一步先选择`EurekaServer`，它优先选择在同一个区域内负载较少的`server`。
  - 第二步再根据用户指定的策略，从`server`拉取到的服务注册列表中选择一个地址。其中`Ribbon`提供了多种策略：比如轮询、随机和根据响应时间加权。

- 总结：
  - `Ribbon`其实就是一个软负载均衡的客户端组件，它可以和其他所需请求的客户端结合使用，和`eureka`结合只是其中的一个实例。（`Eureka、Zookeeper、Consul、OpenFeign`都集成了`Ribbon`）

![image-20230929163451202](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929163451202.png)



#### 9.2.2、`RestTemplate`

##### 1、官网

- `https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html`

![image-20230929163630665](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929163630665.png)



##### 2`getForObject`方法 / `getForEntity`方法

- 返回对象为响应体中数据转化成的对象，基本上可以理解为`Json`。

![image-20230929163720945](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929163720945.png)

- 返回对象为`ResponseEntity`对象，包含了响应中的一些重要信息，比如响应头、响应状态码、响应体等。

![image-20230929163910543](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929163910543.png)



##### 3、`postForObject/postForEntity`

![image-20230929163932398](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929163932398.png)



### 9.3、`Ribbon`核心组件`IRule`

#### 9.3.1、`IRule`

##### 1、`IRule`继承图

- 根据特定算法中从服务列表中选取一个要访问的服务

![image-20230929171322835](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929171322835.png)



##### 2、所有算法

- `RoundRobinRule`：轮询
- `RandomRule`：随机
- `RetryRule`：先按照`RoundRobinRule`的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务。
- `WeightedResponseTimeRule`：对`RoundRobinRule`的扩展，响应速度越快的实例选择权重越大，越容易被选择。
- `BestAvailableRule`：会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务。
- `AvailabilityFilteringRule`：先过滤掉故障实例，再选择并发较小的实例。
- `ZoneAvoidanceRule`：默认规则，复合判断`server`所在区域的性能和`server`的可用性选择服务器。



#### 9.3.2、如何替换

- 修改`cloud-consumer-order80`
- 注意配置细节：官方文档明确给出了警告：这个自定义配置类不能放在`@ComponentScan`所扫描的当前包下以及子包下，否则我们自定义的这个配置类就会被所有的`Ribbon`客户端所共享，达不到特殊化定制的目的了。
- 新建`package`

![image-20230929171828694](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929171828694.png)

- 新建`MySelfRule`规则类

~~~java
@Configuration
public class MySelfRule
{
    @Bean
    public IRule myRule()
    {
        return new RandomRule();//定义为随机
    }
}
~~~

- 主启动类

~~~java
@SpringBootApplication
@EnableEurekaClient
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration=MySelfRule.class) // 添加
public class OrderMain80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderMain80.class,args);
    }
}
~~~

- 验证测试：`http://localhost/consumer/payment/get/1`



### 9.4、`Ribbon`负载均衡算法

#### 9.4.1、原理

- 负载均衡算法：`rest`接口第几次请求数`%`服务器集群总数量`=`实际调用服务器位置下标  ，每次服务重启动后`rest`接口计数从`1`开始。（这是轮询算法）

~~~java
List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
 
如：   List [0] instances = 127.0.0.1:8002
　　　 List [1] instances = 127.0.0.1:8001
 
8001+ 8002 组合成为集群，它们共计2台机器，集群总数为2， 按照轮询算法原理：
 
当总请求数为1时： 1 % 2 =1 对应下标位置为1 ，则获得服务地址为127.0.0.1:8001
当总请求数位2时： 2 % 2 =0 对应下标位置为0 ，则获得服务地址为127.0.0.1:8002
当总请求数位3时： 3 % 2 =1 对应下标位置为1 ，则获得服务地址为127.0.0.1:8001
当总请求数位4时： 4 % 2 =0 对应下标位置为0 ，则获得服务地址为127.0.0.1:8002
如此类推......
~~~



#### 9.4.2、源码





## 7、`OpenFeign`服务接口调用

### 10.1、概述

#### 10.1.1、`OpenFeign`是什么

- `Feign`是一个声明式`WebService`客户端。使用`Feign`能让编写`Web Service`客户端更加简单。
- 它的使用方法是定义一个服务接口然后在上面添加注解。`Feign`也支持可拔插式的编码器和解码器。`Spring Cloud`对`Feign`进行了封装，使其支持了`Spring MVC`标准注解和`HttpMessageConverters`。`Feign`可以与`Eureka`和`Ribbon`组合使用以支持负载均衡。



- 官方网站
  - 源码：`https://github.com/spring-cloud/spring-cloud-openfeign`
  - `https://cloud.spring.io/spring-cloud-static/Hoxton.SR1/reference/htmlsingle/#spring-cloud-openfeign`



#### 10.1.2、`OpenFeign`能干什么

- `Feign`旨在使编写`Java Http`客户端变得更容易。
  - 前面在使用`Ribbon+RestTemplate`时，利用`RestTemplate`对`http`请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，`Feign`在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在`Feign`的实现下，我们只需创建一个接口并使用注解的方式来配置它（以前是`Dao`接口上面标注`Mapper`注解，现在是一个微服务接口上面标注一个`Feign`注解即可），即可完成对服务提供方的接口绑定，简化了使用`Spring cloud Ribbon`时，自动封装服务调用客户端的开发量。

- `Feign`集成了`Ribbon`
  - 利用`Ribbon`维护了`Payment`的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与`Ribbon`不同的是，通过`feign`只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用。



#### 10.1.3、`Feign`和`OpenFeign`两者区别

#####  1、`Feign`

- `Feign`是`Spring Cloud`组件中的一个轻量级`RESTful`的`HTTP`服务客户端。
- `Feign`内置了`Ribbon`，用来做客户端负载均衡，去调用服务注册中心的服务。`Feign`的使用方式是：使用`Feign`的注解定义接口，调用这个接口，就可以调用服务注册中心的服务。

~~~xml 
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
~~~

##### 2、`OpenFeign`

- `OpenFeign`是`Spring Cloud`在`Feign`的基础上支持了`SpringMVC`的注解，如`@RequestMapping`等等。`OpenFeign`的`@FeignClient`可以解析`SpringMVC`的`@RequestMapping`注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
~~~



### 10.2、`OpenFeign`的使用步骤

- 接口`+`注解：微服务调用接口`+ @FeignClient`
- 新建`cloud-consumer-feign-order80`，`Feign`在消费端使用
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-feign-order80</artifactId>

    <dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`

~~~yaml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
~~~

- 主启动类，`@EnableFeignClients`开启`OpenFeign`功能

~~~java
@SpringBootApplication
@EnableFeignClients // 开启 OpenFeign 功能
public class OrderFeignMain80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderFeignMain80.class,args);
    }
}
~~~

- 业务类，`@FeignClient`使用`OpenFeign`功能

~~~java
// 业务逻辑接口+@FeignClient配置调用provider服务
@Component
@FeignClient(value = "CLOUD-PAYMENT-SERVICE") // 使用 OpenFeign 功能
public interface PaymentFeignService
{
    @GetMapping(value = "/payment/get/{id}")
    CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
}
~~~

~~~java
@RestController
public class OrderFeignController
{
    @Resource
    private PaymentFeignService paymentFeignService;

    @GetMapping(value = "/consumer/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id)
    {
        return paymentFeignService.getPaymentById(id);
    }
}
~~~

- 验证测试：`http://localhost/consumer/payment/get/1`

![image-20230929221317931](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929221317931.png)

> 注：`Feign`自带负载均衡配置项。



### 10.3、`OpenFeign`超时控制

#### 10.3.1、演示超时控制

- 服务提供方`8001`故意写暂停程序

~~~java
@GetMapping(value = "/payment/feign/timeout")
public String paymentFeignTimeOut() {
    System.out.println("*****paymentFeignTimeOut from port: " + serverPort);
    //暂停几秒钟线程
    try {
        TimeUnit.SECONDS.sleep(3);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return serverPort;
}
~~~

- 服务消费方`80`添加超时方法`PaymentFeignService`

~~~java
@GetMapping(value = "/payment/feign/timeout")
String paymentFeignTimeOut();
~~~

- 服务消费方`80`添加超时方法`OrderFeignController`

~~~java 
@GetMapping(value = "/consumer/payment/feign/timeout")
public String paymentFeignTimeOut()
{
    return paymentFeignService.paymentFeignTimeOut();
}
~~~

- 验证测试：`http://localhost/consumer/payment/feign/timeout`

![image-20230929223712265](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929223712265.png)

- `OpenFeign`默认等待`1`秒钟，超过后报错。



#### 10.3.2、`OpenFeign`超时控制是什么

-  默认`Feign`客户端只等待一秒钟，但是服务端处理需要超过`1`秒钟，导致`Feign`客户端不想等待了，直接返回报错。
- 为了避免这样的情况，有时候我们需要设置`Feign`客户端的超时控制。

![image-20230929223858187](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230929223858187.png)

- `OpenFeign`超时控制是由`Ribbon`控制的



#### 10.3.2、配置

- `YML`文件里需要开启`OpenFeign`客户端超时控制

~~~yaml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/

#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
#指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000
#指的是建立连接后从服务器读取到可用资源所用的时间
  ReadTimeout: 5000
~~~



### 10.4、`OpenFeign`日志打印功能

#### 10.4.1、概述

- `Feign`提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解`Feign`中`Http`请求的细节。
- 其实就是对`Feign`接口的调用情况进行监控和输出。



#### 10.4.2、日志级别

- `NONE`：默认的，不显示任何日志。
- `BASIC`：仅记录请求方法、`URL`、响应状态码及执行时间。
- `HEADERS`：除了`BASIC`中定义的信息之外，还有请求和响应的头信息。
- `FULL`：除了`HEADERS`中定义的信息之外，还有请求和响应的正文及元数据。



#### 10.4.3、配置

- 配置日志`Bean`

~~~java
@Configuration
public class FeignConfig
{
    @Bean
    Logger.Level feignLoggerLevel()
    {
        return Logger.Level.FULL;
    }
}
~~~

- `YML`文件里需要开启日志的`Feign`客户端

~~~yaml
logging:
  level:
    # feign日志以什么级别监控哪个接口
    com.atguigu.springcloud.service.PaymentFeignService: debug #PaymentFeignService 是 Feign 作用的接口
~~~



## 8、`Hystrix`断路器

### 8.1、概述

#### 8.1.1、分布式系统面临的问题

- 复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。
- 服务雪崩：
  - 多个微服务之间调用的时候，假设微服务`A`调用微服务`B`和微服务`C`，微服务`B`和微服务`C`又调用其它的微服务，这就是所谓的“扇出”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务`A`的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“雪崩效应”。
  - 对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟的服务进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。
  - 所以，通常当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接收流量，然后这个有问题的模块还调用了其他的模块，这样就会发生级联故障，或者叫雪崩。



#### 8.1.2、`Hystrix`是什么

- `Hystrix`是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，`Hystrix`能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。
- “断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（`FallBack`），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。



#### 8.1.3、`Hystrix`能做什么

- 服务熔断
- 服务降级
- 接近实时的监控



#### 8.1.4、官网资料

- `https://github.com/Netflix/Hystrix/wiki/How-To-Use`



### 8.2、`Hystrix`重要概念

#### 8.2.1、服务降级

- 所谓降级，一般是从整体负荷考虑，就是当某个服务熔断之后，服务器将不再被调用，此时客户端可以自己准备一个本地的`fallback`回调，返回一个缺省值，这样做，虽然服务水平下降，但好歹可用，比直接挂掉要强。服务器忙，请稍后再试，不让客户端等待并立刻返回一个友好提示，`fallback`。
- 哪些情况会触发降级：
  - 程序运行异常
  - 超时
  - 服务熔断触发服务降级
  - 线程池/信号量打满也会导致服务降级
- 服务降级既可以在服务端配置，也可以在客户端配置。



#### 8.2.2、服务熔断

- 熔断机制是应对雪崩效应的一种微服务链路保护机制。
- 当扇出链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回"错误"的响应信息。当检测到该节点微服务调用响应正常后恢复调用链路。在`SpringCloud`框架里熔断机制通过`Hystrix`实现。`Hystrix`会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是`@HystrixCommand`。
- 总言而之：一般是某个服务故障或者异常引起，类似现实世界中的“保险丝”，当某个异常条件被触发，直接熔断整个服务，而不是一直等到此服务超时。
- 类比保险丝达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级的方法并返回友好提示。`break`。
- 服务的降级->进而熔断->恢复调用链路。
- 熔断简单来说就是在单个服务出现问题，不可用时，为了避免引发更严重的问题，导致整个服务链路不可用的情况下，可以采用熔断的方式来避免。熔断一般情况下意味着服务的降级，可以理解为是一种异常兜底策略，需要服务的上游调用方来实现。



#### 8.2.3、服务限流

- 秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟N个，有序进行。`flowlimit`



### 8.3、案例

#### 8.3.1、新建提供者工程

- 新建`cloud-provider-hystrix-payment8001`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-hystrix-payment8001</artifactId>

    <dependencies>
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`

~~~yaml
server:
  port: 8001

spring:
  application:
    name: cloud-provider-hystrix-payment

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
      defaultZone: http://eureka7001.com:7001/eureka
~~~

- 主启动类

~~~java
@SpringBootApplication
@EnableEurekaClient //本服务启动后会自动注册进eureka服务中
public class PaymentHystrixMain8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentHystrixMain8001.class,args);
    }
}
~~~

- 业务类

~~~java
@Service
public class PaymentService
{
    /**
     * 正常访问，一切OK
     * @param id
     * @return
     */
    public String paymentInfo_OK(Integer id)
    {
        return "线程池:"+Thread.currentThread().getName()+"paymentInfo_OK,id: "+id+"\t"+"O(∩_∩)O";
    }

    /**
     * 超时访问，演示降级
     * @param id
     * @return
     */
    public String paymentInfo_TimeOut(Integer id)
    {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        return "线程池:"+Thread.currentThread().getName()+"paymentInfo_TimeOut,id: "+id+"\t"+"O(∩_∩)O，耗费3秒";
    }
}
~~~

~~~java
@RestController
@Slf4j
public class PaymentController
{
    @Autowired
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id)
    {
        String result = paymentService.paymentInfo_OK(id);
        log.info("****result: "+result);
        return result;
    }

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id) throws InterruptedException
    {
        String result = paymentService.paymentInfo_TimeOut(id);
        log.info("****result: "+result);
        return result;
    }
}
~~~

- 验证测试：
  - `http://localhost:8001/payment/hystrix/ok/31`
  - `http://localhost:8001/payment/hystrix/timeout/31`



#### 8.3.2、高并发测试

- `Jmeter`压测测试`http://localhost:8001/payment/hystrix/timeout/31`
  - 开启`20000`个线程访问`paymentInfo_TimeOut`服务
  - 所有服务都被拖慢

- 上面还是服务提供者`8001`自己测试，假如此时外部的消费者`80`也来访问，那消费者只能干等，最终导致消费端`80`不满意，服务端`8001`直接被拖死。



#### 8.3.3、新建消费者工程

- 新建`cloud-consumer-feign-hystrix-order80`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-feign-hystrix-order80</artifactId>

    <dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project> 
~~~

- `YUM`

~~~yaml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
~~~

- 主启动类

~~~Java
@SpringBootApplication
@EnableFeignClients
public class OrderHystrixMain80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderHystrixMain80.class,args);
    }
}
~~~

- 业务类

~~~java
@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
public interface PaymentHystrixService
{
    @GetMapping("/payment/hystrix/ok/{id}")
    String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}
~~~

~~~java
@RestController
@Slf4j
public class OrderHystirxController
{
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }
}
~~~

- 验证测试：`http://localhost/consumer/payment/hystrix/ok/31`
- 高并发测试：
  - `20000`个线程压`8001`
  - 消费端`80`微服务再去访问正常的`Ok`微服务`8001`地址：`http://localhost/consumer/payment/hystrix/ok/32`
    - 访问速度变慢
    - 超时



#### 8.3.4、故障现象和导致原因

- `8001`同一层次的其它接口服务被困死，因为`tomcat`线程池里面的工作线程已经被挤占完毕。
- `80`此时调用`8001`，客户端访问响应缓慢，转圈圈。
- 正因为有上述故障或不佳表现才有我们的降级/容错/限流等技术诞生。



#### 8.3.5、如何解决

- 超时导致服务器变慢（转圈）—— 超时不再等待
- 出错（宕机或程序运行出错）—— 出错要有兜底
- 解决：
  - 对方服务（`8001`）超时了，调用者（`80`）不能一直卡死等待，必须有服务降级。
  - 对方服务（`8001`）`down`机了，调用者（`80`）不能一直卡死等待，必须有服务降级。
  - 对方服务（`8001`）`OK`，调用者（`80`）自己出故障或有自我要求（自己的等待时间小于服务提供者），自己处理降级。



#### 8.3.6、服务降级

##### 1、`8001`先从自身找问题

- 设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处理，作服务降级`fallback`。

##### 2、`8001 Fallback`

- 业务类启用

~~~java
@Service
public class PaymentService
{
    /**
     * 正常访问，一切OK
     * @param id
     * @return
     */
    public String paymentInfo_OK(Integer id)
    {
        return "线程池:"+Thread.currentThread().getName()+"paymentInfo_OK,id: "+id+"\t"+"O(∩_∩)O";
    }

    /**
     * 超时访问，演示降级
     * @param id
     * @return
     */
    @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",commandProperties = {
            @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="3000")
    })
    public String paymentInfo_TimeOut(Integer id)
    {	// int age = 10 / 0;
        int second = 5;
        try { TimeUnit.SECONDS.sleep(second); } catch (InterruptedException e) { e.printStackTrace(); }
        return "线程池:"+Thread.currentThread().getName()+"paymentInfo_TimeOut,id: "+id+"\t"+"O(∩_∩)O，耗费秒: "+second;
    }
    public String paymentInfo_TimeOutHandler(Integer id){
        return "/(ㄒoㄒ)/调用支付接口超时或异常：\t"+ "\t当前线程池名字" + Thread.currentThread().getName();
    }
}

// commandProperties = { @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="3000"} 设置客户端能够等待的时间，超过这个时间报错或者走fallback方法

//一旦调用服务方法失败并抛出了错误信息后，会自动调用@HystrixCommand标注好的fallbackMethod调用类中的指定方法

// 线程池:HystrixTimer-1paymentInfo_TimeOut,id: 1 o(╥﹏╥)o  
~~~

![image-20231007194301025](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231007194301025.png)

> 注：当前服务不可用了，做服务降级，兜底的方案都是`paymentInfo_TimeOutHandler`。调用Fallback方法的是Hystrix提供的线程。

- 主启动类激活
  - 添加新注解`@EnableCircuitBreaker`

~~~java
@SpringBootApplication
@EnableEurekaClient //本服务启动后会自动注册进eureka服务中
@EnableCircuitBreaker // 启用 Hystrix 功能
public class PaymentHystrixMain8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(PaymentHystrixMain8001.class,args);
    }
}
~~~



##### 3、`80 Fallback`

- `80`订单微服务，也可以更好的保护自己，自己也依样画葫芦进行客户端降级保护。
- 自己配置过的热部署方式对`java`代码的改动明显，但对`@HystrixCommand`内属性的修改建议重启微服务。
- `YUM`

~~~yaml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/

feign:
  hystrix:
    enabled: true
~~~

- 主启动

~~~Java
@SpringBootApplication
@EnableFeignClients
@EnableHystrix  // 客户端启用Hystrix
public class OrderHystrixMain80
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderHystrixMain80.class,args);
    }
}
~~~

- 业务类

~~~Java
@RestController
@Slf4j
public class PaymentHystirxController
{
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod",commandProperties = {
        @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="1500")
    })
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }
    public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id)
    {
        return "我是消费者80,对方支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,o(╥﹏╥)o";
    }
}
~~~



##### 4、目前问题

- 每个业务方法对应一个兜底的方法，代码膨胀

![image-20231007221922008](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231007221922008.png)

> 注：`@DefaultProperties(defaultFallback = "")`
>
> - `1:1`，每个方法配置一个服务降级方法，技术上可以，实际上傻X。
> - `1:N`，除了个别重要核心业务有专属，其它普通的可以通过`@DefaultProperties(defaultFallback = "") `统一跳转到统一处理结果页面。
> - 通用的和独享的各自分开，避免了代码膨胀，合理减少了代码量。

~~~java
@RestController
@Slf4j
@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")
public class PaymentHystirxController
{
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    @HystrixCommand //加了@DefaultProperties属性注解，并且没有写具体方法名字，就用统一全局的
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }
    public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id)
    {
        return "paymentTimeOutFallbackMethod,对方系统繁忙，请10秒钟后再次尝试/(ㄒoㄒ)/";
    }

    public String payment_Global_FallbackMethod()
    {
        return "Global异常处理信息，请稍后再试，/(ㄒoㄒ)/~~";
    }
}
~~~



- 和业务逻辑混在一起
  - 服务降级，客户端去调用服务端，碰上服务端宕机或关闭
  - 本次案例服务降级处理是在客户端`80`实现完成的，与服务端`8001`没有关系，只需要为`Feign`客户端定义的接口添加一个服务降级处理的实现类即可实现解耦。
  - 根据`cloud-consumer-feign-hystrix-order80`已经有的`PaymentHystrixService`接口，重新新建一个类`(PaymentFallbackService)`实现该接口，统一为接口里面的方法进行异常处理。
  - 在`PaymentHystrixService`接口上`@FeignClient`注解中添加`fallback`属性，指定服务降级具体处理类。

~~~yaml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
#logging:
#  level:
#    # feign日志以什么级别监控哪个接口
#    com.atguigu.springcloud.service.PaymentFeignClientService: debug

# 用于服务降级 在注解@FeignClient中添加fallbackFactory属性值
feign:
  hystrix:
   enabled: true #在Feign中开启Hystrix
~~~

~~~Java
public class PaymentFallbackService implements PaymentHystrixService{
    @Override
    public String paymentInfo_OK(Integer id) {
        return "服务调用失败，提示来自：cloud-consumer-feign-order80";
    }

    @Override
    public String paymentInfo_TimeOut(Integer id) {
        return "服务调用失败，提示来自：cloud-consumer-feign-order80";
    }
}
~~~

~~~java
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT",fallback = PaymentFallbackService.class)
~~~

- 关闭服务端，验证调用：`http://localhost/consumer/payment/hystrix/ok/31`
- 此时服务端`provider`已经`down`了，但是我们做了服务降级处理，让客户端在服务端不可用时也会获得提示信息而不会挂起耗死服务器。

> 注：使用统一`Fallback`实现类进行服务降级处理，`Controller`层处理器方法上不需要使用`@HystrixCommand`注解。如果在处理器方法上使用了`@HystrixCommand`注解，那就必须声明一个方法，进行服务降级。



#### 8.3.7、服务熔断

##### 1、熔断是什么

- 大神论文：`https://martinfowler.com/bliki/CircuitBreaker.html`

-  熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务调用响应正常后，恢复调用链路。
  
- 在`Spring Cloud`框架里，熔断机制通过`Hystrix`实现。`Hystrix`会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是`5`秒内`20`次调用失败，就会启动熔断机制。熔断机制的注解是`@HystrixCommand`。

   


##### 2、实操

- 修改`cloud-provider-hystrix-payment8001`

~~~java 
//PaymentService=========服务熔断
@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
        @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),
        @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"), 
        @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),
        @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60")
})
public String paymentCircuitBreaker(@PathVariable("id") Integer id)
{
    if(id < 0)
    {
        throw new RuntimeException("******id 不能负数");
    }
    String serialNumber = IdUtil.simpleUUID();

    return Thread.currentThread().getName()+"\t"+"调用成功，流水号: " + serialNumber;
}
public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id)
{
    return "id 不能负数，请稍后再试，/(ㄒoㄒ)/~~   id: " +id;
}

// @HystrixProperty配置意思是：至少10次请求才统计失败率,失败率达到60%,这才开启服务熔断,平时错误只是服务降级。
// sleepWindowInMilliseconds：断路器跳闸后，在此值的时间的内，hystrix会拒绝新的请求，只有过了这个时间断路器才会打开闸门。默认值：5000
~~~

~~~java 
@GetMapping("/payment/circuit/{id}")
public String paymentCircuitBreaker(@PathVariable("id") Integer id)
{
    String result = paymentService.paymentCircuitBreaker(id);
    log.info("****result: "+result);
    return result;
}
~~~

- 验证测试：`http://localhost:8001/payment/circuit/-31`，多次错误，然后慢慢正确，发现刚开始不满足条件，就算是正确的访问地址也不能进行。



##### 3、总结

![image-20231007235431041](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231007235431041.png)

- 熔断类型
  - 熔断打开：请求不再进行调用当前服务，内部设置时钟一般为`MTTR`（平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态。
  - 熔断关闭：熔断关闭不会对服务进行熔断。
  - 熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断。

- 官网断路器步骤

![image-20231007235546771](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231007235546771.png)

![image-20231007235558088](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231007235558088.png)

- 断路器在什么情况下开始起作用：
  - 当满足一定的阀值的时候（默认`10`秒内超过`20`个请求次数，请求失败率达到`50%`）断路器将会开启，当开启时，所有请求都不会进行转发，一段时间之后（默认是`5`秒），这个时候断路器是半开状态，会让其中一个请求进行转发，如果成功，断路器会关闭，若失败，继续开启。重复`4`和`5`。

![image-20231007235618782](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231007235618782.png)

- 
  涉及到断路器的三个重要参数：快照时间窗、请求总数阀值、错误百分比阀值。

  - 快照时间窗：断路器确定是否打开需要统计一些请求和错误数据，而统计的时间范围就是快照时间窗，默认为最近的`10`秒。
  - 请求总数阀值：在快照时间窗内，必须满足请求总数阀值才有资格熔断。默认为`20`，意味着在`10`秒内，如果该`hystrix`命令的调用次数不足`20`次，即使所有的请求都超时或其他原因失败，断路器都不会打开。
  - 错误百分比阀值：当请求总数在快照时间窗内超过了阀值，比如发生了`30`次调用，如果在这`30`次调用中，有`15`次发生了超时异常，也就是超过`50%`的错误百分比，在默认设定`50%`阀值情况下，这时候就会将断路器打开。

- 断路器打开之后
  - 再有请求调用的时候，将不会调用主逻辑，而是直接调用降级`fallback`。通过断路器，实现了自动地发现错误并将主逻辑切换为降级逻辑，减少响应延迟的效果。
  - 原来的主逻辑要如何恢复呢？对于这一问题，`hystrix`也为我们实现了自动恢复功能。当断路器打开，对主逻辑进行熔断之后，`hystrix`会启动一个休眠时间窗，在这个时间窗内，降级逻辑是临时的成为主逻辑，当休眠时间窗到期，断路器将进入半开状态，释放一次请求到原来的主逻辑上，如果此次请求正常返回，那么断路器将继续闭合，主逻辑恢复，如果这次请求依然有问题，断路器继续进入打开状态，休眠时间窗重新计时。

~~~java
//========================All
@HystrixCommand(fallbackMethod = "str_fallbackMethod",
        groupKey = "strGroupCommand",
        commandKey = "strCommand",
        threadPoolKey = "strThreadPool",

        commandProperties = {
                // 设置隔离策略，THREAD 表示线程池 SEMAPHORE：信号池隔离
                @HystrixProperty(name = "execution.isolation.strategy", value = "THREAD"),
                // 当隔离策略选择信号池隔离的时候，用来设置信号池的大小（最大并发数）
                @HystrixProperty(name = "execution.isolation.semaphore.maxConcurrentRequests", value = "10"),
                // 配置命令执行的超时时间
                @HystrixProperty(name = "execution.isolation.thread.timeoutinMilliseconds", value = "10"),
                // 是否启用超时时间
                @HystrixProperty(name = "execution.timeout.enabled", value = "true"),
                // 执行超时的时候是否中断
                @HystrixProperty(name = "execution.isolation.thread.interruptOnTimeout", value = "true"),
                // 执行被取消的时候是否中断
                @HystrixProperty(name = "execution.isolation.thread.interruptOnCancel", value = "true"),
                // 允许回调方法执行的最大并发数
                @HystrixProperty(name = "fallback.isolation.semaphore.maxConcurrentRequests", value = "10"),
                // 服务降级是否启用，是否执行回调函数
                @HystrixProperty(name = "fallback.enabled", value = "true"),
                // 是否启用断路器
                @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),
                // 该属性用来设置在滚动时间窗中，断路器熔断的最小请求数。例如，默认该值为 20 的时候，
                // 如果滚动时间窗（默认10秒）内仅收到了19个请求， 即使这19个请求都失败了，断路器也不会打开。
                @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "20"),
                // 该属性用来设置在滚动时间窗中，表示在滚动时间窗中，在请求数量超过
                // circuitBreaker.requestVolumeThreshold 的情况下，如果错误请求数的百分比超过50,
                // 就把断路器设置为 "打开" 状态，否则就设置为 "关闭" 状态。
                @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50"),
                // 该属性用来设置当断路器打开之后的休眠时间窗。 休眠时间窗结束之后，
                // 会将断路器置为 "半开" 状态，尝试熔断的请求命令，如果依然失败就将断路器继续设置为 "打开" 状态，
                // 如果成功就设置为 "关闭" 状态。
                @HystrixProperty(name = "circuitBreaker.sleepWindowinMilliseconds", value = "5000"),
                // 断路器强制打开
                @HystrixProperty(name = "circuitBreaker.forceOpen", value = "false"),
                // 断路器强制关闭
                @HystrixProperty(name = "circuitBreaker.forceClosed", value = "false"),
                // 滚动时间窗设置，该时间用于断路器判断健康度时需要收集信息的持续时间
                @HystrixProperty(name = "metrics.rollingStats.timeinMilliseconds", value = "10000"),
                // 该属性用来设置滚动时间窗统计指标信息时划分"桶"的数量，断路器在收集指标信息的时候会根据
                // 设置的时间窗长度拆分成多个 "桶" 来累计各度量值，每个"桶"记录了一段时间内的采集指标。
                // 比如 10 秒内拆分成 10 个"桶"收集这样，所以 timeinMilliseconds 必须能被 numBuckets 整除。否则会抛异常
                @HystrixProperty(name = "metrics.rollingStats.numBuckets", value = "10"),
                // 该属性用来设置对命令执行的延迟是否使用百分位数来跟踪和计算。如果设置为 false, 那么所有的概要统计都将返回 -1。
                @HystrixProperty(name = "metrics.rollingPercentile.enabled", value = "false"),
                // 该属性用来设置百分位统计的滚动窗口的持续时间，单位为毫秒。
                @HystrixProperty(name = "metrics.rollingPercentile.timeInMilliseconds", value = "60000"),
                // 该属性用来设置百分位统计滚动窗口中使用 “ 桶 ”的数量。
                @HystrixProperty(name = "metrics.rollingPercentile.numBuckets", value = "60000"),
                // 该属性用来设置在执行过程中每个 “桶” 中保留的最大执行次数。如果在滚动时间窗内发生超过该设定值的执行次数，
                // 就从最初的位置开始重写。例如，将该值设置为100, 滚动窗口为10秒，若在10秒内一个 “桶 ”中发生了500次执行，
                // 那么该 “桶” 中只保留 最后的100次执行的统计。另外，增加该值的大小将会增加内存量的消耗，并增加排序百分位数所需的计算时间。
                @HystrixProperty(name = "metrics.rollingPercentile.bucketSize", value = "100"),
                // 该属性用来设置采集影响断路器状态的健康快照（请求的成功、 错误百分比）的间隔等待时间。
                @HystrixProperty(name = "metrics.healthSnapshot.intervalinMilliseconds", value = "500"),
                // 是否开启请求缓存
                @HystrixProperty(name = "requestCache.enabled", value = "true"),
                // HystrixCommand的执行和事件是否打印日志到 HystrixRequestLog 中
                @HystrixProperty(name = "requestLog.enabled", value = "true"),
        },
        threadPoolProperties = {
                // 该参数用来设置执行命令线程池的核心线程数，该值也就是命令执行的最大并发量
                @HystrixProperty(name = "coreSize", value = "10"),
                // 该参数用来设置线程池的最大队列大小。当设置为 -1 时，线程池将使用 SynchronousQueue 实现的队列，
                // 否则将使用 LinkedBlockingQueue 实现的队列。
                @HystrixProperty(name = "maxQueueSize", value = "-1"),
                // 该参数用来为队列设置拒绝阈值。 通过该参数， 即使队列没有达到最大值也能拒绝请求。
                // 该参数主要是对 LinkedBlockingQueue 队列的补充,因为 LinkedBlockingQueue
                // 队列不能动态修改它的对象大小，而通过该属性就可以调整拒绝请求的队列大小了。
                @HystrixProperty(name = "queueSizeRejectionThreshold", value = "5"),
        }
)
public String strConsumer() {
    return "hello 2020";
}
public String str_fallbackMethod()
{
    return "*****fall back str_fallbackMethod";
}
 
~~~





#### 8.3.8、服务限流



#### 8.3.9、`Hystrix`工作流程

- `https://github.com/Netflix/Hystrix/wiki/How-it-Works`

![image-20231008001406039](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231008001406039.png)



创建 HystrixCommand（用在依赖的服务返回单个操作结果的时候） 或 HystrixObserableCommand（用在依赖的服务返回多个操作结果的时候） 对象。
2	命令执行。其中 HystrixComand 实现了下面前两种执行方式；而 HystrixObservableCommand 实现了后两种执行方式：execute()：同步执行，从依赖的服务返回一个单一的结果对象， 或是在发生错误的时候抛出异常。queue()：异步执行， 直接返回 一个Future对象， 其中包含了服务执行结束时要返回的单一结果对象。observe()：返回 Observable 对象，它代表了操作的多个结果，它是一个 Hot Obserable（不论 "事件源" 是否有 "订阅者"，都会在创建后对事件进行发布，所以对于 Hot Observable 的每一个 "订阅者" 都有可能是从 "事件源" 的中途开始的，并可能只是看到了整个操作的局部过程）。toObservable()： 同样会返回 Observable 对象，也代表了操作的多个结果，但它返回的是一个Cold Observable（没有 "订阅者" 的时候并不会发布事件，而是进行等待，直到有 "订阅者" 之后才发布事件，所以对于 Cold Observable 的订阅者，它可以保证从一开始看到整个操作的全部过程）。
3	若当前命令的请求缓存功能是被启用的， 并且该命令缓存命中， 那么缓存的结果会立即以 Observable 对象的形式 返回。
4	检查断路器是否为打开状态。如果断路器是打开的，那么Hystrix不会执行命令，而是转接到 fallback 处理逻辑（第 8 步）；如果断路器是关闭的，检查是否有可用资源来执行命令（第 5 步）。
5	线程池/请求队列/信号量是否占满。如果命令依赖服务的专有线程池和请求队列，或者信号量（不使用线程池的时候）已经被占满， 那么 Hystrix 也不会执行命令， 而是转接到 fallback 处理逻辑（第8步）。
6	Hystrix 会根据我们编写的方法来决定采取什么样的方式去请求依赖服务。HystrixCommand.run() ：返回一个单一的结果，或者抛出异常。HystrixObservableCommand.construct()： 返回一个Observable 对象来发射多个结果，或通过 onError 发送错误通知。
7	Hystrix会将 "成功"、"失败"、"拒绝"、"超时" 等信息报告给断路器， 而断路器会维护一组计数器来统计这些数据。断路器会使用这些统计数据来决定是否要将断路器打开，来对某个依赖服务的请求进行 "熔断/短路"。
8	当命令执行失败的时候， Hystrix 会进入 fallback 尝试回退处理， 我们通常也称该操作为 "服务降级"。而能够引起服务降级处理的情况有下面几种：第4步： 当前命令处于"熔断/短路"状态，断路器是打开的时候。第5步： 当前命令的线程池、 请求队列或 者信号量被占满的时候。第6步：HystrixObservableCommand.construct() 或 HystrixCommand.run() 抛出异常的时候。
9	当Hystrix命令执行成功之后， 它会将处理结果直接返回或是以Observable 的形式返回。
tips：如果我们没有为命令实现降级逻辑或者在降级处理逻辑中抛出了异常， Hystrix 依然会返回一个 Observable 对象， 但是它不会发射任何结果数据， 而是通过 onError 方法通知命令立即中断请求，并通过onError()方法将引起命令失败的异常发送给调用者。



#### 8.3.10、服务监控`HystrixDashboard`

##### 1、概述

- 除了隔离依赖服务的调用以外，`Hystrix`还提供了准实时的调用监控（`Hystrix Dashboard`），`Hystrix`会持续地记录所有通过`Hystrix`发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。`Netflix`通过`hystrix-metrics-event-stream`项目实现了对以上指标的监控。`Spring Cloud`也提供了`Hystrix Dashboard`的整合，对监控内容转化成可视化界面。

   

##### 2、实操

- 新建`cloud-consumer-hystrix-dashboard9001`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-hystrix-dashboard9001</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
~~~

- `YUM`

~~~yaml
server:
  port: 9001
~~~

- 主启动类

~~~java
@SpringBootApplication
@EnableHystrixDashboard
public class HystrixDashboardMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixDashboardMain9001.class,args);
    }
}
~~~

- 所有`Provider`微服务提供类`(8001/8002/8003)`都需要监控依赖配置。
- 启动`cloud-consumer-hystrix-dashboard9001`该微服务后续将监控微服务`8001`
- `http://localhost:9001/hystrix`



- 修改`cloud-provider-hystrix-payment8001`
  - 注意：新版本`Hystrix`需要在主启动类`MainAppHystrix8001`中指定监控路径

~~~java
@SpringBootApplication
@EnableEurekaClient //本服务启动后会自动注册进eureka服务中
@EnableCircuitBreaker//对hystrixR熔断机制的支持
public class MainAppHystrix8001
{
    public static void main(String[] args)
    {
        SpringApplication.run(MainAppHystrix8001.class,args);
    }

    /**
 *此配置是为了服务监控而配置，与服务容错本身无关，springcloud升级后的坑
 *ServletRegistrationBean因为springboot的默认路径不是"/hystrix.stream"，
 *只要在自己的项目里配置上下面的servlet就可以了
 */
    @Bean
    public ServletRegistrationBean getServlet() {
        HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
        registrationBean.setLoadOnStartup(1);
        registrationBean.addUrlMappings("/hystrix.stream");
        registrationBean.setName("HystrixMetricsStreamServlet");
        return registrationBean;
    }

}
~~~

- 监控测试
  - 启动`1`个`eureka`或者`3`个`eureka`集群均可。

![image-20231008003523690](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231008003523690.png)

- `Delay`：该参数用来控制服务器上轮询监控信息的延迟时间，默认为`2000`毫秒，可以通过配置该属性来降低客户端的网络和`CPU`消耗。
- `Title`：该参数对应了头部标题`Hystrix Stream`之后的内容，默认会使用具体监控实例的URL，可以通过配置该信息来展示更合适的标题。 

- 测试：
  - `http://localhost:8001/payment/circuit/31`
  - `http://localhost:8001/payment/circuit/-31`
  - 先访问正确地址，再访问错误地址，再正确地址，会发现图示断路器都是慢慢放开的。

![image-20231008003921529](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231008003921529.png)

![image-20231008003926471](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231008003926471.png)





## 9、`GateWay`网关

- 官网：`https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/`




### 9.1、概述

#### 9.1.1、`GateWay`是什么

- `Cloud`全家桶中有个很重要的组件就是网关，在`1.x`版本中都是采用的`Zuul`网关，但在`2.x`版本中，`zuul`的升级一直跳票，`SpringCloud`最后自己研发了一个网关替代`Zuul`，那就是`SpringCloud Gateway`一句话：`gateway`是原`zuul1.x`版的替代。

![image-20231009165414376](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231009165414376.png)

- `Gateway`是在`Spring`生态系统之上构建的`API`网关服务，基于`Spring 5、Spring Boot 2`和 `Project Reactor`等技术。
- `Gateway`旨在提供一种简单而有效的方式来对`API`进行路由，以及提供一些强大的过滤器功能， 例如：熔断、限流、重试等。

![image-20231009173212411](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231009173212411.png)

- `SpringCloud Gateway`是`Spring Cloud`的一个全新项目，基于`Spring 5.0+Spring Boot 2.0`和`Project Reactor`等技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的`API`路由管理方式。
- `SpringCloud Gateway`作为`Spring Cloud`生态系统中的网关，目标是替代`Zuul`，在`Spring Cloud 2.0`以上版本中，没有对新版本的`Zuul 2.0`以上最新高性能版本进行集成，仍然还是使用的`Zuul 1.x`非`Reactor`模式的老版本。而为了提升网关的性能，`SpringCloud Gateway`是基于`WebFlux`框架实现的，而`WebFlux`框架底层则使用了高性能的`Reactor`模式通信框架`Netty`。
- `Spring Cloud Gateway`的目标提供统一的路由方式且基于`Filter`链的方式提供了网关基本的功能，例如：安全，监控/指标，和限流。
- `SpringCloud Gateway`使用的`Webflux`中的`reactor-netty`响应式编程组件，底层使用了`Netty`通讯框架。

![image-20231009184131469](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231009184131469.png)



#### 9.1.2、`GateWay`能干什么

- 反向代理
- 鉴权
- 流量控制
- 熔断
- 日志监控

![image-20231009211540103](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231009211540103.png)



#### 9.1.3、特点

- 基于`Spring Framework 5, Project Reactor`和`Spring Boot 2.0`进行构建；
- 动态路由：能够匹配任何请求属性；
- 可以对路由指定`Predicate`（断言）和`Filter`（过滤器）；
- 集成`Hystrix`的断路器功能；
- 集成`Spring Cloud`服务发现功能；
- 易于编写的`Predicate`（断言）和`Filter`（过滤器）；
- 请求限流功能；
- 支持路径重写。





#### 9.1.4、`Spring Cloud Gateway`与`Zuul`的区别

- 在`SpringCloud Finchley`正式版之前，`Spring Cloud`推荐的网关是`Netflix`提供的`Zuul`：
  - `Zuul 1.x`，是一个基于阻塞`I/o`的`API Gateway`。
  - `Zuul 1.x`基于`Servlet 2.5`使用阻塞架构它不支持任何长连接(如`WebSocket`) `Zuul`的设计模式和`Nginx`较像，每次`I/ O`操作都是从工作线程中选择一个执行，请求线程被阻塞到工作线程完成，但是差别是`Nginx`用`C++`实现，`Zuul`用`Java`实现，而`JVM`本身会有第一次加载较慢的情况，使得`Zuul`的性能相对较差。
  - `Zuul 2.x`理念更先进，想基于`Netty`非阻塞和支持长连接，但`SpringCloud`目前还没有整合。`Zuul 2.x`的性能较`Zuul 1.x`有较大提升。在性能方面，根据官方提供的基准测试， `Spring Cloud Gateway`的`RPS`（每秒请求数）是`Zuul`的`1.6`倍。
  - `Spring Cloud Gateway`建立在`Spring Framework 5、 Project Reactor`和`Spring Boot 2`之上， 使用非阻塞 `API`。
  - `Spring Cloud Gateway`还支持`WebSocket`，并且与`Spring`紧密集成拥有更好的开发体验。





#### 9.1.5、`Zuul1.x`模型

- `Springcloud`中所集成的`Zuul`版本，采用的是`Tomcat`容器，使用的是传统的`Servlet IO`处理模型。
- `web`中，`Servlet`的生命周期：`servlet`由`servlet container`进行生命周期管理。`container`启动时构造`servlet`对象并调用`servlet init()`进行初始化；`container`运行时接受请求，并为每个请求分配一个线程（一般从线程池中获取空闲线程）然后调用`service()`。`container`关闭时调用`servlet destory()`销毁`servlet`；

![image-20231010153334265](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231010153334265.png)

- 上述模式的缺点：
  - `servlet`是一个简单的网络`IO`模型，当请求进入`servlet container`时，`servlet container`就会为其绑定一个线程，在并发不高的场景下这种模型是适用的。但是一旦高并发(比如抽风用`jemeter`压)，线程数量就会上涨，而线程资源代价是昂贵的（上下文切换，内存消耗大）严重影响请求的处理时间。在一些简单业务场景下，不希望为每个`request`分配一个线程，只需要`1`个或几个线程就能应对极大并发的请求，这种业务场景下`servlet`模型没有优势。
  - 所以`Zuul 1.X`是基于`servlet`之上的一个阻塞式处理模型，即`spring`实现了处理所有`request`请求的一个`servlet(DispatcherServlet)`并由该`servlet`阻塞式处理处理。所以`Springcloud Zuul`无法摆脱`servlet`模型的弊端。



#### 9.1.6、`GateWay`模型

- 传统的`Web`框架，比如说：`struts2，springmvc`等都是基于`Servlet API`与`Servlet`容器基础之上运行的。
- 但是在`Servlet3.1`之后有了异步非阻塞的支持。而`WebFlux`是一个典型非阻塞异步的框架，它的核心是基于`Reactor`的相关`API`实现的。相对于传统的`web`框架来说，它可以运行在诸如`Netty`、`Undertow`及支持`Servlet3.1`的容器上。非阻塞式+函数式编程（`Spring5`必须让你使用`java8`）
- `Spring WebFlux`是`Spring 5.0`引入的新的响应式框架，区别于`Spring MVC`，它不需要依赖`Servlet API`，它是完全异步非阻塞的，并且基于`Reactor`来实现响应式流规范。





### 9.2、三大核心概念

- `Route`（路由）：路由是构建网关的基本模块，它由`ID`，目标`URI`，一系列的断言和过滤器组成，如果断言为`true`则匹配该路由。
- `Predicate`（断言）：参考的是`Java8`的`java.util.function.Predicate`开发人员可以匹配`HTTP`请求中的所有内容(例如请求头或请求参数)，如果请求与断言相匹配则进行路由。
- `Filter`（过滤）：指的是`Spring`框架中`GatewayFilter`的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

![image-20231013154929090](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231013154929090.png)

- `web`请求，通过一些匹配条件，定位到真正的服务节点。并在这个转发过程的前后，进行一些精细化控制。`predicate`就是我们的匹配条件；而`filter`，就可以理解为一个无所不能的拦截器。有了这两个元素，再加上目标`uri`，就可以实现一个具体的路由了



### 9.3、`Gateway`工作流程

![image-20231013155042087](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231013155042087.png)

- 客户端向`Spring Cloud Gateway`发出请求。然后在`Gateway Handler Mapping`中找到与请求相匹配的路由，将其发送到`Gateway Web Handler`。
- `Handler`再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。
- 过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（`pre`）或之后（`post`）执行业务逻辑。`Filter`在`pre`类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在`post`类型的过滤器中可以做响应内容、响应头的修改，日志的输出，流量监控等有着非常重要的作用。
- 核心逻辑：路由转发 + 执行过滤器链



### 9.4、入门配置

#### 9.4.1、新建工程

- 新建`Module`：`cloud-gateway-gateway9527`
- `POM`

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-gateway-gateway9527</artifactId>

    <dependencies>
        <!--gateway-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--一般基础配置类-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
~~~

- `YML`

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~

- 主启动类

~~~Java
@SpringBootApplication
@EnableEurekaClient
public class GateWayMain9527
{
    public static void main(String[] args)
    {
        SpringApplication.run(GateWayMain9527.class,args);
    }
}
~~~

- `9527`网关如何做路由映射?
  - `cloud-provider-payment8001`看看`controller`的访问地址
  - 我们目前不想暴露`8001`端口，希望在`8001`外面套一层`9527`
  - 新增网关配置

~~~yaml
 
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~

- 测试：`http://localhost:8001/payment/get/31`、`http://localhost:9527/payment/get/31`



#### 9.4.2、`YUM`配置说明

##### 1、说明

- `Gateway`网关路由有两种配置方式：
  - 在配置文件中`yum`中配置
  - 代码中注入`RouteLocator`的`Bean`



##### 2、第二种方法的示例

- 官网案例：

![image-20231014140854599](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014140854599.png)

- 百度国内新闻网址，需要外网：`http://news.baidu.com/guonei`
- 通过`9527`网关访问到外网的百度新闻地址：

~~~Java
 
package com.atguigu.springcloud.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @auther zzyy
 * @create 2020-02-06 11:26
 */
@Configuration
public class GateWayConfig
{
    /**
     * 配置了一个id为route-name的路由规则，
     * 当访问地址 http://localhost:9527/guonei时会自动转发到地址：http://news.baidu.com/guonei
     * @param builder
     * @return
     */
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder)
    {
        RouteLocatorBuilder.Builder routes = builder.routes();

        routes.route("path_route_atguigu", r -> r.path("/guonei").uri("http://news.baidu.com/guonei")).build();

        return routes.build();

    }
    @Bean
    public RouteLocator customRouteLocator2(RouteLocatorBuilder builder)
    {
        RouteLocatorBuilder.Builder routes = builder.routes();
        routes.route("path_route_atguigu2", r -> r.path("/guoji").uri("http://news.baidu.com/guoji")).build();
        return routes.build();
    }
}
~~~





### 9.5、通过微服务名实现动态路由

- 默认情况下`Gateway`会根据注册中心注册的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能。
- 启动一个`Eureka7001`和两个微服务提供者`8001、8002`。
- `YUM`

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~

- 需要注意的是`uri`的协议为`lb`，表示启用`Gateway`的负载均衡功能。
- `lb://serviceName`是`spring cloud gateway`在微服务中自动为我们创建的负载均衡`uri`。
- 验证测试：`http://localhost:9527/payment/lb`，两个微服务提供者被轮流调用，这说明`GateWay`负载均衡起作用了。



### 9.6、`Predicate`的使用

- 启动`9527`

![image-20231014153504876](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014153504876.png)



#### 9.6.1、Route Predicate Factories

![image-20231014163627704](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014163627704.png)

- `Spring Cloud Gateway`将路由匹配作为`Spring WebFlux HandlerMapping`基础架构的一部分。
- `Spring Cloud Gateway`包括许多内置的`Route Predicate`工厂。所有这些`Predicate`都与`HTTP`请求的不同属性匹配。多个`Route Predicate`工厂可以进行组合。



#### 9.6.2、常用的`Route Predicate`

![image-20231014164512058](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014164512058.png)

##### 1、`After Route Predicate`

- 在某个固定时间之后才能访问，如果在这个时间之前访问，返回`404`。

![image-20231014164819614](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014164819614.png)

~~~java
package com.atguigu.test;


import java.time.ZoneId;
import java.time.ZonedDateTime;

/**
 * @auther zzyy
 * @create 2019-12-02 17:37
 */
public class ZonedDateTimeDemo
{
    public static void main(String[] args)
    {
        ZonedDateTime zbj = ZonedDateTime.now(); // 默认时区
        System.out.println(zbj);
//        ZonedDateTime zny = ZonedDateTime.now(ZoneId.of("America/New_York")); // 用指定时区获取当前时间
//        System.out.println(zny);
    }
}
~~~

- `YUM`

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
       - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
             - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~



##### 2、`Before Route Predicate`

- 在某个固定时间之前才能访问，如果在这个时间之后访问，返回`404`。

![image-20231014165207776](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014165207776.png)

- `YUM`

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            - Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由


eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~



##### 3、`Between Route Predicate`

~~~yaml
 
- Between=2019-12-02T17:45:06.206+08:00[Asia/Shanghai],2019-12-02T18:59:06.206+08:00[Asia/Shanghai]
 
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            #- After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            - Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]


eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka

#id：我们自定义的路由 ID，保持唯一
 ##uri：目标服务地址
 ##predicates：路由条件，Predicate接受一个输入参数返回一个布尔值。
 ##            该属性包含多种默认方法来将Predicate组合成其他复杂的逻辑(比如：与，或，非)
~~~



##### 4、`Cookie Route Predicate`

![image-20231014170055714](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014170055714.png)

- `Cookie Route Predicate`需要两个参数，一个是`Cookie name`，一个是正则表达式。
- 路由规则会通过获取对应的`Cookie name`值和正则表达式去匹配，如果匹配上就会执行路由，如果没有匹配上则不执行。
- 不带`Cookie`访问

![image-20231014165754688](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014165754688.png)

- 携带`Cookie`访问

![image-20231014165847942](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014165847942.png)

- 解决中文乱码问题：`https://blog.csdn.net/leedee/article/details/82685636`
- `YUM`

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]
       		- Cookie=username,zzyy


eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka

#id：我们自定义的路由 ID，保持唯一
 ##uri：目标服务地址
 ##predicates：路由条件，Predicate接受一个输入参数返回一个布尔值。
 ##            该属性包含多种默认方法来将Predicate组合成其他复杂的逻辑(比如：与，或，非)
~~~



##### 5、`Header Route Predicate`

![image-20231014170044605](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014170044605.png)

- 两个参数：一个是属性名称和一个正则表达式，这个属性值和正则表达式匹配则执行。

~~~yaml
 
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]
            #- Cookie=username,zzyy
            - Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
 
 
curl http://localhost:9588/paymentInfo -H "X-Request-Id:123"
 
curl http://localhost:9527/payment/lb -H "X-Request-Id:123"
~~~

![image-20231014172759249](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014172759249.png)



##### 6、`Host Route Predicate`

![image-20231014172842661](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014172842661.png)

- `Host Route Predicate`接收一组参数，一组匹配的域名列表，这个模板是一个`ant`分隔的模板，用`.`号作为分隔符。
- 它通过参数中的主机地址作为匹配规则。

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]
            #- Cookie=username,zzyy
            #- Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
            - Host=**.atguigu.com


eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka

#id：我们自定义的路由 ID，保持唯一
 ##uri：目标服务地址
 ##predicates：路由条件，Predicate接受一个输入参数返回一个布尔值。
 ##            该属性包含多种默认方法来将Predicate组合成其他复杂的逻辑(比如：与，或，非)

 
curl http://localhost:9588/paymentInfo -H "Host: www.atguigu.com" 
curl http://localhost:9588/paymentInfo -H "Host: news.atguigu.com" 
====================================================================================
正确：curl http://localhost:9527/payment/lb -H "Host: www.atguigu.com"
正确：curl http://localhost:9527/payment/lb -H "Host: java.atguigu.com"
错误：curl http://localhost:9527/payment/lb -H "Host: java.atguigu.net"
~~~

![image-20231014173006920](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014173006920.png)



##### 7、`Method Route Predicate`

![image-20231014173138840](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014173138840.png)

- `YUM`

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]
            #- Cookie=username,zzyy
            #- Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
            #- Host=**.atguigu.com
            - Method=GET

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~

![image-20231014173230579](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014173230579.png)



##### 8、`Path Route Predicate`

![image-20231014173251647](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014173251647.png)

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]
            #- Cookie=username,zzyy
            #- Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
            #- Host=**.atguigu.com
            - Method=GET

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~



##### 9、`Query Route Predicate`

![image-20231014173348618](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014173348618.png)

- 支持传入两个参数，一个是属性名，一个为属性值，属性值可以是正则表达式。

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]
            #- Cookie=username,zzyy
            #- Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
            #- Host=**.atguigu.com
            - Method=GET
            - Query=username, \d+  # 要有参数名username并且值还要是整数才能路由


eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka 
 
http://localhost:9527/payment/lb?username=31
 
http://localhost:9527/payment/lb?username=-31
~~~





### 9.7、`Filter`的使用

#### 9.7.1、是什么？

![image-20231014173916300](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014173916300.png)

- 路由过滤器可用于修改进入的`HTTP`请求和返回的`HTTP`响应，路由过滤器只能指定路由进行使用。
- `Spring Cloud Gateway`内置了多种路由过滤器，他们都由`GatewayFilter`的工厂类来产生。



#### 9.7.2、`Spring Cloud Gateway`的`Filter`

##### 1、生命周期

- `pre`、`post`



##### 2、种类

- `GateWayFilter`
  - `https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/#the-addrequestparameter-gatewayfilter-factory`
- `GlobalFilter`

![image-20231014182323826](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014182323826.png)



#### 9.7.3、常用的`GatewayFilter`

- `AddRequestParameter`

~~~yaml
 
server:
  port: 9588

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
          lower-case-service-id: true #使用小写服务名，默认是大写
      routes:
        - id: payment_routh #payment_route #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: lb://cloud-provider-payment #匹配后的目标服务地址，供服务的路由地址
          #uri: http://localhost:8001 #匹配后提供服务的路由地址
          filters:
            - AddRequestParameter=X-Request-Id,1024 #过滤器工厂会在匹配的请求头加上一对请求头，名称为X-Request-Id值为1024
          predicates:
            - Path=/paymentInfo/**        # 断言，路径相匹配的进行路由
            - Method=GET,POST

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
~~~



#### 9.7.4、自定义过滤器

- 自定义全局`GlobalFilter`

~~~java
@Component //必须加，必须加，必须加
public class MyLogGateWayFilter implements GlobalFilter,Ordered
{
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain)
    {
        System.out.println("time:"+new Date()+"\t 执行了自定义的全局过滤器: "+"MyLogGateWayFilter"+"hello");

        String uname = exchange.getRequest().getQueryParams().getFirst("uname");
        if (uname == null) {
            System.out.println("****用户名为null，无法登录");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder()
    {
        return 0;
    }
}			
~~~

- 验证测试：`http://localhost:9527/payment/lb?uname=z3`



## 10、`SpringCloud Config`分布式配置中心

### 10.1、概述

- 分布式系统面临着配置问题：
  - 微服务意味着要将单体应用中的业务拆分成一个个子服务，每个服务的粒度相对较小，因此系统中会出现大量的服务。由于每个服务都需要必要的配置信息才能运行，所以一套集中式的、动态的配置管理设施是必不可少的。
  - `SpringCloud`提供了`ConfigServer`来解决这个问题，我们每一个微服务自己带着一个`application.yml`，那样管理起来非常麻烦。



#### 10.1.1、`SpringCloud Config`是什么

![image-20231014192914673](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231014192914673.png)

- `SpringCloud Config`为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置。



#### 10.1.2、`SpringCloud Config`能干嘛

- 集中管理配置文件。
- 不同环境不同配置，动态化的配置更新，分环境部署比如：`dev`(开发)/`test`(测试)/`prod`(产品)/`beta`(预发布)/`release`(灰度发布)。
- 运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统一拉取配置自己的信息。
- 当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置。
- 将配置信息以`REST`接口的形式暴露。





#### 10.1.3、`SpringCloud Config`怎么玩

- `SpringCloud Config`分为服务端和客户端两部分。
- 服务端也称为分布式配置中心，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口。
- 客户端则是通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息配置服务器默认采用`git`来存储配置信息，这样就有助于对环境配置进行版本管理，并且可以通过`git`客户端工具来方便的管理和访问配置内容。
- 与`GitHub`整合配置：
  - 由于`SpringCloud Config`默认使用`Git`来存储配置文件（也有其它方式，比如支持`SVN`和本地文件），但最推荐的还是`Git`，而且使用的是`http/https`访问的形式。
- 官网：`https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.2.1.RELEASE/reference/html/`



### 10.2、`Config`服务端配置与测试

- 用你自己的账号在`GitHub`上新建一个名为`springcloud-config`的新`Repository`。
- 





## 11、`SpringCloud Bus`消息总线





## 12、`SpringCloud Stream`消息驱动





## 13、



































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































