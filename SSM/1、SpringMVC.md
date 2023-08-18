## 1、SpringMVC简介

### 1.1、什么是MVC

#### 1.1.1、MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分

M：Model，模型层，指工程中的JavaBean，作用是处理数据

- JavaBean分为两类：
  - 一类称为实体类Bean：专门存储业务数据的，如Student、User等
  - 一类称为业务处理Bean：指Service或Dao对象，专门用于处理业务逻辑和数据访问。

V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据

C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器



#### 1.1.2、MVC的工作流程

用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器。



### 1.2、什么是SpringMVC

- SpringMVC是Spring的一个后续产品，是Spring的一个子项目
- SpringMVC是Spring为表述层开发提供的一整套完备的解决方案。在表述层框架历经Strust、WebWork、Strust2等诸多产品的历代更迭之后，目前业界普遍选择了SpringMVC作为JavaEE项目表述层开发的首选方案。
- 注：三层架构分为表述层（或表示层）、业务逻辑层、数据访问层，表述层表示前台页面和后台servlet



### 1.3、SpringMVC的特点

- Spring家族原生产品，与IOC容器等基础设施无缝对接
- 基于原生的Servlet，通过了功能强大的前端控制器DispatcherServlet，对请求和响应进行统一处理
- 表述层各细分领域需要解决的问题全方位覆盖，提供全面解决方案
- 代码清新简洁，大幅度提升开发效率
- 内部组件化程度高，可插拔式组件即插即用，想要什么功能配置相应组件即可
- 性能卓著，尤其适合现代大型、超大型互联网项目要求



## 2、HelloWorld

### 2.1、开发环境

- IDE：idea2019.2
- 构建工具：maven3.5.4
- 服务器：tomcat7
- spring版本：5.3.1



### 2.2、创建maven工程

- 添加web模块
- 打包方式：war
- 引入依赖

```xml
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>

    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>

    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>

    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```

![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)



### 2.3、配置web.xml

注册SpringMVC的前端控制器DispatcherServlet

