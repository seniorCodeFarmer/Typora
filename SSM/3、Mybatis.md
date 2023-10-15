## 1、简介

### 1.1、Mybatis 的历史

- MyBatis最初是Apache的一个开源项目iBatis, 2010年6月这个项目由Apache Software Foundation迁移到了Google Code。随着开发团队转投Google Code旗下， iBatis3.x正式更名为MyBatis。代码于 2013年11月迁移到Github。

- iBatis一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。 iBatis提供的持久层框架包括SQL Maps和Data Access Objects（DAO）。




### 1.2、Mybatis 的特性

- MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架

- MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集

- MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录

- MyBatis 是一个半自动的ORM（Object Relation Mapping）框架




### 1.3、Mybatis下载

- MyBatis下载地址：https://github.com/mybatis/mybatis-3




### 1.4、和其他持久层技术对比

#### 1.4.1、JDBC

- SQL夹杂在Java代码中耦合度高，导致硬编码内伤

- 维护不易且实际开发需求中 SQL 有变化，频繁修改的情况多见

- 代码冗长，开发效率低




#### 1.4.2、Hibernate 和 JPA

- 操作简便，开发效率高

- 程序中的长难复杂 SQL 需要绕过框架

- 内部自动生产的 SQL，不容易做特殊优化

- 基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难

- 反射操作太多，导致数据库性能下降




#### 1.4.3、Mybatis

- 轻量级，性能出色

- SQL 和 Java 编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据

- 开发效率稍逊于HIbernate，但是完全能够接受




## 2、搭建 Mybatis 环境

### 2.1、开发环境

- IDE：idea 2019.2

- 构建工具：maven 3.5.4

- MySQL版本：MySQL 5.7

- MyBatis版本：MyBatis 3.5.7


```
MySQL不同版本的注意事项
1、驱动类driver-class-name 
MySQL 5版本使用jdbc5驱动，驱动类使用：com.mysql.jdbc.Driver 
MySQL 8版本使用jdbc8驱动，驱动类使用：com.mysql.cj.jdbc.Driver 

2、连接地址url 
MySQL 5版本的url： 
jdbc:mysql://localhost:3306/ssm 

MySQL 8版本的url： 
jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC 
否则运行测试用例报告如下错误： java.sql.SQLException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more
```



### 2.2、创建Maven工程

- 打包方式：jar包

- 引入依赖


```xml
<dependencies>
    <!-- Mybatis核心 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.3</version>
    </dependency>
</dependencies>
```



### 2.3、创建Mybatis的核心配置文件

- 习惯上命名为mybatis-config.xml，这个文件名仅仅只是建议，并非强制要求。将来整合Spring之后，这个配置文件可以省略，操作时可以直接复制、粘贴

- 核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息

- 核心配置文件存放的位置是src/main/resources目录下


```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--设置连接数据库的环境，可以配置多个环境，默认使用development-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url"  value="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>
</configuration>
```



### 2.4、创建Mapper接口

- MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类。


```java
public interface UserMapper {
    /**
     * 添加用户
     *
     * @return
     */
    int insertUser();
}
```



### 2.5、创建 Mybatis 的映射文件

相关概念：ORM（Object Relationship Mapping）对象关系映射。

- 对象：Java的实体类对
- 关系：关系型数据库
- 映射：二者之间的对应关系

![image-20230524170405648](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230524170405648.png)



#### 2.5.1、映射文件的命名规则

- 表所对应的实体类的类名+Mapper.xml。例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml，因此一个映射文件对应一个实体类，对应一张表的操作

- MyBatis映射文件用于编写SQL，访问以及操作表中的数据

- MyBatis映射文件存放的位置是src/main/resources/mappers目录下




#### 2.5.2、两个一致

- mapper接口的全类名和映射文件的命名空间（namespace）保持一致

- mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致


```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.atguigu.dao.UserMapper">
    <!--int insertUser();-->
    <insert id="insertUser">
        insert into t_user values(null,'张三','123',23,'女','165@qq.com')
    </insert>
</mapper>
```



### 2.6、通过Junit测试功能

