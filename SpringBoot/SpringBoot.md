# 1、基础入门



## 1.1、Spring与SpringBoot

### 1.1.1、Spring能做什么

#### 1.1.1.1、Spring的能力

![image-20230313224907593](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313224907593.png)



#### 1.1.1.2、Spring的生态

- web开发
- 数据访问
- 安全控制
- 分布式
- 消息服务
- 移动开发
- 批处理



#### 1.1.1.3、Spring5的重大升级

响应式编程

![image-20230308225242263](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308225242263.png)

基于Java8的一些新特性，如：接口默认实现。重新设计源码架构



### 1.1.2、为什么使用SpringBoot 

Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run".

能快速创建出生产级别的Spring应用

#### 1.1.2.1、SpringBoot的优点

- Create stand-alone Spring applications：创建独立Spring应用

- Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)：内嵌web服务器

- Provide opinionated 'starter' dependencies to simplify your build configuration：自动starter依赖，简化构建配置

- Automatically configure Spring and 3rd party libraries whenever possible：自动配置Spring以及第三方功能

- Provide production-ready features such as metrics, health checks, and externalized configuration：提供生产级别的监控、健康检查及外部化配置

- Absolutely no code generation and no requirement for XML configuration：无代码生成、无需编写XML

- SpringBoot是整合Spring技术栈的一站式框架，是简化Spring技术栈的快速开发脚手架

  



## 1.2、SpringBoot入门

### 1.2.1、系统要求

- [Java 8](https://www.java.com/) & 兼容java14
- Maven 3.3+
- idea 2019.1.2

Maven设置

```xml
<mirrors>
    <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>
<profiles>
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
</profiles>
```



### 1.2.2、HelloWorld

创建Maven工程 （点击IDEA右下角Enable Auto-Import，每次更新依赖会自动导入）

引入依赖

```xml
<parent> <!--指定当前父工程坐标，相当于管理了当前项目SpringBoot的版本-->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

创建主程序

- @SpringBootApplication：这是一个SpringBoot应用

```java
/**
* 主程序类
* @SpringBootApplication：这是一个SpringBoot应用
*/
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```



编写业务

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String handle01(){
        return "Hello, Spring Boot 2!";
    }
}
```



编写配置文件：application.properties

```properties
server.port=8888
```



简化部署

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

- 把项目打成jar包，直接在目标服务器执行即可。



## 1.3、了解自动配置原理

### 1.3.1、`SpringBoot`的特点

#### 1.3.1.1、依赖管理

##### 1、父项目做依赖管理

```xml
依赖管理    
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>

父项目的父项目
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>

spring-boot-dependencies中几乎声明了所有开发中常用的依赖的版本号（自动版本仲裁机制）
```

##### 2、开发导入`starter`场景启动器

- `starter`依赖是一组方便的依赖描述符，可以包含在应用程序中。可以一站式地获得所需的所有`Spring`和相关技术，而不必在示例代码中寻找，也不必复制粘贴加载的依赖描述符

  - 见到很多`spring-boot-starter-*`：`*`就代表某种场景，例如`spring-boot-starter-web`就代表`web`项目

  - 只要引入`starter`，这个场景的所有常规需要的依赖都会被自动引入

  - SpringBoot所有支持的场景：https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

  - 见到的`*-spring-boot-starter`： 第三方为我们提供的简化开发的场景启动器。

  - 所有场景启动器最底层的依赖


```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.3.4.RELEASE</version>
    <scope>compile</scope> 
</dependency>
```



##### 3、无须关注版本号，自动版本仲裁

- 引入依赖默认都可以不写版本

- 引入非版本仲裁的依赖，要写版本号

- 可以修改默认版本号


```xml
1、查看spring-boot-dependencies里面规定当前依赖的版本用的key。
2、在当前项目里面重写配置
<properties>
    <mysql.version>5.1.43</mysql.version>
</properties>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```



### 1.3.2、SpringBoot自动配置

- 自动配好`Tomcat`

  - 自动引入`Tomcat`依赖

  - 自动配置`Tomcat`


```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <version>2.3.4.RELEASE</version>
      <scope>compile</scope>
</dependency>
```

- 自动配好`SpringMVC`。

  - 自动引入`SpringMVC`全套组件。

  - 自动配好`SpringMVC`常用组件（功能）。


- 自动配好`Web`常见功能，如：字符编码问题。

  - `SpringBoot`帮我们配置好了所有`web`开发的常见场景。


```java
/**
* 主程序类
* @SpringBootApplication：标识这是一个SpringBoot应用
*/
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        //1、返回IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class);

        //2、查看容器中的所有组件
        String[] beanDefinitionNames = run.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            System.out.println(beanDefinitionName);
        }
    }
}
```

- 默认的包结构

  - 如果不指定的话，主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来。

  - 无需以前的包扫描配置。

  - 想要改变扫描路径，`@SpringBootApplication(scanBasePackages=**"com.atguigu"**)`或者`@ComponentScan`指定扫描路径（但是需要将`@SpringBootApplication`拆分）。


```java
@SpringBootApplication
等同于
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.atguigu.boot")
```

- 各种配置拥有默认值

  - 默认配置最终都是映射到某个类上，如：`MultipartProperties`。

  - 配置文件的值最终会绑定某个类上，这个类会被创建成对象并放入容器中。


- 按需加载所有自动配置项

  - `SpringBoot`有非常多的`starter`

  - 引入了哪些场景的依赖哪个场景的自动配置才会开启

  - `SpringBoot`所有的自动配置功能都在`spring-boot-autoconfigure`包里面
    - 此包中有众多的场景所需要的类，但是有些发红（并没有生效），原因是并没有引入这些场景的依赖，所以就不会生效






### 1.3.3、SpringBoot基本功能

#### 1.3.3.1、组件添加注解解析

##### 1、`@Configuration`

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";

    boolean proxyBeanMethods() default true;
}
```

- 告诉`SpringBoot`这是一个配置类 == 配置文件（例如：配置`bean`对象的配置文件，相当于以前的`spring`核心配置文件）。

- 配置类本身也是组件（自己也会被注册到容器中）。

- 参数：`proxyBeanMethods`：代理`bean`的方法

  - `Full(proxyBeanMethods = true)`：代理对象调用方法，`SpringBoot`启动或调用时总会检查这个组件是否在容器中存在，保证每个`@Bean`方法被调用多少次返回的组件都是单实例的。

  - `Lite(proxyBeanMethods = false)`：非代理对象调用方法，每个`@Bean`方法被调用多少次返回的组件都是新创建的。

  - 解决场景：组件依赖（例如：`A`组件中`property`中`ref=B`组件）必须使用`Full`模式默认（单例）。其他情况时，为了提高`SpringBoot`项目启动速度，则是用`Lite`模式。


```java
/**
* 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认是单实例的
* 2、配置类本身也是组件
* 3、proxyBeanMethods：代理bean的方法
*      Full(proxyBeanMethods = true)、【保证每个@Bean方法被调用多少次返回的组件都是单实例的】
*      Lite(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的组件都是新创建的】
*      组件依赖必须使用Full模式默认。其他默认是否Lite模式
*/
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {
    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }
    @Bean("tom")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}
```

```
报错:Method annotated with @Bean is called directly in a @Configuration where proxyBeanMethods set to false. Set proxyBeanMethods to true or use dependency injection. 
在@Configuration中直接调用带有@Bean注释的方法，其中proxyBeanMethods设置为false。将proxyBeanMethods设置为true或使用依赖注入。
```

```java
@Configuration(proxyBeanMethods = true) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {
    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }
    @Bean("tom")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}
```

```java
################################@Configuration测试代码如下########################################
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.atguigu.boot")
public class MainApplication {
    public static void main(String[] args) {
        //1、返回我们IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        //2、查看容器里面的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }

        //3、从容器中获取组件

        Pet tom01 = run.getBean("tom", Pet.class);

        Pet tom02 = run.getBean("tom", Pet.class);

        System.out.println("组件："+(tom01 == tom02)); // 不管proxyBeanMethods是true还是false结果都为true，因为这是直接从容器中取出tom组件（只有一个）

        //4、com.atguigu.boot.config.MyConfig$$EnhancerBySpringCGLIB$$51f1e1ca@1654a892 获取到的bean本身就是代理对象
        MyConfig myConfig = run.getBean(MyConfig.class);
        System.out.println(bean);

        //如果@Configuration(proxyBeanMethods = true) 那就是代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有。
        //保持组件单实例
        User user = myConfig.user01();
        User user1 = myConfig.user01();
        System.out.println(user == user1); // true

        User user01 = run.getBean("user01", User.class);
        Pet tom = run.getBean("tom", Pet.class);

        System.out.println("用户的宠物："+(user01.getPet() == tom)); // true
        
        // 如果proxyBeanMethods=false 这两结果都为false
        // @Configuration(proxyBeanMethods = false) bean对象本身调用方法，就会新创建对象
         User user = myConfig.user01();
        User user1 = myConfig.user01();
        System.out.println(user == user1); // false

        User user01 = run.getBean("user01", User.class);
        Pet tom = run.getBean("tom", Pet.class);

        System.out.println("用户的宠物："+(user01.getPet() == tom)); // false
    }
}
```



##### 2、`@Bean`

- `@Bean`：给容器中添加组件，如果不给`value`属性值赋值，则用方法名作为组件的`id`，返回类型就是组件类型，返回的值就是组件在容器中的实例。默认是单实例      

```java
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Bean {
    @AliasFor("name")
    String[] value() default {};

    @AliasFor("value")
    String[] name() default {};

    /** @deprecated */
    @Deprecated
    Autowire autowire() default Autowire.NO;

    boolean autowireCandidate() default true;

    String initMethod() default "";

    String destroyMethod() default "(inferred)";
}
```



##### 3、`@ComponentScan`

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    @AliasFor("basePackages")
    String[] value() default {};

    @AliasFor("value")
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};
}
```

```java
@ComponentScan("com.zhangchengwei.service")
public class AppConfig {
}
```

- 用来标明需要扫描的包



##### 4、`@Import`

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {
    Class<?>[] value();
}
```

```java
@Import({User.class, DBHelper.class})
给容器中自动注册这两个类型的组件（调用这两个组件的无参构造器创建组件）、默认组件的名字就是全限定类名

@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {
}
```

- `@Import`高级用法： https://www.bilibili.com/video/BV1gW411W7wy?p=8



##### 5、`@Conditional`

- `@Conditional`来源于`spring-context`包下的一个注解。`Conditional`中文是条件的意思，`@Conditional`注解它的作用是按照一定的条件进行判断，满足条件给容器注册`bean`。`@Conditional`注解本身并不能注册`Bean`到容器中去。

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}
@Target({ElementType.TYPE, ElementType.METHOD}) 使用范围接口、类、枚举、注解、方法
@Retention(RetentionPolicy.RUNTIME)： @Retention是用来修饰注解的生命周期的，RetentionPolicy.RUNTIME代表的是不仅被保存到class文件中，jvm加载class文件之后，仍然存在；一直有效！
@Documented： @Documented和@Deprecated注解长得有点像，@Deprecated是用来标注某个类或者方法不建议再继续使用，@Documented只能用在注解上，如果一个@B注解，被@Documented标注，那么被@B注解修饰的类，生成Javadoc文档时，会显示@B。 
```

- `@Conditional`只有一个参数，并且这个参数要求是继承于`Condition`类，并且参数是个数组，也就是可以传多个的。`Condition`类是一个函数式接口（只有一个方法的接口被称为函数式接口）。`matches`方法就是匹配方法，如果返回为`true`则配置，如果返回为`false`则不配置。

```java
@FunctionalInterface
public interface Condition {
    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);
}
```

> 注：
>
> - 条件装配：满足`Conditional`指定的条件，则进行配置，通常此注解与`@Configuration`注解一同使用，如果条件没满足，`@Configuration`也不会发挥作用，组件就不会注册进容器中。
> - 使用位置：方法上、类上

![image-20230315121011644](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230315121011644.png)

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({OnClassCondition.class})
public @interface ConditionalOnClass {
    Class<?>[] value() default {};

    String[] name() default {};
}
```



**实战**

（1）自定义`Condition`实现类

```java
public class MyCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Environment environment = context.getEnvironment();
        String isOpen = environment.getProperty("systemLog.isOpen");
        // 就算没有设置systemLog.isOpen，那么isOpen就等于null，Boolean.valueOf对于null照样会返回false的
        return Boolean.valueOf(isOpen);
    }
}
```

（2）自定义一个实体类，用于测试

```java
public class TestBean1 {
    @Override
    public String toString() {
        return super.toString() + "--我是TestBean1";
    }
}
```

（3）添加配置类，并在类上使用 @Conditional

```java
@Configuration
@Conditional(MyCondition.class) // @Conditional会根据自定义条件类来判断
public class Myconfig {

    @Bean
    public TestBean1 testBean1(){
        return new TestBean1();
    }
}
```

（4）添加测试类

```java
@RestController
public class CommonController {

    @Autowired(required = false)
    private Myconfig myconfig;
    
	@Autowired(required = false)
    private TestBean1 testBean1;

    @RequestMapping("/import")
    public void printImportBeanInfo() {
        System.out.println(myconfig);
        System.out.println(testBean1);
    }
}
```

（5）启动测试： 访问`http://localhost:8080/import`，可见`Myconfig`类并没有注入到容器，按正常来说被`@Configuration`修饰之后是会存放到容器当中的，但是显然因为`@Conditional`判断为`false`所以没有注入到容器当中。

![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/505138bbc29a49bba796d604cfd937a9.png)

> 注：通过这个测试不难发现假如`@Bean`所在的类没有注入到容器当中，那么它也不会被注入到容器当中。
>



（6）在`application.yml`当中添加如下配置，然后再启动测试。

```yml
systemLog:
  isOpen: true
```

![在这里插入图片描述](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/07357e66a6a647db911e92c4c8e9d2bf.png)

（7）`@Conditional`还可以应用于方法上，我们可以让他和`@Bean`注解来配合使用。



**`@Conditional`多条件**

- 前言中说，`@Conditional`注解传入的是一个`Class`数组，存在多种条件类的情况。

- 这种情况貌似判断难度加深了，测试一波，新增新的条件类，实现的`matches`返回`false`（这种写死返回`false`的方法纯属测试用，没有实际意义）。


```java
public class ObstinateCondition implements Condition {
 
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
         return false;
    }
}
```

```java
@Configuration
@Conditional({MyCondition.class,ObstinateCondition.class})
public class Myconfig {

    @Bean
    public TestBean1 testBean1(){
        return new TestBean1();
    }
}
```

测试结果得知：

- 第一个条件类实现的方法返回`true`，第二个返回`false`，则结果`false`，不注入进容器。
- 第一个条件类实现的方法返回`true`，第二个返回`true`，则结果`true`，注入进容器中。



**常见扩展注解**

- `@ConditionalOnClass`

![image-20230315114039146](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230315114039146.png)

- 主要是判断是否存在这个类文件，如果有这个文件就相当于满足条件，然后可以注入到容器当中。
- 当然并不是说容器里面是否有这个类类型的组件哈，不要理解错了，这也就是我们有时候使用`springboot`只需要引入个依赖，框架就可以用的原因！

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({OnClassCondition.class})
public @interface ConditionalOnClass {
	// 必须出现的类
    Class<?>[] value() default {};
	
	// 必须存在的类名，必须是全限类名，也就是包含包名+类名。
    String[] name() default {};
}
```

```java
@Configuration
@ConditionalOnClass({TestBean2.class})
public class Myconfig {

    @Bean
    @ConditionalOnClass(name = "com.gzl.cn.springbootcache.config.TestBean3")
    public TestBean1 testBean1(){
        return new TestBean1();
    }
}
```

​	

`@ConditionalOnMissingClass`

- `@ConditionalOnMissingClass`只有一个`value`属性。他和`@ConditionalOnClass`功能正好相反，`@ConditionalOnClass`是`class`存在为`true`，而`@ConditionalOnMissingClass`是不存在为`true`，也就是存在为`false`。为`fasle`就意味着不注入。

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({OnClassCondition.class})
public @interface ConditionalOnMissingClass {
	// 必须不存在的类名称，全类名
    String[] value() default {};
}
```