- 默认配置方式：此配置作用下，SpringMVC的配置文件默认位于WEB-INF下，默认名称为-servlet.xml，例如，以下配置所对应SpringMVC的配置文件位于WEB-INF下，文件名为springMVCservlet.xml

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求
        /*：表示包括.jsp的所有请求
        “/”拦截除jsp外所有请求  “/*”拦截所有请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

- 扩展配置方式可通过init-param标签设置SpringMVC配置文件的位置和名称，通过load-on-startup标签设置SpringMVC前端控制器DispatcherServlet的初始化时间

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理-->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--配置SpringMVC配置文件的位置和名称-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <!--
            使用classpath:表示从类路径查找配置文件，例如maven工程中的
            src/main/resources
        -->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--
        作为框架的核心组件，在启动过程中有大量的初始化操作要做
        而这些操作放在第一次请求时才执行会严重影响访问速度
        因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
    -->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

> 注：
>
> 标签中使用/和/*的区别：
>
> - /所匹配的请求可以是/login或.html或.js或.css方式的请求路径，但是/不能匹配.jsp请求路径的请求，因此就可以避免在访问jsp页面时，该请求被DispatcherServlet处理，从而找不到相应的页面 /\*则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用 /*的写法



### 2.4、创建请求控制器

- 由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即请求控制器
- 请求控制器中每一个处理请求的方法成为控制器方法
- 因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识为一个控制层组件，交给Spring的IoC容器管理，此时SpringMVC才能够识别控制器的存在

```java
@Controller()
public class HelloController {
}
```



> 注：
>
> @Controller
>
> - 表示Spring某个类的是否可以接收HTTP请求。
>
> @RestController
>
> - 表示Spring某个类的是否可以接收HTTP请求，相当于@Controller和@ResponseBody。
> - 需要注意的是当使用@RestController时，视图解析器不起作用：原因是@ResponseBody的作用。
>
> @ResponseBody
>
> - 注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式（一般为application/json）后，写入到Response对象的body数据区。
> - 使用时机： 返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等）使用；
> - 如果返回到页面是map或者json或者list等，加上@ResponseBody准没错，如果你想跳转到一个页面，那么千万别加@ResponseBody，因为这个注解会将你返回的东西放到response的body数据中去，换句话说，你返回的页面将以字符串的形式写到页面上，而不是跳转到这个页面



### 2.5、创建SpringMVC.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.atguigu"></context:component-scan>

    <!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver"
      class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
        </bean>
    </property>
</bean>
    <!--
    处理静态资源，例如html、js、css、jpg
    若只设置该标签，则只能访问静态资源，其他请求则无法访问
    此时必须设置<mvc:annotation-driven/>解决问题
    -->
    <mvc:default-servlet-handler/><!--作用：当DispatcherServlet遇到来自客户端的不能处理的请求时，则会使用Tomcat的Servlet的方法去处理该请求。-->
    <!-- 开启mvc注解驱动  -->
    <mvc:annotation-driven>
        <mvc:message-converters>
            <!-- 处理响应中文内容乱码 -->
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="defaultCharset" value="UTF-8" />
                <property name="supportedMediaTypes">
                    <list>
                        <value>text/html</value>
                        <value>application/json</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
</beans>
```

> 注：
>
> \<mvc:default-servlet-handler/>的作用
>
> ```xml
> 现在如果在index页面加入一个本应该由tomcat的Servlet处理的请求，而我们自定义的Handler不能处理的请求
> <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
> <!DOCTYPE html >
> <html>
>     <head>
>         <title>Insert title here</title>
>     </head>
>     <body>
>         <a href="./userinfo/toView.do?address.detail=老家河南">点我一下</a>
>        <!-- img标签请求访问luna.jpg的请求会被DispatcherServlet拦截，但我们的UserInfoController里面没有定义能处理该请求的Handle方法-->
>         <img src="./img/luna.jpg">
>     </body>
> </html>
> ```
>
> 就会出现下面错误：图片不能加载
>
> ![image-20230307230243050](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230307230243050.png)
>
> 此时就需要在xml配置中添加\<mvc:default-servlet-handler>标签， 但一般来说，这样会占用过多系统资源，并不是个明智的解决方案，最好的办法是在xml配置中将请求拦截设置为指定后缀，减少资源占用。
>
> ```xml
> <servlet-mapping>
>         <servlet-name>DispatcherServlet</servlet-name>
>         <!-- /拦截除jsp外所有请求  /*拦截所有请求 -->
>         <url-pattern>*.do</url-pattern>
> </servlet-mapping>
> ```



### 2.6、测试HelloWorld：访问指定页面

```java
// @RequestMapping注解：处理请求和控制器方法之间的映射关系 
// @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径 也就是 localhost:8080/springMVC/
@Controller
public class HelloController {

    //"/" --> /WEB-INF/templates/index.xml

    @RequestMapping("/")//http://localhost:8099/springMVC/
    public String index() {//返回值会被DispatcherSevlet前端控制器获取，然后在视图解析器中被解析，加上前缀和后缀
        //返回视图名称
        return "index";
    }

    @RequestMapping("/target")//http://localhost:8099/springMVC/target
    public String target() {
        return "target";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"><!--thymeleaf命名空间-->
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<a th:href="@{/target}">    </a>//再新建一个名为target.html的页面。使用thymeleaf之后，会在/target之前自动加上上下文路径（上下文路径是tomcat中配置的application context）
</body>
</html>


<a href="/target">访问目标页面target.html</a>
```

> 注：
>
> 浏览器会将/target中的"/"解析成localhost:8080/，然后在/target再加上springmvc
>
> 超链接/target是绝对路径，是由浏览器解析的，访问时会加上前缀localhost:8080，解析为localhost:8080/springmvc/target

![image-20230307230835291](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230307230835291.png)

![image-20230307230909244](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230307230909244.png)



### 2.7、总结

浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器， 将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面。



## 3、@RequestMapping注解详解

### 3.1、@RequestMapping注解的功能

- 从注解名称上我们可以看到，@RequestMapping注解的作用就是将请求和处理请求的控制器方法关联起来，建立映射关系。
- SpringMVC接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。



### 3.2、@RequestMapping注解的位置

- @RequestMapping标识一个类：设置映射请求的请求路径的初始信息
- @RequestMapping标识一个方法：设置映射请求请求路径的具体信息

```java
@Controller
@RequestMapping("/hello")
public class RequestMappingController {

    @RequestMapping("/testRequetMapping")
    public String success() {
        return "success";
    }
}
```

```html
<h1>首页</h1>
<a th:href="@{/hello/testRequetMapping}">测试</a>
```



### 3.3、 @RequestMapping注解的value属性

- @RequestMapping注解的value属性通过请求的请求地址匹配请求映射
- @RequestMapping注解的value属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址所对应的请求
- @RequestMapping注解的value属性必须设置，至少通过请求地址匹配请求映射
- 若当前请求的请求地址不满足请求映射的value属性，则浏览器报错404

```html
<a th:href="@{/testRequestMapping}">测试@RequestMapping的value属性-->/testRequestMapping</a>

<a th:href="@{/test}">测试@RequestMapping的value属性-->/test</a>
```

```java
@RequestMapping(value = {"/testRequestMapping", "/test"})
public String testRequestMapping(){
    return "success";
}
```



### 3.4、@RequestMapping注解的method属性

@RequestMapping注解的method属性通过请求的请求方式（get或post）匹配请求映射

- get请求和post请求的区别
  - get请求会以?username=xxx&password=xxx的方式将请求参数拼接在请求地址后，post请求会将请求参数放在请求体中
  - get请求传输速度快，post请求传输速度较慢
  - get传输数据量有限，post传输数据量较大

@RequestMapping注解的method属性是一个RequestMethod类型的数组，表示该请求映射能够匹配多种请求方式的请求，如果不设置method属性，则任何类型的请求方法都支持。

若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported

> 注：
>
> - 对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解处理get请求的映射-->@GetMapping、处理post请求的映射-->@PostMapping、处理put请求的映射-->@PutMapping、处理delete请求的映射-->@DeleteMapping
> - 常用的请求方式有get，post，put，delete
> - 但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符串（put或delete），则按照默认的请求方式get处理，若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter

```html
<body>
<h1>首页</h1>
<a th:href="@{/hello/testRequetMapping}">测试</a>
<form th:action="@{/hello/testRequetMapping}" method="post">
    <input type="submit">
</form>
</body>
```

```java
@Controller
@RequestMapping("/hello")
public class RequestMappingController {
    @RequestMapping(value = "/testRequetMapping",method = RequestMethod.POST)
    public String success() {
        return "success";
    }
}
```



### 3.5、@RequestMapping注解的params属性（了解）

@RequestMapping注解的params属性通过请求的请求参数匹配请求映射

@RequestMapping注解的params属性是一个字符串类型的数组，可以通过四种表达式设置请求参数和请求映射的匹配关系

- "param"：要求请求映射所匹配的请求必须携带param请求参数
- "!param"：要求请求映射所匹配的请求必须不能携带param请求参数
- "param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value
- "param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是param!=value

若当前请求满足@RequestMapping注解的value和method属性，但是不满足params属性，此时页面回报错400：Parameter conditions "username, password!=123456" not met for actual request parameters: username={admin}, password={123456}

```html
<a th:href="@{/test(username='admin',password=123456)">测试@RequestMapping的params属性-->/test</a>
```

```java
@RequestMapping(value = {"/testRequestMapping", "/test"},method = {RequestMethod.GET, RequestMethod.POST},params = {"username","password!=123456"}) // 要求请求参数中包含username,password，且password不能等于123456
public String testRequestMapping(){
    return "success";
}
```



### 3.6、@RequestMapping注解的headers属性（了解）

@RequestMapping注解的headers属性通过请求的请求头信息匹配请求映射

@RequestMapping注解的headers属性是一个字符串类型的数组，可以通过四种表达式设置请求头信息和请求映射的匹配关系

- "header"：要求请求映射所匹配的请求必须携带header请求头信息
- "!header"：要求请求映射所匹配的请求必须不能携带header请求头信息
- "header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value
- "header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

若当前请求满足@RequestMapping注解的value和method属性，但是不满足headers属性，此时页面显示404错误，即资源未找到

```java
@RequestMapping(value = {"/testRequestMapping", "/test"},method = {RequestMethod.GET, RequestMethod.POST},params = {"username","password!=123456"},headers={"Host=localhost:8080"}) // 要求请求头必须带有Host并且值为localhost:8080
public String testRequestMapping(){
    return "success";
}
```



### 3.7、SpringMVC支持ant风格的路径

- ？：表示任意的单个字符，必须有单个字符
- *：表示任意的0个或多个字符
- **：表示任意的一层或多层目录
- 注意：在使用**时，只能使用/**/xxx的方式