```java
@Test
public void test1() throws IOException {
    //加载核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
    //获取SQLSessionFactoryBuilder
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    //获取sqlSessionFactory
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
    //获取sqlSession
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);事务自动提交
    //获取mapper接口对象,底层使用代理模式
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    int result = userMapper.insertUser();

    System.out.println("result : " + result);
    //提交事务
    sqlSession.commit();

    sqlSession.close();
}
```

- SqlSession：代表Java程序和数据库之间的会话。（HttpSession是Java程序和浏览器之间的会话）

- SqlSessionFactory：是“生产”SqlSession的“工厂”。

- 工厂模式：如果创建某一个对象，使用的过程基本固定，那么我们就可以把创建这个对象的相关代码封装到一个“工厂类”中，以后都使用这个工厂类来“生产”我们需要的对象。




### 2.7、加入log4j日志功能

#### 2.7.1、加入依赖

```xml
<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



#### 2.7.2、加入log4j的配置文件

- log4j的配置文件名为log4j.xml，存放的位置是src/main/resources目录下。
- 日志的级别：

  - 。FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试)从左到右打印的内容越来越详细

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n" />
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug" />
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>
    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>
</log4j:configuration>
```



## 3、核心配置文件

核心配置文件中的标签必须按照固定的顺序：

- properties
- settings
- typeAliases
- typeHandlers
- objectFactory
- objectWrapperFactory
- reflectorFactory
- plugins
- environments
- databaseIdProvider
- mappers

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//MyBatis.org//DTD Config 3.0//EN"
        "http://MyBatis.org/dtd/MyBatis-3-config.dtd">
<configuration>
    <!--引入properties文件，此时就可以${属性名}的方式访问属性值-->
    <properties resource="jdbc.properties"></properties>
    <settings>
        <!--将表中字段的下划线自动转换为驼峰-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
    </settings>
    <typeAliases>
        <!--
            typeAlias：设置某个具体的类型的别名
            属性：
            type：需要设置别名的类型的全类名
            alias：设置此类型的别名，若不设置此属性，该类型拥有默认的别名，即类名且不区分大小写
            若设置此属性，此时该类型的别名只能使用alias所设置的值
        -->
        <!--<typeAlias type="com.atguigu.mybatis.bean.User"></typeAlias>-->
        <!--<typeAlias type="com.atguigu.mybatis.bean.User" alias="abc">
        </typeAlias>-->
        <!--以包为单位，设置改包下所有的类型都拥有默认的别名，即类名且不区分大小写-->
        <package name="com.atguigu.mybatis.bean"/>
    </typeAliases>
    <!--
        environments：设置多个连接数据库的环境
        属性：
            default：设置默认使用的环境的id
    -->
    <environments default="mysql_test">
        <!--
        environment：设置具体的连接数据库的环境信息
        属性：
            id：表示连接数据库的环境的唯一标识，可通过environments标签中的default设置某一个环境的id，表示默认使用的环境
    -->
        <environment id="mysql_test">
            <!--
            transactionManager：设置事务管理方式
            属性：
                type：设置事务管理方式，type="JDBC|MANAGED"
                type="JDBC"：设置当前环境的事务管理都必须手动处理
                type="MANAGED"：设置事务被管理，例如spring中的AOP
            -->
            <transactionManager type="JDBC"/>
            <!--
                dataSource：设置数据源
                属性：
                type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
                type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从缓存中直接获取，不需要重新创建
                type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
                type="JNDI"：调用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <!--设置驱动类的全类名-->
                <property name="driver" value="${jdbc.driver}"/>
                <!--设置连接数据库的连接地址-->
                <property name="url" value="${jdbc.url}"/>
                <!--设置连接数据库的用户名-->
                <property name="username" value="${jdbc.username}"/>
                <!--设置连接数据库的密码-->
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <mapper resource="UserMapper.xml"/>
        <!--
            以包为单位，将包下所有的映射文件引入核心配置文件
            注意：此方式必须保证mapper接口和mapper映射文件所在的包的路径名相同、mapper接口和映射文件名字一致
        -->
        <package name="com.atguigu.mybatis.mapper"/>
    </mappers>
    <!--
    <mappers>
        <!--该包、及子包 映射接口实现全部注册为映射器-->
    	<package name="com.yichun.dao"/>

    	<!--相对于类路径的资源引用方式-->
    	<mapper resource="com/yichun/dao/IuserDao.xml"/>

    	<!--映射器接口实现类的完全限定类名-->
    	<mapper class="com.yichun.dao.IUserMapper"></mapper>
        <mapper class="com.yichun.dao.OrderMapper"></mapper>
    </mappers>
	-->
