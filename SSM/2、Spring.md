## 1、`Spring`框架概述

### 1.1、概述

- `Spring`是一个生态，可以构建`Java`应用所需要的一切设施。

- 通常`Spring`指的是`Spring Framework`。

- `Spring`是轻量级的开源的`JavaEE`的框架。

- `Spring`可以解决企业应用开发的复杂性。

- `Spring`有两个核心部分：`IOC`和`AOP`。
- `IOC`：控制反转，把创建对象过程交给`Spring`进行管理。
  
- `AOP`：面向切面编程，不修改源代码进行功能增强。




### 1.2、`Spring`特点

- 方便解耦，简化开发
- `AOP`编程支持
- 方便程序的测试
- 方便和其他框架进行整合
- 方便进行事务的操作
- 降低`API`开发难度
- `Java`源码是经典学习范例



### 1.3、`Spring Framework`特性

- 非侵入式：使用`Spring Framework`开发应用程序时，`Spring`对应用程序本身的结构影响非常小。对领域模型可以做到零污染；对功能性组件也只需要使用几个简单的注解进行标记，完全不会破坏原有结构，反而能将组件结构进一步简化。这就使得基于`Spring Framework`开发应用程序时结构清晰、简洁优雅。

- 控制反转：`IOC——Inversion of Control`，翻转资源获取方向。把自己创建资源、向环境索取资源变成环境将资源准备好，我们享受资源注入。 面向切面编程：`AOP——Aspect Oriented Programming`，在不修改源代码的基础上增强代码功能。

- 容器：`Spring IOC`是一个容器，因为它包含并且管理组件对象的生命周期。组件享受到了容器化的管理，替程序员屏蔽了组件创建过程中的大量细节，极大的降低了使用门槛，大幅度提高了开发效率。
- 组件化：`Spring`实现了使用简单的组件配置组合成一个复杂的应用。在`Spring`中可以使用`XML`和`Java`注解组合这些对象。这使得我们可以基于一个个功能明确、边界清晰的组件有条不紊的搭建超大型复杂应用系统。
- 声明式：很多以前需要编写代码才能实现的功能，现在只需要声明需求即可由框架代为实现。
- 一站式：在`IOC`和`AOP`的基础上可以整合各种企业应用的开源框架和优秀的第三方类库。而且`Spring`旗下的项目已经覆盖了广泛领域，很多方面的功能性需求可以在`Spring Framework`的基础上全部使用`Spring`来实现。



### 1.4、Spring5的相关模块

![image-20230919094646984](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230919094646984.png)

- 下载地址：`https://repo.spring.io/ui/native/release/org/springframework/spring/`




## 2、`IOC`容器

### 2.1、`IOC`底层原理

#### 2.1.1、什么是`IOC`（`IOC`是依赖倒置原则的设计思想）

- `IOC`就是控制反转思想，它把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓“控制反转”的概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。

- 控制反转（`Inversion of Control`）把创建对象的权利交给框架，是框架的重要特征，并非面向对象编程的专用术语，它包括依赖注入（`Dependency Injection`）和依赖查找（`Dependency Lookup`）

  - `DI`是`IOC`的另一种表述方式：即组件以一些预先定义好的方式（例如：`setter`方法）接受来自于容器的资源注入。相对于`IOC`而言，这种表述更直接。 所以结论是：`IOC`就是一种控制反转的思想， 而`DI`是对`IOC`的一种具体实现。



#### 2.1.2、`IOC`的实现机制

- `Spring`中的`IOC`的实现原理就是工厂模式加反射机制。

- 工厂相对应的就是`beanfactory`的`getBean()`方法，反射就是`Spring`底层实例化对象所采用的机制。


```java
interface fruit{
    public abstract void eat();
}

class Apple implements fruit {
    @Override
    public void eat() {
        System.out.println("Apple");
    }
}

class Orange implements fruit {
    @Override
    public void eat() {
        System.out.println("Orange");
    }
}

class Factory {
    public static fruit getInstance(String ClassName) {
        fruit f = null;
        //通过反射创建对象 
        try {
            // 相当于Spring中new了一个ClassPathXmlApplicationContext
            f = (fruit)Class.forName(ClassName).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return f;
    }
}

class Test {
    public static void main(String[] args) {
        //getInstance时静态的，直接调用即可Factory.getInstance()
        fruit f = Factory.getInstance("Apple");
        if(f != null)
            f.eat();
    }
}

//输出 Apple
```

> 注：
>
> - `Class.forName(“”)`返回的是类
> - Class.forName(“”).newInstance()返回的是`object`



### 2.2、`IOC`实现（`BeanFactory`）

- `IOC`思想基于`IOC`容器完成，`IOC`容器底层就是对象工厂，`Spring`的`IOC`容器就是`IOC`思想的一个落地的产品实现。`IOC`容器中管理的组件也叫做`bean`。在创建`bean`之前，首先需要创建`IOC`容器。




#### 2.2.1、`Spring`提供`IOC`容器实现的两种方式：（两个接口）

##### 1、`BeanFactory`

- `Spring`中非常核心的一个顶层接口（它没有实现其他接口），IOC容器基本实现方式，是`Spring`内部使用接口。
- 它是`Bean`的“工厂”，它的主要职责就是生产`Bean`，实现了简单工厂模式，通过调用`getBean`传入标识（类名）再通过反射生产一个`Bean`。
- 它有非常多的实现类、每个工厂都有不同的职责（单一职责）功能。
- 最强大的工厂是：`DefaultListableBeanFactory`，`Spring`底层就是使用的该实现工厂进行生产`Bean`的。
- 创建对象采取的策略是采用延迟加载（懒加载）的方式，加载配置文件时不会创建对象，在获取对象（使用）才去创建对象。
- `BeanFactory`它也是容器！`Spring`容器（管理着`Bean`的生命周期）；`Tomcat`是`Servlet`的容器（它管理着`Servlet`的生命周期）



##### 2、`ApplicationContext`

- `BeanFactory`接口的子接口，是其扩展容器，基于`BeanFactory`将其和其他组件整合到了一起，提供更多更强大的功能，一般由开发人员进行使用。
- 创建对象采取的策略是采用立即加载（饿汉式）的方式，加载配置文件时就会把配置文件对象进行创建，创建并加载`IOC`容器。

> 注：上面两个类会加载并读取`Spring`配置文件，同时根据配置文件中配置的`bean`对象标签，延迟或立即创建对象，放在容器中（`Map`中）当调用`getBean`方法会从容器中取出对象。
>



#### 2.2.2、`ApplicationContext`的实现类

- `ClassPathXmlApplicationContext`：它可以加载类路径下的配置文件，要求配置文件必须在类路径下。不在的话，加载不了（`Maven`工程中`resources`文件夹也是类路径下，因为`Maven`打包时会将`resources`文件夹中的文件和`src`路径下的文件打包在一起）。
- `FileSystemXmlApplicationContext`：它可以加载计算机磁盘任意路径下的配置文件(必须有访问权限)，配置文件可以在磁盘的任意位置。
- `AnnotationConfigApplicationContext`：当我们使用注解配置容器对象时，需要使用此类来创建`spring`容器，它是用于读取注解创建容器。

![image-20230308203607088](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308203607088.png)



### 2.3、`IOC`操作`Bean`管理（基于`XML`）

#### 2.3.1、什么是`Bean`管理

- `Bean`管理指的是两个操作

  - `Spring`创建对象

  - `Spring`注入属性




#### 2.3.2、`Bean`管理操作有两种方式

- 基于`XML`配置文件方式实现
- 基于注解方式实现



#### 2.3.3、`Bean`对象的生命周期

- 单例对象：

  - 出生：当容器创建时对象出生

  - 活着：只要容器还在，对象一直活着

  - 死亡：容器销毁，对象消亡

  - 总结：单例对象的生命周期和容器相同


- 多例对象：

  - 出生：当我们使用对象时Spring框架创建对象

  - 活着：对象只要是在使用过程中就一直活着

  - 死亡；当对象长时间不用，且没有别的对象引用时，由Java的垃圾回收器回收




#### 2.3.4、基于`XML`方式注入对象

- `DI`依赖注入：`Dependency Injection`。它是`spring`框架核心`IOC`的具体实现。我们的程序在编写时，通过控制反转，把对象的创建交给了`spring`，但是代码中不可能出现没有依赖的情况。`IOC`解耦只是降低他们的依赖关系，但不会消除。例如：我们的业务层仍会调用持久层的对象。那这种业务层和持久层的依赖关系，在使用`spring`之后，就让`spring`来维护了。简单的说，就是让框架把持久层对象传入业务层而不用我们自己去获取。依赖关系的维护就称之为依赖注入。