```java
@RequestMapping("/a?a/testRequestMapping")
public String testRequestMapping(){
    return "success";
}
```



### 3.8、SpringMVC支持路径中的占位符（重点）

- 原始方式：/deleteUser?id=1
- rest方式：/deleteUser/1
- SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的@RequestMapping注解的value属性中通过占位符{xxx}表示传输的数据，再通过@PathVariable注解，将占位符所表示的数据赋值给控制器方法的形参

```html
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a>
```

```java
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
//最终输出的内容为-->id:1,username:admin
```



## 4、SpringMVC获取请求参数

### 4.1、通过servletAPI获取请求参数

将HttpServletRequest作为控制器方法的形参，此时HttpServletRequest类型的参数表示封装了当前请求的请求报文的对象。

```html
<a th:href="@{/testServletAPI(username='admin',password=123456)}">测试使用ServletAPI获取请求参数</a>
```

```java
@RequestMapping("/testServletAPI")
//形参位置的request表示当前请求
public String testServletAPI(HttpServletRequest request) {
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println("username:" + username + "password:" +password);
    return "success";
}
```



### 4.2、通过控制器方法的形参获取请求参数

在控制器方法的形参位置，设置和请求参数同名的形参，当浏览器发送请求，匹配到请求映射时，在DispatcherServlet中就会将请求参数赋值给相应的形参

```java
@RequestMapping("/testParam")
public String testParam(String username,String password,String[] hobby) {
    //若请求参数中出现多个同名的请求参数，可以在控制器方法的形参位置设置字符串类型或字符串数组来接收此请求参数
    //若使用字符串类型的形参，最终结果为请求参数的每一个值之间使用逗号进行拼接
    System.out.println("username" + username + "password" + password + ",hobby" + Arrays.toString(hobby));
    return "success";
}
```

- 若请求所传输的请求参数中有多个同名的请求参数，此时可以在控制器方法的形参中设置字符串数组或者字符串类型的形参接收此请求参数
- 若使用字符串数组类型的形参，此参数的数组中包含了每一个数据，若使用字符串类型的形参，此参数的值为每个数据中间使用逗号拼接的结果。



### 4.3、通过@RequestParam注解获取请求参数

@RequestParam是将请求参数和控制器方法的形参创建映射关系

@RequestParam注解一共有三个属性：

- value：指定为形参赋值的请求参数的参数名
- required：设置是否必须传输此请求参数，默认值为true，若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置 defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为 false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null
- defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

```html
<a th:href="@{/testParam(user_name='admin',password=123456)}">测试使用ServletAPI获取请求参数</a>
```

```java
@RequestMapping("/testParam")
public String testParam(@RequestParam(value = "user_name",required = false,defaultValue = "xiaoming") String username,String password) {
    System.out.println("username" + username + "password" + password);
    return "success";
}
```



### 4.4、通过POJO获取请求参数

- 可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实体类中的属性名一致，那么请求参数就会为此属性赋值

```html
<form th:action="@{/testpojo}" method="post">
    用户名：<input type="text" name="username">
    密码：<input type="password" name="password">
    性别：<input type="radio" name="sex" value="男">男
          <input type="radio" name="sex" value="女">女
    年龄：<input type="text" name="age">
    邮箱：<input type="text" name="email">
    <input type="submit">
</form>
```

```java
@RequestMapping("/testpojo")
public String testPOJO(User user){
    System.out.println(user);
    return "success";
}
//最终结果-->User{id=null, username='张三', password='123', age=23, sex='男',email='123@qq.com'}
```



### 4.5、通过RequestHeader获取请求头

- @RequestHeader是将请求头信息和控制器方法的形参创建映射关系
- @RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```html
<a th:href="@{/testParam(user_name='admin',password=123456)}">测试使用ServletAPI获取请求参数</a>
```