```java
@ConditionalOnMissingClass("com.gzl.cn.springbootcache.config.TestBean5")
```

```java
@ConditionalOnMissingClass(value = "com.gzl.cn.springbootcache.config.TestBean5")
```



@ConditionalOnBean

- bean存在的时候注入，不存在的时候不注入，这块就是指的spring的ioc容器了。


```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({OnBeanCondition.class})
public @interface ConditionalOnBean {

	// 指定bean的类类型。当所有指定类的bean都包含在容器中时，条件匹配。
    Class<?>[] value() default {};
	// 指定bean的全类名。当指定的所有类的bean都包含在容器中时，条件匹配。
    String[] type() default {};
	// bean所声明的注解,当ApplicationContext中存在声明该注解的bean时返回true
    Class<? extends Annotation>[] annotation() default {};
	// bean的id,当ApplicationContext中存在给定id的bean时返回true,这个id指的就是容器当中对象的id
    String[] name() default {};
	// 搜索容器层级,默认是所有上下文搜索
    SearchStrategy search() default SearchStrategy.ALL;
	// 可能在其泛型参数中包含指定bean类型的其他类
    Class<?>[] parameterizedContainer() default {};
}
```

- 代码示例：假如我把testBean2方法删掉，那么testBean1也将会不注入。

```java
@Configuration
public class Myconfig {

    @Bean
    public TestBean2 testBean2(){
        return new TestBean2();
    }

    @Bean
    @ConditionalOnBean(TestBean2.class)
    public TestBean1 testBean1() {
        return new TestBean1();
    }
}
```

```java
@RestController
public class CommonController {

    @Autowired(required = false)
    private TestBean1 testBean1;

    @Autowired(required = false)
    private TestBean2 testBean2;

    @Autowired(required = false)
    private Myconfig myconfig;

    @RequestMapping("/import")
    public void printImportBeanInfo() {
        System.out.println(myconfig);
        System.out.println(testBean1);
        System.out.println(testBean2);
    }
}
```

运行输出结果：会发现三个对象是都注入到容器当中了。

- 目前存在的问题

> 注：
>
> 这里还存在一个执行顺序问题，假如我把以下代码放到启动类当中，而不是Myconfig配置文件当中，这时候会发现一个问题，testBean2注入进去了，但是带有@ConditionalOnBean(TestBean2.class)条件的testBean1没有注入进去。原因其实很简单，执行testBean1的时候，testBean2并没有注入进去，然后testBean1就注入失败了，紧接着失败后testBean2又注入进来了。

```java
@Bean
public TestBean2 testBean2(){
    return new TestBean2();
}
```

紧接着我又做了个试验，把启动类当中的testBean2删掉了，又创建了如下一个配置类，但是testBean1还是注入失败。

```java
@Configuration
public class MyconfigTest {
    @Bean
    public TestBean2 testBean2(){
        return new TestBean2();
    }
}
```

想要解决这个问题很简单，想办法让`MyconfigTest`比`Myconfig`先加载就可以了

- 于是我在MyconfigTest 添加了如下order注解

```java
@Order(Ordered.HIGHEST_PRECEDENCE) //最高优先级
```

- 然后在Myconfig 也添加了Order注解

```java
@Order(Ordered.LOWEST_PRECEDENCE) //最低优先级
```

但是仍然没有解决该问题。@Configuration并不能通过@Order指定顺序

@Configuration通过配置类名的自然顺序来加载的。但不可能每次遇到这种问题都改名字吧，经查文档，终于找到了需要的东西：我们可以通过@AutoConfigureBefore，@AutoConfigureAfter来控制配置类的加载顺序。

在MyconfigTest类上添加`@AutoConfigureBefore(Myconfig.class)`，意思是在Myconfig实例化之前加载。如果要让@AutoConfigureBefore生效，还需要在META-INF/spring.factories文件中添加如下内容：

![在这里插入图片描述](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/3835c7e106fa4db082222215e14634e3.png)

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.gzl.cn.springbootcache.config.MyconfigTest,\
com.gzl.cn.springbootcache.config.Myconfig
```



@ConditionalOnMissingBean

- bean不存在的时候注入，存在的时候为false。跟@ConditionalOnBean正好是相反的。

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({OnBeanCondition.class})
public @interface ConditionalOnMissingBean {
    Class<?>[] value() default {};

    String[] type() default {};
	// 识别匹配 bean 时，可以被忽略的 bean 的 class 类型
    Class<?>[] ignored() default {};
	//识别匹配 bean 时，可以被忽略的 bean 的 class 类型名称
    String[] ignoredType() default {};

    Class<? extends Annotation>[] annotation() default {};

    String[] name() default {};

    SearchStrategy search() default SearchStrategy.ALL;

    Class<?>[] parameterizedContainer() default {};
}
```

@ConditionalOnMissingBean代表的是如果容器里面没有`TestBean1`的实例，那么就运行@Bean修饰的方法注入对象，不管注入的什么对象。

```java
@Configuration
public class Myconfig {

    @Bean
    @ConditionalOnMissingBean(TestBean1.class)
    public TestBean1 testBean1() {
        return new TestBean1();
    }
}
```

> 注：
>
> @ConditionalOnMissingBean和@ConditionalOnBean使用的时候是可以不带任何属性的，不带任何属性的时候他就是判断的当前注入的类型。@ConditionalOnMissingBean是判断当没有的时候进行注入，例如如下：他只会注入一个TestBean1进去。

```java
@Configuration
public class Myconfig {

    @Bean
    @ConditionalOnMissingBean
    public TestBean1 testBean1() {
        System.out.println("1111111111");
        return new TestBean1();
    }

    @Bean
    @ConditionalOnMissingBean
    public TestBean1 testBean2() {
        System.out.println("2_222_222_222");
        return new TestBean1();
    }
}
```





@ConditionalOnProperty

- @ConditionalOnProperty主要可用于通过和`springboot`当中`application`配置文件来使用。在实战当中我们也可以通过他来实现配置化管理bean。


```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@Documented
@Conditional({OnPropertyCondition.class})
public @interface ConditionalOnProperty {
	// 指定的属性完整名称，不能和name同时使用。
    String[] value() default {};
	// //配置文件中属性的前缀
    String prefix() default "";
	// //指定的属性名称
    String[] name() default {};
	// 指定的属性的属性值要等于该指定值，当value或name为一个时使用
    String havingValue() default "";
	// 当不匹配时是否允许加载，当为true时就算不匹配也不影响bean的注入或配置类的生效。
    boolean matchIfMissing() default false;
}
```

使用实例：

```java
@Configuration
@ConditionalOnProperty(
        prefix = "system.log",
        name = {"open"},
        havingValue = "true",
        matchIfMissing = false
)
public class Myconfig {

    @Bean
    public TestBean1 testBean1() {
        return new TestBean1();
    }
}
```

然后只要在`application.yaml`添加如下配置，`Myconfig`和`testBean1`就会注入到容器当中。如果不设置也不会报错，只是注入不到容器里而已。

```yaml
system:
  log:
    open: true
```



其他注解

- @ConditionalOnJava：只有运行指定版本的 Java 才会加载 Bean
- @ConditionalOnWebApplication 和 @ConditionalOnNotWebApplication：只有运行在(不在)web 应用里才会加载这个 bean
- @ConditionalOnCloudPlatform：只有运行在指定的云平台上才加载指定的 bean
- @ConditionalOnJndi：只有指定的资源通过 JNDI 加载后才加载 bean
- @ConditionalOnExpression(“${test.express}==true”) ：可通过spring提供的spEL表达式灵活配置，当表达式为true的时候，才会实例化一个Bean
- @ConditionalOnSingleCandidate(UserService.class) ：表示ioc容器中只有一个UserService类型的Bean，才生效
- @ConditionalOnResource：指定的静态资源⽂件存在才加载





#### 1.3.3.2、原生配置文件引入

##### 1、@ImportResource

通过此注解可以导入并解析其他spring配置文件

```java
@ImportResource("classpath:beans.xml")
public class MyConfig {}
```



#### 1.3.3.3、配置绑定

如何使用Java读取到properties文件中的内容，并且把它封装到JavaBean中，以供随时使用；

```java
public class getProperties {
     public static void main(String[] args) throws FileNotFoundException, IOException {
         Properties pps = new Properties();
         pps.load(new FileInputStream("a.properties"));
         Enumeration enum1 = pps.propertyNames();//得到配置文件的名字
         while(enum1.hasMoreElements()) {
             String strKey = (String) enum1.nextElement();
             String strValue = pps.getProperty(strKey);
             System.out.println(strKey + "=" + strValue);
             //封装到JavaBean。
         }
     }
}
```



##### 1、@ConfigurationProperties

将注解标识的类与配置文件绑定，类中属性和配置文件中的配置项绑定。如果标识在@Bean修饰的方法上， 那就意味着将方法返回的组件属性和配置文件中的配置项绑定

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ConfigurationProperties {
    @AliasFor("prefix")
    String value() default "";

    @AliasFor("value")
    String prefix() default "";

    boolean ignoreInvalidFields() default false;

    boolean ignoreUnknownFields() default true;
}
```

```java
@ConfigurationProperties(prefix = "mycar")
public class CarProperties {
    private String brand;
    private Integer price;
}
```

```properties
#application.properties
mycar.brand=BYD
mycar.price=10000
```



##### 2、@Component + @ConfigurationProperties

```java
/**
* 只有在容器中的组件，才会拥有SpringBoot提供的强大功能
*/
@Component
@ConfigurationProperties(prefix = "mycar")
public class CarProperties {
    private String brand;
    private Integer price;
}

public class MyConfig {
    @Bean
    public Car car(CarProperties carProperties) {
        Car car = new Car();
        car.setBrand(carProperties.getBrand());
        car.setPrice(carProperties.getPrice());
        return car;
    }
}
```



##### 3、@EnableConfigurationProperties +  @ConfigurationProperties

开启配置绑定功能，并将配置项中的组件注入到容器中

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import({EnableConfigurationPropertiesRegistrar.class})
public @interface EnableConfigurationProperties {
    String VALIDATOR_BEAN_NAME = "configurationPropertiesValidator";

    Class<?>[] value() default {};
}
```

```java
@Configuration(proxyBeanMethods = true) //告诉SpringBoot这是一个配置类 == 配置文件
@EnableConfigurationProperties(CarProperties.class)
//1、开启CarProperties配置文件绑定功能
//2、把这个CarProperties这个组件自动注册到容器中
public class MyConfig {
    @Bean
    public Car car(CarProperties carProperties) {
        Car car = new Car();
        car.setBrand(carProperties.getBrand());
        car.setPrice(carProperties.getPrice());
        return car;
    }
}

@ConfigurationProperties(prefix = "mycar")//绑定配置文件中以“mycar”开头的配置项
public class CarProperties {
    private String brand;
    private Integer price;
}
```

> 注：
>
> Enable：使能够、使可能、激活、启动
>
> 在第三方提供的配置类上没有Component，这时我们就需要使用这个方法



### 1.3.4、自动配置原理入门

#### 1.3.4.1、引导加载自动配置类（SpringBootApplication）

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication{}
```



##### 1、@SpringBootConfiguration

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
@Indexed
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

- @Configuration：代表当前是一个配置类



##### 2、@ComponentScan

- 指定扫描哪些包里的注解



##### 3、@EnableAutoConfiguration（实现自动装配主要靠此注解）

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {}
```

- @AutoConfigurationPackage：自动配置包，指定了默认的包规则

```java
@Import(AutoConfigurationPackages.Registrar.class)  // 给容器中导入一个组件
public @interface AutoConfigurationPackage {}

// 利用Registrar给容器中注册一系列组件（批量注册）
// 将指定的一个包下（MainApplication 所在包下）的所有组件导入进来
```

- @Import(AutoConfigurationImportSelector.class)

```java
/**
* 此方法是ImportSelector接口的实现方法
* 根据要导入的@Configuration类的AnnotationMetadata，选择并返回应该导入哪些类的名称。
* 返回：类名，如果没有则为空数组
*/
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    } else {
        AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry( );
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```



利用getAutoConfigurationEntry(annotationMetadata)：给容器中批量导入一些组件

```java
/**
* 返回AutoConfigurationImportSelector。基于导入@Configuration类的AnnotationMetadata的AutoConfigurationEntry。
* 参数：annotationMetadata——配置类的注释元数据
* 返回：应该导入的自动配置
*/
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    }
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    configurations = removeDuplicates(configurations);
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    checkExcludedClasses(configurations, exclusions);
    configurations.removeAll(exclusions);
    configurations = getConfigurationClassFilter().filter(configurations);
    fireAutoConfigurationImportEvents(configurations, exclusions);
    return new AutoConfigurationEntry(configurations, exclusions);
}
```



调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)：获取到所有需要导入到容器中的配置类

```java
/**
* 返回应该考虑的自动配置类名。默认情况下，该方法将使用springfactoresloader和getspringfactoresloaderfactoryclass()加载候选程序。
* 参数：元数据——源元数据
	   属性——注释属性
* 返回：候选配置的列表
*/
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
                                                                         getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
                    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```



利用工厂加载 Map<String,List<String>> loadSpringFactories(@Nullable ClassLoader classLoader)：得到所有的组件

```java
/** 
* 从“META-INF/spring”加载给定类型的工厂实现的完全限定类名。工厂”，使用给定的类装入器。
* 参数：factoryType——表示工厂的接口或抽象类
       classLoader——用来加载资源的classLoader;可以为空来使用默认值吗
* 抛出：IllegalArgumentException -如果在加载工厂名称时发生错误
* 参见：loadFactories
*/
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    String factoryTypeName = factoryType.getName();
    return loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

![image-20230408165607646](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230408165607646.png)

从META-INF/spring.factories位置来加载这个文件。

- 默认扫描我们当前系统里面所有jar包下META-INF/spring.factories位置的文件
- spring-boot-autoconfigure-2.3.4.RELEASE.jar包里面也有META-INF/spring.factories

![image-20230408165221068](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230408165221068.png)

```
文件里面写死了spring-boot一启动就要给容器中加载的所有配置类
spring-boot-autoconfigure-2.3.4.RELEASE.jar/META-INF/spring.factories
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
.....
```



#### 1.3.4.2、按需开启自动配置项

虽然我们127个场景的所有自动配置启动的时候默认全部加载。xxxxAutoConfiguration，但按照条件装配规则（@Conditional），最终会按需配置



#### 1.3.4.3、修改默认配置

```java
@Bean
@ConditionalOnBean(MultipartResolver.class)  //要求容器中有这个类型组件
@ConditionalOnMissingBean(name = DispatcherServlet.MULTIPART_RESOLVER_BEAN_NAME) //要求容器中没有这个名字 multipartResolver 的组件
public MultipartResolver multipartResolver(MultipartResolver resolver) {
    // 给@Bean标注的方法传入了对象参数，这个参数的值就会从容器中找。
    // SpringMVC multipartResolver。防止有些用户配置的文件上传解析器不符合规范
    // Detect if the user has created a MultipartResolver but named it incorrectly
    return resolver;
}
给容器中加入了文件上传解析器；
```

SpringBoot默认会在底层配好所有的组件，但是如果用户自己配置了以用户的优先

```java
@Bean
@ConditionalOnMissingBean
public CharacterEncodingFilter characterEncodingFilter() {
}
```



#### 1.3.4.4、什么是`SpringBoot`的自动配置

- `SpringBoot`自动配置，英文是`Auto-Configuration`

  - 它是指基于你引入的依赖`jar`包，对`SpringBoot`应用进行自动配置。

  - 它为`SpringBoot`框架的”开箱即用“提供了基础支撑。

- 术语“配置类”，英文`Configuration Class`：

  - 广义的“配置类”：被注解`@Component直接或间接修饰的某个类，即我们常说Spring组件，其中包括了@Configuration类

  - 狭义的”配置类“：特指被注解@Configuration所修饰的类，又称为@Configuration类


注解 @Import，是来自Spring框架的一个注解：