- 第一步：导入依赖到`pom.xml`中。


```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.2.RELEASE</version>
    </dependency>
</dependencies>
```

- 第二步：在`resources`中创建一个`xml`文件，并导入约束。


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
```

- 第三步：让`Spring`管理资源，在`XML`配置文件中配置`service`和`dao`、创建对象时默认执行无参数构造方法。


```xml
<!--bean标签：用于配置让Spring创建对象，并且存入ioc容器之中
    id属性：对象的唯一标识
    class属性：指定要创建对象的全限定类名
对象的创建交给spring来管理-->

<bean id="accountService" class="com.aust.service.impl.AccountServiceImpl"></bean>//使用无参构造创建对象

<bean id="accountDao" class="com.aust.dao.impl.AccountDaoImpl"></bean>
```

![image-20230308204057187](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308204057187.png)

>  注：`Spring`底层默认通过反射技术调用组件类的无参构造器来创建组件对象，这一点需要注意。如果在需要无参构造器时，没有无参构造器，则会抛出异常。
>



#### 2.3.5、获取`Bean`对象

- 根据`id`获取

  - 由于`id`属性指定了`bean`的唯一标识，所以根据`bean`标签的`id`属性可以精确获取到一个组件对象。 上个实验中我们使用的就是这种方式。


- 根据类型获取


```java
@Test public void testHelloWorld() {
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    HelloWorld bean = ac.getBean(HelloWorld.class); 
    bean.sayHello();
}
```

- 根据`id`和类型获取


```java
@Test public void testHelloWorld() {
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    HelloWorld bean = ac.getBean("helloworld", HelloWorld.class);
    bean.sayHello();
}
```

> 注：
>
> - 当根据类型获取`bean`时，要求`IOC`容器中指定类型的`bean`有且只能有一个
> - 如果组件类实现了接口，根据接口类型可以获取`bean`吗？ 可以
> - 如果一个接口有多个实现类，这些实现类都配置了`bean`，根据接口类型可以获取`bean`吗？不可以
>
> 根据类型来获取`bean`时，在满足`bean`唯一性的前提下，其实只是看：『对象 `instanceof`指定的类型』的返回结果，只要返回的是`true`就可以认定为和类型匹配，能够获取到。



#### 2.3.6、基于`XML`方式注入属性

- 依赖注入能注入的数据：有三类

  - 基本类型和`String`

  - 其他`bean`类型（在配置文件中或者注解配置过的`bean`）

  - 复杂类型/集合类型



##### 1、`Set`方法注入（常用）

```xml
<--!
创建属性对应的Set方法
*Set方法注入属性

*使用的标签：property
*出现的位置：bean标签的内部
*标签的属性：
     name：用于指定注入时所调用的set方法名称
     value：用于提供基本类型和String类型的数据
     ref：用于指定其他的bean类型数据，就是在spring的IOC核心容器中出现的bean对象

优势：创建对象时没有明确的限制，可以直接使用默认构造函数
弊端：如果有某个成员必须有值，则获取对象时有可能set方法没有执行

注入属性的类要有属性的set方法
--></--!>
<bean id="accountService2" class="com.aust.service.impl.AccountServiceImpl2">
    <property name="name" value="张三"></property>
    <property name="age" value="19"></property>
    <property name="birthday" ref="birthdayClass"></property>
</bean>

<bean id="birthdayClass" class="java.util.Date"></bean>
```

##### 2、使用有参数构造注入

```xml
<--!
*提供有参构造
*在Spring配置文件中进行配置

*使用的标签：constructor-arg
*标签的属性：
     type：用于指定要注入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型
     index：用于指定要注入的数据会给构造函数中指定索引位置的参数赋值，索引位置是从0开始
     name：用于指定给构造函数中指定名称的参数赋值(常用)
     value：用于提供基本类型和String类型的数据
     ref：用户指定其他的bean类型数据，就是在spring的IOC核心容器中出现的bean对象

要求：类中需要提供一个对应参数列表的构造函数
--></--!>
<bean id="accountService" class="com.aust.service.impl.AccountServiceImpl">
    <constructor-arg name="name" value="张三"></constructor-arg>
    <constructor-arg name="age" value="18"></constructor-arg>
    <constructor-arg name="birthday" ref="birthdayClass"></constructor-arg>
</bean>

<bean id="birthdayClass" class="java.util.Date"></bean>

优势；在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功
弊端：改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供
```

##### 3、`p`名称空间注入

- 使用`p`名称空间注入，可以简化基于`xml`配置方式注入(了解)


（1）第一步：添加p名称空间在配置文件中

![image-20230308205325769](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308205325769.png)

（2）第二步：进行属性注入，在bean标签中进行操作

![image-20230308205338597](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308205338597.png)



##### 4、外部属性文件注入

- 直接配置数据库信息

  - 配置德鲁伊连接池

  - 引入德鲁伊连接池的依赖`jar`包


```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/atguigudb"></property>
    <property name="username" value="root"></property>
    <property name="password" value="19981025"></property>
</bean>
```

- 引入外部属性文件配置数据库连接池

  - 创建外部属性文件，`properties`格式文件


```properties
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/atguigudb
jdbc.userName=root
jdbc.password=19981025
```

- 把外部`properties`属性文件引入到`spring`配置文件中

```xml
 *引入context名称空间
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

 *在Spring配置文件中使用标签引入外部属性文件
<!--引入外部属性文件-->
<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
<!--配置连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driverClass}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
```



##### 5、对象类型属性自动装配

- 什么是自动装配：根据指定装配规则（属性名称或者属性类型），`Spring`自动将匹配的属性值进行注入

- 演示自动装配过程：

  - 根据属性名称进行自动装配


```xml
<!--自动装配
    bean标签属性autowire，配置自动装配
    autowire属性常用两个值：
        byName,根据属性名称进行注入，bean的id值和类属性名称相同，当某个接口有多个实现类时
        byType，根据属性类型注入，当接口的实现类只有一个时
        若在IOC中，没有任何一个兼容类型的bean能够为属性赋值，则该属性不装配，即值为默认值null 
        若在IOC中，有多个兼容类型的bean能够为属性赋值，则抛出异常 NoUniqueBeanDefinitionException
-->
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName"></bean>

<bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```

- 根据属性类型进行自动装配（相同类型的`bean`不能定义多个）

```xml
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byType"></bean>
<bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```

> 注：因为`Emp`类中有类型为`Dept`的成员变量，可用属性注入对`Emp`中的`Dept`类型的变量进行自动注入
>



#### 2.3.7、基于`XML`注入其他类型属性

##### 1、字面量

（1）`Null`值

```xml
<bean id="user" class="com.atguigu.spring5.User">
    <property name="name">
        <null></null>
    </property>
</bean>
```

（2）属性值包含特殊符号

```xml
<!--属性值包含特殊符号
    1把◇进行转义&lt; &gt;
    2把带特殊符号内容写到CDATA
-->
<property name="address">
    <value><! [CDATA[<<南京>>]]</value>
</property>
```



##### 2、注入属性-外部`Bean`

```xml
UserService中代码：
//创建UserDao类型属性，生成set方法
private UserDao userDao;

public void setUserDao(UserDao userDao) {
    this.userDao = userDao;
}

bean配置文件中代码：
<!--service和dao对象创建-->
<bean id="userService" class="com.atguigu.service.UserService">
    <!--注入UserDao对象
        name属性：类里面的属性名称
        ref属性：创建userDao对象bean标签id值
    -->
    <property name="userDao" ref="userDaoImpl"></property>
</bean>

<bean id="userDaoImpl" class="com.atguigu.dao.UserDaoImpl"></bean>
```



##### 3、注入属性- 内部`Bean`

- 一对多关系：部门和员工


```xml
<!--内部bean-->
<bean id="emp" class="com.atguigu.spring5.bean.Emp">
    <!--设置两个普通属性-->
    <property name="ename" value="Lucy"></property>
    <property name="gender" value="女"></property>
    <!--设置对象属性-->
    <property name="dept">
        <bean id="dept" class="com.atguigu.spring5.bean.Dept">
            <property name="dname" value="安保部"></property>
        </bean>
    </property>
</bean>
```



##### 4、注入属性-级联赋值

```xml
(1)第一种写法：
<bean id="emp" class="com.atguigu.spring5.bean.Emp">
    <property name="ename" value="Lucy"></property>
    <property name="gender" value="女"></property>
    <property name="dept" ref="dept"></property>