```java
@RequestMapping("/testParam")
public String testParam(@RequstHeader(value = "host",required = true,defaultValue = "localhost:8080") String host) {
    System.out.println("host" + host);
    return "success";
}
```



### 4.6、通过@CookieValue获取Cookie值

- @CookieValue是将cookie数据和控制器方法的形参创建映射关系
- @CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```java
@RequestMapping("/testParam")
public String testParam(@CookieValue("JSESSIONID") String jSessionId) {
    System.out.println("jSessionId" + jSessionId;
    return "success";
}
```



### 4.7、解决获取请求参数的乱码问题

解决获取请求参数的乱码问题，可以使用SpringMVC提供的编码过滤器CharacterEncodingFilter，但是必须在web.xml中进行注册

```xml
<!--配置SpringMVC的编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>//强制响应体使用设置的编码
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



## 5、向域对象中共享数据

### 5.1、使用servletAPI向requet域对象中共享数据

```java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
    request.setAttribute("testScope", "hello,servletAPI");
    return "success";
}
```



### 5.2、使用ModelAndView向request域对象中共享数据（重要）

不管是哪种方式向request域对象中共享数据，DispatchServlet前端控制器都会将控制器方法中的模型（指的是使用了Model的模型，如果没有使用则为空）和视图封装为一个ModelAndView对象

![image-20230308224103784](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308224103784.png)

```java
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
    /**
        * ModelAndView有Model和View的功能
        * Model主要用于向请求域共享数据
        * View主要用于设置视图，实现页面跳转 
        * 实现页面跳转，控制器设置视图名称，视图名称由视图解析器解析，来找到最终的页面
    */
    ModelAndView mav = new ModelAndView();
    //处理模型数据，向请求域request共享数据
    mav.addObject("testScope", "hello,ModelAndView");
    //设置视图名称，实现页面跳转
    mav.setViewName("success");
    return mav;//将mav返回给前端控制器解析
}
```



### 5.3、使用Model向request域对象中共享数据

```java
@RequestMapping("/testModel")
public String testModel(Model model){
    model.addAttribute("testScope", "hello,Model");
    return "success";
}
```



### 5.4、使用Map向request域对象中共享数据

```java
@RequestMapping("/testMap")
public String testMap(Map<String, Object> map){
    map.put("testScope", "hello,Map");
    return "success";
}
```



### 5.5、使用ModelMap向request域对象中共享数据

```java
@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap){
    modelMap.addAttribute("testScope", "hello,ModelMap");
    return "success";
}
```



### 5.6、 Model、ModelMap、Map的关系

Model、ModelMap、Map类型的参数其实本质上都是 BindingAwareModelMap类型的

```java
public interface Model{}
public class ModelMap extends LinkedHashMap<String, Object> {}
public class ExtendedModelMap extends ModelMap implements Model {}
public class BindingAwareModelMap extends ExtendedModelMap {}
```

![image-20230308224415596](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308224415596.png)



### 5.7、向session域中共享数据

```java
@RequestMapping("/testSession")
public String testSession(HttpSession session) {
    session.setAttribute("testSessionScope", "hello,session");
    return "success"; 
}
```



### 5.8、向application域中共享数据

```java
@RequestMapping("/testApplication")
public String testApplication(HttpSession session){
    ServletContext application = session.getServletContext();
    application.setAttribute("testApplicationScope", "hello,application");
    return "success";
}
```



## 6、SpringMVC的视图

- SpringMVC中的视图是View接口，视图的作用渲染数据，将模型Model中的数据展示给用户
- SpringMVC视图的种类很多，默认有转发视图（InternalResourceView）和重定向视图（RedirectView）
- 当工程引入jstl的依赖，转发视图会自动转换为JstlView（JSP技术）
- 若使用的视图技术为Thymeleaf，在SpringMVC的配置文件中配置了Thymeleaf的视图解析器，由此视图解析器解析之后所得到的是ThymeleafView



### 6.1、ThymeleafView

当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过转发的方式实现跳转

![image-20230313215507616](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313215507616.png)

### 6.2、转发视图

SpringMVC中默认的转发视图是InternalResourceView

SpringMVC中创建转发视图的情况：

- 当控制器方法中所设置的视图名称以"forward:"为前缀时，创建InternalResourceView视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"forward:"去掉，剩余部分作为最终路径通过转发的方式实现跳转

- 例如"forward:/"，"forward:/employee"

![image-20230313215617893](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313215617893.png)



### 6.3、重定向视图

SpringMVC中默认的重定向视图是RedirectView

SpringMVC中创建重定向视图的情况：

- 当控制器方法中所设置的视图名称以"redirect:"为前缀时，创建RedirectView视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"redirect:"去掉，剩余部分作为最终路径通过重定向的方式实现跳转
- 例如"redirect:/"，"redirect:/employee


![image-20230313215736760](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313215736760.png)

> 注：
>
> 重定向视图在解析时，会先将redirect:前缀去掉，然后会判断剩余部分是否以/开头，若是则会自动拼接上下文路径
>
> 转发和重定向的区别：
>
> - 转发是一次请求，重定向是两次请求
> - 转发可以获取请求域中的数据，重定向不可以获取
> - 转发可以访问WEBINF下的资源，重定向不可以访问
> - 转发不可以跨域（只能访问服务器内容资源，重定向可以（可以跳转到百度或其他域））





### 6.4、视图控制器

控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用viewcontroller标签进行表示

```xml
<!--
    path：设置处理的请求地址
    view-name：设置请求地址所对应的视图名称