- 它的作用是提供了一种显式地从其他地方（第三方Jar包）加载配置类的方式，这样可以避免使用性能较差的组件扫描（Component Scan）
- 支持导入：
  - 普通类（这里的”普通“，是相对于随后的两个接口而言的），效果类似于这个普通类被@Component 所修饰，例如：在配置类ConfigB上修饰使用@Import(ConfigA.class)，这相当于将ConfigA类注册进IOC容器中，使其可用，这里ConfigA只是一个并未做任何修饰的普通类
  - 接口 ImportSelector 的实现类。可用通过@Import导入一个接口ImportSelector 的实现类。接口ImportSelector 中有一个selectImports方法，它的返回值是一个字符串数组，数组中的每一个元素分别代表一个将被导入的配置类的全限定名。利用该特性我们可以给IOC容器动态地导入多个配置类
  - 接口 ImportBeanDefinitionRegistrar 的实现类。可以通过@Import导入一个接口ImportBeanDefinitionRegistrar 的实现类。通过它，我们可以手动将多个BeanDefinition 注册到IOC容器中，从而实现个性化定制。利用该特性我们可以给IOC容器动态地导入多个BeanDefinition

```java
//对于第二种情况的测试
public class ConfigA {
    @Bean
    public A a() {
        return new A();
    }
}
public class A {
}

@Configuration
@Import(MyConfigImportSelector.class)
public class ConfigB {
}

public class MyConfigImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"com.atguigu.review.config.ConfigA"};
    }
}

测试
@SpringBootApplication
public class SpringBootReviewApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext run = SpringApplication.run(SpringBootReviewApplication.class, args);

        ConfigA configA = run.getBean(ConfigA.class);
        A a = run.getBean(A.class);
        System.out.println(configA.getClass().getSimpleName());
        System.out.println(a.getClass().getSimpleName());
    }
}
```

```java
// 对于第三种情况的测试
public class Dog{
}

public class MyRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        GenericBeanDefinition genericBeanDefinition = new GenericBeanDefinition();
        genericBeanDefinition.setBeanClass(Dog.class);
        registry.registerBeanDefinition("dog",genericBeanDefinition);// 手动向IOC容器中注册一个类型为Dog，名称为dog的Bean
    }
}

@Configuration
@Import({MyRegistrar.class})
public class ConfigC {
}
```



总结一下SpringBoot加载配置类的方式

- 使用注解@ComponentScan （不合适，程序员需要知道包名，非常麻烦）
- 使用@Import
  - 导入普通类 （不合适，程序员需要记住类名，非常麻烦）
  - 导入选择器ImportSelector
  - 导入注册器 ImportBeanDefinitionRegistrar （不合适，BeanDefinition注册器的设计目标就是对@Bean方法的一个补充。从名字就能看出来，它针对的是BeanDefinition层面的

![image-20230509152037756](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230509152037756.png)

@SpringBootApplication修饰的类，也会被@Configuration 简介修饰，即“源配置类”

SpringBoot 框架会对“源配置类”所在的package 进行组件扫描（Component Scan）

SpringBoot 框架最终会通过@EnableAutoConfiguration 导入 AutoConfigurationImportSelector 选择器来实现自动配置

> AutoConfigurationImportSelector 如何实现才能优雅地去发现 classpath中Jar包的自动配置类？
>
> - 优雅：用户只需要引入Jar包即可，至于Jar包中有哪些自动配置类、类名是什么，等等这些信息用户都不关心
> - 这不就是Java SPI的优点吗？刚好Spring框架有一个SpringFactories机制，它是Java SPI设计思想的延伸和扩展，我们不妨使用它来实现



SpringFactories机制

- Java SPI机制的延伸和扩展
- Spring框架的基础机制，在Spring以及SpringBoot源码中到处可见
- 可以基于它来实现SpringBoot的自动配置功能
- 它的核心逻辑是从 classpath 中读取到所有Jar包中的配置文件 META-IF/spring.factories，然后根据指定的key从配置文件中解析出来对应的value值

![image-20230509154752659](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230509154752659.png)



SpringBoot自动配置源码剖析

```java
@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    }
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata); // 自动配置的入口
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```

```java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    }
    // 1、获取annotationMetadata 的注解 @EnableAutoConfiguration 的属性
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    // 2、从资源文件 spring.factories 中获取EnableAutoConfiguration对应的所有类
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    configurations = removeDuplicates(configurations);
    // 3、通过在注解@EnableAutoConfiguration 设置exclude的相关属性，可以排除指定的自动配置类
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    checkExcludedClasses(configurations, exclusions);
    configurations.removeAll(exclusions);
    // 4、根据注解 @Conditional 来判断是否需要排除某些自动配置类
    configurations = getConfigurationClassFilter().filter(configurations);
    // 5、触发 AutoConfiguration 导入的相关事件
    fireAutoConfigurationImportEvents(configurations, exclusions);
    return new AutoConfigurationEntry(configurations, exclusions);
}
```

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    // 通过 SpringFactories 机制，从配置文件 spring.factories 中找出所有的自动配置类
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
                                                                         getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
                    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

![image-20230509155632629](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230509155632629.png)

![image-20230509155726280](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230509155726280.png)



总结SpringBoot从启动到自动配置的整个流程

![image-20230509155819958](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230509155819958.png)





![image-20230509000232066](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230509000232066.png)



> 注：最终语言总结
>
> - 在`SpringBoot`中完成自动配置的工作主要依靠的是`@SpringBootApplication`注解，而`@SpringBootApplication`注解是一个复合注解，其中`@EnableAutoConfiguration`才是完成自动配置功能的，这个注解会导入`ImportSelector`实现类`AutoConfigurationImportSelector`选择器，这个选择器通过`SpringFactories`机制从`classpath`中读取到所有`Jar`包中的配置文件`META-IF/spring.factories`，然后根据指定的`key`从配置文件中解析出来对应的`value`值，通过`SpringFactoriesLoader`拿到自动配置类的集合，最后由注解`@Conditional`过滤掉不需要的自动配置类，对剩下的自动配置类进行自动配置。



#### 1.3.4.5、总结

SpringBoot先加载所有的自动配置类 xxxxxAutoConfiguration

每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值。xxxxProperties里面拿，xxxProperties类和配置文件进行了绑定

生效的配置类就会给容器中装配很多组件，只要容器中有这些组件，相当于这些功能就有了

定制化配置

- 用户可以在配置类中直接使用@Bean替换底层的组件
- 用户去看这个组件是获取的配置文件的什么值，用户可以修改

**xxxxxAutoConfiguration ---> 组件 --->** **xxxxProperties里面拿值 ----> application.properties**



## 1.4、最佳实践

引入场景依赖

- https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

查看自动配置了哪些（选做）

- 自己分析，引入场景对应的自动配置一般都生效了
- 配置文件中debug=true开启自动配置报告（打印在控制台）。Negative（不生效）\Positive（生效）

是否需要修改

- 参照文档修改配置项
  - https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties
  - 自己分析。xxxxProperties绑定了配置文件的哪些。
- 自定义加入或者替换组件
  - @Bean、@Component。。。

- 自定义器 **XXXXXCustomizer**





# 2、核心功能



## 2.1、配置文件

### 2.1.1、文件类型

#### 2.1.1.1、properties

同以前的properties用法



#### 2.1.1.2、yaml

##### 1、简介

YAML 是 "YAML Ain't Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。

非常适合用来做以数据为中心的配置文件



##### 2、基本语法

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，' '与" "表示字符串内容会不转义/转义（单引号会将\n作为字符串输出，双引号会将\n作为换行输出）



##### 3、数据类型

- 字面量：单个的、不可再分的值。date、boolean、string、number、null

```yaml
k: v
```

- 对象：键值对的集合。map、hash、set、objec

```yaml
行内写法：  k: {k1:v1,k2:v2,k3:v3}
#或
k:
 k1: v1
 k2: v2
 k3: v3
```

- 数组：一组按次序排列的值。array、list、queue

```yaml
行内写法：  k: [v1,v2,v3]
#或者
k:
- v1
- v2
- v3
```



##### 4、示例

```java
@Data
public class Person {
   
   private String userName;
   private Boolean boss;
   private Date birth;
   private Integer age;
   private Pet pet;
   private String[] interests;
   private List<String> animal;
   private Map<String, Object> score;
   private Set<Double> salarys;
   private Map<String, List<Pet>> allPets;
}

@Data
public class Pet {
   private String name;
   private Double weight;
}
```

```yaml
# yaml表示以上对象
person:
 userName: zhangsan
 boss: false
 birth: 2019/12/12 20:12:33
 age: 18
 pet:
   name: tomcat
   weight: 23.4
 interests: [篮球,游泳]
 animal:
   - jerry
   - mario
 score: {english:80,math:90}
   english:
     first: 30
     second: 40
     third: 50
   math: [131,140,148]
   chinese: {first: 128,second: 136}
 salarys: [3999,4999.98,5999.99]
 allPets:
   sick:
     - {name: tom}
     - {name: jerry,weight: 47}
     - name: 阿猫
       weight: 88.88
   health: [{name: mario,weight: 47}]
```



### 2.1.2、配置提示

自定义的类和配置文件绑定一般没有提示，但可以通过配置处理器来配置提示功能

```xml
<!--配置配置文件自动提示-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-configuration-processor</artifactId>
   <optional>true</optional>
</dependency>

<build>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
               <configuration>
                   <excludes>
                       <!--在项目打包时排除此依赖-->
                       <exclude>
                           <groupId>org.springframework.boot</groupId>
                           <artifactId>spring-boot-configuration-processor</artifactId>
                       </exclude>
                   </excludes>
               </configuration>
           </plugin>
       </plugins>
   </build>
```





## 2.2、Web开发

### 2.2.1、SpringMVC自动配置概览

- Spring Boot provides auto-configuration for Spring MVC that **works well with most applications.(大多场景我们都无需自定义配置)**

- The auto-configuration adds the following features on top of Spring’s defaults:

- - Inclusion of ContentNegotiatingViewResolver and BeanNameViewResolver beans.

  - - 内容协商视图解析器和BeanName视图解析器

  - Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).

  - - 静态资源（包括webjars）

  - Automatic registration of Converter, GenericConverter, and Formatter beans.

  - - 自动注册 Converter，GenericConverter，Formatter

  - Support for HttpMessageConverters (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).

  - - 支持 HttpMessageConverters （后来我们配合内容协商理解原理）

  - Automatic registration of MessageCodesResolver (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-message-codes)).

  - - 自动注册 MessageCodesResolver （国际化用）

  - Static index.html support.

  - - 静态index.html 页支持

  - Custom Favicon support (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)).

  - - 自定义 Favicon

  - Automatic use of a ConfigurableWebBindingInitializer bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).

  - - 自动使用 ConfigurableWebBindingInitializer ，（DataBinder负责将请求数据绑定到JavaBean上）





### 2.2.2、简单功能分析

#### 2.2.2.1、静态资源访问

##### 1、静态资源目录

只要将静态资源（CSS、JS、图片等）放在类路径下：/static (or /public or /resources or /META-INF/resources

访问：当前项目根路径“/”（http://localhost:8080/）+ 静态资源名

原理：静态映射：“/**”（所有请求）

- 请求进来，先让Controller（DispatcherServlet）处理，不能处理的所有请求，则交给静态资源处理器，如果静态资源也找不到则报404错误

改变默认的静态资源路径

```yml
 resources:
   static-locations: [classpath:/haha/]
```



##### 2、静态资源访问前缀

静态资源默认无前缀，可以加前缀，如下：

```yaml
spring:
 mvc:
   static-path-pattern: /res/**
```

当前项目根路径 + static-path-pattern + 静态资源名

此操作会影响欢迎页和Favicon功能



##### 3、webjar

自动映射 /[webjars](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)/**

https://www.webjars.org/

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>3.5.1</version>
</dependency>
```

- 访问地址：[http://localhost:8080/webjars/](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)[jquery/3.5.1/jquery.js](http://localhost:8080/webjars/jquery/3.5.1/jquery.js) 后面地址要按照依赖里面的包路径

![image-20230411143504801](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230411143504801.png)

> 官方文档：
>
> 默认情况下，Spring Boot从classpath的/static目录（或/public、/resources、/META-INF/resources）或者ServletContext的根目录中提供静态内容。它使用了Spring MVC中的ResourceHttpRequestHandler，这样你就可以通过添加自己的WebMvcConfigurer并覆盖addResourceHandlers方法来修改行为。
>
> 在独立的web应用程序中，容器中的默认servlet也会被启用，并充当备用，如果Spring决定不处理它，它会从ServletContext的根目录提供内容。大多数情况下，这不会发生（除非修改默认的MVC配置），因为Spring总是可以通过DispatcherServlet处理请求。
>
> 默认情况下，资源映射在/\*\*上，但你可以通过spring.mvc.static-path-pattern 配置调整。例如，可以将所有资源重定位到/resources/**，如下所示：
>
> ```properties
> spring.mvc.static-path-pattern=/resources/**
> ```

> 你还可以使用spring.web.resources.static-locations 属性自定义静态资源的位置（用目录位置列表替换默认值）。Servlet的根上下文路径“/”也会被自动添加为一个位置。
>
> 除了前面提到的“标准”静态资源位置之外，还有一种针对Webjars内容的特殊情况。任何路径在/webjars/**的资源，如果以webjars格式打包，都是从jar文件中提供的。



#### 2.2.2.2、欢迎页支持

静态资源路径下index.html

- 可以配置静态资源路径，但是不可以配置静态资源的访问前缀，否则导致index.html不能被默认访问

使用controller方法处理/index

```yaml
spring:
#  mvc:
#    static-path-pattern: /res/**   这个会导致welcome page功能失效

 resources:
   static-locations: [classpath:/haha/]
```





#### 2.2.2.3、自定义Favicon

favicon.ico放在静态资源目录下即可

```yaml
spring:
#  mvc:
#    static-path-pattern: /res/**   这个会导致 Favicon 功能失效
```



#### 2.2.2.4、静态资源配置原理

SpringBoot启动默认加载很多的xxxAutoConfiguration 类（自动配置类）

SpringMVC功能的自动配置类 WebMvcAutoConfiguration，看其是否生效

```java
生效了

@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
       ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {}
```



看其给容器中配置了什么

```java
   。。。
   @Configuration(proxyBeanMethods = false)
   @Import(EnableWebMvcConfiguration.class)
   @EnableConfigurationProperties({ WebMvcProperties.class, ResourceProperties.class })
   @Order(0)
   public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {}
       。。。
```

看到了以上几个注解，配置文件的相关属性与xxx进行了绑定，WebMvcProperties==spring.mvc，ResourceProperties==spring.resources

配置类中只有一个有参构造

- 有参构造器中所有参数的值都会默认从容器中确定

```java
//有参构造器所有参数的值都会从容器中确定
//WebProperties webProperties；获取和spring.resources绑定的所有的值的对象
//WebMvcProperties mvcProperties 获取和spring.mvc绑定的所有的值的对象
//ListableBeanFactory beanFactory Spring的beanFactory
//HttpMessageConverters 找到所有的HttpMessageConverters，报文信息转换类
//ResourceHandlerRegistrationCustomizer 找到资源处理器的自定义器。=========
//DispatcherServletPath  
//ServletRegistrationBean  给应用注册原生的Servlet、Filter....
    public WebMvcAutoConfigurationAdapter(WebProperties webProperties, WebMvcProperties mvcProperties, ListableBeanFactory beanFactory, ObjectProvider<HttpMessageConverters> messageConvertersProvider, ObjectProvider<WebMvcAutoConfiguration.ResourceHandlerRegistrationCustomizer> resourceHandlerRegistrationCustomizerProvider, ObjectProvider<DispatcherServletPath> dispatcherServletPath, ObjectProvider<ServletRegistrationBean<?>> servletRegistrations) {
   this.resourceProperties = webProperties.getResources();
   this.mvcProperties = mvcProperties;
   this.beanFactory = beanFactory;
   this.messageConvertersProvider = messageConvertersProvider;
   this.resourceHandlerRegistrationCustomizer = (WebMvcAutoConfiguration.ResourceHandlerRegistrationCustomizer)resourceHandlerRegistrationCustomizerProvider.getIfAvailable();
   this.dispatcherServletPath = dispatcherServletPath;
   this.servletRegistrations = servletRegistrations;
   this.mvcProperties.checkConfiguration();
}
```

静态资源处理的默认规则

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
  if (!this.resourceProperties.isAddMappings()) {
     logger.debug("Default resource handling disabled");
     return;
  }
  addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
  addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
     registration.addResourceLocations(this.resourceProperties.getStaticLocations());
     if (this.servletContext != null) {
        ServletContextResource resource = new ServletContextResource(this.servletContext, SERVLET_LOCATION);
        registration.addResourceLocations(resource);
     }
  });
}

private void addResourceHandler(ResourceHandlerRegistry registry, String pattern, String... locations) {
  addResourceHandler(registry, pattern, (registration) -> registration.addResourceLocations(locations));
}

private void addResourceHandler(ResourceHandlerRegistry registry, String pattern,
     Consumer<ResourceHandlerRegistration> customizer) {
  if (registry.hasMappingForPattern(pattern)) {
     return;
  }
  ResourceHandlerRegistration registration = registry.addResourceHandler(pattern);
  customizer.accept(registration);
  registration.setCachePeriod(getSeconds(this.resourceProperties.getCache().getPeriod())); // 静态资源缓存时间
  registration.setCacheControl(this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl());
  registration.setUseLastModified(this.resourceProperties.getCache().isUseLastModified());
  customizeResourceHandlerRegistration(registration);
}
```



```yaml
spring:
  resources:
   add-mappings: false   false禁用所有静态资源访问规则
```

```yaml
spring:
 mvc:
   static-path-pattern:  配置静态资源访问路径，默认值是/**
```

```java
默认静态资源访问路径
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties {

   private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
           "classpath:/resources/", "classpath:/static/", "classpath:/public/" };

   /**
    * Locations of static resources. Defaults to classpath:[/META-INF/resources/,
    * /resources/, /static/, /public/].
    */
   private String[] staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
```



欢迎页的处理规则

```java
   HandlerMapping：处理器映射。保存了每一个Handler能处理哪些请求。   

   @Bean
       public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
               FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
           WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
                   new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
                   this.mvcProperties.getStaticPathPattern());
           welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
           welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
           return welcomePageHandlerMapping;
       }

   WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders,
           ApplicationContext applicationContext, Optional<Resource> welcomePage, String staticPathPattern) {
       if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
           //要用欢迎页功能，静态资源访问路径必须是/**
           logger.info("Adding welcome page: " + welcomePage.get());
           setRootViewName("forward:index.html");
       }
       else if (welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
           // 调用控制器方法， 看哪个方法能处理 /index
           logger.info("Adding welcome page template: index");
           setRootViewName("index");
       }
   }