</bean>

<bean id="dept" class="com.atguigu.spring5.bean.Dept">
    <property name="dname" value="财务部"></property>
</bean>

(2)第二种写法：类中必须有此对象属性的get方法
<bean id="emp" class="com.atguigu.spring5.bean.Emp">
    <property name="ename" value="Lucy"></property>
    <property name="gender" value="女"></property>
    <property name="dept.dname" value="技术部"></property>
</bean>

//员工属于某个部门
private Dept dept;

public Dept getDept() {
    return dept;
}
```



##### 5、注入属性-集合属性

```xml
<bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
    <!--数组类型属性注入-->
    <property name="courses">
        <array>
            <value>java</value>
            <value>mysql</value>
        </array>
    </property>

    <!--list集合类型属性注入-->
    <property name="list">
        <list>
            <value>张三</value>
            <value>小三</value>
        </list>
    </property>

    <!--map类型属性注入-->
    <property name="maps">
        <map>
            <entry key="JAVA" value="java"></entry>
            <entry key="PHP" value="php"></entry>
        </map>
    </property>

    <!--set集合属性注入-->
    <property name="sets">
        <set>
            <value>MySQL</value>
            <value>JAVA</value>
        </set>
    </property>

    <!--注入list对象集合-->
    <property name="courseList">
        <list>
            <ref bean="course1"></ref>
            <ref bean="course2"></ref>
        </list>
    </property>
</bean>

<bean id="course1" class="com.atguigu.spring5.collectiontype.Course">
    <property name="cname" value="Spring5"></property>
</bean>
<bean id="course2" class="com.atguigu.spring5.collectiontype.Course">
    <property name="cname" value="Mybaties"></property>
</bean>
```

- 把集合注入部分提取出来


```xml
*(1)在spring配置文件中引入名称空间
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

*(2)使用util标签完成list集合注入提取
<!--1、提取list集合类型属性注入-->
<util:list id="bookList">
    <value>易筋经</value>
    <value>九阳神功</value>
    <value>九阴真经</value>
</util:list>

<!--2、提取list集合类型属性注入使用-->
<bean id="book" class="com.atguigu.spring5.collectiontype.Book">
    <property name="list" ref="bookList"></property>
</bean>
```



#### 2.3.8、`FactoryBean`

- `FactoryBean`是`Spring`提供的一种整合第三方框架的常用机制。和普通的`bean`不同，配置一个`FactoryBean`类型的`bean`，在获取`bean`的时候得到的并不是`class`属性中配置的这个类的对象，而是`getObject()`方法的返回值。通过这种机制，`Spring`可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。可以直接获取工厂所提供的对象。

- 将来我们整合`Mybatis`时，`Spring`就是通过`FactoryBean`机制来帮我们创建`SqlSessionFactory`对象。

- `Spring`有两种类型`bean`，一种普通`bean`，另外一种工厂`bean`。

  - 普通`bean`：在配置文件中定义`bean`的类型就是能拿到的类型。

  - 工厂`bean`：在配置文件中定义`bean`的类型可以和返回的类型不一样。

- 创建工厂`bean`

  - 第一步：创建类，让这个类作为工厂`bean`，实现接口`FactoryBean`。

  - 第二步：实现接口中的方法，在实现的方法中定义返回的`bean`类型


```java
public class DateTimeFormatterFactoryBean extends DateTimeFormatterFactory implements FactoryBean<DateTimeFormatter>, InitializingBean {
    @Nullable
    private DateTimeFormatter dateTimeFormatter;

    public DateTimeFormatterFactoryBean() {
    }

    public void afterPropertiesSet() {
        this.dateTimeFormatter = this.createDateTimeFormatter();
    }

    @Nullable
    public DateTimeFormatter getObject() {
        return this.dateTimeFormatter;
    }

    public Class<?> getObjectType() {
        return DateTimeFormatter.class;
    }

    public boolean isSingleton() {
        return true;
    }
}
```

```java
public class UserFactoryBean implements FactoryBean<User> {
    @Override
    public User getObject() throws Exception {
        return new User();
    }
    @Override
    public Class<?> getObjectType() {
        return User.class;
    }
}

@Test 
public void testUserFactoryBean(){ 
    //获取IOC容器 
    ApplicationContext ac = new ClassPathXmlApplicationContext("springfactorybean.xml");
    User user = (User) ac.getBean("user");
    System.out.println(user);
}

<bean id = "user" class="com.atguigu.bean.UserFactoryBean">
```

```xml
<!--配置SqlSessionFactoryBean整合Mybatis-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="typeAliasesPackage" value="com.atguigu.crowd.entity"></property>
    <!--指定Mybatis全局配置文件的位置-->
    <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    <!--指定Mapper.xml配置文件位置-->
    <property name="mapperLocations" value="classpath:mybatis/mapper/*Mapper.xml"></property>
    <!--装配数据源-->
    <property name="dataSource" ref="dataSources"></property>
    <!--配置Mybatis插件-->
    <property name="plugins">
        <array>
            <bean class="com.github.pagehelper.PageHelper">
                <property name="properties">
                    <props>
                        <!--配置数据库方言，告诉PageHelper当前使用的数据库-->
                        <prop key="dialect">mysql</prop>
                        <!--配置页码的合理化修正，在1~总页数之间修正页码-->
                        <prop key="reasonable">true</prop>
                    </props>
                </property>
            </bean>
        </array>
    </property>
</bean>
```

> 注：`BeanFactory`和`FactoryBean`有什么区别？
>
> - `BeanFactory`是一个工厂，也就是一个容器，是来管理和生产`bean`的；
>- `FactoryBean`是一个`bean`，但是它是一个特殊的`bean`，所以也是由`BeanFactory`来管理的， 它是一个接口，他必须被一个`bean`去实现。
> 
> 一个`Bean A`如果实现了`FactoryBean`接口，那么`A`就变成了一个工厂，根据`A`的名称获取到的实际上是工厂调用`getObject()`返回的对象（工厂产生的对象），而不是`A`本身；如果要获取工厂`A`自身的实例，那么需要在名称前面加上'&'符号。
>
> 简单的说就是：
>
> - `getObject(name)`：返回工厂中的实例
>- `getObject(&name)`：返回工厂本身的实例



#### 2.3.9、`Bean`的作用域

- 在`Spring`中可以通过配置`bean`标签的`scope`属性来指定`bean`的作用域范围，各取值含义参加下表：


![image-20230308211647795](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308211647795.png)

- 如果是在`WebApplicationContext`环境下还会有另外两个作用域（但不常用）：


![image-20230308211708886](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230308211708886.png)



#### 2.3.10、`Bean`的生命周期

1. `bean`对象创建（调用无参构造器）
2. 给`bean`对象设置属性
3. `bean`对象初始化之前操作（由`bean`的后置处理器负责）
4. `bean`对象初始化（需在配置`bean`时指定初始化方法）
5. `bean`对象初始化之后操作（由`bean`的后置处理器负责）
6. `bean`对象就绪可以使用
7. `bean`对象销毁（需在配置`bean`时指定销毁方法）
8. `IOC`容器关闭

- 演示`Bean`生命周期

```java
public class Orders {
    private String oName;

    public void setoName(String oName) {
        this.oName = oName;
        System.out.println("第二步，调用set方法");
    }

    public Orders() {
        System.out.println("第一步，执行过无参构造创建bean实例");
    }

    //创建执行的初始化的方法
    public void initMethod() {
        System.out.println("第三步，执行初始化的方法");
    }

    //创建执行的销毁的方法
    public void destroyMethod() {
        System.out.println("第五步，执行了销毁的方法");
    }
}

<bean id="orders" class="com.atguigu.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">
    <property name="oName" value="手机"></property>
</bean>