-->
<mvc:view-controller path="/testView" view-name="success"></mvc:view-controller>
```

当SpringMVC中设置任何一个view-controller时，其他控制器中的请求映射将全部失效，此时需要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签：

```xml
<!--开启mvc的注解驱动-->
<mvc:annotation-driven />
```



## 7、RESTFul

### 7.1、RESTFul简介

REST：Representational State Transfer，表现层资源状态转移。

#### 7.1.1、资源

资源是一种看待服务器的方式，即，将服务器看作是由很多离散的资源组成。每个资源是服务器上一个可命名的抽象概念。因为资源是一个抽象的概念，所以它不仅仅能代表服务器文件系统中的一个文件、 数据库中的一张表等等具体的东西，可以将资源设计的要多抽象有多抽象，只要想象力允许而且客户端 应用开发者能够理解。与面向对象设计类似，资源是以名词为核心来组织的，首先关注的是名词。一个 资源可以由一个或多个URI来标识。URI既是资源的名称，也是资源在Web上的地址。对某个资源感兴趣的客户端应用，可以通过资源的URI与其进行交互。



#### 7.1.2、资源的表述

资源的表述是一段对于资源在某个特定时刻的状态的描述。可以在客户端-服务器端之间转移（交 换）。资源的表述可以有多种格式，例如HTML/XML/JSON/纯文本/图片/视频/音频等等。资源的表述格式可以通过协商机制来确定。请求-响应方向的表述通常使用不同的格式。



#### 7.1.3、状态转移

状态转移说的是：在客户端和服务器端之间转移（transfer）代表资源状态的表述。通过转移和操作资源的表述，来间接实现操作资源的目的。



### 7.2、RESTFul的实现

具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。

它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

![image-20230313220704363](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313220704363.png)





### 7.3、HiddenHttpMethodFilter

#### 7.3.1、如何发送put和delete请求

SpringMVC 提供了 HiddenHttpMethodFilter 帮助我们将 POST 请求转换为 DELETE 或 PUT 请求 HiddenHttpMethodFilter 处理put和delete请求的条件：

- 当前请求的请求方式必须为post
- 当前请求必须传输请求参数_method

满足以上条件，HiddenHttpMethodFilter 过滤器就会将当前请求的请求方式转换为请求参数 \_method的值，因此请求参数_method的值才是最终的请求方式

在web.xml中注册HiddenHttpMethodFilter

```xml
<filter>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 注：
>
> 目前为止，SpringMVC中提供了两个过滤器：CharacterEncodingFilter和 HiddenHttpMethodFilter， 在web.xml中注册时，必须先注册CharacterEncodingFilter，再注册HiddenHttpMethodFilter
>
> - 在 CharacterEncodingFilter 中通过 request.setCharacterEncoding(encoding) 方法设置字符集的 request.setCharacterEncoding(encoding) 方法要求前面不能有任何获取请求参数的操作，而 HiddenHttpMethodFilter 恰恰有一个获取请求方式的操作：
>
> ```java
> String paramValue = request.getParameter(this.methodParam);
> ```



### 7.4、案例

#### 7.4.1、准备工作

准备实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class Employee {
    private Integer id;
    private String lastName;
    private String email;
    //1 male, 0 female
    private Integer gender;
}
```



准备DAO模拟数据

```java
@Repository
public class EmployeeDao {
    private static Map<Integer, Employee> employees = null;
    static{
        employees = new HashMap<Integer, Employee>();
        employees.put(1001, new Employee(1001, "E-AA", "aa@163.com", 1));
        employees.put(1002, new Employee(1002, "E-BB", "bb@163.com", 1));
        employees.put(1003, new Employee(1003, "E-CC", "cc@163.com", 0));
        employees.put(1004, new Employee(1004, "E-DD", "dd@163.com", 0));
        employees.put(1005, new Employee(1005, "E-EE", "ee@163.com", 1));
    }
    private static Integer initId = 1006;
    public void save(Employee employee){
        if(employee.getId() == null){
            employee.setId(initId++);
        }
        employees.put(employee.getId(), employee);
    }
    public Collection<Employee> getAll(){
        return employees.values();
    }
    public Employee get(Integer id){
        return employees.get(id);
    }
    public void delete(Integer id){
        employees.remove(id);
    }
}
```



#### 7.4.2、功能清单

![image-20230313221221118](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313221221118.png)



#### 7.4.3、具体功能

##### 1、访问首页

配置view-controller

```xml
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>
```

创建页面

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<a th:href="@{/employee}">查看员工信息</a>
</body>
</html>
```



##### 2、查询所有员工数据

控制器方法

```java
@RequestMapping(value = "/employee",method = RequestMethod.GET)
public String getAllEmployee(Model model) {
    Collection<Employee> employees = employeeDao.getAll();
    model.addAttribute("employeeList",employees);
    return "employee_list";
}
```

创建employee_list.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Employee Info</title>
</head>
<body>
  <table id="dataTable" border="1" cellspacing="0" cellpadding="0" style="text-align: center;">
    <tr>
      <th colspan="5">Employee Info</th>
    </tr>
    <tr>
      <th>id</th>
      <th>lastName</th>
      <th>email</th>
      <th>gender</th>
      <th>options(<a th:href="@{/toAdd}"><add></add></a> )</th>
    </tr>
    <tr th:each="employee : ${employeeList}">
      <td th:text="${employee.id}"></td>
      <td th:text="${employee.lastName}"></td>
      <td th:text="${employee.email}"></td>
      <td th:text="${employee.gender}"></td>
      <td>
        <a @click="deleteEmployee" th:href="@{/employee/}+${employee.id}">delete</a>
        <a href="">update</a>
      </td>
    </tr>
  </table>