</configuration>
```



## 4、Mybatis获取参数值的两种方式（重点）

MyBatis获取参数值的两种方式：${}和#{}

- ${}的本质就是字符串拼接，#{}的本质就是占位符赋值。
- ${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号。

- \#{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号。可以防止sql注入。



### 4.1、单个字面量类型的参数

- 若mapper接口中的方法参数为单个的字面量类型，此时可以使用${}和#{}以任意的参数名称获取参数的值，注意${}需要手动加单引号。




### 4.2、多个字面量类型的参数

- 若mapper接口中的方法参数为多个时，此时MyBatis会自动将这些参数放在一个map集合中，以arg0、arg1...为键，以参数为值；以param1、param2...为键，以参数为值；因此只需要通过${}或#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号。


```xml
<select id="getUser" resultType="user">
    select * from t_user where user_name = #{param1} and password = #{param2};
</select>
```



### 4.3、Map集合类型的参数

- 若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中，只需要通过${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号。


```java
User checkLoginByMap(Map<String,Object> map);
```

```xml
<select id="checkLoginByMap" resultType="user">
    select * from t_user where user_name = #{userName} and password = #{password};
</select>
```

```java
@Test
public void test3() {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
    Map<String,Object> map = new HashMap<String, Object>();
    map.put("userName","张三");
    map.put("password","123");
    User user = mapper.checkLoginByMap(map);

    System.out.println(user);
}
```



### 4.4、实体类类型的参数

- 若mapper接口中的方法参数为实体类对象时，此时可以使用${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号。


```java
void insert(User user);
```

```xml
<insert id="insert">
    insert into t_user values(null,#{userName},#{password},#{userAge},#{userSex},#{userEmail});
</insert>
```



### 4.5、使用@Param标识参数

- 可以通过@Param注解标识mapper接口中的方法参数，此时，会将这些参数放在**map集合**中，以@Param注解的value属性值为键，以参数为值；以param1、param2...为键，以参数为值；只需要通过${}和#{}访问map集合的键就可以获取相对应的值， 注意${}需要手动加单引号。


```java
User getUser(@Param("userName") String userName, @Param("password") String password);
```

```xml

<select id="getUser" resultMap="selectMap">
    select * from t_user where user_name = #{userName} and password = #{password};
</select>
```



## 5、Mybatis的各种查询功能

### 5.1、查询一个实体类对象

```java
User selectById(@Param("id") Integer id);
```

```xml
<select id="selectById" resultType="user">
    select * from t_user where id = #{id};
</select>
```

> 注：
>
> - 查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射关系。
>
>   - resultType：自动映射，用于属性名和表中字段名一致的情况。
>
>   - resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况。
>
>
> - 当查询的数据为多条时，不能使用实体类作为返回值，只能使用集合，否则会抛出异常TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值。



### 5.2、查询一个List集合

```java
List<User> selectAll();
```

```xml
<select id="selectAll" resultMap="userMap">
    select * from t_user;
</select>

<select id="getAllUser" resultType="user"> <!--这样写也能返回正确结果-->
    select * from t_user;
</select>
```

- 当查询的数据为多条时，不能使用实体类作为返回值，否则会抛出异常 TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值。




### 5.3、查询单个数据

```java
/**
* 查询用户的总记录数
* @return
* 在MyBatis中，对于Java中常用的类型都设置了类型别名
* 例如：java.lang.Integer-->int|integer
* 例如：int-->_int|_integer
* 例如：Map-->map,List-->list
*/
int getCount();
```

```xml
<!--int getCount();-->
<select id="getCount" resultType="integer">
    select count(*) from t_user;
</select>
```



### 5.4、查询一条数据为map集合

```java
Map<String,Object> getUserByIdToMap(@Param("id") Integer id);
```

```xml
<select id="getUserByIdToMap" resultType="map"> <!--映射成map-->
    select * from t_user where id = #{id};
</select>
```

```
{user_sex=女, password=123, user_email=165@qq.com, user_name=李四, user_age=23, id=3}
```



### 5.5、查询多条数据为map集合

- 方式一：


```java
List<Map<String,Object>> getAllToMap();
```

```xml
<select id="getAllToMap" resultType="map">
    select * from t_user;
</select>
```

- 方式二：


```java
/**
* 查询所有用户信息为map集合
* @return
* 将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，并且最终要以一个map的方式返回数据，此时需要通过@MapKey注解设置map集合的键，值是每条数据所对应的map集合
*/
@MapKey("id")
Map<String,Object> getAllToMap();
```

```xml
{3={user_sex=女, password=123, user_email=165@qq.com, user_name=李四, user_age=23, id=3}, 4={user_sex=女, password=123, user_email=165@qq.com, user_name=张三, user_age=23, id=4}, 5={user_sex=男, password=234, user_email=2313@qq.com, user_name=王五, user_age=23, id=5}}
```





## 6、特殊SQL执行

### 6.1、模糊查询

~~~xml
// 方式一：
/**
* 测试模糊查询
* @param userName
* @return
*/
List<User> getUserByName(@Param("userName") String userName);

<select id="getUserByName" resultMap="selectMap">
    select * from t_user where user_name like '%${userName}%';
</select>
~~~

~~~xml
// 方式二：
<select id="getUserByName" resultMap="selectMap">
    select * from t_user where user_name like concat('%',#{userName},'%');
</select>
~~~

~~~xml
// 方式三：建议使用
<select id="getUserByName" resultMap="selectMap">
    select * from t_user where user_name like "%"#{userName}"%";
</select>
~~~



### 6.2、批量删除

~~~xml
/*** 批量删除* @param ids* @return*/
int deleteMore(@Param("ids") String ids);
// int deleteMore(@Param("ids") String ids);
<delete id="deleteMore">   
    delete from t_user where id in (${ids}) <--!因为#{}会自动添加单引号，会导致执行的SQL语句错误-->
</delete>
~~~



### 6.3、动态设置表名

~~~xml
/**
* 动态设置表名，查询所有的用户信息
* @param tableName
* @return
*/
List<User> getAllUser(@Param("tableName") String tableName);

<!--List<User> getAllUser(@Param("tableName") String tableName);-->
<select id="getAllUser" resultType="User">
    select * from ${tableName}
</select>
~~~



### 6.4、添加功能获取自增的主键

~~~
t_clazz(clazz_id,clazz_name)
t_student(student_id,student_name,clazz_id) //一对多，表关系设置在多的一方
~~~

- 添加班级信息 
- 获取新添加的班级的id 
- 为班级分配学生，即将某学的班级id修改为新添加的班级的id

~~~java 
/**
* 添加用户信息
* @param user
* @return
* useGeneratedKeys：设置当前标签中的sql使用自增的主键
* keyProperty：将自增的主键的值赋值给传输到映射文件中参数的某个属性，因为增删改有统一的返回值是受影响的行数，因此只能将获取的自增的主键放在传输的参数user对象的某个属性中
*/
int insertUser(User user);
~~~

~~~xml 
<!--int insertUser(User user);-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values(null,#{username},#{password},#{age},#{sex})
</insert>
~~~



## 7、自定义映射resultMap

- 解决字段名和属性名不一致的情况
  - 为字段起别名，保持和属性名一致
  - 设置全局配置，将_自动映射成驼峰
  - 使用resultMap

~~~xml
<!--全局配置-->
<settings>
    <!--将_自动映射为驼峰，emp_name -> empName-->
    <setting name="mpaUndrescoreToCamelCase" value="true"/>
</settings>
~~~



### 7.1、resultMap处理字段和属性的映射关系

- 若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射

~~~xml
<!--
    resultMap：设置自定义映射
        属性：
            id：表示自定义映射的唯一标识
            type：查询的数据要映射的实体类的类型
    子标签：
        id：设置主键的映射关系
        result：设置普通字段的映射关系
        association：设置多对一的映射关系
        collection：设置一对多的映射关系
        属性：
            property：设置映射关系中实体类中的属性名
            column：设置映射关系中表中的字段名
-->
<resultMap id="selectMap" type="Emp">
    <id property="eid" column="e_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="did" column="d_id"></result>
</resultMap>
~~~

- 若字段名和实体类中的属性名不一致，但是字段名符合数据库的规则（使用_），实体类中的属性名符合Java的规则（使用驼峰）此时也可通过以下两种方式处理字段名和实体类中的属性的映射关系

- - 可以通过为字段起别名的方式，保证和实体类中的属性名保持一致

  - 可以在MyBatis的核心配置文件中设置一个全局配置信息mapUnderscoreToCamelCase，可以在查询表中数据时，自动将_类型的字段名转换为驼峰

  - - 例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为userName



### 7.2、多对一映射

#### 7.2.1、级联属性赋值

~~~xml
<resultMap id="selectMap" type="Emp">
    <id property="eid" column="e_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="dept.did" column="d_id"></result>
    <result property="dept.deptName" column="dept_name"></result>
</resultMap>

<select id="getEmp" resultMap="selectMap">
    select * from t_emp left join t_dept on t_emp.d_id = t_dept.d_id where t_emp.e_id = #{eid};
</select>
~~~



#### 7.2.2、使用association处理映射关系

~~~xml
<!--
association：处理多对一的映射关系
    property：需要处理多的映射关系的属性名
    javaType：该属性的类型
-->
<resultMap id="selectMap" type="Emp">
    <id property="eid" column="e_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <association property="dept" javaType="Dept">
        <id property="did" column="d_id"></id>
        <result property="deptName" column="dept_name"></result>
    </association>
</resultMap>
~~~



#### 7.2.3、分步查询（用于Mybatis延迟加载）

- 优点：可以实现延迟加载，但是必须在核心配置文件中设置全局配置信息。

- - LazyLoadingEnabled：延迟加载的全局开关。设置为true为开启，当开启时，所有关联对象都会延迟加载。
  - aggressiveLazyLoading：设置为true为开启，当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属性会按需加载。

![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)

- 懒加载针对什么使用的？为什么要用懒加载？

- - 懒加载针对级联使用的，懒加载的目的是减少内存的浪费和减轻系统负担。

- 懒加载是什么？

- - 你可以理解为按需加载，当我调用到关联的数据时才与数据库交互否则不交互。再具体点来说：比如user表和role表有关联关系，有这样一条语句：查询uesr的同时将user的某一列数据作为参数一并查询role表符合条件的数据，mybatis里叫做级联。只要执行这条语句，就会将这两张表符合需求的信息一起加载出来。而懒加载只会加载uesr表的数据出来不加载role表的数据

- 此时就可以实现按需加载，获取的数据是什么，就只会执行相应的sql。此时可通过association和collection中的fetchType属性设置当前的分步查询是否使用延迟加载，fetchType="lazy（延迟加载）| eager（立即加载）"，当开启了全局的延迟加载之后，可用过此属性手动控制延迟加载的效果

~~~java
/**
* 分布查询第一步：查询员工信息
* @return
*/
Emp getEmpAndDeptByStep(@Param("eid") Integer eid);
~~~

~~~xml
<!--
    select：设置分步查询的sql的唯一标识(namespace.SQLId或mapper接口的全类名.方法名)
    column：设置分步查询的条件参数
-->
<resultMap id="empAndDeptByStep" type="Emp">
    <id property="eid" column="e_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <association property="dept" select="com.atguigu.mybatis.mapper.DeptMapper.getDeptByStep" column="d_id">
    </association>
</resultMap>

<select id="getEmpAndDeptByStep" resultMap="empAndDeptByStep">
    select * from t_emp where id = #{eid}
</select>
~~~

~~~ java
/**
* 分布查询第二步：查询部门信息
* @return
*/
Dept getDeptByStep(@Param("did") Integer did);
~~~

~~~xml
<resultMap id="deptMap" type="Dept">
    <id property="did" column="d_id"></id>
    <result property="deptName" column="dept_name"></result>
</resultMap>

<select id="getDeptByStep" resultMap="deptMap">
    select * from t_dept where did = #{did}
</select>
~~~



### 7.3、一对多映射处理

#### 7.3.1、使用collection

- ofType：表示该属性所对应的集合中存储数据的类型

~~~xml
<resultMap id="deptAndEmp" type="Dept">
    <id property="did" column="d_id"></id>
    <result property="deptName" column="dept_name"></result>
    <collection property="emp" ofType="Emp">
        <id property="eid" column="e_id"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
    </collection>
</resultMap>

<select id="getDeptAndEmp" resultMap="deptMap">
    select * from t_dept left join t_emp on t_dept.d_id = t_emp.d_id where t_dept.d_id = #{did}
</select>
~~~



#### 7.3.2、分步查询

- 查询部门信息

~~~java
/**
* 分步查询部门和部门中的员工
* @param did
* @return
*/
Dept getDeptByStep(@Param("did") int did);
~~~

~~~xml
<resultMap id="deptEmpStep" type="Dept">
    <id property="did" column="did"></id>
    <result property="dname" column="dname"></result>
    <collection property="emps" fetchType="eager"
                select="com.atguigu.MyBatis.mapper.EmpMapper.getEmpListByDid" column="did">
    </collection>
</resultMap>
<!--Dept getDeptByStep(@Param("did") int did);-->
<select id="getDeptByStep" resultMap="deptEmpStep">
    select * from t_dept where did = #{did}
</select>
~~~

- 根据部门id查询部门中所有员工

~~~java
/**
* 根据部门id查询员工信息
* @param did
* @return
*/
List<Emp> getEmpListByDid(@Param("did") int did);
~~~

~~~xml
<!--List<Emp> getEmpListByDid(@Param("did") int did);-->
<select id="getEmpListByDid" resultType="Emp">
    select * from t_emp where did = #{did}
</select>
~~~





## 8、动态SQL

Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。

### 8.1、if

- if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行
- 1 = 1 恒成立条件，防止where后面的条件直接是and ....

~~~xml
List<Emp> getEmpByCondition(Emp Emp);
    
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp where 1 = 1
    <if test="empName != null and empName !=''">
        and emp_name = #{empName}
    </if>
    <if test="age != null and age != ''">
        and age = #{age}
    </if>
</select>
~~~



### 8.2、where

- where和if一般结合使用：

- - 若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字
  - 若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件前方多余的and或or去掉
  - 注意：where标签不能去掉条件后面多余的and

~~~xml
List<Emp> getEmpByCondition(Emp Emp);
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp
    <where>
        <if test="empName != null and empName !=''">
            emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
    </where>
</select>
~~~



### 8.3、trim

- trim用于去掉或添加标签中的内容

- 常用属性：

- - prefix：在trim标签中的内容的前面添加某些内容
  - suffix：在trim标签中的内容的后面添加某些内容
  - prefixOverrides：在trim标签中的内容的前面去掉某些内容
  - suffixOverrides：在trim标签中的内容的后面去掉某些内容

- 若标签中的if条件都不满足时，trim标签没有效果

~~~xml 
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and|or">
        <if test="empName != null and empName !=''">
            emp_name = #{empName} and
        </if>
        <if test="age != null and age != ''">
            age = #{age}
        </if>
    </trim>
</select>
~~~



### 8.4、choose、when、otherwise

- choose（父标签）、when（至少有一个）、otherwise（最多有一个）相当于if...else if..else

- - 有一个when成立，其他when都不会执行
  - 没有一个when成立，则执行otherwise

~~~xml 
<!--List<Emp> getEmpListByChoose(Emp emp);-->
<select id="getEmpListByChoose" resultType="Emp">
    select <include refid="empColumns"></include> from t_emp
    <where>
        <choose>
            <when test="empName != '' and ename != null">
                emp_name = #{empName}
            </when>
            <when test="age != '' and age != null">
                age = #{age}
            </when>
            <otherwise>
                d_id = 1
            </otherwise>
        </choose>
    </where>
</select>
~~~



### 8.5、foreach

- 属性

- - collection：设置要循环的数组或集合
  - item：表示集合或数组中的每一个数据
  - separator：设置循环体之间的分隔符
  - open：设置foreach标签中的内容的开始符
  - close：设置foreach标签中的内容的结束符

~~~xml
<!--int insertMoreEmp(@Param("emps") List<Emp> emps);-->
<insert id="insertMoreEmp">
    insert into t_emp values
    <foreach collection="emps" item="emp" separator=",">
        (null,#{emp.empName},#{emp.age},null)
    </foreach>
</insert>

<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray">
    delete from t_emp where
    <foreach collection="eids" item="eid" separator="or">
        eid = #{eid}
    </foreach>
</delete>

<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray">
    delete from t_emp where eid in
    <foreach collection="eids" item="eid" separator="," open="(" close=")">
        #{eid}
    </foreach>
</delete>
~~~



### 8.6、SQL片段

- sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入

~~~xml
<sql id="empColumns">
    eid,ename,age,sex,did
</sql>
select <include refid="empColumns"></include> from t_emp
~~~



## 9、Mybatis的缓存

### 9.1、Mybatis的一级缓存

- 一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问。

- 使一级缓存失效的四种情况：

- - 不同的SqlSession对应不同的一级缓存。
  - 同一个SqlSession但是查询条件不同。
  - 同一个SqlSession两次查询期间执行了任何一次增删改操作（任意一次增删改会清空缓存）。
  - 同一个SqlSession两次查询期间手动清空了缓存。

~~~java
//手动清空缓存
sqlSession.clearCache();
~~~



### 9.2、Mybatis的二级缓存

- 二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取。
- 二级缓存开启的条件：
  - 在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置。
  - 在映射文件中设置标签\<cache/>。
  - 二级缓存只有在SqlSession关闭或提交之后有效（只有在关闭或提交之后，一级缓存中的数据才会保存到二级缓存）。
  - 查询的数据所转换的实体类类型必须实现序列化的接口。
- 使二级缓存失效的情况：
  - 两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

~~~xml
<cache/>
~~~



### 9.3、二级缓存的相关配置

在mapper映射文件中添加的cache标签可以设置一些属性：

- eviction属性：缓存回收策略，默认是LRU
  - LRU（Least Recently Used）最近最少使用的：移除最长时间不被使用的对象。
  - FIFO（First in First out）  先进先出：按对象进入缓存的顺序来移除它们。
  - SOFT 软引用：移除基于垃圾回收器状态和软引用规则的对象。
  - WEAK 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。 默认的是 LRU。
- flushInterval属性：刷新间隔，单位毫秒，二级缓存多久刷新一次。
  - 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新（增删改操作）
- size属性：引用数目，正整数，代表缓存最多可以存储多少个对象，太大容易导致内存溢出
- readOnly属性：只读，true/false
  - true：只读缓存；会给所有调用者返回缓存对象的相同实例，因此这些对象不能被修改。这提供了很重要的性能优势。
  - false：读写缓存；会返回缓存对象的拷贝（通过序列化），对缓存对象没有影响。这会慢一些，但是安全，因此默认是 false。



### 9.4、Mybatis缓存查询的顺序

- 先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。
- 如果二级缓存没有命中，再查询一级缓存（在某个时间点，一级缓存与二级缓存并不是包含的关系）
- 如果一级缓存也没有命中，则查询数据库
- SqlSession关闭之后，一级缓存中的数据会写入二级缓存 



### 9.5、整合第三方缓存EHCache

- 添加依赖

~~~xml
<!-- Mybatis EHCache整合包 -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.1</version>
</dependency>
<!-- slf4j日志门面（接口）的一个具体实现 -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
~~~

- 各Jar包的功能

![image-20230904181434437](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230904181434437.png)



- 创建EHCache的配置文件ehcache.xml（必须是这个名字）

~~~xml
<?xml version="1.0" encoding="utf-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="D:\atguigu\ehcache"/>
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
~~~



- 设置二级缓存的类型

~~~xml
在mapper.xml映射文件中加入
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
~~~



- 加入logback日志
  - 存在SLF4J时，作为简易日志的log4j将失效，此时我们需要借助SLF4J的具体实现logback来打印日志。 创建logback的配置文件logback.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置 -->
    <appender name="STDOUT"
              class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger][%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别 -->
    <logger name="com.atguigu.crowd.mapper" level="DEBUG"/>
</configuration>
~~~



- EHCache配置文件说明

![image-20230906153752279](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230906153752279.png)







## 10、Mybatis的逆向工程

- 正向工程：先创建Java实体类，由框架负责根据实体类生成数据库表。Hibernate是支持正向工程的。
- 逆向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源： Java实体类 Mapper接口 Mapper映射文件



### 10.1、创建逆向工程的步骤

- 添加依赖和插件

~~~xml
<!-- 依赖MyBatis核心包 -->
<dependencies>
   <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
<!-- junit测试 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
    
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>
</dependencies>
<!-- 控制Maven在构建过程中相关配置 -->
<build>
    <!-- 构建过程中用到的插件 -->
    <plugins>
        <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.0</version>
            <!-- 插件的依赖 -->
            <dependencies>
                <!-- 逆向工程的核心依赖 -->
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.2</version>
                </dependency>
                <!-- 数据库连接池 -->
                <dependency>
                    <groupId>com.mchange</groupId>
                    <artifactId>c3p0</artifactId>
                    <version>0.9.2</version>
                </dependency>
                <!-- MySQL驱动 -->
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>5.1.8</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
~~~



- 创建MyBatis的核心配置文件
- 创建逆向工程的配置文件
  - 文件名必须是：generatorConfig.xml

~~~xml 
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
        targetRuntime: 执行生成的逆向工程的版本
        MyBatis3Simple: 生成基本的CRUD（清新简洁版）
        MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3Simple">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:23306/mybatis?serverTimezone=UTC"
                        userId="root"
                        password="123456">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.atguigu.mybatis.pojo"
                            targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" /><!--使用子包，即com.atguitu.mybatis.pojo每一层都是一个包-->
            <property name="trimStrings" value="true" /><!--去除表中字段名前后空格-->
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.atguigu.mybatis.mapper"
                         targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.atguigu.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
~~~



- 执行MBG插件的generate目标


![image-20230906153950147](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230906153950147.png)



### 10.2、QBC查询

~~~java
@Test
public void testMBG() throws IOException {
    InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSession sqlSession = new
        SqlSessionFactoryBuilder().build(is).openSession(true);
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    EmpExample empExample = new EmpExample();
    //创建条件对象，通过andXXX方法为SQL添加查询添加，每个条件之间是and关系
    empExample.createCriteria().andEnameLike("a").andAgeGreaterThan(20).andDidIsNotNull();
    //将之前添加的条件通过or拼接其他条件
    empExample.or().andSexEqualTo("男");
    List<Emp> list = mapper.selectByExample(empExample);
    for (Emp emp : list) {
        System.out.println(emp);
    }
}
~~~





## 11、分页插件

### 11.1、分页插件使用步骤

- 添加依赖

~~~xml
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
~~~



- 配置分页插件：在Mybatis核心配置文件中配置

~~~xml
<plugins>
    <!--设置分页插件-->
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
~~~



### 11.2、分页插件的使用

#### 11.2.1、开启分页功能

在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能

- PageNum：当前页的页码
- PageSize：每页显示的条数

~~~java
@Test
public void test1() {
    try {
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        EmpMapper empMapper = sqlSession.getMapper(EmpMapper.class);
        //开启分页pageNum,pageSize
        Page<Object> page =  PageHelper.startPage(1, 2);
        //Page<Emp> page = empMapper.selectByExample(null);
        List<Emp> emps = empMapper.selectByExample(null);

        //查询功能之后可以获取分页相关的所有数据
        PageInfo<Emp> page = new PageInfo<>(emps,3);
        emps.forEach(emp -> System.out.println(emp));
    } catch (IOException e) {
        e.printStackTrace();
    }
}
~~~



#### 11.2.2、获取分页相关数据

在查询获取list集合之后，使用PageInfo pageInfo = new PageInfo<>(List list, int navigatePages)获取分页相关数据

- list：分页之后的数据
- navigatePages：导航分页的页码数

~~~java
PageInfo<Emp> page = new PageInfo<>(emps,3);
~~~



#### 11.2.3、分页相关数据

~~~java
PageInfo{ pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, pages=8, list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, total=30, pages=8, reasonable=false, pageSizeZero=false}, prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, hasPreviousPage=true, hasNextPage=false, navigatePages=5, navigateFirstPage4, navigateLastPage8, navigatepageNums=[4, 5, 6, 7, 8] }
~~~

- 常用数据

- - pageNum：当前页的页码
  - pageSize：每页显示的条数
  - size：当前页显示的真实条数 
  - total：总记录数
  - pages：总页数
  - prePage：上一页的页码
  - nextPage：下一页的页码
  - isFirstPage/isLastPage：是否为第一页/最后一页
  - hasPreviousPage/hasNextPage：是否存在上一页/下一页
  - navigatePages：导航分页的页码数，5
  - navigatepageNums：导航分页的页码，[1,2,3,4,5]