@Test
public void testBean2() {
//  ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean3.xml");
    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean4.xml");
    Orders orders = applicationContext.getBean("orders", Orders.class);
    System.out.println("第四步，获取创建bean实例对象");
    System.out.println(orders);

    //手动销毁bean实例
    applicationContext.close();
}
```

- 注意其中的`initMethod()`和`destroyMethod()`，可以通过配置`bean`指定为初始化和销毁的方法。


```xml
<bean id = "user" class="com.atguigu.bean.User" scope="prototype" init-method="initMethod" destroy-method="destoryMethod">
```

- `Bean`的后置处理器会在生命周期的初始化前后添加额外的操作，需要实现`BeanPostProcessor`接口， 且配置到`IOC`容器中，需要注意的是，`Bean`后置处理器不是单独针对某一个`Bean`生效，而是针对`IOC`容器中所有`Bean`都会执行。

- `Bean`的后置处理器，`Bean`的生命周期共有七步：

  - 通过构造器创建`Bean`实例（无参数构造）。

  - 为`Bean`的属性设置值和对其他`Bean`引用（`set`方法）。

  - 把`Bean`实例传递给`Bean`的后置处理器的方法`postProcessBeforeInitialization()`（`Bean`初始化之前需要对`Bean`进行的处理）。

  - 调用`Bean`的初始化方法（`InitializingBean`的`afterPropertiesSet()`方法，这个初始化方法需要进行实现）。

  - 把`Bean`实例传递给`Bean`的后置处理器的方法`postProcessAfterInitialization()`（`Bean`初始化之后需要对`Bean`进行的处理）。

  - `Bean`可以使用了。

  - 当容器关闭时，调用`Bean`的销毁方法（需要进行配置销毁的方法）。

- 演示添加后置处理器效果：

```java
public class MyBeanPost implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之前执行的方法");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之后执行的方法");
        return bean;
    }
}

<!--配置后置处理器-->
<bean id="myPost" class="com.atguigu.bean.MyBeanPost"></bean>
```



### 2.4、`IOC`操作`Bean`管理（基于注解）

#### 2.4.1、创建`Bean`实例并注入容器的注解

- `@Component`
- `@Service`
- `@Controller`
- `@Repository`
- 上面四个注解功能是一样的，都可以用来创建`Bean`实例



#### 2.4.2、基于注解方式实现对象创建

- 第一步：引入`aop`依赖
- 第二步：开启组件扫描

```xml
 *引入名称空间
xmlns:context="http://www.springframework.org/schema/context"

<!--开启组件扫描
    1、如果扫描多个包，多个包使用逗号隔开
    2、扫描包上层目录
--> 
<context:component-scan base-package="com.atguigu"></context:component-scan>
```

- 第三步：创建类，在类上面添加创建对象注解

```java
//在注解中的value属性值可以省略不写
//默认值是类名称首字母小写
@Component(value = "userService")
public class UserService 
```



#### 2.4.3、开启组件扫描细节配置

```xml
<!--示例1
    use-default-filters="false" :表示现在不使用默认filter，自己配置filter
    context:include-filter :设置扫描哪些内容
    扫描com.atguigu中含有@Controller注解的类
-->
<context:component-scan base-package="com.atguigu" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>

<!--示例2
    context:exclude-filter:设置哪些内容不进行扫描
    不扫描com.atguigu中含有@Controller注解的类
-->
<context:component-scan base-package="com.atguigu">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```



#### 2.4.4、基于注解方式实现自动注入

##### 1、`@Autowired`

- 根据属性类型进行自动装配，`@Autowired`是先根据类型（`byType`）查找，如果存在多个相同类型的`Bean`再根据名称（`byName`）进行查找，它的具体查找流程如下：

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/v2-9c555901799d1cb11b596db116c4db86_720w.webp)

```java
@Service
public class UserService {
    //不需要添加set方法
    @Autowired
    private UserDao userDao;

    public void add() {
        System.out.println("service add......");
        userDao.add();
    }
}
```

- `@Autowired`支持属性注入、构造方法注入和`Setter`注入

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/v2-603ec5f33ccb4aadf9a539390492835e_720w.webp)

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/v2-603ec5f33ccb4aadf9a539390492835e_720w.webp)

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/v2-1e08ab0a7375a06325d3e2f93c7a726f_720w.webp)



##### 2、`@Qualifier`

- 根据属性名称进行注入

  - 此注解需要和`@Autowired`一起使用

  - 当需要注入某接口的实现类不止一个时


```java
@Service
public class UserService {
    //不需要添加set方法
    @Autowired
    @Qualifier(value = "userDaoImpl1")
    private UserDao userDao;

    public void add() {
        System.out.println("service add......");
        userDao.add();
    }
}
```



##### 3、`@Resource`

- 可以根据类型注入，也可以根据名称注入。`@Resource`是先根据名称查找，如果（根据名称）查找不到，再根据类型进行查找，它的具体流程如下图所示：

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/v2-a894818ec090b73d71e9964266796a74_720w.webp)

```java
@Service
public class UserService {
    @Resource(name = "userDaoImpl1")//根据名称注入
    private UserDao userDao;

    public void add() {
        System.out.println("service add......");
        userDao.add();
    }
}
```

- `@Resource`只支持属性注入和`Setter`注入，当使用`@Resource`实现构造方法注入时就会提示以下错误：

![img](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/v2-46c6885cf4db50b58c9ca9067655f0a5_720w.webp)

> 注：
>
> `@Autowired`和`@Resource`都是用来实现依赖注入的注解（在`Spring/Spring Boot`项目中），但二者却有着`5`点不同：
>
> - 来源不同：`@Autowired`来自`Spring`框架，而`@Resource`来自于`(Java)JSR-250`；
> - 依赖查找的顺序不同：`@Autowired`先根据类型再根据名称查询，而`@Resource`先根据名称再根据类型查询；
> - 支持的参数不同：`@Autowired`只支持设置`1`个参数，而`@Resource`支持设置 7 个参数；
> - 依赖注入的用法支持不同：`@Autowired`既支持构造方法注入，又支持属性注入和`Setter`注入，而 `@Resource`只支持属性注入和`Setter`注入；
> - 编译器`IDEA`的提示不同：当注入`Mapper`对象时，使用`@Autowired`注解编译器会提示错误，而使用 `@Resource`注解则不会提示错误。



##### 4、`@Value`：注入普通类型属性

```java
@Value(value = "abc")
private String name;
```

> 注：`@Autowired`工作流程：
>
> - 首先根据所需要的组件类型到`IOC`容器中查找：
>
>   - 能够找到唯一的`bean`：直接执行装配
>
>   - 如果完全找不到匹配这个类型的`bean`：装配失败
>     - 和所需类型匹配的`bean`不止一个
>     - 没有`@Qualifier`注解：根据`@Autowired`标记位置成员变量的变量名作为`bean`的`id`进行匹配。
>       - 能够找到：执行装配
>       - 找不到：装配失败
>     - 使用`@Qualifier`注解：根据`@Qualifier`注解中指定的名称作为`bean`的`id`进行匹配。
>       - 能够找到：执行装配
>       - 找不到：装配失败
>
> - `@Autowired`中有属性`required`，默认值为`true`，因此在自动装配无法找到相应的`bean`时，会装配失败。
>
> - 可以将属性`required`的值设置为`false`，则表示能装就装，装不上就不装，此时自动装配的属性为默认值但是实际开发时，基本上所有需要装配组件的地方都是必须装配的，用不上这个属性。



### 2.5、完全注解开发

- 创建配置类，替代`xml`配置文件


```java
@Configuration//作为配置类，替代xml配置文件
@ComponentScan(basePackages = "com.atguigu") // 代替<context:component-scan>标签
public class SpringConfig {
}