</body>
</html>
```



##### 3、删除

创建处理delete请求方式的表单

```html
<!--作用：通过超链接控制表单的提交，将post请求装换为delete请求-->
<form id="deleteForm" method="post">
  <input type="hidden" name="_method" value="delete">
</form>
```

删除超链接绑定点击事件

```javascript
script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
<script type="text/javascript">
  var vue = new Vue({
    el:"#dataTable",
    methods:{
      deleteEmployee:function (event) {
        var deleteForm = document.getElementById("deleteForm");
        //将触发点击事件的超链接的href属性赋值给表单的action
        deleteForm.action = event.target.href;
        //提交表单
        deleteForm.submit();
        //取消超链接的默认行为
        event.preventDefault();
      }
    }
  });
</script>
```

控制器方法

```java
@RequestMapping(value = "/employee/{id}",method = RequestMethod.DELETE)
public String deleteEmployee(@PathVariable("id") Integer id) {
    employeeDao.delete(id);
    return "redirect:/employee";
}
```



7.5、处理静态资源的过程

```xml
<!--配置视图控制器-->
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>
<mvc:view-controller path="/toAdd" view-name="employee_add"></mvc:view-controller>

<!--开放对静态资源的访问-->
<mvc:default-servlet-handler/>

<!--开启mvc注解驱动-->
<mvc:annotation-driven/>
```

- 如果没有设置开放对静态资源的访问，所有的请求（包括请求静态资源）都会被springMVC处理（DispatherServlet），而SpringMVC无法处理静态资源，就会报404错误
- 如果只设置了开放对静态资源的访问，没有设置MVC注解驱动，则所有的请求都将被默认的Servlet（Tomcat目录conf目录中的配置文件web.xml中配置的默认Servlet）处理，会导致对控制器方法的访问请求失败
- 开放对静态资源的访问和MVC注解驱动要同时使用，此时所有的请求会先由SpringMVC（DispatcherServlet）处理，如果SpringMVC处理不了，则交由默认Servlet处理，如果都处理不了则报404错误



## 8、HttpMessageConverter

HttpMessageConverter，报文信息转换器，将请求报文转换为Java对象，或将Java对象转换为响应报文 HttpMessageConverter提供了两个注解和两个类型：@RequestBody，@ResponseBody， RequestEntity， ResponseEntity



### 8.1、@RequestBody

@RequestBody可以获取请求体，需要在控制器方法设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值

一般用来处理 application/json、application/xml等类型的数据

```html
<form th:action="@{/testRequestBody}" method="post">
    用户名：<input type="text" name="username">
    密码：<input type="password" name="password">
    <input type="submit">
</form>
```

```java
@RequestMapping("/testRequestBody")
public String testRequestBody(@RequestBody String requestBody){
    System.out.println("requestBody:"+requestBody);
    return "success";
}
```

```
输出：requestBody:username=admin&password=123456
```



### 8.2、RequestEntity

RequestEntity封装请求报文的一种类型，需要在控制器方法的形参中设置该类型的形参，当前请求的**请求报文**就会赋值给该形参，可以通过getHeaders()获取请求头信息，通过getBody()获取请求体信息

```java
@RequestMapping("/testRequestEntity")
public String testRequestEntity(RequestEntity<String> requestEntity){
    System.out.println("requestHeader:"+requestEntity.getHeaders());
    System.out.println("requestBody:"+requestEntity.getBody());
    return "success";
}
```

```
输出结果：requestHeader:[host:"localhost:8080", connection:"keep-alive", content-length:"27", cache-control:"max-age=0", sec-ch-ua:"" Not A;Brand";v="99", "Chromium";v="90", "Google Chrome";v="90"", sec-ch-ua-mobile:"?0", upgrade-insecure-requests:"1", origin:"http://localhost:8080", user-agent:"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36"] requestBody:username=admin&password=123
```



### 8.3、@ResponseBody

@ResponseBody用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

```java
@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    return "success";
}
```

结果：浏览器页面显示success（success字符串作为响应体显示在浏览器页面上）



### 8.4、SpringMVC处理json

#### 8.4.1、回顾JSON

- json是JavaScript中的一种数据交互格式
- json包含json对象和json数组



#### 8.4.2、@ResponseBody处理json的步骤

导入jackson的依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

在SpringMVC的核心配置文件中开启mvc的注解驱动，此时在HandlerAdaptor中会自动装配一个消息转换器：MappingJackson2HttpMessageConverter，可以将响应到浏览器的Java对象转换为Json格式的字符串

```xml
<mvc:annotation-driven/>
```

在处理器方法上使用@ResponseBody注解进行标识

将Java对象直接作为控制器方法的返回值返回，就会自动转换为Json格式的字符串

```java
@RequestMapping("/testResponseUser")
@ResponseBody
public User testResponseUser(){
    return new User(1001,"admin","123456",23,"男");
}
```

浏览器的页面显示结果： {"id":1001,"username":"admin","password":"123456","age":23,"sex":"男"}



### 8.5、SpringMVC处理ajax

请求超链接

```html
<div id="app">
    <a th:href="@{/testAjax}" @click="testAjax">testAjax</a>
</div>
```

通过vue和axios处理点击事件

```javascript
<script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
<script type="text/javascript" th:src="@{/static/js/axios.min.js}"></script>
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            testAjax:function (event) {
                axios({
                    method:"post",
                    url:event.target.href,
                    params:{
                        username:"admin",
                        password:"123456"
                    }
                }).then(function (response) {
                    alert(response.data);
                });
                event.preventDefault();
            }
        }
    });
