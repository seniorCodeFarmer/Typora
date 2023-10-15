项目开发生命周期

![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)



## 1、什么是 Maven

- Maven 是 Apache 软件基金会组织维护的一款专门为 Java 项目提供**构建和依赖管理支持的工具**。



### 1.1、构建

- Java 项目开发过程中，构建指的是使用『原材料生产产品』的过程。
- 原材料：
  - Java源码
  - 基于 HTML 的 Thymeleaf 文件
  - 图片
  - 配置文件

- 产品
  - 一个可以在服务器上运行的项目

- 构建过程包含的主要的环节：

  - 清理：删除上一次构建的结果，为下一次构建做好准备

  - 编译：Java 源程序编译成 *.class 字节码文件

  - 测试：运行提前准备好的测试程序

  - 报告：针对刚才测试的结果生成一个全面的信息

  - 打包

  - - Java工程：jar包
    - Web工程：war包

- 安装：把一个 Maven 工程经过打包操作生成的 jar 包或 war 包存入 Maven 仓库
- 部署
  - 部署 jar 包：把一个 jar 包部署到 Nexus 私服服务器上
  - 部署 war 包：借助相关 Maven 插件（例如 cargo），将 war 包部署到 Tomcat 服务器上



### 1.2、依赖

- 如果A工程里面用到了B工程的类、接口、配置文件等等这样的资源，那么我们就可以说A依赖B。

- 依赖管理中要解决的具体问题

- - jar 包的下载：使用 Maven 之后，jar 包会从规范的远程仓库下载到本地
  - jar 包之间的依赖：通过依赖的传递性自动完成
  - jar 包之间的冲突：通过对依赖的配置进行调整，让某些jar包不会被导入



### 1.3、Maven 的工作机制

![image-20230918200044102](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918200044102.png)



## 2、Maven 核心程序解压与配置

### 2.1、Maven 核心程序解压与配置

#### 2.1.1、Maven 官网地址