```

> 注：
>
> 静态资源配置原理：当请求静态资源时，SpringBoot底层默认要求请求路径为/**，





### 2.2.3、请求映射及参数处理

#### 2.2.3.1、请求映射

##### 1、Rest使用与原理

@xxxMapping

Rest风格支持（使用**HTTP**请求方式动词来表示对资源的操作）

- 以前：/getUser  获取用户 /deleteUser 删除用户  /editUser  修改用户  /saveUser 保存用户
- 现在： /user GET-获取用户 DELETE-删除用户 PUT-修改用户 POST-保存用户

核心Filter：HiddenHttpMethodFilter

用法： 表单必须为method=post，使用隐藏域 _method=put

Rest风格请求路径，需要SpringBoot配置文件中手动开启

```yaml
spring:
 mvc:
   hiddenmethod:
     filter:
       enabled: true #开启页面表单的Rest功能
```



Rest原理（表单提交要使用REST风格的时候）

- 表单提交需要带上_method=PUT/DELETE
- 请求过来被HiddenHttpMethodFilter拦截，需要请求正常并且是POST请求
  - 获取_method的值
  - 兼容以下请求；PUT、DELETE、PATCH
  - 将原生request（post）以及请求方式使用包装模式HttpMethodRequestWrapper重新包装了一下，并重写了getMethod方法，返回值的是传入的真实请求方式的值
  - 过滤器链放行了wrapper（包装器），以后的方法调用getMethod实际上是调用HttpMethodRequestWrapper的getMethod

```java
@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
    throws ServletException, IOException {

    HttpServletRequest requestToUse = request;

    if ("POST".equals(request.getMethod()) && request.getAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE) == null) {
        String paramValue = request.getParameter(this.methodParam);
        if (StringUtils.hasLength(paramValue)) {
            String method = paramValue.toUpperCase(Locale.ENGLISH);
            if (ALLOWED_METHODS.contains(method)) {
                requestToUse = new HttpMethodRequestWrapper(request, method);
            }
        }
    }

    filterChain.doFilter(requestToUse, response); // 放行
}

private static class HttpMethodRequestWrapper extends HttpServletRequestWrapper {

    private final String method;

    public HttpMethodRequestWrapper(HttpServletRequest request, String method) {
        super(request);
        this.method = method;
    }

    @Override
    public String getMethod() {
        return this.method;
    }
}
```

使用客户端工具发送RestFul风格请求

- 如POSTMan直接发送Put、Delete等方式请求，进入过滤器后，没有进行请求方式的转换直接放行



扩展：如何把_method 这个名字换成我们自己喜欢的

```java
@Bean
@ConditionalOnMissingBean(HiddenHttpMethodFilter.class) //如果容器中没有HiddenHttpMethodFilter组件，就用原生的
@ConditionalOnProperty(prefix = "spring.mvc.hiddenmethod.filter", name = "enabled", matchIfMissing = false)
public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
    return new OrderedHiddenHttpMethodFilter();
}

//自定义一个hiddenHttpMethodFilter
@Bean
public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
    HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
    methodFilter.setMethodParam("_m");//把_method修改为_m
    return methodFilter;
}
```



##### 2、请求映射原理

SpringMVC功能分析都从 org.springframework.web.servlet.DispatcherServlet——>doDispatch()来看

![image-20230411200905568](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230411200905568.png)

DispatcherServlet中的doDispatch方法接收客户端发送来的请求，并做分发处理（第一步）

确定当前请求的处理程序（处理器方法）（第二步）

```java
// Determine handler for the current request.* 确定当前请求的处理程序（处理器方法）
mappedHandler = getHandler(processedRequest);

//HandlerMapping：处理器映射器，据请求的url、method等信息查找Handler，即控制器方法，帮忙寻找Handler的，将处理器方法封装为HandlerMethod
```

- `WebMvcAutoConfiguration`为容器中自动注册了五个的HandlerMapping，其中RequestMappingHandlerMapping就是映射被@RequestMapping注解标识的控制器方法和所接收的请求

- RequestMappingHandlerMapping：保存了所有@RequestMapping注解中的请求方式、映射地址以及和handler进行映射的规则
- 所有的请求映射都保存在HandlerMapping中
  - SpringBoot自动配置欢迎页的 WelcomePageHandlerMapping 。访问/能转发到index.html
  - SpringBoot自动配置了默认的 RequestMappingHandlerMapping
  - 请求进来，挨个尝试所有的HandlerMapping看是否有请求信息
    - 如果有就找到这个请求对应的handler
    - 如果没有就是下一个 HandlerMapping

  - 我们需要一些自定义的映射处理，我们也可以自定义HandlerMapping

![image-20230426151631697](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230426151631697.png)

![image-20230411203931722](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230411203931722.png)







#### 2.2.3.2、参数处理

##### 1、参数分类

（1）@PathVariable、@RequestHeader、@ModelAttribute、@RequestParam、@CookieValue、@RequestBody

```java
@RestController
public class ParameterTestController {
    //  car/2/owner/zhangsan?age=12&inters=basketball&inters=game
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String,Object> getCar(@PathVariable("id") Integer id,
                                     @PathVariable("username") String name,
                                     @PathVariable Map<String,String> pv,  // 可以将所有路径变量的值封装到map中
                                     @RequestHeader("User-Agent") String userAgent,
                                     @RequestHeader Map<String,String> header,  // 可以将所有请求头封装到map中
                                     @RequestParam("age") Integer age, 
                                     @RequestParam("inters") List<String> inters,
                                     @RequestParam Map<String,String> params,
                                     @CookieValue("_ga") String _ga,
                                     @CookieValue("_ga") Cookie cookie){

        Map<String,Object> map = new HashMap<>();

        //        map.put("id",id);
        //        map.put("name",name);
        //        map.put("pv",pv);
        //        map.put("userAgent",userAgent);
        //        map.put("headers",header);
        map.put("age",age);
        map.put("inters",inters);
        map.put("params",params);
        map.put("_ga",_ga);
        System.out.println(cookie.getName()+"===>"+cookie.getValue());
        return map;
    }

    @PostMapping("/save")
    public Map postMethod(@RequestBody String content){
        Map<String,Object> map = new HashMap<>();
        map.put("content",content);
        return map;
    }
}
```



（2）@RequestAttribute

```java
@Controller
public class TestController {
   @RequestMapping("/goto")
   public String goTo(HttpServletRequest request) {
       request.setAttribute("msg","成功");
       request.setAttribute("code",200);
       return "forward:/success";
   }
   @ResponseBody
   @RequestMapping("/success")
   public Map success(@RequestAttribute("msg") String msg,
                      @RequestAttribute("code") Integer code) {
       Map<String,Object> map = new HashMap<>();
       map.put("msg",msg);
       map.put("code",code);
       return map;
   }
}
```



（3）@MatrixVariable-矩阵变量

- 使用场景： 页面开发，cookie被禁用了，session里面的内容怎么使用

```
session.set("a",b)-->jsessionid 保存在cookie中--> cookie每次发送请求携带
每一个用户都有一个不同的sessionid，我们称之为jsessionid，jsessionid会被保存在cookie里面，我们用户在发送请求的时候都会携带cookie。因此禁用了cookie就不能获取里面保存的对象
   
解决方法：
   URL重写：/abc;jsessionid=xxx 把cookie的值使用矩阵变量的方式进行传递
```

```java
//1、语法： 请求路径：/cars/sell;low=34;brand=byd,audi,yd (另一种写法：/cars/sell;low=34;brand=byd;brand=audi)
//2、SpringBoot默认是禁用了矩阵变量的功能
   //手动开启，原理：对于路径的处理都是使用UrlPathHelper来进行解析。
   //属性removeSemicolonContent（移除分号内容）默认为true
//3、矩阵变量必须有url路径变量才能被解析
   @GetMapping("/cars/{path}")
   public Map carsSell(@MatrixVariable("low") Integer low,
                       @MatrixVariable("brand") List<String> brand,
                       @PathVariable("path") String path){
       Map<String,Object> map = new HashMap<>();

       map.put("low",low);
       map.put("brand",brand);
       map.put("path",path); 
       return map;
   }
map 具体内容为： {"path":"sell","low":34,"brand":["byd","audi","yd"]}

   // /boss/1;age=20/2;age=10
   @GetMapping("/boss/{bossId}/{empId}")
   public Map boss(@MatrixVariable(value = "age",pathVar = "bossId") Integer bossAge,
                   @MatrixVariable(value = "age",pathVar = "empId") Integer empAge){
       Map<String,Object> map = new HashMap<>();

       map.put("bossAge",bossAge);
       map.put("empAge",empAge);
       return map;
   }
}
```

- 矩阵变量可以指定为哪一层路径的矩阵变量
- 矩阵变量必须由Rest风格的路径映射来解析

- SpringBoot开启支持矩阵变量的方式

```java
第一种方法：自定义WebMvcConfigurer组件
@Bean
public WebMvcConfigurer getWebMvcConfigurer() {
   return new WebMvcConfigurer() {
       @Override
       public void configurePathMatch(PathMatchConfigurer configurer) {
           UrlPathHelper urlPathHelper = new UrlPathHelper();
           urlPathHelper.setRemoveSemicolonContent(false);
           configurer.setUrlPathHelper(urlPathHelper);
       }
   };
}

第二种方法：自定义配置类实现WebMvcConfigurer接口，并实现接口中的configurePathMatch()
@Override
public void configurePathMatch(PathMatchConfigurer configurer) {
   UrlPathHelper ur1PathHelper = new Ur1PathHelper();
   //不移除;后面的内容。矩阵变量功能就可以生效
   ur1PathHelper.setRemoveSemicolonContent(false) ;
   configurer.setUrlPathHelper(urlPathHelper) ;
}
```



（4）Servlet API

- WebRequest、ServletRequest、MultipartRequest、 HttpSession、javax.servlet.http.PushBuilder、Principal、InputStream、Reader、HttpMethod、Locale、TimeZone、ZoneId
- ServletRequestMethodArgumentResolver 处理以上类型的参数

```java
@Override
public boolean supportsParameter(MethodParameter parameter) {
    Class<?> paramType = parameter.getParameterType();
    return (WebRequest.class.isAssignableFrom(paramType) ||
            ServletRequest.class.isAssignableFrom(paramType) ||
            MultipartRequest.class.isAssignableFrom(paramType) ||
            HttpSession.class.isAssignableFrom(paramType) ||
            (pushBuilder != null && pushBuilder.isAssignableFrom(paramType)) ||
            Principal.class.isAssignableFrom(paramType) ||
            InputStream.class.isAssignableFrom(paramType) ||
            Reader.class.isAssignableFrom(paramType) ||
            HttpMethod.class == paramType ||
            Locale.class == paramType ||
            TimeZone.class == paramType ||
            ZoneId.class == paramType);
}
```



（5）复杂参数

- Map、Model（向map、model里面放入的数据会被放在request的请求域 request.setAttribute）、Errors/BindingResult、RedirectAttributes（ 重定向携带数据）、ServletResponse（response）、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder

```java
Map<String,Object> map,  Model model, HttpServletRequest request 都是可以给request域中放数据，
request.getAttribute();
```

- Map、Model类型的参数，会调用mavContainer.getModel()，返回BindingAwareModelMap（是Model 也是Map）,ModelAndViewContainer包含视图和数据

![image-20230507130826344](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230507130826344.png)

![image-20230501174258772](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501174258772.png)

![image-20230501174317181](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501174317181.png)



（6）自定义对象参数

- 可以自动类型转换与格式化，可以级联封装。

```java
/**
*     姓名： <input name="userName"/> <br/>
*     年龄： <input name="age"/> <br/>
*     生日： <input name="birth"/> <br/>
*     宠物姓名：<input name="pet.name"/><br/>
*     宠物年龄：<input name="pet.age"/>
*/
@Data
public class Person {
   
   private String userName;
   private Integer age;
   private Date birth;
   private Pet pet;
}

@Data
public class Pet {
   private String name;
   private String age;
}

/**
*数据绑定：页面提交的请求数据（GET、POST）都可以和对象属性进行绑定
*
*/
public class ParameterTestController {
    @PostMapping("/saveUser")
    public Person save(Person person) {
        return person;
    }
}
```

- 自定义类型参数封装POJO

  - ServletModelAttributeMethodProcessor，此参数处理器支持POJO类型参数解析

  - 判断是否为简单类型（对象参数不是简单类型）

**源码**

```java