</script>
```

控制器方法

```java
@RequestMapping("/testAjax")
@ResponseBody
public String testAjax(String username, String password){
    System.out.println("username:"+username+",password:"+password);
    return "hello,ajax";
}
```



### 8.6、@RestController注解

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了 @Controller注解，并且为其中的每个方法添加了@ResponseBody注解



### 8.7、ResponseEntity

ResponseEntity用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文





## 9、文件下载和上传

### 9.1、文件下载

使用ResponseEntity实现下载文件的功能

```java
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws
        IOException {
    //获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //获取服务器中文件的真实路径（这个真实路径可以通过变量传递）
    String realPath = servletContext.getRealPath("/static/img/1.jpg");
    //创建输入流，读取文件系统的字节码文件
    InputStream is = new FileInputStream(realPath);
    //创建字节数组
    byte[] bytes = new byte[is.available()];//is.available()：获取要下载的文件的字节数
    //将流读到字节数组bytes中
    is.read(bytes);
    //创建HttpHeaders对象设置响应头信息
    MultiValueMap<String, String> headers = new HttpHeaders();
    //设置要下载方式以及下载文件的名字
    headers.add("Content-Disposition", "attachment;filename=1.jpg");//以附件的形式下载
    //设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    //创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, statusCode);
    //关闭输入流
    is.close();
    return responseEntity;
}
```



### 9.2、文件上传

文件上传要求form表单的请求方式必须为post，并且添加属性enctype="multipart/form-data"

SpringMVC中将上传的文件封装到MultipartFile对象中，通过此对象可以获取文件相关信息

上传步骤：

- 添加依赖

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

- 在SpringMVC.xml中添加配置

```xml
<!--必须通过文件解析器的解析才能将文件转换为MultipartFile对象-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
id的值是固定的：multipartResolver
```

- 控制器方法

```java
@RequestMapping("/testUp")
public String testUp(MultipartFile photo,HttpSession session) throws IOException {
    //获取上传的文件的文件名
    String filename = photo.getOriginalFilename();//获取photo的文件名
    //获取上传的文件的后缀名
    String suffixName = fileName.substring(fileName.lastIndexOf("."));
    //将uuid作为文件名
    String uuid = UUID.randomUUID().toString().replaceAll("-","");
    //将uuid和后缀名拼接后的结果作为最终的文件名
    fileName = uuid + suffixName;
    ServletContext servletContext = session.getServletContext();
    String photoPath = servletContext.getRealPath("photo");//获取服务器中photo文件夹的路径
    File file = new File(photoPath);
    //判断photoPath所对应路径是否存在
    if (!file.exists()) {
        //若不存在，则创建目录
        file.mkdir();
    }
    String finalPath = photoPath + File.separator + filename;//最终的上传路径
    photo.transferTo(new File(finalPath));//最终的上传功能
    return "success";
}
```



### 9.3、解决文件的重名问题

当向同一个文件夹中上传同名文件是，会将原有的文件覆盖（是文件内容的覆盖，输出流的问题），使用UUID为文件重新起名





## 10、拦截器

### 10.1、拦截器设置

- SpringMVC中的拦截器用于拦截控制器方法的执行
- SpringMVC中的拦截器需要实现HandlerInterceptor
- SpringMVC的拦截器必须在SpringMVC的配置文件中进行配置：

```xml
    <!--配置拦截器-->
    <mvc:interceptors>