- 首页：[Maven – Welcome to Apache Maven](https://maven.apache.org/)[(opens new window)](https://maven.apache.org/)
- 下载页面：[Maven – Download Apache Maven](https://maven.apache.org/download.cgi)[(opens new window)](https://maven.apache.org/download.cgi)
- 下载链接：

![image-20230918200023964](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918200023964.png)





#### 2.1.2、解压Maven核心程序

- 解压到非中文、无空格的目录
- 在解压目录中，我们需要着重关注Maven的核心配置文件：conf/settings.xml



#### 2.1.3、指定本地仓库

- 本地仓库默认值：用户家目录/.m2/repository。由于本地仓库的默认位置是在用户的家目录下，而家目录往往是在 C盘，也就是系统盘。将来 Maven 仓库中 jar 包越来越多，仓库体积越来越大，可能会拖慢 C 盘运行速度，影响系统性能。所以建议将 Maven 的本地仓库放在其他盘符下。配置方式如下：

~~~
<localRepository>e:\localRepository</localRepository>
~~~

- 本地仓库这个目录，我们手动创建一个空的目录即可。
- 记住：一定要把 localRepository 标签从注释中拿出来。
- 注意：本地仓库本身也需要使用一个非中文、没有空格的目录



#### 2.1.4、配置阿里云提供的镜像仓库

- Maven 下载 jar 包默认访问境外的中央仓库，而国外网站速度很慢。改成阿里云提供的镜像仓库，访问国内网站，可以让 Maven 下载 jar 包的时候速度更快。配置的方式是：
  - 将原有的例子配置注释掉，加入阿里云的配置

~~~xml
<!-- <mirror>
  <id>maven-default-http-blocker</id>
  <mirrorOf>external:http:*</mirrorOf>
  <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
  <url>http://0.0.0.0/</url>
  <blocked>true</blocked>
</mirror> -->

<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
~~~



#### 2.1.5、配置Maven工程基本JDK版本

- 如果按照默认配置运行，Java 工程使用的默认 JDK 版本是 1.5，而我们熟悉和常用的是 JDK 1.8 版本。修改配置的方式是：将 profile 标签整个复制到 settings.xml 文件的 profiles 标签内。

~~~xml
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
~~~



### 2.2、配置环境变量

#### 2.2.1、检查JAVA_HOME配置是否正确

~~~
java -version
echo &JAVA_HOME%
~~~



#### 2.2.2、配置MAVEN_HOME

![image-20230918201242035](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918201242035.png)



#### 2.2.3、配置PATH

#### 2.2.4、验证

~~~cmd
// 在cmd环境下运行
mvn -v
~~~



## 3、根据坐标创建Maven工程

### 3.1、Maven中的坐标

- 向量说明：使用三个向量在Maven仓库中唯一定位到一个jar包

- - groupId：公司或组织的ID
  - artifactId：一个项目或者是项目中的一个模块的id
  - version：版本号

- 三个向量的取值方式

- - groupId：公司或组织域名的倒序，通常也会加上项目名称；例如：com.atguigu.HAGPlatform
  - artifactId：模块的名称，将来作为 Maven 工程的工程名
  - version：模块的版本号，根据自己的需要设定；例如：SNAPSHOT 表示快照版本，正在迭代过程中，不稳定的版本，例如：RELEASE 表示正式版

- 举例：
  - groupId：com.atguigu.maven
  - artifactId：pro01-atguigu-maven
  - version：1.0-SNAPSHOT

- 坐标和仓库中jar包的存储路径之间的对应关系

![image-20230918201733593](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918201733593.png)



### 3.2、实验操作

#### 3.2.1、创建目录作为后面操作的工作空间

- 例如：D:\maven-workspace\por01![image-20230918201926368](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918201926368.png)



#### 3.2.2、在工作空间以管理员权限打开命令行

![image-20230918220629014](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918220629014.png)



#### 3.2.3、使用命令生成 Maven 工程

![image-20230918202056506](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918202056506.png)

- 运行mvn archetype:generate命令
- 下面根据提示操作

![image-20230918202108686](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918202108686.png)



#### 3.2.4、调整

- Maven 默认生成的工程，对 junit 依赖的是较低的 3.8.1 版本，我们可以改成较适合的 4.12 版本
- 自动生成的 App.java 和 AppTest.java 可以删除

![image-20230918202201397](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918202201397.png)



#### 3.2.5、自动生成的 pom.xml 解读

![image-20230918210514869](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918210514869.png)





### 3.3、Maven 的核心概念：POM

#### 3.3.1、含义

- POM：**P**roject **O**bject **M**odel，项目对象模型。和 POM 类似的是：DOM（Document Object Model），文档对象模型。它们都是模型化思想的具体体现。



#### 3.3.2、模型化思想

- POM 表示将工程抽象为一个模型，再用程序中的对象来描述这个模型。这样我们就可以用程序来管理项目了。我们在开发过程中，最基本的做法就是将现实生活中的事物抽象为模型，然后封装模型相关的数据作为一个对象，这样就可以在程序中计算与现实事物相关的数据。



#### 3.3.3、对应的配置文件

- POM 理念集中体现在 Maven 工程根目录下 **pom.xml** 这个配置文件中。所以这个 pom.xml 配置文件就是 Maven 工程的核心配置文件。其实学习 Maven 就是学这个文件怎么配置，各个配置有什么用。



### 3.4、Maven 核心概念：约定的目录结构

- 各个目录的作用
- 另外还有一个 target 目录专门存放构建操作输出的结果

![image-20230918210914132](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918210914132.png)

- 约定目录结构的意义
  - Maven 为了让构建过程能够尽可能自动化完成，所以必须约定目录结构的作用。例如：Maven 执行编译操作，必须先去 Java 源程序目录读取 Java 源代码，然后执行编译，最后把编译结果存放在 target 目录。

- 约定大于配置
  - Maven 对于目录结构这个问题，没有采用配置的方式，而是基于约定。这样会让我们在开发过程中非常方便。如果每次创建 Maven 工程后，还需要针对各个目录的位置进行详细的配置，那肯定非常麻烦。
  - 目前开发领域的技术发展趋势就是：约定大于配置，配置大于编码。





## 4、在Maven工程中编写代码并构建

### 4.1、主体程序

![image-20230918211655999](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918211655999.png)

- 主体程序指的是被测试的程序，同时也是将来在项目中真正要使用的程序

~~~Java
package com.atguigu.maven;
public class Calculator {    
    public int sum(int i,int j) {        
        return i + j;    
    }
}
~~~





### 4.2、测试程序

![image-20230918212043288](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918212043288.png)

~~~java
package com.atguigu.maven;
import org.junit.Test;
import com.atguigu.maven.Calculator;
    
// 静态导入的效果是将Assert类中的静态资源导入当前类
// 这样一来，在当前类中就可以直接使用Assert类中的静态资源，不需要写类名
import static org.junit.Assert.*;
    
public class CalculatorTest{
    @Test
    public void testSum(){
        
        // 1.创建Calculator对象
        Calculator calculator = new Calculator();
        
        // 2.调用Calculator对象的方法，获取到程序运行实际的结果
        int actualResult = calculator.sum(5, 3);
        
        // 3.声明一个变量，表示程序运行期待的结果
        int expectedResult = 8;
        
        // 4.使用断言来判断实际结果和期待结果是否一致
        // 如果一致：测试通过，不会抛出异常
        // 如果不一致：抛出异常，测试失败
        assertEquals(expectedResult, actualResult);
    }
}
~~~



### 4.3、执行Maven的构建命令

#### 4.3.1、要求

- 运行 Maven 中和构建操作相关的命令时，必须进入到 pom.xml 所在的目录。如果没有在 pom.xml 所在的目录运行 Maven 的构建命令，那么会看到下面的错误信息：

~~~
The goal you specified requires a project to execute but there is no POM in this directory
~~~

- mvn -v 命令和构建操作无关，只要正确配置了 PATH，在任何目录下执行都可以。而构建相关的命令要在 pom.xml 所在目录下运行——操作哪个工程，就进入这个工程的 pom.xml 目录。



#### 4.3.2、清理

- mvn clean
- 效果：删除target目录



#### 4.3.3、编译操作

- 主程序编译：mvn compile、
- 测试程序编译：mvn test-compile
- 主体程序编译结果存放的目录：target/classes
- 测试程序编译结果存放的目录：target/test-classes



#### 4.3.4、测试操作

- mvn test
- 测试的报告存放的目录：target/surefire-reports



#### 4.3.5、打包操作

- mvn package
- 打包的结果——jar 包，存放的目录：target

![image-20230918220440034](C:\Users\张成伟\AppData\Roaming\Typora\typora-user-images\image-20230918220440034.png)



#### 4.3.6、安装操作

- mvn install

~~~
[INFO] Installing D:\maven-workspace\space201026\pro01-maven-java\target\pro01-maven-java-1.0-SNAPSHOT.jar to D:\maven-rep1026\com\atguigu\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.jar
[INFO] Installing D:\maven-workspace\space201026\pro01-maven-java\pom.xml to D:\maven-rep1026\com\atguigu\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.pom
~~~

- 安装的效果是将本地构建过程中生成的 jar 包存入 Maven 本地仓库。这个 jar 包在 Maven 仓库中的路径是根据它的坐标生成的。
- 坐标信息如下：

~~~xml
<groupId>com.atguigu.maven</groupId>
<artifactId>pro01-maven-java</artifactId>
<version>1.0-SNAPSHOT</version>
~~~

- 在 Maven 仓库中生成的路径如下：

~~~
D:\maven-rep1026\com\atguigu\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.jar
~~~

- 另外，安装操作还会将 pom.xml 文件转换为 XXX.pom 文件一起存入本地仓库。所以我们在 Maven 的本地仓库中想看一个 jar 包原始的 pom.xml 文件时，查看对应 XXX.pom 文件即可，它们是名字发生了改变，本质上是同一个文件。



## 5、创建Maven版的web工程

### 5.1、说明

- 使用 mvn archetype:generate 命令生成 Web 工程时，需要使用一个专门的 archetype。这个专门生成 Web 工程骨架的 archetype 可以参照官网看到它的用法：

![image-20230918213213115](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918213213115.png)

- 参数 archetypeGroupId、archetypeArtifactId、archetypeVersion 用来指定现在使用的 maven-archetype-webapp 的坐标。



### 5.2、操作

- 注意：如果在上一个工程的目录下执行 mvn archetype:generate 命令，那么 Maven 会报错：不能在一个非 pom 的工程下再创建其他工程。所以不要再刚才创建的工程里再创建新的工程，**请回到工作空间根目录**来操作。
- 然后运行生成工程的命令：

~~~
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.4
~~~



### 5.3、生成的pom.xml

- 确认打包的方式是war包形式

~~~xml
<packaging>war</packaging>
~~~



### 5.4、生成的web工程的目录结构

![image-20230918213359338](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918213359338.png)



### 5.5、创建Servlet

- 在main目录下创建java目录
- 在java目录下创建Servlet类所在的包的目录
- 在包下创建Servlet类

~~~java
package com.atguigu.maven;
import javax.servlet.http.HttpServlet;import javax.servlet.http.HttpServletRequest;import javax.servlet.http.HttpServletResponse;import javax.servlet.ServletException;import java.io.IOException;

public class HelloServlet extends HttpServlet{
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.getWriter().write("hello maven web");
    }
}
~~~

- 在web.xml中注册Servlet

~~~xml
<servlet>
    <servlet-name>helloServlet</servlet-name>
    <servlet-class>com.atguigu.maven.HelloServlet</servlet-class>
    <servlet-mapping>
        <servlet-name>helloServlet</servlet-name>
        <url-pattern>/helloServlet</url-pattern>
    </servlet-mapping>
</servlet>
~~~



### 5.6、在index.jsp页面编写超链接

~~~html
<html><body><h2>Hello World!</h2><a href="helloServlet">Access Servlet</a></body></html>
~~~



### 5.7、编译

- 此时直接执行mvn compile命令出错

~~~
DANGER
程序包 javax.servlet.http 不存在
程序包 javax.servlet 不存在
找不到符号
符号: 类 HttpServlet
……
~~~

- 上面的错误信息说明：我们的 Web 工程用到了 HttpServlet 这个类，而 HttpServlet 这个类属于 servlet-api.jar 这个 jar 包。此时我们说，Web 工程需要依赖 servlet-api.jar 包。



### 5.8、配置对servlet-api.jar包的依赖

- 对于不知道详细信息的依赖可以到https://mvnrepository.com/网站查询。使用关键词搜索，然后在搜索结果列表中选择适合的使用。

~~~xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
~~~

- 这样就可以把上面的信息加入 pom.xml。重新执行 mvn compile 命令。



### 5.9、将Web工程打包为war包

- 运行 mvn package 命令，生成 war 包的位置如下图所示：

  ![image-20230918213747512](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918213747512.png)



### 5.10、将war包部署到Tomcat上运行

- 将 war 包复制到 Tomcat/webapps 目录下

![image-20230918213752156](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918213752156.png)

- 启动Tomcat：

![image-20230918213838398](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918213838398.png)

![image-20230918213844901](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918213844901.png)

- 通过浏览器尝试访问：http://localhost:8080/pro02-maven-web/index.jsp







## 6、让Web工程依赖Java工程

### 6.1、概念

- 明确一个意识：从来只有 Web 工程依赖 Java 工程，没有反过来 Java 工程依赖 Web 工程。本质上来说，Web 工程依赖的 Java 工程其实就是 Web 工程里导入的 jar 包。最终 Java 工程会变成 jar 包，放在 Web 工程的 WEB-INF/lib 目录下。



### 6.2、操作

- 在 pro02-maven-web 工程的 pom.xml 中，找到 dependencies 标签，在 dependencies 标签中做如下配置：

~~~xml
<!-- 配置对Java工程pro01-maven-java的依赖 -->
<!-- 具体的配置方式：在dependency标签内使用坐标实现依赖 -->
<dependency>
        <groupId>com.atguigu.maven</groupId>
        <artifactId>pro01-maven-java</artifactId>
        <version>1.0-SNAPSHOT</version>
</dependency>
~~~



### 6.3、在Web工程中，编写测试代码

- 补充创建目录

- - pro02-maven-web\src\test\java\com\atguigu\maven

- 确认Web工程依赖了junit

~~~xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
~~~

- 创建测试类
  - 把 Java 工程的 CalculatorTest.java 类复制到 pro02-maven-wb\src\test\java\com\atguigu\maven 目录下



### 5.4、执行Maven命令

- 测试命令
  - mvn test
  - 说明：测试操作中会提前自动执行编译操作，测试成功就说明编译也是成功的

- 打包命令
  - mvn package
  - 通过查看 war 包内的结构，我们看到被 Web 工程依赖的 Java 工程确实是会变成 Web 工程的 WEB-INF/lib 目录下的 jar 包

![image-20230918214357902](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918214357902.png)

![image-20230918214348944](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918214348944.png)



- 查看当前Web工程所依赖的jar包列表
  - mvn dependency:list

~~~
[INFO] The following files have been resolved:
[INFO] org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] javax.servlet:javax.servlet-api:jar:3.1.0:provided
[INFO] com.atguigu.maven:pro01-maven-java:jar:1.0-SNAPSHOT:compile
[INFO] junit:junit:jar:4.12:test
~~~