public static boolean isSimpleValueType(Class<?> type) {
    return (Void.class != type && void.class != type &&
            (ClassUtils.isPrimitiveOrWrapper(type) ||
             Enum.class.isAssignableFrom(type) ||
             CharSequence.class.isAssignableFrom(type) ||
             Number.class.isAssignableFrom(type) ||
             Date.class.isAssignableFrom(type) ||
             Temporal.class.isAssignableFrom(type) ||
             URI.class == type ||
             URL.class == type ||
             Locale.class == type ||
             Class.class == type));
}
```

```java
@Override
@Nullable
public final Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
                                    NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {

    Assert.state(mavContainer != null, "ModelAttributeMethodProcessor requires ModelAndViewContainer");
    Assert.state(binderFactory != null, "ModelAttributeMethodProcessor requires WebDataBinderFactory");

    String name = ModelFactory.getNameForParameter(parameter);
    ModelAttribute ann = parameter.getParameterAnnotation(ModelAttribute.class);
    if (ann != null) {
        mavContainer.setBinding(name, ann.binding());
    }

    Object attribute = null;
    BindingResult bindingResult = null;

    if (mavContainer.containsAttribute(name)) {
        attribute = mavContainer.getModel().get(name);
    }
    else {
        // Create attribute instance
        try {
            attribute = createAttribute(name, parameter, binderFactory, webRequest);
        }
        catch (BindException ex) {
            if (isBindExceptionRequired(parameter)) {
                // No BindingResult parameter -> fail with BindException
                throw ex;
            }
            // Otherwise, expose null/empty value and associated BindingResult
            if (parameter.getParameterType() == Optional.class) {
                attribute = Optional.empty();
            }
            bindingResult = ex.getBindingResult();
        }
    }

    if (bindingResult == null) {
        // Bean property binding and validation;
        // skipped in case of binding failure on construction.
        // WebDataBinder：Web数据绑定器，将请求参数的值绑定到指定的JavaBean里面
        WebDataBinder binder = binderFactory.createBinder(webRequest, attribute, name);
        if (binder.getTarget() != null) {
            if (!mavContainer.isBindingDisabled(name)) {
                bindRequestParameters(binder, webRequest);
            }
            validateIfApplicable(binder, parameter);
            if (binder.getBindingResult().hasErrors() && isBindExceptionRequired(binder, parameter)) {
                throw new BindException(binder.getBindingResult());
            }
        }
        // Value type adaptation, also covering java.util.Optional
        if (!parameter.getParameterType().isInstance(attribute)) {
            attribute = binder.convertIfNecessary(binder.getTarget(), parameter.getParameterType(), parameter);
        }
        bindingResult = binder.getBindingResult();
    }

    // Add resolved attribute and BindingResult at the end of the model
    Map<String, Object> bindingResultModel = bindingResult.getModel();
    mavContainer.removeAttributes(bindingResultModel);
    mavContainer.addAllAttributes(bindingResultModel);

    return attribute;
}
```

![image-20230504151654541](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504151654541.png)

- WebDataBinder binder = binderFactory.createBinder(webRequest, attribute, name)
- WebDataBinder ：web数据绑定器，将请求参数的值绑定到指定的JavaBean里面
- 利用WebDataBinder里面的 Converters 将请求数据转成指定的数据类型。再次封装到JavaBean中
  - GenericConversionService：在设置每一个值的时候，找它里面的所有converter哪个converter可以将这个数据类型（request带来参数的字符串）转换到指定的类型（JavaBean -- Integer）

- 未来我们可以给WebDataBinder里面放自己的Converter

```java
private static final class StringToNumber<T extends Number> implements Converter<String, T>
```

- 自定义Converter

```java
//1、WebMvcConfigurer定制化SpringMVC的功能
@Bean
public WebMvcConfigurer webMvcConfigurer(){
    return new WebMvcConfigurer() {
        @Override
        /*
        	Add Converters and Formatters in addition to the ones registered by default.
        	default void addFormatters(FormatterRegistry registry) {}
		*/
        public void addFormatters(FormatterRegistry registry) {
            registry.addConverter(new Converter<String, Pet>() {
                @Override
                public Pet convert(String source) {
                    // 啊猫,3
                    if(!StringUtils.isEmpty(source)){
                        Pet pet = new Pet();
                        String[] split = source.split(",");
                        pet.setName(split[0]);
                        pet.setAge(Integer.parseInt(split[1]));
                        return pet;
                    }
                    return null;
                }
            });
        }
    };
}
```



##### 2、参数处理过程

（1）HandlerMapping中找到能处理请求的Handler（Controller.method()）例如RequestMappingHandler

（2）为当前Handler 找一个适配器 HandlerAdapter； RequestMappingHandlerAdapter（当前适配器支持处理此Handler），最终由这个适配器反射调用处理器方法

```java
// Determine handler adapter for the current request.确定当前请求的处理程序适配器
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```

![image-20230501135226188](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501135226188.png)

（3）适配器执行目标方法并确定方法参数的每一个值（通过反射）

- HandleAdapter
  - RequestMappingHandlerAdapter支持处理方法上标注@RequestMapping的控制器方法
  - HandleFunctionAdapter支持函数式编程

（4）执行目标方法

```java
// Actually invoke the handler.
//DispatcherServlet -- doDispatch
mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
```

```java
//RequestMappingHandlerAdapter.class中
//执行目标方法
mav = invokeHandlerMethod(request, response, handlerMethod); 

//真正执行目标方法
invocableMethod.invokeAndHandle(webRequest, mavContainer);
//在ServletInvocableHandlerMethod类中
Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs);
//获取方法的参数值
Object[] args = getMethodArgumentValues(request, mavContainer, providedArgs);
```



（5）参数解析器-HandlerMethodArgumentResolver（接口，有众多解析器实现类）

- 确定将要执行的目标方法的每一个参数的值是什么
- SpringMVC目标方法的参数列表能写多少种参数类型。取决于参数解析器有多少种

![image-20230501135135731](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501135135731.png)

![image-20230501135347358](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501135347358.png)

- 判断当前解析器支持哪种参数

- 支持就调用resolveArgument

- 确定目标方法每一个参数的值

```java
============InvocableHandlerMethod==========================
protected Object[] getMethodArgumentValues(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer,
           Object... providedArgs) throws Exception {

       MethodParameter[] parameters = getMethodParameters();
       if (ObjectUtils.isEmpty(parameters)) {
           return EMPTY_ARGS;
       }

       Object[] args = new Object[parameters.length];
       for (int i = 0; i < parameters.length; i++) {
           MethodParameter parameter = parameters[i];
           parameter.initParameterNameDiscovery(this.parameterNameDiscoverer);
           args[i] = findProvidedArgument(parameter, providedArgs);
           if (args[i] != null) {
               continue;
           }
           if (!this.resolvers.supportsParameter(parameter)) {
               throw new IllegalStateException(formatArgumentError(parameter, "No suitable resolver"));
           }
           try {
               args[i] = this.resolvers.resolveArgument(parameter, mavContainer, request, this.dataBinderFactory);
           }
           catch (Exception ex) {
               // Leave stack trace for later, exception may actually be resolved and handled...
               if (logger.isDebugEnabled()) {
                   String exMsg = ex.getMessage();
                   if (exMsg != null && !exMsg.contains(parameter.getExecutable().toGenericString())) {
                       logger.debug(formatArgumentError(parameter, exMsg));
                   }
               }
               throw ex;
           }
       }
       return args;
   }
```

- 逐个判断所有参数解析器哪个参数解析器支持解析这个参数

```java
@Nullable
private HandlerMethodArgumentResolver getArgumentResolver(MethodParameter parameter) {
    HandlerMethodArgumentResolver result = this.argumentResolverCache.get(parameter);
    if (result == null) {
        for (HandlerMethodArgumentResolver resolver : this.argumentResolvers) {
            if (resolver.supportsParameter(parameter)) {
                result = resolver;
                this.argumentResolverCache.put(parameter, result);
                break;
            }
        }
    }
    return result;
}
```

- 解析这个参数的值

```java
调用各自 HandlerMethodArgumentResolver 的 resolveArgument 方法即可
```



（6）返回值处理器

![image-20230501140021763](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501140021763.png)





（7）目标方法执行完成

- 将所有的数据都放在 **ModelAndViewContainer**：包含要去的页面地址View。还包含Model数据

![image-20230501143416753](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230501143416753.png)





（8）处理派发结果（渲染页面，处理请求域参数）

- **processDispatchResult**(processedRequest, response, mappedHandler, mv, dispatchException);
- renderMergedOutputModel(mergedModel, getRequestToExpose(request), response);

```java
InternalResourceView：
    @Override
    protected void renderMergedOutputModel(
    Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws Exception {

    // Expose the model object as request attributes.
    exposeModelAsRequestAttributes(model, request);

    // Expose helpers as request attributes, if any.
    exposeHelpers(request);

    // Determine the path for the request dispatcher.
    String dispatcherPath = prepareForRendering(request, response);

    // Obtain a RequestDispatcher for the target resource (typically a JSP).
    RequestDispatcher rd = getRequestDispatcher(request, dispatcherPath);
    if (rd == null) {
        throw new ServletException("Could not get RequestDispatcher for [" + getUrl() +
                                   "]: Check that the corresponding file exists within your web application archive!");
    }

    // If already included or response already committed, perform include, else forward.
    if (useInclude(request, response)) {
        response.setContentType(getContentType());
        if (logger.isDebugEnabled()) {
            logger.debug("Including [" + getUrl() + "]");
        }
        rd.include(request, response);
    }

    else {
        // Note: The forwarded resource is supposed to determine the content type itself.
        if (logger.isDebugEnabled()) {
            logger.debug("Forwarding to [" + getUrl() + "]");
        }
        rd.forward(request, response);
    }
}
```

```java
暴露模型作为请求域属性
// Expose the model object as request attributes.
exposeModelAsRequestAttributes(model, request);
```

```java
protected void exposeModelAsRequestAttributes(Map<String, Object> model,
                                              HttpServletRequest request) throws Exception {
    //model中的所有数据遍历挨个放在请求域中
    model.forEach((name, value) -> {
        if (value != null) {
            request.setAttribute(name, value);
        }
        else {
            request.removeAttribute(name);
        }
    });
}
```



### 2.2.4、数据响应与内容协商

![image-20230504154715633](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504154715633.png)

#### 2.2.4.1、响应JSON

##### 1、jackson.jar+@ResponseBody

**给前端返回JSON数据**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

web场景自动引入了json场景
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-json</artifactId>
    <version>2.3.4.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

**返回值处理器**

![image-20230504162841248](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504162841248.png)

```java
try {
    this.returnValueHandlers.handleReturnValue(
        returnValue, getReturnValueType(returnValue), mavContainer, webRequest);
}
```

```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception {
    HandlerMethodReturnValueHandler handler = this.selectHandler(returnValue, returnType);
    if (handler == null) {
        throw new IllegalArgumentException("Unknown return value type: " + returnType.getParameterType().getName());
    } else {
        handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);
    }
}
```

**返回值处理器原理**

- 返回值处理器判断是否支持这种类型返回值，supportsReturnType
- 返回值处理器调用handleReturnValue进行处理
- RequestResponseBodyMethodProcessor可以处理返回值标注了@ResponseBody注解的控制器方法的返回值
  - 利用MessageConverters进行处理，将数据写为json
    - 内容协商（浏览器默认会以请求头的方式告诉服务器它能接受什么样的内容类型）
    - 服务器最终根据自身的能力，决定能响应什么样内容类型的数据
    - SpringMVC会逐个遍历所有容器底层的HttpMessageConverter，看谁能处理
      - 得到MappingJackson2HttpMessageConverter可以将对象写为json
      - 利用MappingJackson2HttpMessageConverter将对象转为json再写出去

![image-20230504163001726](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504163001726.png)

**源码**

```java
RequestResponseBodyMethodProcessor中的方法
    @Override
    public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
                                  ModelAndViewContainer mavContainer, NativeWebRequest webRequest)
    throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {

    mavContainer.setRequestHandled(true);
    ServletServerHttpRequest inputMessage = createInputMessage(webRequest);
    ServletServerHttpResponse outputMessage = createOutputMessage(webRequest);


    // Try even with null return value. ResponseBodyAdvice could get involved.
    // 使用消息转换器进行写出操作
    writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage);
}
```

```java
MediaType selectedMediaType = null;
MediaType contentType = outputMessage.getHeaders().getContentType();
boolean isContentTypePreset = contentType != null && contentType.isConcrete();
if (isContentTypePreset) {
    if (logger.isDebugEnabled()) {
        logger.debug("Found 'Content-Type:" + contentType + "' in response");
    }
    selectedMediaType = contentType;
}
else {
    HttpServletRequest request = inputMessage.getServletRequest();
    List<MediaType> acceptableTypes = getAcceptableMediaTypes(request);
    List<MediaType> producibleTypes = getProducibleMediaTypes(request, valueType, targetType);

    if (body != null && producibleTypes.isEmpty()) {
        throw new HttpMessageNotWritableException(
            "No converter found for return value of type: " + valueType);
    }
    List<MediaType> mediaTypesToUse = new ArrayList<>();
    for (MediaType requestedType : acceptableTypes) {
        for (MediaType producibleType : producibleTypes) {
            if (requestedType.isCompatibleWith(producibleType)) {
                mediaTypesToUse.add(getMostSpecificMediaType(requestedType, producibleType));
            }
        }
    }
    if (mediaTypesToUse.isEmpty()) {
        if (body != null) {
            throw new HttpMediaTypeNotAcceptableException(producibleTypes);
        }
        if (logger.isDebugEnabled()) {
            logger.debug("No match for " + acceptableTypes + ", supported: " + producibleTypes);
        }
        return;
    }

    MediaType.sortBySpecificityAndQuality(mediaTypesToUse);

    for (MediaType mediaType : mediaTypesToUse) {
        if (mediaType.isConcrete()) {
            selectedMediaType = mediaType;
            break;
        }
        else if (mediaType.isPresentIn(ALL_APPLICATION_MEDIA_TYPES)) {
            selectedMediaType = MediaType.APPLICATION_OCTET_STREAM;
            break;
        }
    }

    if (logger.isDebugEnabled()) {
        logger.debug("Using '" + selectedMediaType + "', given " +
                     acceptableTypes + " and supported " + producibleTypes);
    }
}

if (selectedMediaType != null) {
    selectedMediaType = selectedMediaType.removeQualityValue();
    // 遍历所有的消息转换器
    for (HttpMessageConverter<?> converter : this.messageConverters) {
        GenericHttpMessageConverter genericConverter = (converter instanceof GenericHttpMessageConverter ?
                                                        (GenericHttpMessageConverter<?>) converter : null);
        if (genericConverter != null ?
            ((GenericHttpMessageConverter) converter).canWrite(targetType, valueType, selectedMediaType) :
            converter.canWrite(valueType, selectedMediaType)) {
            body = getAdvice().beforeBodyWrite(body, returnType, selectedMediaType,
                                               (Class<? extends HttpMessageConverter<?>>) converter.getClass(),
                                               inputMessage, outputMessage);
            if (body != null) {
                Object theBody = body;
                LogFormatUtils.traceDebug(logger, traceOn ->
                                          "Writing [" + LogFormatUtils.formatValue(theBody, !traceOn) + "]");
                addContentDispositionHeader(inputMessage, outputMessage);
                if (genericConverter != null) {
                    genericConverter.write(body, targetType, selectedMediaType, outputMessage);
                }
                else {
                    ((HttpMessageConverter) converter).write(body, selectedMediaType, outputMessage);
                }
            }
            else {
                if (logger.isDebugEnabled()) {
                    logger.debug("Nothing to write: null body");
                }
            }
            return;
        }
    }
}
```



##### 2、SpringMVC到底支持哪些返回值

```
ModelAndView
Model
View
ResponseEntity
ResponseBodyEmitter
StreamingResponseBody
HttpEntity
HttpHeaders
Callable
DeferredResult
ListenableFuture
CompletionStage
WebAsyncTask
有 @ModelAttribute 且为对象类型的
@ResponseBody 注解 ---> 使用RequestResponseBodyMethodProcessor此处理器来处理
```



##### 3、HTTPMessageConverter原理

**MessageConverter规范**

![image-20230504193708579](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504193708579.png)

- HTTPMessageConverter：看是否支持将此Class类型的对象，转换为MediaType类型的数据

- canRead()：判断能否读取MediaType类型的内容，例如：浏览器发送给服务端JSON数据，控制器方法参数使用@RequestBody注解标注的参数接收，Converter会将JSON数据重新转换对象数据，这样就是读

- canWrite()：判断能否写出MediaType类型的内容，例如：服务端控制器方法标注@Response注解，返回值为对象数据，而SpringMVC底层默认操作普通对象数据的组件为MappingJackson2HttpMessageConverter，此Converter会将对象数据转换为JSON数据，这样就是写



**SpringMVC自带的默认MessageConverter**

![image-20230504194627588](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504194627588.png)

- 0：只支持Byte类型的返回值
- 1：只支持String类型的返回值
- 3：只支持Resource类型的返回值
- 4：只支持ResourceRegion类型的返回值
- 5：支持DOMSource.class、SAXSource.class、StAXSource.class、StreamSource.class、Source.class类型的返回值
- 6 - 只支持MultiValueMap类型的返回值
- 7 - true，支持所有
- 8 - true，支持所有
- 9 - 支持注解方式xml处理的

​	

最终由MappingJackson2HttpMessageConverter把对象转为JSON（利用底层jackson的objectMapper转换的）

![image-20230504195745644](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504195745644.png)





#### 2.2.4.2、内容协商

根据客户端接收能力的不同，返回不同媒体类型的数据

##### 1、引入XML依赖

```xml
<dependency>
   <groupId>com.fasterxml.jackson.dataformat</groupId>
   <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```



##### 2、psotman分别测试返回json和xml

只需要改变请求头中Accept字段。Http协议中规定的，告诉服务器本客户端可以接收的数据类型

![image-20230504202718723](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230504202718723.png)



##### 3、开启浏览器参数方式内容协商功能

为了方便内容协商，开启基于请求参数的内容协商功能

```yaml
spring:
 mvc:
   contentnegotiation:
     favor-parameter: true  //开启请求参数内容协商功能