<!--        <bean class="com.atguigu.mvc.interceptor.FirstInterceptor"></bean>-->
<!--        <ref bean="firstInterceptor"></ref>-->
<!--以上两种配置方式都是对DispatcherServlet所处理的所有请求进行拦截-->
        <mvc:interceptor>
            <!--
                在拦截器中配置/*表示拦截上下文路径下的一层路径的所有请求，配置/**才表示拦截所有请求
            -->
            <mvc:mapping path="/**"/>
            <mvc:exclude-mapping path="/"/>
            <ref bean="firstInterceptor"></ref>
        </mvc:interceptor> 
    </mvc:interceptors>
<!--
以上配置方式可以通过ref或bean标签设置拦截器，通过mvc:mapping设置需要拦截的请求，通过
mvc:exclude-mapping设置需要排除的请求，即不需要拦截的请求
-->
```



### 10.2、拦截器的三个抽象方法

SpringMVC中的拦截器有三个抽象方法：

- preHandle：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法
- postHandle：控制器方法执行之后执行postHandle()
- afterComplation：处理完视图和模型数据，渲染视图完毕之后执行afterComplation()



### 10.3、多个拦截器的执行顺序

若每个拦截器的preHandle()都返回true， 此时多个拦截器的执行顺序和拦截器在SpringMVC的配置文件的配置顺序有关：

- preHandle()会按照配置的顺序执行，而postHandle()和afterComplation()会按照配置的反序执行

若某个拦截器的preHandle()返回了false

- preHandle()返回false和它之前的拦截器的preHandle()都会执行，postHandle()都不执行，返回false 的拦截器之前的拦截器的afterComplation()会执行





## 11、异常处理器

### 11.1、基于配置的异常处理

- SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：HandlerExceptionResolver
- HandlerExceptionResolver接口的实现类有：DefaultHandlerExceptionResolver和SimpleMappingExceptionResolver
- SpringMVC提供了自定义的异常处理器SimpleMappingExceptionResolver，使用方式：

```xml
<!--配置异常处理-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <!--
            properties的键表示处理器方法执行过程中出现的异常
            properties的值表示若出现指定异常时，设置一个新的视图名称，跳转到指定页面
            error是视图名称
            -->
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <!--
    exceptionAttribute属性设置一个属性名，将出现的异常信息在请求域中进行共享
    以ex为键，以当前异常为值
    -->
    <property name="exceptionAttribute" value="ex"></property>
</bean>
```



### 11.2、基于注解的异常处理

```java
//@ControllerAdvice将当前类标识为异常处理的组件，这是一个增强的 Controller，对Controller层做异常处理、数据预处理、全局数据绑定, springboot会自动扫描到,不需要调用。

这个注解是spring MVC提供的，在springboot中也可以使用
@ControllerAdvice
public class ExceptionController {
    //@ExceptionHandle用于设置所标识方法的处理的异常
    @ExceptionHandler(value = {ArithmeticException.class,NullPointerException.class})
    //ex表示当前请求处理中出现的异常对象
    public String testException(Exception ex, Model model) {
        model.addAttribute("ex",ex);
        return "error";
    }
}
```



### 11.3、注意

- mvc:view-controller 映射的请求如果有异常，则是基于XML的异常映射来处理（因为此标签是配置在XML中）
- @RequestMapping 映射的请求如果有异常，则是基于注解的异常映射来处理（如果有异常处理的注解，没有则还是XML来处理）





## 12、注解配置SpringMVC

使用配置类和注解代替web.xml和SpringMVC配置文件的功能



### 12.1、创建初始化类，代替web.xml

在Servlet3.0环境中，容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类， 如果找到的话就用它来配置Servlet容器。 Spring提供了这个接口的实现，名为 SpringServletContainerInitializer，这个类反过来又会查找实现WebApplicationInitializer的类并将配置的任务交给它们来完成。Spring3.2引入了一个便利的WebApplicationInitializer基础实现，名为 AbstractAnnotationConfigDispatcherServletInitializer，当我们的类扩展了 AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自动发现它，并用它来配置Servlet上下文。

```java
//Web工程的初始化类，用来代替web.xml
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceResponseEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter,hiddenHttpMethodFilter};
    }
}
```



### 12.2、创建SpringConfig配置类，代替Spring的配置文件

```java
@Configuration
public class SpringConfig {
}
```



### 12.3、创建WebConfig配置类，代替SpringMVC的配置文件

```java
/**
* 代替springmvc.xml配置文件
* 1、扫描组件 2、视图解析器、3、视图控制器（view-controller） 4、default-servlet-handle
* 5、mvc注解驱动 6、文件上传解析器（CommonsMultipartResolver) 7、拦截器 8、异常处理
*/
//将当前类标识为一个配置类
@Configuration
//扫描组件
@ComponentScan("com.atguigu.mvc.controller")
//mvc注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    //使用默认的servlet处理静态资源
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        FirstInterceptor firstInterceptor = new FirstInterceptor();
        registry.addInterceptor(firstInterceptor).addPathPatterns("/**");
    }

    //配置视图控制器
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/hello").setViewName("hello");
    }

    //配置文件上传解析器
    @Bean
    public CommonsMultipartResolver multipartResolver(){
        return new CommonsMultipartResolver();
    }

    //异常处理映射
    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
        Properties properties = new Properties();
        properties.setProperty("java.lang.ArithmeticException","error");
        exceptionResolver.setExceptionMappings(properties);
        exceptionResolver.setExceptionAttribute("exception");
        resolvers.add(exceptionResolver);
    }

    //配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }
    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver
                                                       templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }
    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
}
```



## 13、SpringMVC执行流程

### 13.1、SpringMVC常用组件

DispatcherServlet：前端控制器，不需要工程师开发，由框架提供

- 作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求

HandlerMapping：处理器映射器，不需要工程师开发，由框架提供

- 作用：根据请求的url、method等信息查找Handler，即控制器方法

Handler：处理器，需要工程师开发（就是控制器）

- 作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理

HandlerAdapter：处理器适配器，不需要工程师开发，由框架提供

- 作用：通过HandlerAdapter利用反射对处理器（控制器方法）进行执行

ViewResolver：视图解析器，不需要工程师开发，由框架提供

- 作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、 RedirectView

View：视图

- 作用：将模型数据通过页面展示给用户



### 13.2、DispatcherServlet初始化过程

DispatcherServlet 本质上是一个 Servlet，所以天然的遵循 Servlet 的生命周期。所以宏观上是 Servlet 生命周期来进行调度。

- 初始化WebApplicationContext
  - WebApplicationContext 就是SpringMVC的IOC容器

- 创建WebApplicationContext
- DispatcherServlet初始化策略



### 13.3、DispatcherServlet调用组件处理请求

- processRequest()
- doService()
- doDispatch()





### 13.4、SpringMVC的执行流程

用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获

DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射

不存在

- 再判断是否配置了mvc:default-servlet-handler

- 如果没配置，则控制台报映射查找不到，客户端展示404错误

![image-20230329231518877](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230329231518877.png)

- 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404 错误

![image-20230329231656469](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230329231656469.png)

存在则执行下面的流程：

- 根据该URI，调用HandlerMapping获得该Handler（控制器方法）配置的所有相关的对象（包括Handler对象以及 Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。


- DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。


- 如果成功获得HandlerAdapter，此时将开始执行拦截器的preHandler(…)方法【正向】

- 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。 在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

  - HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息

  - 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等
  - 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等
  - 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

- Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。

- 此时将开始执行拦截器的postHandle(...)方法【逆向】。

- 根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行 HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model 和View，来渲染视图。

- 渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。

- 将渲染结果返回给客户端。























































































































































































