- 说明：javax.servlet:javax.servlet-api:jar:3.1.0:provided 格式显示的是一个 jar 包的坐标信息。格式是：

~~~
groupId:artifactId:打包方式:version:依赖的范围
~~~

- 这样的格式虽然和我们 XML 配置文件中坐标的格式不同，但是本质上还是坐标信息，需要能够认识这样的格式，将来从 Maven 命令的日志或错误信息中看到这样格式的信息，就能够识别出来这是坐标。进而根据坐标到 Maven 仓库找到对应的jar包，用这样的方式解决我们遇到的报错的情况。

- 以树形结构查看当前Web工程的依赖信息
  - mvn dependency:tree

~~~
[INFO] com.atguigu.maven:pro02-maven-web:war:1.0-SNAPSHOT
[INFO] +- junit:junit:jar:4.12:test
[INFO] | \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] +- javax.servlet:javax.servlet-api:jar:3.1.0:provided
[INFO] \- com.atguigu.maven:pro01-maven-java:jar:1.0-SNAPSHOT:compile
~~~

- 我们在 pom.xml 中并没有依赖 hamcrest-core，但是它却被加入了我们依赖的列表。原因是：junit 依赖了hamcrest-core，然后基于依赖的传递性，hamcrest-core 被传递到我们的工程了。