```

```
http://localhost:8080/person?format=json
http://localhost:8080/person?format=xml
```

![image-20230507152910619](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230507152910619.png)

![image-20230507130716347](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230507130716347.png)



确定客户端接收什么样的内容类型

- Parameter策略优先确定是要返回json/xml数据（获取请求头中的format的值）

```java
protected String getMediaTypeKey(NativeWebRequest request) {
    return request.getParameter(this.getParameterName());
}
```

- 最终进行内容协商返回给客户端json/xml即可。



##### 4、内容协商原理

判断当前响应头中是否已经有确定的媒体类型，MediaType

获取客户端（PostMan、浏览器）支持接收的内容类型（获取客户端Accept请求头字段）

- contentNegotiationManager：内容协商管理器，默认使用基于请求头的策略
- HeaderContentNegotiationStrategy：基于请求头的内容协商策略，通过请求头确定客户端可以接收的内容类型

**源码**

```java
String[] headerValueArray = request.getHeaderValues("Accept");	
```

```java
AbstractMessageConverterMethodProcessor.class

HttpServletRequest request = inputMessage.getServletRequest();
List<MediaType> acceptableTypes = getAcceptableMediaTypes(request);// 获取客户端支持接收的内容类型
List<MediaType> producibleTypes = getProducibleMediaTypes(request, valueType, targetType); // 获取服务端能够
```

遍历循环所有当前系统的 MessageConverter，看哪个Converter支持操作这个对象（例如：Person），将此Converter支持的媒体类型统计出来

**源码**

```java
for (HttpMessageConverter<?> converter : this.messageConverters) {
    if (converter instanceof GenericHttpMessageConverter && targetType != null) {
        if (((GenericHttpMessageConverter<?>) converter).canWrite(targetType, valueClass, null)) {
            result.addAll(converter.getSupportedMediaTypes());// 拿到converter支持的媒体类型
        }
    }
    else if (converter.canWrite(valueClass, null)) {
        result.addAll(converter.getSupportedMediaTypes());
    }
}
```

客户端需要【application/xml】（目标类型），服务端能力【10种，包括json，xml】

进行内容协商的最佳匹配

**源码**

```java
List<MediaType> mediaTypesToUse = new ArrayList<>();
for (MediaType requestedType : acceptableTypes) {
    for (MediaType producibleType : producibleTypes) {
        if (requestedType.isCompatibleWith(producibleType)) {
            mediaTypesToUse.add(getMostSpecificMediaType(requestedType, producibleType));
        }
    }
}
```

获取支持将对象转为已选中的媒体类型的Converter，调用支持将对象转为最佳匹配媒体类型的Converter进行转换

**源码**

```java
if (selectedMediaType != null) {
    selectedMediaType = selectedMediaType.removeQualityValue();
    // 循环判断所有的MessageConverter判断哪个MessageConverter能够处理selectedMediaType内容类型
    for (HttpMessageConverter<?> converter : this.messageConverters) {
        GenericHttpMessageConverter genericConverter = (converter instanceof GenericHttpMessageConverter ?
                                                        (GenericHttpMessageConverter<?>) converter : null);
        if (genericConverter != null ?
            ((GenericHttpMessageConverter) converter).canWrite(targetType, valueType, selectedMediaType) :
            converter.canWrite(valueType, selectedMediaType)) {
            body = getAdvice().beforeBodyWrite(body, returnType, selectedMediaType,
                                               (Class<? extends HttpMessageConverter<?>>) converter.getClass(),
                                               inputMessage, outputMessage);
            if (body != null) {
                Object theBody = body;
                LogFormatUtils.traceDebug(logger, traceOn ->
                                          "Writing [" + LogFormatUtils.formatValue(theBody, !traceOn) + "]");
                addContentDispositionHeader(inputMessage, outputMessage);
                if (genericConverter != null) {
                    genericConverter.write(body, targetType, selectedMediaType, outputMessage);
                }
                else {
                    ((HttpMessageConverter) converter).write(body, selectedMediaType, outputMessage);
                }
            }
            else {
                if (logger.isDebugEnabled()) {
                    logger.debug("Nothing to write: null body");
                }
            }
            return;
        }
    }
}
```



> 导入了jackson处理xml的包，xml的converter就会自动进来
>
> **源码**
>
> ```java
> WebMvcConfigurationSupport.class中
> 
> jackson2XmlPresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.xml.XmlMapper", classLoader);
> 
> if (jackson2XmlPresent) {
>     Jackson2ObjectMapperBuilder builder = Jackson2ObjectMapperBuilder.xml();
>     if (this.applicationContext != null) {
>         builder.applicationContext(this.applicationContext);
>     }
>     messageConverters.add(new MappingJackson2XmlHttpMessageConverter(builder.build()));
> }
> ```



##### 5、自定义MessageConverter

实现多协议数据兼容：JSON、XML、X-guigu

标注@ResponseBody注解的控制器方法结果是响应数据出去，调用RequestResponseBodyMethodProcessor处理

- Processor处理方法返回值，通过调用MessageConverter处理

- 所有MessageConverter合起来可以支持各种媒体类型数据的操作（读、写）
- 内容协商找到最终的MessageConverer

场景步骤

```
1、浏览器发请求直接返回xml   【application/xml】   jacksonXmlConverter
2、如果是ajax请求，返回json  【applicatino/json】 jacksonJsonConverter
3、如果硅谷app发请求，返回自定义协议数据 【application/x-guigu】  xxxxConverter
```

- 添加自定义的MessageConverter到系统底层
- 系统底层就会统计出所有MessageConverter能操作哪些媒体类型
- 客户端内容协商

对SpringMVC新增自定义组件，只有一个入口，给容器中添加WebMvcConfigurer

```java
public class GuiguMessageConverter implements HttpMessageConverter<Person> {
    @Override
    public boolean canRead(Class<?> clazz, MediaType mediaType) {
        return false;
    }

    @Override
    public boolean canWrite(Class<?> clazz, MediaType mediaType) {
        return clazz.isAssignableFrom(Person.class);
    }

    /**
    * 服务器要统计所有MessageConverter都能写出哪些内容类型
    * applicatin/x-guigu
    */
    @Override
    public List<MediaType> getSupportedMediaTypes() {
        return MediaType.parseMediaTypes("application/x-guigu");
    }

    @Override
    public Person read(Class<? extends Person> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException {
        return null;
    }

    @Override
    public void write(Person person, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
        //自定义协议数据的写出
        String data = person.getUserName() + ";" + person.getAge() + ";" + person.getBirth();

        //写出去
        OutputStream body = outputMessage.getBody();
        body.write(data.getBytes());
    }
}
```

```java
// WebMvcConfigurer 定制化SpringMVC的功能
@Bean
public WebMvcConfigurer getWebMvcConfigurer() {
   return new WebMvcConfigurer() {
       /**
       * 扩展MessageConverters，WebMvcConfigurer中还有一个configureMessageConverters，实现此方法会覆盖掉默认的MessageConverter
       */
       @Override
       public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
           converters.add(new GuiguMessageConverter);
       }
   };
}
```

原有的浏览器参数方式内容协商功能

![image-20230507164426281](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230507164426281.png)



新增浏览器参数方式内容协商功能

```java
@Bean
public WebMvcConfigurer getWebMvcConfigurer() {
    return new WebMvcConfigurer() {
        /**
        * 自定义内容协商策略
        * @param configurer
        */
        @Override
        public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
            //Map<String, MediaType> mediaTypes
            Map<String, MediaType> mediaTypes = new HashMap<>();
            mediaTypes.put("json",MediaType.APPLICATION_JSON);
            mediaTypes.put("xml",MediaType.APPLICATION_XML);
            mediaTypes.put("gg",MediaType.parseMediaType("application/x-guigu"));
            //指定支持解析哪些参数对应的哪些媒体类型
            ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypes);
            configurer.strategies(Arrays.asList(parameterStrategy));
        }
    };
}
```

![image-20230507164535899](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230507164535899.png)

因为自定义参数方式的内容协商，导致基于请求头的内容协商失效了

```java
@Bean
public WebMvcConfigurer getWebMvcConfigurer() {

    return new WebMvcConfigurer() {
        /**
        * 自定义内容协商策略
        * @param configurer
        */
        @Override
        public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
            //Map<String, MediaType> mediaTypes
            Map<String, MediaType> mediaTypes = new HashMap<>();
            mediaTypes.put("json",MediaType.APPLICATION_JSON);
            mediaTypes.put("xml",MediaType.APPLICATION_XML);
            mediaTypes.put("gg",MediaType.parseMediaType("application/x-guigu"));
            //指定支持解析哪些参数对应的哪些媒体类型
            ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypes);

            HeaderContentNegotiationStrategy headerContentNegotiationStrategy = new HeaderContentNegotiationStrategy();

            configurer.strategies(Arrays.asList(parameterStrategy,headerContentNegotiationStrategy));
        }
    };
}
```

> 注：
>
> 有可能我们添加的自定义的功能会覆盖默认很多功能，导致一些默认的功能失效。



### 2.2.5、视图解析与模板引擎

视图解析：SpringBoot默认不支持JSP，需要引入第三方模板引擎技术实现页面渲染

#### 2.2.5.1、视图解析

![image-20230507182921920](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230507182921920.png)



#### 2.2.5.2、模板引擎

##### 1、thymeleaf简介

Thymeleaf is a modern server-side Java template engine for both web and standalone environments, capable of processing HTML, XML, JavaScript, CSS and even plain text

Thymeleaf是一个现代的服务器端Java模板引擎，用于web和独立环境，能够处理HTML, XML, JavaScript, CSS，甚至纯文本



##### 2、基本语法

表达式

![image-20230510175649316](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230510175649316.png)

字面量

- 文本值：'one text' 、'Another one!' 、…  数字：0 、34 、3.0 、12.3 、… 布尔值：true 、false
- 空值：null
- 变量：one，two，.... 变量不能有空格



文本操作

- 字符串拼接：+
- 变量替换: |The name is ${name}|



数学运算

- 运算符: +、- 、* 、/ 、%



布尔运算

- 运算符：and、or
- 一元运算：! 、not



比较运算

- 比较：>、< 、>=、<=（gt、lt 、ge、le）等式：==、!=（eq、ne）



条件运算

- If-then: (if) ? (then)
- If-then-else: (if) ? (then) : (else)
- Default: (value) ?: (defaultvalue)



特殊操作

- 无操作：_



##### 3、设置属性值：th:attr

设置单个值

```html
<form action="subscribe.html" th:attr="action=@{/subscribe}">
    <fieldset>
        <input type="text" name="email" />
        <input type="submit" value="Subscribe!" th:attr="value=#{subscribe.submit}"/>
    </fieldset>
</form>
```

设置多个值

```html
<img src="../../images/gtvglogo.png"  th:attr="src=@{/images/gtvglogo.png},title=#{logo},alt=#{logo}" />
```

以上两种的代替写法：th:xxx

```html
<input type="submit" value="Subscribe!" th:value="#{subscribe.submit}"/>
<form action="subscribe.html" th:action="@{/subscribe}">
```

所有h5兼容的标签写法

- https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#setting-value-to-specific-attributes



##### 4、迭代

```html
<tr th:each="prod : ${prods}">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
```

```html
<tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
```



##### 5、条件运算

```html
<a href="comments.html"
   th:href="@{/product/comments(prodId=${prod.id})}"
   th:if="${not #lists.isEmpty(prod.comments)}">view</a>
```

```html
<div th:switch="${user.role}">
    <p th:case="'admin'">User is an administrator</p>
    <p th:case="#{roles.manager}">User is a manager</p>
    <p th:case="*">User is some other thing</p>
</div>
```



##### 6、属性优先级

![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)





#### 2.2.5.3、thymeleaf使用

##### 1、引入start

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```



##### 2、SpringBoot自动配置好了Thymeleaf

```java
@AutoConfiguration(after = { WebMvcAutoConfiguration.class, WebFluxAutoConfiguration.class })
@EnableConfigurationProperties(ThymeleafProperties.class)
@ConditionalOnClass({ TemplateMode.class, SpringTemplateEngine.class })
@Import({ TemplateEngineConfigurations.ReactiveTemplateEngineConfiguration.class,
         TemplateEngineConfigurations.DefaultTemplateEngineConfiguration.class })
public class ThymeleafAutoConfiguration {
```

自动配置好的策略

- 所有thymeleaf的配置值都在ThymeleafProperties
- 配置好了 SpringTemplateEngine
- 配好了 ThymeleafViewResolver
- 我们只需要直接开发页面

```java
public static final String DEFAULT_PREFIX = "classpath:/templates/";//前缀

public static final String DEFAULT_SUFFIX = ".html";//后缀
```



##### 3、页面开发

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <h1 th:text="${msg}">哈哈</h1>
        <h2>
            <a href="www.atguigu.com" th:href="${link}">去百度1</a>
            <a href="www.atguigu.com" th:href="@{/link}">去百度2</a>
        </h2>
    </body>
</html>
```

```java
@Controller
public class ViesTestController {

    @GetMapping("/atguigu")
    public String atguigu(Model model) {
        model.addAttribute("msg","你好，尚硅谷");
        model.addAttribute("link","http://www.baidu.com");
        return "success";
    }
}
```

```yaml
server:
  port: 8080
  servlet:
    context-path: /world  # 服务的前置路径，在项目路径下加上world才能访问
```

![image-20230510210424969](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230510210424969.png)

![image-20230510210816315](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230510210816315.png)



##### 4、构建后台管理系统

https://www.yuque.com/atguigu/springboot/vgzmgh#PcDub



#### 2.2.5.4、后台管理系统小项目

##### 1、表单

```html
<form class="form-signin" action="main.html" method="post" th:action="@{/login}">
</form>
```



##### 2、后台

```java
/**
* 登录页
* 发送get请求访问项目根路径或/login进入登录页面
* @return
*/
@GetMapping(value = {"/", "/login"})
public String loginPage() {
    return "login";
}

/**
* 登录页面中的表单提交到这个路径，登录可进入主页面
* @return
*/
@PostMapping("/login")  
public String main(String userName,String passWord){
    return "main";
}
```



##### 3、解决表单重复提交问题

每次刷新页面，表单都会重复提交，因为是转发到main页面，这样的话浏览器状态栏一直保持的是请求登录地址，每次刷新相当于重新提交表单登录

![image-20230514145320112](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230514145320112.png)

使用重定向解决表单重复提交问题

```java
@PostMapping("/login")
public String main(String userName,String passWord){

    // 登录成功，重定向到main页面
    return "redirect:main.html";
}

/**
* 访问main页面
* @return
*/
@GetMapping("/main.html")
public String mainPage(){
    return "main";
}
```

![image-20230514145939497](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230514145939497.png)





##### 4、解决未登录访问主页面问题

```java
/**
* 访问main页面
*
* @return
*/
@GetMapping("/main.html")
public String mainPage(HttpSession session,Model model) {
    // 判断是否是登录状态，如果没有登录，那么不能访问主页面，重新进入登录页面
    // 这一部分应该使用拦截器来实现
    User loginUser = (User) session.getAttribute("loginUser");
    if (loginUser != null) {
        return "main";
    } else {
        model.addAttribute("msg","请重新登录");
        return "login";
    }

}
```

```html
<label style="color: red" th:text="${msg}"></label>
```



##### 5、姓名显示问题

使用Thymeleaf的行内写法

```html
<a href="#" class="btn btn-default dropdown-toggle" data-toggle="dropdown">
    <img src="images/photos/user-avatar.png" alt="" />
    [[${session.loginUser.userName}]]
    <span class="caret"></span>