@Test
public void testBean2() {
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
    UserService userService = applicationContext.getBean("userService", UserService.class);
    userService.add();
}
```



### 2.6、组件所对应的`bean`的`id`

- 在我们使用`XML`方式管理`bean`的时候，每个`bean`都有一个唯一标识，便于在其他地方引用。现在使用注解后，每个组件仍然应该有一个唯一标识。
- 默认情况
  - 类名首字母小写就是`bean`的`id`。例如：`UserController`类对应的`bean`的`id`就是`userController`。
- 自定义`bean`的`id`
  - 可通过标识组件的注解的`value`属性设置自定义的`bean`的`id`，`@Service("userService")`




## 3、`AOP`

### 3.1、什么是`AOP`

- 面向切面编程：利用`AOP`可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
- 通俗描述：不通过修改源代码的方式，在主干功能中添加新功能。
- `AOP(Aspect Oriented Programming)`是一种设计思想，是软件设计领域中的面向切面编程，它是面向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况下给程序动态统一添加额外功能的一种技术。



### 3.2、`AOP`底层原理

#### 3.2.1、代理模式

- 二十三种设计模式中的一种，属于结构型模式。它的作用就是通过提供一个代理类，让我们在调用目标方法的时候，不再是直接对目标方法进行调用，而是通过代理类间接调用。让不属于目标方法核心逻辑的代码从目标方法中剥离出来——解耦。调用目标方法时先调用代理对象的方法，减少对目标方法的调用和打扰，同时让附加功能能够集中在一起也有利于统一维护。


##### 1、静态代理

- 创建静态代理类


```java
public class CalculatorStaticProxy implements Calculator {
    // 将被代理的目标对象声明为成员变量
    private Calculator target;
    public CalculatorStaticProxy(Calculator target) {
        this.target = target;
    }
    @Override
    public int add(int i, int j) {
        // 附加功能由代理类中的代理方法来实现
        System.out.println("[日志] add 方法开始了，参数是：" + i + "," + j);
        // 通过目标对象来实现核心业务逻辑
        int addResult = target.add(i, j);
        System.out.println("[日志] add 方法结束了，结果是：" + addResult);
        return addResult;
    }
}
```

- 静态代理确实实现了解耦，但是由于代码都写死了，完全不具备任何的灵活性。就拿日志功能来说，将来其他地方也需要附加日志，那还得再声明更多个静态代理类，那就产生了大量重复的代码，日志功能还是分散的，没有统一管理。 提出进一步的需求：将日志功能集中到一个代理类中，将来有任何日志需求，都通过这一个代理类来实现。这就需要使用动态代理技术了。




##### 2、动态代理

![image-20230313210727976](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313210727976.png)

- 生产代理对象的工厂类：


```java
public class ProxyFactory {
    private Object target;
    public ProxyFactory(Object target) {
        this.target = target;
    }
    public Object getProxy(){ 
    /**
    * newProxyInstance()：创建一个代理实例
     * 其中有三个参数：
    * 1、classLoader：加载动态生成的代理类的类加载器
    * 2、interfaces：目标对象实现的所有接口的class对象所组成的数组
    * 3、invocationHandler：设置代理对象实现目标对象方法的过程，即代理类中如何重写接
     口中的抽象方法
*/
        ClassLoader classLoader = target.getClass().getClassLoader();
        Class<?>[] interfaces = target.getClass().getInterfaces();
        InvocationHandler invocationHandler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    /**
     * proxy：代理对象
     * method：代理对象需要实现的方法，即其中需要重写的方法
     * args：method所对应方法的参数
     */
                Object result = null; // 返回值为真实方法的返回值
                try {
                    System.out.println("[动态代理][日志] "+method.getName()+"，参数："+ Arrays.toString(args));
                    result = method.invoke(target, args);//反射调用目标对象的目标方法
                    System.out.println("[动态代理][日志] "+method.getName()+"，结果："+ result);
                } catch (Exception e) {
                    e.printStackTrace();
                    System.out.println("[动态代理][日志] "+method.getName()+"，异常："+e.getMessage());
                } finally {
                    System.out.println("[动态代理][日志] "+method.getName()+"，方法执行完毕");
                }
                return result;
            }
        };
        return Proxy.newProxyInstance(classLoader, interfaces,invocationHandler);// 返回代理对象实例
    }
}
```





#### 3.2.2、`AOP`底层使用动态代理

- 第一种：有接口，使用`JDK`动态代理

  - 创建`UserDao`接口实现类代理对象，通过代理对象增强方法。

- 第二种：没有接口，使用`CGLB`动态代理
  - 创建当前类子类的代理对象，增强类中方法。



##### 1、`AOP`（`JDK`动态代理实现）

- 使用`JDK`动态代理，使用`Proxy`类里面的方法创建代理对象（`java.lang`）

- 调用`newProxyInstance`方法

  - 第一个参数，类加载器

  - 第二个参数，增强方法所在的类，这个类实现的接口，支持多个接口

  - 第三个参数，实现这个接口`InvocationHandler`，创建代理对象，写增强的方法



##### 2、编写`JDK`动态代理代码

- 创建接口，定义方法


```java
public interface UserDao {

    public int add(int a,int b);

    public void update(String id);
}
```

- 创建接口实现类，实现方法


```java
public class UserDaoImpl implements UserDao {//需要增强的实现类
    @Override
    public int add(int a, int b) {
        return a+b;
    }

    @Override
    public String update(String id) {
        return id;
    }
}
```

- 使用`Proxy`类创建接口代理对象


```java
//创建代理对象代码
class UserDaoProxy implements InvocationHandler {
    //1 把需要增强的对象传递过来
    //有参数构造
    private Object object;
    public UserDaoProxy(Object object) {
        this.object = object;
    }

    //2 增强的逻辑
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //方法之前
        System.out.println("在方法之前执行...." + method.getName() + "：传递的参数..." + Arrays.toString(args));
        //被增强的方法
        Object res = method.invoke(object, args);
        //方法之后
        System.out.println("在方法之后执行...." + object);

        return res;
    }
}
```

```java
public class JDKProxy {
    public static void main(String[] args) {
        //创建接口实现类代理对象
        Class[] intreface = {UserDao.class};
        UserDaoImpl userDaoImpl = new UserDaoImpl();
        UserDao userDao = (UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), intreface, new UserDaoProxy(userDaoImpl));//Proxy是java.lang.reflect包下提供的类，用来创建代理对象实例，其并非是代理类

        int add = userDao.add(1, 2);
        System.out.println(add);
    }
}
```



### 3.3、`AOP`操作-基本术语

##### 1、横切关注点

- 从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强。 这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个横切关注点。


![image-20230313211713711](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313211713711.png)

##### 2、Joinpoint（连接点）

- 这也是一个纯逻辑概念，不是语法定义的。所谓连接点是指那些被拦截到的点，在`spring`中，这些点指的是方法，因为`spring`只支持方法类型的连接点（业务层接口中的方法），所有的切入点都是连接点，但并不是所有的连接点都是切入点。简单的说就是类中哪些方法可以被增强，这些方法被称为连接点。（类中的方法都可以称为连接点，但类中的方法并不都是切入点）


##### 3、`Pointcut`（切入点）

- 定位连接点的方式。 每个类的方法中都包含多个连接点，所以连接点是类中客观存在的事物（从逻辑上来说）。 如果把连接点看作数据库中的记录，那么切入点就是查询记录的`SQL`语句。 `Spring`的`AOP`技术可以通过切入点定位到特定的连接点。 切点通过`org.springframework.aop.Pointcut`接口进行描述，它使用类和方法作为连接点的查询条件。


##### 4、`Advice`（通知/增强）

- 每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法。 通知的类型：前置通知，后置通知、异常通知，最终通知，环绕通知，实际增强的逻辑部分被称为通知(增强)。


##### 5、`Introduction`（引介）

- 引介是一种特殊的通知在不修改类代码的前提下，引介可以在运行期为类动态地添加一些方法或`Filed`。


##### 6、`Target`（目标对象）

- 代理的目标对象，被代理对象。


##### 7、`Weaving`（织入）

- 是指把增强应用到目标对象来创建新的代理对象的过程，`spring`采用动态代理织入，而`AspectJ`采用编译期织入和类装载期织入。


##### 8、`Proxy`（代理）

- 一个类被`AOP`织入增加后，就产生一个结果代理类，一个类被代理后产生的代理类。


##### 9、`Aspect`（切面）

- 封装通知方法的类。是切入点和通知（引介）的结合，切入点方法和通知方法在执行调用时的对应关系 。简单的说，把通知应用到切入点的过程就叫做切面，这是一个动作。




### 3.4、AOP操作-准备工作

#### 3.4.1、`AspectJ`

- Spring框架一般基于`AspectJ`来实现`AOP`操作

- `AspectJ`不是`Spring`组成部分，独立于`AOP`框架，一般把`AspectJ`和`Spring`框架一起使用，进行`AOP`操作



#### 3.4.2、引入依赖

```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>4.3.7.RELEASE</version>
    </dependency>