## 7、测试依赖的范围

### 7.1、依赖范围

- 标签的位置：dependencies/dependency/scope
- 标签的可选值：compile/test/provided/system/runtime/import



#### 7.1.1、compile 和 test 对比

![image-20230918214750729](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918214750729.png)



#### 7.1.2、compile 和 provided 对比

![image-20230918214837185](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918214837185.png)



#### 7.1.3、结论

- compile：通常使用的第三方框架的 jar 包这样在项目实际运行时真正要用到的 jar 包都是以 compile 范围进行依赖的。比如 SSM 框架所需jar包。
- test：测试过程中使用的 jar 包，以 test 范围依赖进来。比如 junit。
- provided：在开发过程中需要用到的“服务器上的 jar 包”通常以 provided 范围依赖进来。比如 servlet-api、jsp-api。而这个范围的 jar 包之所以不参与部署、不放进 war 包，就是避免和服务器上已有的同类 jar 包产生冲突，同时减轻服务器的负担。说白了就是：“服务器上已经有了，你就别带啦！”



## 8、测试依赖的传递性

### 8.1、依赖的传递性

- 概念

- - A 依赖 B，B 依赖 C，那么在 A 没有配置对 C 的依赖的情况下，A 里面能不能直接使用 C？

- 传递的原则： 在 A 依赖 B，B 依赖 C 的前提下，C 是否能够传递到 A，取决于 B 依赖 C 时使用的依赖范围。

- - B 依赖 C 时使用 compile 范围：可以传递
  - B 依赖 C 时使用 test 或 provided 范围：不能传递，所以需要这样的 jar 包时，就必须在需要的地方明确配置依赖才可以



### 8.2、使用compile范围依赖spring-core