</a>
```



#### 2.2.5.5、视图解析原理

能处理返回值是视图地址的返回值处理器为ViewNameMethodReturnValueHandler

目标方法处理的过程中，所有数据都会被放在ModelAndViewContainer里面，包括数据和视图地址

如果处理器方法的参数是一个自定义类型对象（从请求参数中确定的），那也会把它放在 ModelAndViewContainer

任何目标方法执行完成以后都会返回ModelAndView（数据和视图地址）

processDispatchResult 处理派发结果（决定页面该如何响应）

- render(mv, request, response)：进行页面渲染

  - 根据控制器方法的String返回值（viewName）得到View对象（定义了页面的渲染逻辑）
  - 所有的视图解析器尝试能否解析当前控制器方法返回值得到View对象

  ![image-20230514222201469](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230514222201469.png)

  - 得到RedirectView（redirect:/main.html），在ThymeleafViewResolver中判断viewName是否是以redirect开始，是的话就会直接创建RedirectView对象
  - ContentNegotiationViewResolver里面包含了下面所有的视图解析器，内部还是利用视图解析器得到视图对象

  ![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230514222623248.png)

  - view.render(mv.getModelInternal(), request, response)：视图对象调用自定义的reder方法进行页面渲染工作
    - RedirectView如何渲染【重定向到一个页面】
      - 获取目标URL地址（/main.html）	
      - response.sendRedirect(encodedURL)；// servlet原生重定向方法



> 注：
>
> 视图解析：
>
> - 返回值以forward:开始：new InternalResourceView(forwardUrl)；底层是转发
> - 返回值以redirect:开始：new RedirectView()；底层是重定向
> - 返回值是普通字符串：new ThymeleafView()；





### 2.2.6、拦截器

#### 2.2.6.1、`HandlerInterceptor`接口

![image-20230516151813151](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230516151813151.png)

- `preHandle`：目标方法执行前执行

- `postHandle`：目标方法执行后执行

- `afterCompletion`：页面渲染完成之后执行




#### 2.2.6.2、登录检查拦截器

```java
/**
 * 登录检查
 *
 * @author zhangchengwei
 * @create 2023-05-16 15:22
 */
public class LoginInterceptor implements HandlerInterceptor {

    /**
     * 目标方法执行之前
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 登录检查逻辑
        HttpSession session = request.getSession();

        User loginUser = (User) session.getAttribute("loginUser");

        if (loginUser != null) {
            // 放行
            return true;
        }

        // 拦截，未登录，跳转到登录页
        //session.setAttribute("msg","请先登录"); 页面拿不出msg，因为页面是从request域中拿数据，需要从session域中取才可取出
        //response.sendRedirect("/");
        request.setAttribute("msg","请先登录");
        request.getRequestDispatcher("/").forward(request,response);
        return false;

    }

    /**
     * 目标方法执行之后
     * @param request
     * @param response
     * @param handler
     * @param modelAndView
     * @throws Exception
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    /**
     * 页面渲染以后
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @throws Exception
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```



#### 2.2.6.3、配置拦截器

```java
/**
 * @author zhangchengwei
 * @create 2023-05-16 15:30
 *
 * 定制化SpringMVC功能，实现WebMvcConfigurer接口
 */
@Configuration
public class AdminWebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
            .addPathPatterns("/**") // 所有请求都会被拦截，包括静态资源
            .excludePathPatterns("/","/login","/css/**","fonts/**","/images/**","/js/**"); // 放行的请求
    }
}
```



#### 2.2.6.4、拦截器原理	

- 根据当前请求，找到`HandlerExcutionChain`【可以处理请求的`Handler`以及`Handler`的所有拦截器】。

- 先顺序执行所有拦截器的`preHandle`方法 （一个控制器方法可以有多个拦截器）。

  - 如果当前拦截器`preHandle`返回true，则执行下一个拦截器的`preHandle`。
  - 如果当前拦截器返回为`false`，直接跳出不执行目标方法，并倒序执行所有已经执行了的拦截器的`afterCompletion`。
- 如果任何一个拦截器的`preHandle`返回`false`，直接跳出不执行目标方法。
- 所有拦截器都返回`true`，则执行目标方法，目标方法执行完，倒序执行所有拦截器的`postHandle`方法。
- 前面的步骤有任何异常都会立即倒序执行已经执行了的拦截器的`afterCompletion`，页面成功渲染完成以后，也会倒序执行`afterCompletion`（已经执行指的是拦截器的`preHandle`已经执行）。

![image-20230516162053170](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230516162053170.png)

![image-20230516162216807](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230516162216807.png)





### 2.2.7、文件上传

#### 2.2.7.1、页面表单

```html
<form role="form" th:action="@{/upload}" method="post" enctype="multipart/form-data">
    <div class="form-group">
        <label for="exampleInputEmail1">邮箱</label>
        <input type="email" name="email" class="form-control" id="exampleInputEmail1" placeholder="Enter email">
    </div>
    <div class="form-group">
        <label for="exampleInputPassword1">名字</label>
        <input type="username" name="username" class="form-control" id="exampleInputPassword1" placeholder="Password">
    </div>
    <div class="form-group">
        <label for="exampleInputFile">头像</label>
        <input type="file" name="headerImg" id="exampleInputFile"> <!--单文件上传-->
    </div>
    <div class="form-group">
        <label for="exampleInputFile">生活照</label>
        <input type="file" name="photos" multiple> <!--多文件上传-->
    </div>
    <div class="checkbox">
        <label>
            <input type="checkbox"> Check me out
        </label>
    </div>
    <button type="submit" class="btn btn-primary">提交</button>
</form>
```



#### 2.2.7.2、文件上传代码

```java
@PostMapping("upload")
public String upload(@RequestParam("email") String email,
                     @RequestParam("username") String username,
                     @RequestPart("headerImg") MultipartFile headerImg,
                     @RequestPart("photos") MultipartFile[] photos) throws IOException {

    log.info("上传的信息:email={},username={},headerImage={},photos={}",email,username,headerImg.getSize(),photos.length);
    if (!headerImg.isEmpty()) {
        String originalFilename = headerImg.getOriginalFilename();
        headerImg.transferTo(new File("D:\\Others\\File" + originalFilename));
    }

    if (photos.length > 0) {
        for (MultipartFile photo : photos) {
            if (!photo.isEmpty()) {
                String originalFilename = photo.getOriginalFilename();
                photo.transferTo(new File("D:\\Others\\File" + originalFilename));
            }
        }
    }
    return "main";
}
```

> 注：
>
> - 文件上传自动配置类MultipartAutoConfiguration，单个文件上传最大默认为1MB，整体文件最大默认为10MB



#### 2.2.7.3、文件上传自动配置原理

- MultipartAutoConfiguration 自动配置文件上传规则

- 自动配置好了StandardServletMultipartResolver【文件上传解析器】

- 原理步骤：

  - 请求进来使用文件上传解析器判断（isMultipart()） 请求的内容类型是否为"multipart/"并封装（resolveMultipart(request)，返回StandardMultipartHttpServletRequest）文件上传请求


```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    processedRequest = this.checkMultipart(request);
}

protected HttpServletRequest checkMultipart(HttpServletRequest request) throws MultipartException {
    if (this.multipartResolver != null && this.multipartResolver.isMultipart(request)) {
        if (WebUtils.getNativeRequest(request, MultipartHttpServletRequest.class) != null) {
            if (DispatcherType.REQUEST.equals(request.getDispatcherType())) {
                this.logger.trace("Request already resolved to MultipartHttpServletRequest, e.g. by MultipartFilter");
            }
        } else if (this.hasMultipartException(request)) {
            this.logger.debug("Multipart resolution previously failed for current request - skipping re-resolution for 										   undisturbed error rendering");
        } else {
            try {
                return this.multipartResolver.resolveMultipart(request);
            } catch (MultipartException var3) {
                if (request.getAttribute("javax.servlet.error.exception") == null) {
                    throw var3;
                }
            }

            this.logger.debug("Multipart resolution failed for error dispatch", var3);
        }
    }

    return request;
}


public MultipartHttpServletRequest resolveMultipart(HttpServletRequest request) throws MultipartException {
    return new StandardMultipartHttpServletRequest(request, this.resolveLazily);
}
```

- 参数解析器来解析请求中的文件内容并封装为MultipartFile

![image-20230516214614548](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230516214614548.png)

- 将request中文件信息封装为一个Map
- 工具类FileCopyUtils，实现文件流的拷贝

![image-20230516180250142](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230516180250142.png)





### 2.2.8、异常处理

#### 2.2.8.1、默认规则

默认情况下，Spring Boot提供/error处理所有错误的映射

对于机器客户端（例如Postman），它将生成JSON响应，其中包含错误，HTTP状态和异常消息的详细信息。对于浏览器客户端，响应一个“ whitelabel”错误视图，以HTML格式呈现相同的数据

![image-20230517133439001](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230517133439001.png)

要对其进行自定义，添加View解析为error

要完全替换默认行为，可以实现 ErrorController 并注册该类型的Bean定义，或添加ErrorAttributes类型的组件以使用现有机制但替换其内容。

error/下的4xx，5xx页面会被自动解析

![image-20230517140108711](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230517140108711.png)

例如，要将404映射到静态HTML文件，你的目录结构将如下所示：

```xml
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- public/
             +- error/
             |   +- 404.html
             +- <other public assets>
```

使用FreeMarker模板映射所有5xx错误，你的目录结构如下:

```xml
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- templates/
             +- error/
             |   +- 5xx.ftlh
             +- <other templates>
```



#### 2.8.8.2、定制错误处理逻辑

（1）自定义错误页

- error/404.html error/5xx.html；有精确的错误状态码页面就匹配精确，没有就找 4xx.html；如果都没有就触发白页



（2）@ControllerAdvice+@ExceptionHandler处理全局异常；底层是 ExceptionHandlerExceptionResolver 支持的

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler({ArithmeticException.class,NullPointerException.class})
    public String handleArithException(Exception exception) {
        log.info("异常是{}",exception);
        return "login";//视图地址
    }
}
```



（3）@ResponseStatus+自定义异常 ；底层是 ResponseStatusExceptionResolver ，拿到@ResponeStatus注解的信息，底层调用 response.sendError(statusCode, resolvedReason)；此次请求结束，并将信息发送给tomcat，由tomcat发送/error请求

```java
@ResponseStatus(value = HttpStatus.FORBIDDEN,reason = "用户数量太多")
public class UserTooManyException extends RuntimeException{
    public UserTooManyException() {}
    public UserTooManyException(String message) {
        super(message);
    }
}
```



（4）Spring底层的异常，如参数类型转换异常等皆由DefaultHandlerExceptionResolver处理

- response.sendError(HttpServletResponse.*SC_BAD_REQUEST*, ex.getMessage());



（5）自定义实现HandlerExceptionResolver处理异常，调高优先级，可以作为默认的全局异常处理规则【实现HandlerExceptionResolver接口】

```java
@Order(value = Ordered.HIGHEST_PRECEDENCE)
@Component
public class CustomerHandlerExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        try {
            response.sendError(511,"发生了错误");
        } catch (IOException e) {
            e.printStackTrace();
        }
        return new ModelAndView();
    }
}
```



（6）总结

ErrorViewResolver 实现自定义处理异常

- response.sendError，error请求就会转给BasicErrorController
- 异常没有任何解析器能够处理，Tomcat底层response.sendError，error请求也会转给BasicErrorController
- BasicErrorController要去的页面地址是ErrorViewResolver解析的




#### 2.8.8.3、异常处理自动配置原理

ErrorMvcAutoConfiguration 自动配置异常处理规则

- 组件类型：DefaultErrorAttributes —> 组件id：errorAttributes
  - 定义返回错误页面中数据（json或其他）的属性（status、timestamp）
- 组件类型：BasicErrorController —> 组件id：basicErrorController （JSON和白页的适配响应）
  - 处理默认/error 路径的请求；页面响应 new ModelAndView("error", model)
  - 容器中组件View —> id：error；（响应默认错误页）
  - 容器中组件 BeanNameViewResolver（视图解析器），按照返回的视图名作为组件的id去容器中找View对象
  - 容器中组件DefaultErrorViewResolver —> id：conventionErrorViewResolver
    - 如果发生错误，会以HTTP状态码作为视图页地址（viewName），找到真正的错误页面
    - error/404、5xx.html

```java
private ModelAndView resolve(String viewName, Map<String, Object> model) {
    String errorViewName = "error/" + viewName;
    TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
                                                                                           this.applicationContext);
    if (provider != null) {
        return new ModelAndView(errorViewName, model);
    }
    return resolveResource(errorViewName, model);
}
```

如果想要返回页面，就会找error视图【StaticView】（默认是一个白页）

![image-20230517140257180](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230517140257180.png)

 

#### 2.8.8.4、异常处理步骤流程

执行目标方法，目标方法运行期间有任何异常都会被catch，而且标志当前请求结束，异常用 dispatchException进行封装

进入视图解析流程（页面跳转）

```java
processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException)；

mv = processHandlerException(request, response, handler, exception)：处理handler发生的异常，处理完成返回ModelAndView
```

- 遍历所有的handlerExceptionResolvers，看谁能处理当前异常【HandlerExceptionResolver 处理器异常解析器】

![image-20230517153442483](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230517153442483.png)

-  系统默认的异常解析器

![image-20230517153457166](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230517153457166.png)

- DefaultErrorAttributes先来处理异常，把异常信息保存到request域中，并且返回null； 
- 没有任何默认异常处理器能够处理异常，所有异常被抛出
  - 如果没有任何异常处理器能够处理异常，底层就会发送/error请求，此请求会被底层的BasicErrorController处理
  - 解析错误视图：遍历所有的ErrorViewResolver 
  - 默认的DefaultErrorViewResolver的作用是把响应状态码作为错误页的地址，拼接error/500.html
  -  模板引擎最终响应这个页面


![image-20230517164128267](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230517164128267.png)





## 2.3、Web原生组件注入

### 2.3.1、使用Servlet API

```java
@ServletComponentScan(basePackages = "com.atguigu") ：标识在主程序类上，指定扫描的包，扫描原生Servlet组件
```

```java
@WebServlet("/my")：声明在继承了HttpServlet的自定义Servlet组件上，效果：直接响应，不会经过Spring的拦截器（因为精确优选原则，选择的是Tomcat处理流程）
```

```java
@WebFilter(urlPatterns = {"/css/*","/images/*"})：声明在实现了Filter接口的自定义Filter组件上

@WebListener ：声明在实现了ServletContextListener接口的自定义Listener组件上
```



![image-20230522164421111](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230522164421111.png)



> 注：
>
> 为什么 @WebServlet("/my") 声明的Servlet组件直接响应，不会经过Spring的拦截器（因为精确优先原则，选择的是Tomcat处理流程）？
>
> - 多个 Servlet 都能处理到同一层路径，精确优先原则
> - 上例中，如果请求路径为 /my，那么直接由 MyServlet 进行处理



### 2.3.2、使用RegistrationBean

ServletRegistrationBean、FilterRegistrationBean、ServletListenerRegistrationBean

```java
@Configuration(proxyBeanMethod = true)
public class MyRegistConfig {
    @Bean
    public ServletRegistrationBean myServlet(){
        MyServlet myServlet = new MyServlet();
        return new ServletRegistrationBean(myServlet,"/my","/my02");
    }

    @Bean
    public FilterRegistrationBean myFilter(){

        MyFilter myFilter = new MyFilter();
        // return new FilterRegistrationBean(myFilter,myServlet());//第一种写法
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(myFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my","/css/*"));
        return filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MySwervletContextListener mySwervletContextListener = new MySwervletContextListener();
        return new ServletListenerRegistrationBean(mySwervletContextListener);
    }
}
```