```



#### 3.4.3、切入点表达式

- 切入点表达式作用：知道对哪个类里面的哪个方法进行增强

- 语法结构：`execution`(\[权限修饰符]\[返回类型]\[类全路径]\[方法名称]([参数列表])）

- 举例：

  - 对`com.atguigu.dao.BookDao`类中的`add`进行增强

    - `excution(* com.atguigu.daoBookDao.add(..))`


  - 对`com.atguigu.dao.BookDao`类中的所有方法进行增强

    - `excution(* com.atguigu.daoBookDao.*(..))`


  - 对`com.atguigu.dao`包里所有类，类里面所有方法进行增强
    - `excution(* com.atguigu.dao.*.*(..))`




### 3.5、`AOP`操作-`AspectJ`注解操作

- `AspectJ`：本质上是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最终效果是动态的。`weaver`就是织入器。`Spring`只是借用了`AspectJ`中的注解。

#### 3.5.1、添加依赖

- 在`IOC`所需依赖基础上再加入下面依赖即可：


```xml
<!-- spring-aspects会帮我们传递过来aspectjweaver -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>
```



#### 3.5.2、 准备被代理的目标资源

```java
public interface Calculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}
```



#### 3.5.3、实现类

```java
@Component
public class CalculatorPureImpl implements Calculator {
    @Override
    public int add(int i, int j) {
        int result = i + j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int sub(int i, int j) {
        int result = i - j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int mul(int i, int j) {
        int result = i * j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int div(int i, int j) {
        int result = i / j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
}
```



#### 3.5.4、创建切面类并配置

```java
// @Aspect表示这个类是一个切面类
@Aspect
// @Component注解保证这个切面类能够放入IOC容器
@Component
public class LogAspect {
    @Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.* (..))")
    public void beforeMethod(JoinPoint joinPoint){
        String methodName = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args);
    }
    @After("execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))")
    public void afterMethod(JoinPoint joinPoint){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->后置通知，方法名："+methodName);
    }
    @AfterReturning(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))", returning = "result")
    public void afterReturningMethod(JoinPoint joinPoint, Object result){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->返回通知，方法名："+methodName+"，结果："+result);
    }
    @AfterThrowing(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))", throwing = "ex")
    public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->异常通知，方法名："+methodName+"，异常："+ex);
    }

    @Around("execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))")
    public Object aroundMethod(ProceedingJoinPoint joinPoint){//返回值为目标方法的返回值
        String methodName = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        Object result = null;
        try {
            System.out.println("环绕通知-->目标对象方法执行之前");//前置通知
            //目标方法的执行
            result = joinPoint.proceed();
            System.out.println("环绕通知-->目标对象方法返回值之后");//返回通知
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println("环绕通知-->目标对象方法出现异常时");//异常通知
        } finally {
            System.out.println("环绕通知-->目标对象方法执行完毕");//后置通知
        }
        return result;
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

<!--
        基于注解的AOP的实现：
        1、将目标对象和切面交给IOC容器管理（注解+扫描）
        2、开启AspectJ的自动代理，为目标对象自动生成代理
        3、将切面类通过注解@Aspect标识
-->
    <!--开启注解扫描-->
    <context:component-scan base-package="com.atguigu.spring5.aopanno"> </context:component-scan>
    <!--开启基于注解的AOP-->
    <aop:aspectj-autoproxy />
</beans>
```



#### 3.5.5、各种通知

- 在切面类中的通知方法上面添加通知类型注解，使用切入点表达式配置


```java
//增强的类
@Component
@Aspect
public class UserProxy {
    //前置通知
    @Before(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void before() {
        System.out.println("before....");
    }

    //返回通知，当被增强的方法有异常时就不会执行
    @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterReturning() {
        System.out.println("afterReturning....");
    }

    //异常通知
    @AfterThrowing(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterThrowing() {
        System.out.println("afterThrowing....");
    }

    //环绕通知
    @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("around....环绕之前");
        //被增强的方法执行
        proceedingJoinPoint.proceed();

        System.out.println("around....环绕之后");
    }

    //后置通知，不管有没有异常都会执行
    @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void after() {
        System.out.println("after....");
    }
}

无异常时执行结果
around....环绕之前
before....
add...
around....环绕之后
after....
afterReturning....

有异常时执行结果
around....环绕之前
before....
after....
afterThrowing....
```

- 前置通知：使用@Before注解标识，在被代理的目标方法前执行
- 返回通知：使用@AfterReturning注解标识，在被代理的目标方法成功结束后执行（寿终正寝）
- 异常通知：使用@AfterThrowing注解标识，在被代理的目标方法异常结束后执行（死于非命）
- 后置通知：使用@After注解标识，在被代理的目标方法最终结束后执行（盖棺定论）
- 环绕通知：使用@Around注解标识，使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置

> 注：
>
> 各种通知的执行顺序：
>
> - Spring版本5.3.x以前：前置通知、目标操作、后置通知、返回通知或异常通知
> - Spring版本5.3.x以后：前置通知、目标操作、返回通知或异常通知、后置通知



**环绕通知**

```java
@Around("execution(* com.atguigu.aop.annotation.CalculatorImpl.*(..))")
public Object aroundMethod(ProceedingJoinPoint joinPoint){//返回值为目标方法的返回值
    String methodName = joinPoint.getSignature().getName();
    String args = Arrays.toString(joinPoint.getArgs());
    Object result = null;
    try {
        System.out.println("环绕通知-->目标对象方法执行之前");//前置通知
        //目标方法的执行
        result = joinPoint.proceed();
        System.out.println("环绕通知-->目标对象方法返回值之后");//返回通知
    } catch (Throwable throwable) {
        throwable.printStackTrace();
        System.out.println("环绕通知-->目标对象方法出现异常时");//异常通知
    } finally {
        System.out.println("环绕通知-->目标对象方法执行完毕");//后置通知
    }
    return result;
}
```





#### 3.5.6、重用切入点表达式

- 声明


```java
//相同切入点抽取
@Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
public void pointExtract() {
}
```

- 在同一个切面中使用


```java
@Before("pointCut()")
public void beforeMethod(JoinPoint joinPoint){
    String methodName = joinPoint.getSignature().getName();
    String args = Arrays.toString(joinPoint.getArgs());
    System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args);
}
```

- 在不同切面中使用


```java
@Before("com.atguigu.aop.CommonPointCut.pointCut()")
public void beforeMethod(JoinPoint joinPoint){
    String methodName = joinPoint.getSignature().getName();
    String args = Arrays.toString(joinPoint.getArgs());
    System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args);
}
```



#### 3.5.7、获取通知的相关信息

##### 1、获取连接点信息

- 获取连接点信息可以在通知方法的参数位置设置JoinPoint类型的形参


```java
@Before("execution(public int com.atguigu.aop.annotation.CalculatorImpl.*(..))")
public void beforeMethod(JoinPoint joinPoint){
    //获取连接点的签名信息
    String methodName = joinPoint.getSignature().getName();
    //获取目标方法到的实参信息
    String args = Arrays.toString(joinPoint.getArgs());
    System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args);
}
```



##### 2、获取目标方法的返回值

- `@AfterReturning`中的属性`returning`，用来将通知方法的某个形参，接收目标方法的返回值

```java
@AfterReturning(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.* (..))", returning = "result")
public void afterReturningMethod(JoinPoint joinPoint, Object result){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->返回通知，方法名："+methodName+"，结果："+result);
}
```



##### 3、获取目标方法的异常

- `@AfterThrowing`中的属性`throwing`，用来将通知方法的某个形参，接收目标方法的异常

```java
@AfterThrowing(value = "execution(* com.atguigu.aop.annotation.CalculatorImpl.* (..))", throwing = "ex")
public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex){
        String methodName = joinPoint.getSignature().getName();
        System.out.println("Logger-->异常通知，方法名："+methodName+"，异常："+ex);
}
```



#### 3.5.8、切面的优先级

- 相同目标方法上同时存在多个切面时，切面的优先级控制切面的内外嵌套顺序。

  - 优先级高的切面：外面

  - 优先级低的切面：里面

- 在增强类上添加注解`@Order`（数字类型值，越小优先级越高）

```java
@Component
@Aspect
@Order(1)
public class PersonProxy {}
```



#### 3.5.9、完全使用注解开发

```java
@Configuration
@ComponentScan(basePackages = {"com.atguigu"})
@EnableAspectJAutoProxy(proxyTargetClass = true)//开启Aspect生成代理对象
public class ConfigAop {
}
```



### 3.6、AOP操作-XML配置文件

```xml
<!--创建对象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>

<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>

<!--配置AOP的增强-->
<aop:config>
    <!--切入点-->
    <aop:pointcut id="buy" expression="execution(* com.atguigu.spring5.aopxml.Book.buy(..))"/>

    <!--配置切面-->
    <aop:aspect ref="bookProxy">
        <!--增强作用在具体的方法上-->
        <aop:before method="before" pointcut-ref="buy"></aop:before>
    </aop:aspect>