- 测试方式：让 pro01-maven-java 工程依赖 spring-core
- 具体操作：编辑 pro01-maven-java 工程根目录下 pom.xml

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
~~~

- 使用 mvn dependency:tree 命令查看效果：

~~~xml
[INFO] com.atguigu.maven:pro01-maven-java:jar:1.0-SNAPSHOT
[INFO] +- junit:junit:jar:4.12:test
[INFO] | \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] \- org.springframework:spring-core:jar:4.0.0.RELEASE:compile
[INFO] \- commons-logging:commons-logging:jar:1.1.1:compile
~~~

- 还可以在 Web 工程中，使用 mvn dependency:tree 命令查看效果（需要重新将 pro01-maven-java 安装到仓库）：

~~~
[INFO] com.atguigu.maven:pro02-maven-web:war:1.0-SNAPSHOT
[INFO] +- junit:junit:jar:4.12:test
[INFO] | \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] +- javax.servlet:javax.servlet-api:jar:3.1.0:provided
[INFO] \- com.atguigu.maven:pro01-maven-java:jar:1.0-SNAPSHOT:compile
[INFO] \- org.springframework:spring-core:jar:4.0.0.RELEASE:compile
[INFO] \- commons-logging:commons-logging:jar:1.1.1:compile
~~~



### 8.3、验证 test 和 provided 范围不能传递

- 从上面的例子已经能够看到，pro01-maven-java 依赖了 junit，但是在 pro02-maven-web 工程中查看依赖树的时候并没有看到 junit。
- 要验证 provided 范围不能传递，可以在 pro01-maven-java 工程中加入 servlet-api 的依赖。

~~~xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
~~~

- 效果还是和之前一样：

~~~
[INFO] com.atguigu.maven:pro02-maven-web:war:1.0-SNAPSHOT
[INFO] +- junit:junit:jar:4.12:test
[INFO] | \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] +- javax.servlet:javax.servlet-api:jar:3.1.0:provided
[INFO] \- com.atguigu.maven:pro01-maven-java:jar:1.0-SNAPSHOT:compile
[INFO] \- org.springframework:spring-core:jar:4.0.0.RELEASE:compile
[INFO] \- commons-logging:commons-logging:jar:1.1.1:compile
~~~





## 9、测试依赖的排除

### 9.1、概念

- 当 A 依赖 B，B 依赖 C 而且 C 可以传递到 A 的时候，A 不想要 C，需要在 A 里面把 C 排除掉。而往往这种情况都是为了避免 jar 包之间的冲突。

![image-20230918215530113](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918215530113.png)

- 所以配置依赖的排除其实就是阻止某些 jar 包的传递。因为这样的 jar 包传递过来会和其他 jar 包冲突。



### 9.2、配置方式

~~~xml
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>pro01-maven-java</artifactId>
    <version>1.0-SNAPSHOT</version>
    <scope>compile</scope>
    <!-- 使用excludes标签配置依赖的排除      -->
    <exclusions>
        <!-- 在exclude标签中配置一个具体的排除 -->
        <exclusion>
            <!-- 指定要排除的依赖的坐标（不需要写version） -->
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
~~~



### 9.3、测试

- 测试的方式：在 pro02-maven-web 工程中配置对 commons-logging 的排除

~~~xml
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>pro01-maven-java</artifactId>
    <version>1.0-SNAPSHOT</version>
    <scope>compile</scope>
    <!-- 使用excludes标签配置依赖的排除      -->
    <exclusions>
        <!-- 在exclude标签中配置一个具体的排除 -->
        <exclusion>
            <!-- 指定要排除的依赖的坐标（不需要写version） -->
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
~~~

- 运行 mvn dependency:tree 命令查看效果

~~~xml
[INFO] com.atguigu.maven:pro02-maven-web:war:1.0-SNAPSHOT
[INFO] +- junit:junit:jar:4.12:test
[INFO] | \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] +- javax.servlet:javax.servlet-api:jar:3.1.0:provided
[INFO] \- com.atguigu.maven:pro01-maven-java:jar:1.0-SNAPSHOT:compile
[INFO] \- org.springframework:spring-core:jar:4.0.0.RELEASE:compile
~~~

- 发现在 spring-core 下面就没有 commons-logging 了。





## 10、继承

### 10.1、概念

- Maven工程之间，A 工程继承 B 工程

- - B 工程：父工程
  - A 工程：子工程

- 本质上是 A 工程的 pom.xml 中的配置继承了 B 工程中 pom.xml 的配置。



### 10.2、作用

- 在父工程中统一管理项目中的依赖信息，具体来说是管理依赖信息的版本。

- 它的背景是：

- - 对一个比较大型的项目进行了模块拆分。
  - 一个 project 下面，创建了很多个 module。
  - 每一个 module 都需要配置自己的依赖信息。

- 它背后的需求是：

- - 在每一个 module 中各自维护各自的依赖信息很容易发生出入，不易统一管理。
  - 使用同一个框架内的不同 jar 包，它们应该是同一个版本，所以整个项目中使用的框架版本需要统一。
  - 使用框架时所需要的 jar 包组合（或者说依赖信息组合）需要经过长期摸索和反复调试，最终确定一个可用组合。这个耗费很大精力总结出来的方案不应该在新的项目中重新摸索。