> 注：DispatcherServlet如何注册进来
>
> - 容器中自动配置了 DispatcherServlet，属性绑定到WebMvcProperties，对应的配置项前缀为spring.mvc
> - 通过ServletRegistrationBean<DispatcherServlet> 把DispatcherServlet 注册进容器
> - 默认映射的路径是 / （当前项目路径的所有请求都由 DispatcherServlet 处理）





## 2.4、嵌入式Servlet容器

### 2.4.1、切换嵌入式Servlet容器

> 官方文档：
>
> 在底层，Spring Boot使用不同类型的ApplicationContext来支持嵌入式servlet容器。ServletWebServerApplicationContext是WebApplicationContext的一种特殊类型，它通过搜索单个ServletWebServerFactory bean来引导自己。通常TomcatServletWebServerFactory，jetyservletwebserverfactory或UndertowServletWebServerFactory已经被自动配置。



##### 1、默认支持的webServer

Tomcat，Jetty，or Undertow

ServletWebServerApplicationContext 容器启动寻找 ServletWebServerFactory 并引导创建服务器



##### 2、切换服务器

![SpringBoot支持的Web服务器](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230522195033898.png)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- 排除 Tomcat-->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



##### 3、原理

SpringBoot应用启动发现当前是Web应用，Web场景包导入Tomcat

Web应用会创建一个web版的IOC容器 ServletWebServerApplicationContext

ServletWebServerApplicationContext 启动的时候寻找 ServletWebServerFactory（Servlet的Web服务器工厂，专门生产Servlet的Web服务器）

- SpringBoot底层默认有很多的WebServer工厂：`TomcatServletWebServerFactory、JettyServletWebServerFactory、UndertowServletWebServerFactory`

底层直接会有一个自动配置类：ServletWebServerFactoryAutoConfiguration

ServletWebServerFactoryAutoConfiguration导入了ServletWebServerFactoryConfiguration（配置类）

ServletWebServerFactoryConfiguration 配置类，根据@Condition 注解动态判断系统中到底导入了哪个Web服务器的包。（web-starter默认导入tomcat包），容器中就会有 TomcatServletWebServerFactory

TomcatServletWebServerFactory 创建出Tomcat服务器并启动；TomcatWebServer 的构造器拥有初始化方法initialize（其中调用this.tomcat.start()）

内嵌服务器，就是手动把启动服务器的代码调用（服务器核心jar包存在）	





##### 4、定制Servlet容器

- 实现 WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>（把配置文件的值和ServletWebServerFactory 进行绑定（可以对配置文件中的值进行重新定制））

- 修改配置文件 server.xxx

- 直接自定义 ConfigurableServletWebServerFactory

- xxxxxCustomizer：定制化器，可以改变xxxx的默认规则

```java
import org.springframework.boot.web.server.WebServerFactoryCustomizer;
import org.springframework.boot.web.servlet.server.ConfigurableServletWebServerFactory;
import org.springframework.stereotype.Component;

@Component
public class CustomizationBean implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {

    @Override
    public void customize(ConfigurableServletWebServerFactory server) {
        server.setPort(9000);
    }
}
```





## 2.5、定制化原理

### 2.5.1、定制化常见方式

- 修改配置文件
- xxxxCustomizer 实现定制化器
- 编写自定义的配置类 xxxConfiguration + @Bean 替换、增加容器中默认组件。例如：视图解析器
- web应用编写一个配置类实现 WebMvcConfigurer 即可定制化 SpringMVC 功能（常用） + @Bean 给容器中再扩展一些组件

```java
@Configuration
public class AdminConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
            .addPathPatterns("/**") //所有请求都被拦截包括静态资源
            .excludePathPatterns("/","/login","/css/**","/js/**","/fonts/**","/images/**"); //放行的请求
    }
}
```

- 给容器中注入WebMvcRegistrations类型的Bean，实现对SpringMVC底层的参数解析器等组件的定制
- @EnableWebMvc + @WebMvcConfigurer ：@Bean 可以全面接管SpringMVC，所有规则全部自己重新配置，实现定制和扩展功能
  - 静态资源、欢迎页、视图解析器全部失效
    - WebMvcAutoConfiguration 是默认的SpringMVC的自动配置功能类。自动配置了静态资源、欢迎页、视图解析器等组件

  - 一旦使用@EnableWebMvc，则会@Import(DelegatingWebMvcConfiguration.class)
  - DelegatingWebMvcConfiguration 的作用，只能保证SpringMVC最基本的使用
    - 把所有系统中的 WebMvcConfigurer 拿过来。所有功能的定制都是这些 WebMvcConfigurer 合起来一起生效
    - 自动配置了一些非常底层的组件。RequestMappingHandlerMapping等，这些组件依赖的组件都是从容器中获取
    - `public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport`

  - WebMvcAutoConfiguration 里面的配置要能生效必须保证（@ConditionalOnMissingBean(WebMvcConfigurationSupport.class) ）没有WebMvcConfigurationSupport.class，而@EnableWebMvc 却会导入DelegatingWebMvcConfiguration.class，也就简介的导入WebMvcConfigurationSupport.class，这会导致WebMvcAutoConfiguration类失效
  - @EnableWebMvc 导致了 WebMvcAutoConfiguration 没有生效。


```java
@Configuration
@EnableWebMvc
public class AdminWebConfig implements WebMvcConfigurer {

    /**
     * 定义静态资源行为，
     * @param registry
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        /**
         *  访问 /aa/** 所有请求都去 classpath:/static/ 下面进行匹配
         */
        registry.addResourceHandler("/aa/**")
            .addResourceLocations("classpath:/static/");
    }
}
```



### 2.5.2、原理分析套路

场景starter - xxxxAutoConfiguration - 导入xxx组件 - 绑定xxxProperties - 绑定配置文件项 	

# 3、数据访问

## 3.1、SQL

### 3.1.1、数据源的自动配置-HikariDataSource

#### 3.1.1.1、导入JDBC场景

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```

![image-20230601154839979](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230601154839979.png)

为何没有数据库驱动？

- 官方并不知道我们接下来操作什么数据库

```xml
默认版本：<mysql.version>8.0.22</mysql.version>

想要修改版本
1、直接依赖引入具体版本（maven的就近依赖原则）
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <!--<version>5.1.49</version>-->
</dependency>

2、重新声明版本（maven的pom文件属性标签的就近优先原则）
<properties>
    <java.version>1.8</java.version>
    <mysql.version>5.1.49</mysql.version>
</properties>
```



#### 3.1.1.2、分析自动配置原理

##### 自动配置配置的类：

- **DataSourceAutoConfiguration**：数据源的自动配置

  - 修改数据源相关配置：spring.datasource

  - 数据库连接池的配置，是自己容器中没有DataSource才自动配置

  - SpringBoot底层已经配置好的默认连接池是：HikariDataSource

- **DataSourceTransactionManagerAutoConfiguration**：事务管理器的自动配置

- **JdbcTemplateAutoConfiguration**：JdbcTemplate的自动配置，可以对数据库进行增删改查
  - 可以修改这个配置项@ConfigurationProperties(prefix = "spring.jdbc")来修改JdbcTemplate


- **JndiDataSourceAutoConfiguration**：JNDI的自动配置
- **XADataSourceAutoConfiguration**：分布式事务相关的



#### 3.1.1.3、修改配置项

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/db_account
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
```



#### 3.1.1.4、测试

```java
@Slf4j
@SpringBootTest
class Boot05WebAdminApplicationTests {
    @Autowired
    JdbcTemplate jdbcTemplate;

    @Test
    void contextLoads() {

        //jdbcTemplate.queryForObject("select * from account_tbl")
        //jdbcTemplate.queryForList("select * from account_tbl",)
        Long aLong = jdbcTemplate.queryForObject("select count(*) from account_tbl", Long.class);
        log.info("记录总数：{}",aLong);
    }
}
```



## 3.2、使用Druid数据源

### 3.2.1、druid官方github地址

https://github.com/alibaba/druid



整合第三方技术的两种方式

- 自定义
- 找starter



### 3.2.2、自定义方式

#### 3.2.2.1、创建数据源

##### 1、使用XML文件

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.17</version>
</dependency>

<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
      destroy-method="close">
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />
    <property name="maxActive" value="20" />
    <property name="initialSize" value="1" />
    <property name="maxWait" value="60000" />
    <property name="minIdle" value="1" />
    <property name="timeBetweenEvictionRunsMillis" value="60000" />
    <property name="minEvictableIdleTimeMillis" value="300000" />
    <property name="testWhileIdle" value="true" />
    <property name="testOnBorrow" value="false" />
    <property name="testOnReturn" value="false" />
    <property name="poolPreparedStatements" value="true" />
    <property name="maxOpenPreparedStatements" value="20" />
</bean>
```



##### 2、使用配置类

```java
// SpringBoot底层DataSource默认配置 @ConditionalOnMissingBean({DataSource.class})，没有导入DataSource.class的话就会默认导入Hikari数据源
@Bean
@ConfigurationProperties("spring.datasource") 
public DataSource dataSource() {
    DruidDataSource druidDataSource = new DruidDataSource();
    //开启监控功能
    druidDataSource.setFilters("stat");
    return druidDataSource;
}
```



#### 3.2.2.2、StatViewServlet（Druid监控页）

提供监控信息展示的html页面

提供监控信息的JSON API

##### 1、web.xml配置

```xml
<servlet>
    <servlet-name>DruidStatView</servlet-name>
    <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>DruidStatView</servlet-name>
    <url-pattern>/druid/*</url-pattern>
</servlet-mapping>
```



##### 2、配置类配置

```java
/**
* 配置Druid监控页功能
* @return
*/
@Bean
public ServletRegistrationBean servletRegistrationBean() {
    StatViewServlet statViewServlet = new StatViewServlet();
    ServletRegistrationBean<StatViewServlet> servletServletRegistrationBean = new ServletRegistrationBean<>(statViewServlet,"/druid/*");
    return servletServletRegistrationBean;
}
```



```java
@Bean
@ConfigurationProperties("spring.datasource")
public DataSource dataSource() {
    DruidDataSource druidDataSource = new DruidDataSource();
    //开启监控功能
    druidDataSource.setFilters("stat");
    return druidDataSource;
}
```



#### 3.2.2.3、StatFilter

用于统计监控信息：如SQL监控、URI监控

```
需要给数据源中配置如下属性；可以允许多个filter，多个用，分割；如：
<property name="filters" value="stat,slf4j" />
```

![image-20230605154851613](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230605154851613.png)

##### 1、内置监控中的Web关联监控设置

- web.xml配置


```xml
<filter>
    <filter-name>DruidWebStatFilter</filter-name>
    <filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
    <init-param>
        <param-name>exclusions</param-name>
        <param-value>*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>DruidWebStatFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



- 配置类配置


```java
/**
* WebStatFilter 用于采集web-jdbc关联监控信息
* @return
*/
@Bean
public FilterRegistrationBean webStatFilter() {
    WebStatFilter webStatFilter = new WebStatFilter();
    FilterRegistrationBean<WebStatFilter> filterFilterRegistrationBean = new FilterRegistrationBean<>(webStatFilter);
    filterFilterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
    filterFilterRegistrationBean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
    return filterFilterRegistrationBean;
}
```





### 3.2.3、使用官方starter方式

#### 3.2.3.1、引入druid-starter

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.17</version>
</dependency>
```



#### 3.2.3.2、分析自动配置

- 扩展配置项 spring.datasource.druid
- DruidSpringAopConfiguration.class，监控SpringBean的，配置项：spring.datasource.druid.aop-patterns
- DruidStatViewServletConfiguration.class，监控页的配置：spring.datasource.druid.stat-view-servlet，默认开启
- DruidWebStatFilterConfiguration.class，web监控配置；spring.datasource.druid.web-stat-filter，默认开启
- DruidFilterConfiguration.class}) 所有Druid自己filter的配置

```java
private static final String FILTER_STAT_PREFIX = "spring.datasource.druid.filter.stat";
private static final String FILTER_CONFIG_PREFIX = "spring.datasource.druid.filter.config";
private static final String FILTER_ENCODING_PREFIX = "spring.datasource.druid.filter.encoding";
private static final String FILTER_SLF4J_PREFIX = "spring.datasource.druid.filter.slf4j";
private static final String FILTER_LOG4J_PREFIX = "spring.datasource.druid.filter.log4j";
private static final String FILTER_LOG4J2_PREFIX = "spring.datasource.druid.filter.log4j2";
private static final String FILTER_COMMONS_LOG_PREFIX = "spring.datasource.druid.filter.commons-log";
private static final String FILTER_WALL_PREFIX = "spring.datasource.druid.filter.wall";
private static final String FILTER_WALL_CONFIG_PREFIX = "spring.datasource.druid.filter.wall.config";
```



#### 3.2.3.3、配置实例

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/db_account
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver

    druid:
      aop-patterns: com.atguigu.admin.*  #监控SpringBean
      filters: stat,wall     # 底层开启功能，stat（sql监控），wall（防火墙）

      stat-view-servlet:   # 配置监控页功能
        enabled: true
        login-username: admin
        login-password: admin
        resetEnable: false

      web-stat-filter:  # 监控web
        enabled: true
        urlPattern: /*
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'

      filter:
        stat:    # 对上面filters里面的stat的详细配置
          slow-sql-millis: 1000
          logSlowSql: true
          enabled: true
        wall:
          enabled: true
          config:
            drop-table-allow: false
```



- SpringBoot配置示例
  - https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter

- 配置项列
  - [https://github.com/alibaba/druid/wiki/DruidDataSource%E9%85%8D%E7%BD%AE%E5%B1%9E%E6%80%A7%E5%88%97%E8%A1%A8](https://github.com/alibaba/druid/wiki/DruidDataSource配置属性列表)






## 3.3、整合Mybatis操作

- 官方网站：https://github.com/mybatis


```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```



### 3.3.1、配置文件模式

- 全局配置文件
- SqlSessionFactory：自动配置好了
- SqlSession：自动配置了 SqlSessionTemplate 组合了 SqlSession
- @Import(**AutoConfiguredMapperScannerRegistrar**.**class**）：导入了注册器
- Mapper：只要我们写的操作Mybatis的接口标注了@Mapper就会被自动扫描

~~~Java
@EnableConfigurationProperties(MybatisProperties.class) ： MyBatis配置项绑定类。
@AutoConfigureAfter({ DataSourceAutoConfiguration.class, MybatisLanguageDriverAutoConfiguration.class })
public class MybatisAutoConfiguration{}

@ConfigurationProperties(prefix = "mybatis")
public class MybatisProperties
~~~

- 可以修改配置文件中以mybatis开头的配置项

~~~yaml
# 配置mybatis规则 （配置文件中）
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml  #全局配置文件位置
  mapper-locations: classpath:mybatis/mapper/*.xml  #映射文件位置
~~~

~~~xml
Mapper接口 ---> 绑定SQL映射文件
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.atguigu.admin.mapper.AccountMapper">
    <!--    public Account getAcct(Long id);  -->
    <select id="getAcct" resultType="com.atguigu.admin.bean.Account">
        select * from  account_tbl where  id=#{id}
    </select>
</mapper>
~~~

- 之前的全局配置文件中 configuration 标签中的所有配置标签，现在可以在配置文件中使用mybaits.configuration来进行配置（Mybatis中提供的Configuration类）

~~~yaml
# 配置mybatis规则
mybatis:
# config-location: classpath:mybatis/mybatis-config.xml   //指定配置文件路径（如果指定了此配置，配置文件中configuration中的配置则不能在此配置，需在配置文件中配置）
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:  //此配置会与上面的config-location冲突
    map-underscore-to-camel-case: true
    
可以不写全局配置文件，所有全局配置文件的配置都放在application.yaml中configuration配置项中即可
~~~



- 总结步骤：
  - 导入mybatis官方starter
  - 编写mapper接口。标识@Mapper注解
  - 编写sql映射文件并绑定mapper接口
  - 在application.yaml中指定Mapper配置文件的位置，以及指定全局配置文件的信息 （建议用mybatis.configuration进行配置）

































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































