</aop:config>
```





## 4、事务操作

### 4.1、事务概念

#### 4.1.1、什么是事务

- 事务是数据库操作的最基本单元，逻辑上一组操作，要么都成功，要么都失败。
- 典型场景：银行转账



#### 4.1.2、事务的特性（`ACID`）

- 原子性(`Atomicity`)：一个事务不可再分割，要么都执行要么都不执行。
- 一致性(`Consistency`)：一个事务执行会使数据从一个一致状态切换到另外一个一致状态。
- 隔离性(`Isolation`)：一个事务的执行不受其他事务的干扰。
- 持久性(`Durability`)：一个事务一旦提交，则会永久的改变数据库的数据。



### 4.2、搭建事务操作环境

#### 4.2.1、建表

#### 4.2.2、代码实现

```java
DAO代码
@Repository
public class UserDaoImpl implements UserDao{
    @Autowired
    private JdbcTemplate jdbcTemplate;

    //Lucy转账100给Marry
    @Override
    public void addMoney() {
        String sql = "update t_account set money = money + ? where username = ?";
        jdbcTemplate.update(sql, 100, "Marry");
    }

    @Override
    public void reduceMoney() {
        String sql = "update t_account set money = money - ? where username = ?";
        jdbcTemplate.update(sql, 100, "Lucy");
    }
}

Service代码
@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    //转账方法
    public void transfer() {
        //第一步 开启事务
        //第二步 进行业务操作
        //第三步 没有发生异常，提交事务
        //第四步 出现异常，事务回滚
        //Lucy少100
        userDao.reduceMoney();
        
        //会发生异常
        int i = 1/0;

        //marry多100
        userDao.addMoney();
    }
}
```



### 4.3、事务管理

#### 4.3.1、概述

- 事务添加到`JavaEE`三层结构中的`Service`层（业务逻辑层）

- 有两种方式：

  - 编程式事务和声明式事务（常用）




#### 4.3.2、声明式事务管理

- 有两种方式：

  - 基于注解方式

  - 基于`XML`配置文件方式

- 在`Spring`中进行声明式事务管理，底层其实使用的是`AOP`。

- `Spring`事务管理`API`：`PlatformTransactionManager`，事务管理器，针对不同的框架提供不同的实现类。



#### 4.3.3、注解声明式事务管理

- 在配置文件中配置事务管理器（事务管理器就是个切面，其中有许多已有的通知）


```xml
<!--创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

- 在配置文件中开启事务注解

  - 引入名称空间`tx`


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
```

- 开启事务的注解驱动

```xml
<!--开启事务注解-->将使用@Transactionl注解所标识的方法或类中所有的方法使用事务进行管理，被@Transactional此注解标识的方法为连接点
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>

transaction-manager属性设置事务管理器id
若事务管理器的bean的id默认为transactionManager，则该属性可以不写
```

- 在`service`层上（`service`中的方法上）添加事务注解
  - `@Transactional`（这个注解可以添加类和方法上）
  - 如果添加到类上，这个类中所有方法都添加事务
  - 如果添加到方法上，为这个方法添加事务




#### 4.3.4、声明式事务管理注解参数的配置

##### 1、相关参数

![image-20230407210944115](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230407210944115.png)



##### 2、`propagation`：事务传播行为

- 当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。
- 事务方法：对数据库表数据进行增删改的操作。
- 传播行为：多事务方法互相调用过程中事务的处理。
- `Spring`定义了`7`种传播行为

![image-20230407214017959](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230407214017959.png)

- `REQUIRED`：如果有事务在运行，当前的方法就在这个事务内运行，否则，就启动一个新的事务，并在自己的事务内运行。如果`add`方法本身有事务，调用`update`方法之后，`update`使用当前`add`方法的事务，如果`add`方法本身没有事务，调用`update`方法之后，创建新事务，但是用别人的事务有可能被回滚。（默认值）

- `REQUERS_NEW`：当前的方法必须启动新事务，并在它自己的事务内运行，如果有事务正在运行，应该将它挂起。使用`add`方法调用`update`方法，`add`方法无论是否有事务，都创建新的事务。不会受到其他事务的影响，推荐使用。

- `SUPPORTS`：如果有事务在运行，当前的方法就在这个事务内运行，否则它可以不运行在事务中。

- `NOT_SUPPORTE`：当前的方法不应该运行在事务中，如果有运行的事务，将当前事务挂起。

- `MANDATORY`：当前的方法必须运行在事务内部，如果没有正在运行的事务，就抛出异常。

- `NEVER`：当前的方法不应该运行在事务中，如果有运行的事务，就抛出异常。

- `NESTED`：如果有事务在运行，当前的方法就应该在这个事务的嵌套事务内运行，否则，就启动一个新的事务，并在它自己的事务内运行，执行`REQUIRED`类似操作。

> 注：
>
> - `REQUIRED`：如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。一般的选择（默认值）。
>- `SUPPORTS`：支持当前事务，如果当前没有事务，就以非事务方式执行（没有事务）。
> - `MANDATORY`：使用当前的事务，如果当前没有事务，就抛出异常。
>- `REQUERS_NEW`：新建事务，如果当前在事务中，把当前事务挂起。
> - `NOT_SUPPORTED`：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
>- `NEVER`：以非事务方式运行，如果当前存在事务，抛出异常。
> - `NESTED`：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行`REQUIRED`类似的操作。



##### 3、`Isolation`：事务隔离级别

- `MySQL`默认事务隔离级别为可重复读，并解决了幻读问题 

- 多事务操作之间不会产生影响

- 三个问题：脏读、不可重复读、虚（幻）读

- 脏写、脏读、不可重复读、幻读

  - 脏读：一个未提交的事务读取到另一个事务未提交的数据。它的本质是事务`B`去查询了事务`A`修改过的数据，但是此时事务`A`还没提交，所以事务`A`随时会回滚导致事务`B`再次查询就读不到刚才事务`A`修改过的数据了，这就是脏读。
  - 不可重复读：一个未提交事务读取到另一提交事务修改的数据，不能算是一个问题。
  - 幻读：一个未提交事务读取到另一个提交事务添加的数据。

- 通过设置事务的隔离级别解决读问题

![image-20230407220434903](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230407220434903.png)

```java
@Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.REPEATABLE_READ)//这两个都是默认值
```

- `timeout`：超时时间， 事务在执行过程中，有可能因为遇到某些问题，导致程序卡住，从而长时间占用数据库资源。而长时间占用资源，大概率是因为程序运行出现了问题（可能是`Java`程序或`MySQL`数据库或网络连接等等）。 此时这个很可能出问题的程序应该被回滚，撤销它已做的操作，事务结束，把资源让出来，让其他正常程序可以执行。
  - 事务需要在一定时间内提交，如果不提交就会强制回滚并抛出异常。
  - 默认值是`-1`为永不超时，如果设置了时间数值，则单位为秒。

- `readOlny`：是否只读， 对一个查询操作来说，如果我们把它设置成只读，就能够明确告诉数据库，这个操作不涉及写操作。这样数据库就能够针对查询操作来进行优化。
  - 读：查询操作，写：添加修改删除操作。
  - `readOnly`默认值为`false`，表示可以查询，也可以进行添加修改删除操作。
  - 设置`readOnly`值是`true`，设置成`true`后，只能查询。
  - 对增删改操作设置只读会抛出下面异常：`Caused by: java.sql.SQLException: Connection is read-only. Queries leading to data modification are not allowed`。

- `rollbackFor`：回滚，声明式事务默认只针对出现运行时异常时进行回滚。
  - 设置出现哪些异常进行事务回滚。

- `noRollbackFor`：不回滚
  - 设置出现哪些异常不进行事务回滚。

- `rollbackForClassName`属性：需要设置一个字符串类型的全类名。



#### 4.3.5、XML配置文件声明式事务管理

##### 1、在spring配置文件中进行配置

- 配置事务管理器
- 配置通知
- 配置切入点和切面

```xml
<!--数据库连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:23306/atguigudb"></property>
    <property name="username" value="root"></property>
    <property name="password" value="123456"></property>
</bean>

<!--jdbcTemplate对象-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <!--注入dataSource-->
    <property name="dataSource" ref="dataSource"></property>
</bean>

<!--1.创建事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>

<!--2.配置通知-->
<tx:advice id="txadvice" transaction-manager="transactionManager">
    <!--配置事务参数-->
    <tx:attributes>
        <!--指定那种方法上面添加事务和事务属性-->
        <tx:method name="accoutMoney" propagation="REQUIRED"/>
        <!--tx:method name="account*"/>-->
    </tx:attributes>
</tx:advice>

<!--3.配置切入点和切面-->
<aop:config>
    <!--配置切入点-->
    <aop:pointcut id="pt" expression="execution(* com.atguigu.spring5.service.UserService.*(..))"/>
    <!--配置切面,将通知配置到连接点-->
    <aop:advisor advice-ref="txadvice" pointcut-ref="pt"></aop:advisor>