- 通过在父工程中为整个项目维护依赖信息的组合既保证了整个项目使用规范、准确的 jar 包；又能够将以往的经验沉淀下来，节约时间和精力。



### 10.3、举例

- 在一个工程中依赖多个 Spring 的 jar 包

~~~
[INFO] +- org.springframework:spring-core:jar:4.0.0.RELEASE:compile
[INFO] | \- commons-logging:commons-logging:jar:1.1.1:compile
[INFO] +- org.springframework:spring-beans:jar:4.0.0.RELEASE:compile
[INFO] +- org.springframework:spring-context:jar:4.0.0.RELEASE:compile
[INFO] +- org.springframework:spring-expression:jar:4.0.0.RELEASE:compile
[INFO] +- org.springframework:spring-aop:jar:4.0.0.RELEASE:compile
[INFO] | \- aopalliance:aopalliance:jar:1.0:compile
~~~

- 使用 Spring 时要求所有 Spring 自己的 jar 包版本必须一致。为了能够对这些 jar 包的版本进行统一管理，我们使用继承这个机制，将所有版本信息统一在父工程中进行管理。





### 10.4、操作

#### 10.4.1、创建父工程

- 创建的过程和前面创建 pro01-maven-java 一样
- 工程名称：pro03-maven-parent
- 工程创建好之后，要修改它的打包方式：

~~~xml
<groupId>com.atguigu.maven</groupId>
<artifactId>pro03-maven-parent</artifactId>
<version>1.0-SNAPSHOT</version>

<!-- 当前工程作为父工程，它要去管理子工程，所以打包方式必须是 pom -->
<packaging>pom</packaging>
~~~

- 只有打包方式为 pom 的 Maven 工程能够管理其他 Maven 工程。打包方式为 pom 的 Maven 工程中不写业务代码，它是专门管理其他 Maven 工程的工程。



#### 10.4.2、创建模块工程

- 模块工程类似于 IDEA 中的 module，所以需要进入 pro03-maven-parent 工程的根目录，然后运行 mvn archetype:generate 命令来创建模块工程。
- 假设，我们创建三个模块工程：

![image-20230918220836048](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918220836048.png)

#### 10.4.3、查看被添加新内容的父工程pom.xml

- 下面是modules 和 module 标签是聚合功能的配置

~~~xml
<modules>  
    <module>pro04-maven-module</module>
    <module>pro05-maven-module</module>
    <module>pro06-maven-module</module>
</modules>
~~~



#### 10.4.4、解读子工程的pom.xml

~~~xml
<!-- 使用parent标签指定当前工程的父工程 -->
<parent>
    <!-- 父工程的坐标 -->
    <groupId>com.atguigu.maven</groupId>
    <artifactId>pro03-maven-parent</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>

<!-- 子工程的坐标 -->
<!-- 如果子工程坐标中的groupId和version与父工程一致，那么可以省略 -->
<!-- <groupId>com.atguigu.maven</groupId> -->
<artifactId>pro04-maven-module</artifactId>
<!-- <version>1.0-SNAPSHOT</version> -->
~~~



#### 10.4.5、在父工程中配置依赖的统一管理

~~~xml
<!-- 使用dependencyManagement标签配置对依赖的管理 --><!-- 被管理的依赖并没有真正被引入到工程 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.0.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.0.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.0.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>4.0.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>4.0.0.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>
~~~



#### 10.4.6、子工程中引用那些被父工程管理的依赖

- 关键点：省略版本号

~~~xml
<!-- 子工程引用父工程中的依赖信息时，可以把版本号去掉。-->
<!-- 把版本号去掉就表示子工程中这个依赖的版本由父工程决定。-->
<!-- 具体来说是由父工程的dependencyManagement来决定。 -->
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
    </dependency>
</dependencies>
~~~



#### 10.4.7、在父工程中升级依赖信息的版本

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>4.1.4.RELEASE</version>
</dependency>
~~~

- 然后在子工程中运行mvn dependency:list，效果如下：

~~~
[INFO] org.springframework:spring-aop:jar:4.1.4.RELEASE:compile
[INFO] org.springframework:spring-core:jar:4.1.4.RELEASE:compile
[INFO] org.springframework:spring-context:jar:4.1.4.RELEASE:compile
[INFO] org.springframework:spring-beans:jar:4.1.4.RELEASE:compile
[INFO] org.springframework:spring-expression:jar:4.1.4.RELEASE:compile
~~~



#### 10.4.8、在父工程中声明自定义属性

~~~xml
<!-- 通过自定义属性，统一指定Spring的版本 -->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

    <!-- 自定义标签，维护Spring版本数据 -->
    <atguigu.spring.version>4.3.6.RELEASE</atguigu.spring.version>
</properties>
~~~

- 在需要的地方使用${}的形式来引用自定义的属性名：

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${atguigu.spring.version}</version>
</dependency>
~~~

- 真正实现“一处修改，处处生效”。



### 10.5、真正意义

![image-20230918221307972](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918221307972.png)

- 编写一套符合要求、开发各种功能都能正常工作的依赖组合并不容易。如果公司里已经有人总结了成熟的组合方案，那么再开发新项目时，如果不使用原有的积累，而是重新摸索，会浪费大量的时间。为了提高效率，我们可以使用工程继承的机制，让成熟的依赖组合方案能够保留下来。
- 如上图所示，公司级的父工程中管理的就是成熟的依赖组合方案，各个新项目、子系统各取所需即可。



## 11、聚合

### 11.1、聚合本身的含义

- 部分组成整体

### **11.2、Maven中的聚合**

- 使用一个“总工程”将各个“模块工程”汇集起来，作为一个整体对应完整的项目。

- - 项目：整体
  - 模块：部分

~~~
概念的对应关系：
从继承关系角度来看：
* 父工程
* 子工程
从聚合关系角度来看：
* 总工程
* 模块工程
~~~



### **11.3、好处**

- 一键执行 Maven 命令：很多构建命令都可以在“总工程”中一键执行

- - 以 mvn install 命令为例：Maven 要求有父工程时先安装父工程；有依赖的工程时，先安装被依赖的工程。我们自己考虑这些规则会很麻烦。但是工程聚合之后，在总工程执行 mvn install 可以一键完成安装，而且会自动按照正确的顺序执行。

- 配置聚合之后，各个模块工程会在总工程中展示一个列表，让项目中的各个模块一目了然。



### **11.4、聚合的配置**

- 在总工程中配置 modules 即可：

~~~xml
<modules>  
    <module>pro04-maven-module</module>
    <module>pro05-maven-module</module>
    <module>pro06-maven-module</module>
</modules>
~~~



### 11.5、**依赖循环问题**

- 如果 A 工程依赖 B 工程，B 工程依赖 C 工程，C 工程又反过来依赖 A 工程，那么在执行构建操作时会报下面的错误：

~~~
DANGER
[ERROR] [ERROR] The projects in the reactor contain a cyclic reference:
~~~





## 12、使用Maven-IDEA环境

### 12.1、创建Project

![image-20230918221742237](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918221742237.png)

![image-20230918221754527](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918221754527.png)



### 12.2、开启自动导入

- 创建 Project 后，IDEA 会自动弹出下面提示，我们选择**『Enable Auto-Import』**，意思是启用自动导入。
- 这个自动导入**一定要开启**，因为 Project、Module 新创建或 pom.xml 每次修改时都应该让 IDEA 重新加载 Maven 信息。这对 Maven 目录结构认定、Java 源程序编译、依赖 jar 包的导入都有非常关键的影响。
- 另外也可以通过 IDEA 的 Settings 设置来开启：

![image-20230918221828639](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918221828639.png)





### 12.3、配置Maven信息

- 每次创建 Project 后都需要设置 Maven 家目录位置，否则 IDEA 将使用内置的 Maven 核心程序（不稳定）并使用默认的本地仓库位置。这样一来，我们在命令行操作过程中已下载好的 jar 包就白下载了，默认的本地仓库通常在 C 盘，还影响系统运行。
- 配置之后，IDEA 会根据我们在这里指定的 Maven 家目录自动识别到我们在 settings.xml 配置文件中指定的本地仓库。

![image-20230918221913043](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918221913043.png)





### 12.4、创建Java模块工程

![image-20230918222003257](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222003257.png)

![image-20230918222013093](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222013093.png)



### 12.5、创建Web模块工程

#### 12.5.1、创建模块

- 按照前面的同样操作创建模块，此时这个模块其实还是一个Java模块。



#### 12.5.2、修改打包方式

- Web 模块将来打包当然应该是 **war** 包。

~~~xml
<packaging>war</packaging>
~~~



#### 12.5.3、Web设定

- 首先打开项目结构菜单：

![image-20230918222137841](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222137841.png)

- 然后到 Facets 下查看 IDEA 是否已经帮我们自动生成了 Web 设定。正常来说只要我们确实设置了打包方式为 war，那么 IDEA 2019 版就会自动生成 Web 设定。

![image-20230918222147605](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222147605.png)

- 另外，对于 IDEA 2018 诸版本没有自动生成 Web 设定，那么请参照下面两图，我们自己创建：

![image-20230918222207644](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222207644.png)

![image-20230918222236745](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222236745.png)





#### 12.5.4、借助IDEA生成web.xml

![image-20230918222311532](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222311532.png)



#### 12.5.5、设置Web资源的根目录

- 结合 Maven 的目录结构，Web 资源的根目录需要设置为 src/main/webapp 目录。

![image-20230918222337487](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222337487.png)





### 12.6、其他操作

#### 12.6.1、在IDEA中执行Maven命令

- 直接执行

![image-20230918222411312](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222411312.png)

- 手动输入

![image-20230918222425547](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222425547.png)

![image-20230918222557763](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222557763.png)

- 如果有需要，还可以给命令后面附加参数：

![image-20230918222637186](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222637186.png)

~~~
# -D 表示后面要附加命令的参数，字母 D 和后面的参数是紧挨着的，中间没有任何其它字符
# maven.test.skip=true 表示在执行命令的过程中跳过测试
mvn clean install -Dmaven.test.skip=true
~~~



#### 12.6.2、在IDEA中查看某个模块的依赖信息