</aop:config>
```

```xml
<aop:config>
    <!-- 配置事务通知和切入点表达式 -->
    <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.atguigu.spring.tx.xml.service.impl.*.*(..))"></aop:advisor>
</aop:config>
<!-- tx:advice标签：配置事务通知 -->
<!-- id属性：给事务通知标签设置唯一标识，便于引用 -->
<!-- transaction-manager属性：关联事务管理器 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!-- tx:method标签：配置具体的事务方法 -->
        <!-- name属性：指定方法名，可以使用星号代表多个字符 -->
        <tx:method name="get*" read-only="true"/>
        <tx:method name="query*" read-only="true"/>
        <tx:method name="find*" read-only="true"/>
        <!-- read-only属性：设置只读属性 -->
        <!-- rollback-for属性：设置回滚的异常 -->
        <!-- no-rollback-for属性：设置不回滚的异常 -->
        <!-- isolation属性：设置事务的隔离级别 -->
        <!-- timeout属性：设置事务的超时属性 -->
        <!-- propagation属性：设置事务的传播行为 -->
        <tx:method name="save*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
        <tx:method name="update*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
        <tx:method name="delete*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
    </tx:attributes>
</tx:advice>
```

- 基于xml实现的声明式事务，必须引入aspectJ的依赖



#### 4.3.6、完全注解声明式事务管理（替代XML配置文件）

```java
@Configuration
@ComponentScan(basePackages = "com.atguigu")
@EnableTransactionManagement
public class SpringConfiguration {

    //创建数据库连接池
    @Bean
    public DruidDataSource getDruidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
        druidDataSource.setUrl("jdbc:mysql://localhost:23306/atguigu");
        druidDataSource.setUsername("root");
        druidDataSource.setPassword("123456");
        return druidDataSource;
    }

    //创建JdbcTemplate对象
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        //注入DataSource
        //到ioc容器中根据类型找到dataSource
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    //创建事务管理器对象
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource) {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }
}
```





#### 4.3.7、`Spring`事务失效的八大场景

##### 1、方法自调用

- `Spring`事务是基于`AOP`动态代理的，需要使用代理对象调用加了事务的方法，`Spring`事务才能生效，而在一个方法中使用`this.xxx()`调用方法时，`this`并不是代理对象，所有会导致事务注解`@Transactional`失效。
  - 解决方法1：把调用方法拆分到另一个`Bean`中。
  - 解决方法2：自己注入自己。
  - 解决方法3：`AopContent.currentProxy() + @EnableAspectJAutoProxy(exposeProxy = true)`

~~~java
// 事务失效演示代码
public class OrderService {
    @Transactional
    public void a() {
        ...insert //对数据库的插入操作 
            throw new RuntimeException();
    }

    public void b() {
        a();
    }
}
// 插入操作成功，这就说明a方法的事务并没有生效，原因就是调用a方法的对象并不是代理对象，而是this对象
~~~

~~~java
// 事务失效解决方案1 把调用方法拆分到另一个Bean中。
public class UserService {
    @Autowired
    private OrderService orderService;
    
    public void b() {
        oderService.a();
    }
}
~~~

~~~java
// 事务失效解决方案2 自己注入自己。
public class UserService {
    @Autowired
    private UserService userService;
    
    @Autowired
    private OrderService orderService;

    @Transactional
    public void a() {
        ...insert //对数据库的插入操作 
            throw new RuntimeException();
    }

    @Transactional
    public void b() {
        userService.a();
    }
}
~~~

~~~java 
// 事务失效解决方案3
public class UserService {
    @Autowired
    private OrderService orderService;
    
    public void b() {
        UserService userService = (UserService) AopContent.currentProxy();
        userService.a();
    }
}

// @EnableAspectJAutoProxy(exposeProxy = true) exposeProxy表示暴露代理对象
~~~



##### 2、方法被`private`修饰

- `Spring`事务会基于`CGLIB`来进行`AOP`，而`CGLIB`会基于父子类来生效，子类是代理类，父类是被代理类，如果父类中某个方法是`private`的，那么子类就没有办法重写它，也就没有办法额外增加`Spring`事务的逻辑。



##### 3、方法是`final`修饰

- 原因和`private`是一样的，也是由于子类不能重写父类中的`final`方法。



##### 4、单独的线程调用方法

- 当`Mybatis`或`JdbcTemplate`执行`SQL`时，会从`ThreadLocal`中获取数据库连接对象，如果开启事务的线程和执行`SQL`的线程是同一个，那么就能拿到数据库连接对象，如果不是同一个对象，那就拿不到数据库连接对象，这样，`Mybaits`或`JdbcTemplate`就会自己去新建一个数据库连接用来执行`SQL`，此数据库连接的`autocommit`为`true`，那么执行完`SQL`就会提交，后续再抛异常也就不能再回滚之前已经提交了的`SQL`了。

~~~java
@Transactional
public void a() {
    new Thread({
        public void run() {
            ...insert //对数据库的插入操作 
                throw new RuntimeException();
        }
    }).start(); 
}
~~~



##### 5、没加`@Configuration`注解

- 如果用`SpringBoot`基本没有这个问题，但是如果用的是`Spring`，那么可能会有这个问题，这个问题的原因其实也是由于`Mybatis`或`JdbcTemplate`会从`ThreadLocal`中去获取数据库连接，但是`ThreadLocal`中存储的是一个`Map`，`Map`的`key`为`DataSource`对象，`value`为连接对象，而如果我们没有在`AppConfig`上添加`@Configuration`注解的话，会导致`Map`中存的`DataSource`对象和`Mybatis`以及`JdbcTemplate`中的`DataSource`对象不相等，从而也拿不到数据库连接，导致自己去创建数据库连接了。



##### 6、异常被吃掉

- 如果`Spring`事务没有捕获到异常，那么也就不会回滚了，默认情况下`Spring`会捕获`RuntimeException`和`Error`。



##### 7、类没有被`Spring`管理



##### 8、数据库不支持事务





## 5、Spring框架新功能

- 整个Spring5框架的代码基于Java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除




### 5.1、Spring5框架自带了通用的日志封装

Spring5已经移除了Log4jConfigListener，官方建议使用Log4j2

Spring5框架整合Log4j2

- 引入相关依赖

![image-20230407224725469](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230407224725469.png)

- 创建log4j2.xml配置文件，名字不能更改


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->
<configuration status="INFO">
    <!--先定义所有的appender-->
    <appenders>
        <!--输出日志信息到控制台-->
        <console name="Console" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </console>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <!--root：用于指定项目的根日志，如果没有单独指定Logger，则会使用root作为默认的日志输出-->
    <loggers>
        <root level="info">
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```



### 5.2、`Spring5`框架核心容器支持`@Nullable`注解

- `@Nullable`注解可以使用在方法、属性、参数上，表示方法返回、属性值、参数值可以为空。



### 5.3、Spring5核心容器支持函数式风格GenericApplicationContext

```java
//1.创建GenericApplicationContext对象
GenericApplicationContext genericApplicationContext = new GenericApplicationContext();
//2.调用context的方法对象注册
genericApplicationContext.refresh();
genericApplicationContext.registerBean("user1",User.class,() -> new User());
//3.获取在spring注册的对象
User user = (User) genericApplicationContext.getBean("user1");//也可写对象类的全限定类名
System.out.println(user);
```



### 5.4、Spring5支持整合JUnit5

#### 5.4.1、整合JUnit4

引入相关依赖

![image-20230407225104947](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230407225104947.png)

创建测试类，使用注解方式

```java
@RunWith(SpringJUnit4ClassRunner.class)//单元测试框架，指定当前测试类在Spring的测试环境中执行，此时就可以通过注入的方式直接获取IOC容器中的Bean
@ContextConfiguration("classpath:bean1.xml")//加载spring配置文件
public class JTest4 {
    @Autowired
    private UserService userService;
    @Test
    public void test1() {
        userService.transfer();
    }
}
```



#### 5.4.2、整合JUnit5

引入相关依赖

![image-20230407224927890](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230407224927890.png)

创建测试类

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:bean1.xml")
public class JTest5 {
    @Autowired
    private UserService userService;
    @Test
    public void test1() {
        userService.transfer();
    }
}

@SpringJUnitConfig(locations = "classpath:bean1.xml")//代替上面两个注解
public class JTest5 {
    @Autowired
    private UserService userService;

    @Test
    public void test1() {
        userService.transfer();
    }
}
```





























































































































































































