![image-20230918222723580](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222723580.png)



#### 12.6.3、工程导入

- Maven工程除了自己创建的，还有很多情况是别人创建的。而为了参与开发或者是参考学习，我们都需要导入到 IDEA 中。下面我们分几种不同情况来说明

##### 1、来自版本控制系统

- 目前我们通常使用的都是 Git（本地库） + 码云（远程库）的版本控制系统，结合 IDEA 的相关操作方式请点[这里](http://heavy_code_industry.gitee.io/code_heavy_industry/pro008-Git/lecture/chapter05/verse03.html)[ ](http://heavy_code_industry.gitee.io/code_heavy_industry/pro008-Git/lecture/chapter05/verse03.html)[(opens new window)](http://heavy_code_industry.gitee.io/code_heavy_industry/pro008-Git/lecture/chapter05/verse03.html)查看**克隆远程库**部分。

##### 2、来自工程目录：直接使用 IDEA 打开工程目录即可。下面咱们举个例子：

- 工程压缩包

- - 假设别人发给我们一个 Maven 工程的 zip 压缩包：maven-rest-demo.zip。从码云或GitHub上也可以以 ZIP 压缩格式对项目代码打包下载。

- 解压

- - 如果你的所有 IDEA 工程有一个专门的目录来存放，而不是散落各处，那么首先我们就把 ZIP 包解压到这个指定目录中。

![image-20230918222910252](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222910252.png)

- 打开
  - 只要我们确认在解压目录下可以直接看到 pom.xml，那就能证明这个解压目录就是我们的工程目录。那么接下来让 IDEA 打开这个目录就可以了。

![image-20230918222932195](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222932195.png)

- 设置Maven核心程序位置

- - 打开一个新的 Maven 工程，和新创建一个 Maven 工程是一样的，此时 IDEA 的 settings 配置中关于 Maven 仍然是默认值：

![image-20230918222949569](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918222949569.png)

- 所以我们还是需要像新建 Maven 工程那样，指定一下 Maven 核心程序位置：

![image-20230918223027667](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223027667.png)



#### 12.6.4、模块导入

##### 1、情境重现

- 在实际开发中，通常会忽略模块（也就是module）所在的项目（也就是project）仅仅导入某一个模块本身。这么做很可能是类似这样的情况：比如基于 Maven 学习 SSM 的时候，做练习需要导入老师发给我们的代码参考。

![image-20230918223108751](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223108751.png)



##### 2、导入Java类型模块

- 找到老师发的工程目录

![image-20230918223128833](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223128833.png)

- 复制我们想要导入的模块

![image-20230918223142045](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223142045.png)

- 粘贴到我们自己的工程目录下

![image-20230918223202845](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223202845.png)

- 在IDEA中执行导入

![image-20230918223212475](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223212475.png)

![image-20230918223227144](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223227144.png)

![image-20230918223427679](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223427679.png)

![image-20230918223436843](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223436843.png)

- 修改pom.xml

![image-20230918223527151](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223527151.png)

- 最终效果

![image-20230918223545633](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223545633.png)



##### 3、导入Web类型模块

- 其它操作和上面演示的都一样，只是多一步：删除多余的、不正确的 web.xml 设置。如下图所示：

![image-20230918223614000](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223614000.png)



## 13、生命周期

### 13.1、作用

- 为了让构建过程自动化完成，Maven 设定了三个生命周期，生命周期中的每一个环节对应构建过程中的一个操作。



### 13.2、三个生命周期

![image-20230918223710391](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230918223710391.png)



### 13.3、特点

- 前面三个生命周期彼此是独立的。
- 在任何一个生命周期内部，执行任何一个具体环节的操作，都是从本周期最初的位置开始执行，直到指定的地方。（本节记住这句话就行了，其他的都不需要记）
- Maven 之所以这么设计其实就是为了提高构建过程的自动化程度：让使用者只关心最终要干的即可，过程中的各个环节是自动执行的。



## 14、插件和目标

### 14.1、插件

- Maven 的核心程序仅仅负责宏观调度，不做具体工作。具体工作都是由 Maven 插件完成的。例如：编译就是由 maven-compiler-plugin-3.1.jar 插件来执行的。



### 14.2、目标

- 一个插件可以对应多个目标，而每一个目标都和生命周期中的某一个环节对应。
- Default 生命周期中有 compile 和 test-compile 两个和编译相关的环节，这两个环节对应 compile 和 test-compile 两个目标，而这两个目标都是由 maven-compiler-plugin-3.1.jar 插件来执行的。





### 14.3、仓库

- 本地仓库：在当前电脑上，为电脑上所有 Maven 工程服务

- 远程仓库：需要联网

- - 局域网：我们自己搭建的 Maven 私服，例如使用 Nexus 技术。

  - Internet

  - - 中央仓库
    - 镜像仓库：内容和中央仓库保持一致，但是能够分担中央仓库的负载，同时让用户能够就近访问提高下载速度，例如：Nexus aliyun

- 建议：不要中央仓库和阿里云镜像混用，否则 jar 包来源不纯，彼此冲突。

- 专门搜索 Maven 依赖信息的网站：https://mvnrepository.com/













