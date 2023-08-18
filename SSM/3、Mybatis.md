## 1、简介

### 1.1、Mybatis 的历史

MyBatis最初是Apache的一个开源项目iBatis, 2010年6月这个项目由Apache Software Foundation迁移到了Google Code。随着开发团队转投Google Code旗下， iBatis3.x正式更名为MyBatis。代码于 2013年11月迁移到Github。

iBatis一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。 iBatis提供的持久层框架包括SQL Maps和Data Access Objects（DAO）。



### 1.2、Mybatis 的特性

MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架

MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集

MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录

MyBatis 是一个半自动的ORM（Object Relation Mapping）框架



### 1.3、Mybatis下载

MyBatis下载地址：https://github.com/mybatis/mybatis-3



### 1.4、和其他持久层技术对比

#### 1.4.1、JDBC

SQL夹杂在Java代码中耦合度高，导致硬编码内伤

维护不易且实际开发需求中 SQL 有变化，频繁修改的情况多见

代码冗长，开发效率低



#### 1.4.2、Hibernate 和 JPA

操作简便，开发效率高

程序中的长难复杂 SQL 需要绕过框架

内部自动生产的 SQL，不容易做特殊优化

基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难

反射操作太多，导致数据库性能下降



#### 1.4.3、Mybatis

轻量级，性能出色

SQL 和 Java 编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据

开发效率稍逊于HIbernate，但是完全能够接受





## 2、搭建 Mybatis 环境

### 2.1、开发环境

IDE：idea 2019.2

构建工具：maven 3.5.4

MySQL版本：MySQL 5.7

MyBatis版本：MyBatis 3.5.7

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

打包方式：jar包

引入依赖

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

习惯上命名为mybatis-config.xml，这个文件名仅仅只是建议，并非强制要求。将来整合Spring之后，这个配置文件可以省略，所以大家操作时可以直接复制、粘贴

核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息

核心配置文件存放的位置是src/main/resources目录下

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

MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类。

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



### 2.5、创建Mybatis 的映射文件

相关概念：ORM（Object Relationship Mapping）对象关系映射。

- 对象：Java的实体类对象
- 关系：关系型数据库
- 映射：二者之间的对应关系

![image-20230524170405648](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230524170405648.png)



#### 2.5.1、映射文件的命名规则

表所对应的实体类的类名+Mapper.xml。例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml，因此一个映射文件对应一个实体类，对应一张表的操作

MyBatis映射文件用于编写SQL，访问以及操作表中的数据

MyBatis映射文件存放的位置是src/main/resources/mappers目录下



#### 2.5.2、两个一致

mapper接口的全类名和映射文件的命名空间（namespace）保持一致

mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致

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

SqlSession：代表Java程序和数据库之间的会话。（HttpSession是Java程序和浏览器之间的会话）

SqlSessionFactory：是“生产”SqlSession的“工厂”。

工厂模式：如果创建某一个对象，使用的过程基本固定，那么我们就可以把创建这个对象的相关代码封装到一个“工厂类”中，以后都使用这个工厂类来“生产”我们需要的对象。



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

log4j的配置文件名为log4j.xml，存放的位置是src/main/resources目录下

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

日志的级别

- FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试)从左到右打印的内容越来越详细



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

- ${}的本质就是字符串拼接，#{}的本质就是占位符赋值
- ${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号

- \#{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号。可以防止sql注入



### 4.1、单个字面量类型的参数

若mapper接口中的方法参数为单个的字面量类型，此时可以使用${}和#{}以任意的参数名称获取参数的值，注意${}需要手动加单引号



### 4.2、多个字面量类型的参数

若mapper接口中的方法参数为多个时，此时MyBatis会自动将这些参数放在一个map集合中，以arg0、arg1...为键，以参数为值；以param1、param2...为键，以参数为值；因此只需要通过${}或#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号

```xml
<select id="getUser" resultType="user">
    select * from t_user where user_name = #{param1} and password = #{param2};
</select>
```



### 4.3、Map集合类型的参数

若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中，只需要通过${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号

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

若mapper接口中的方法参数为实体类对象时，此时可以使用${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号

```java
void insert(User user);
```

```xml
<insert id="insert">
    insert into t_user values(null,#{userName},#{password},#{userAge},#{userSex},#{userEmail});
</insert>
```



### 4.5、使用@Param标识参数

可以通过@Param注解标识mapper接口中的方法参数，此时，会将这些参数放在**map集合**中，以@Param注解的value属性值为键，以参数为值；以param1、param2...为键，以参数为值；只需要通过${}和#{}访问map集合的键就可以获取相对应的值， 注意${}需要手动加单引号

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
> 查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射关系
>
> - resultType：自动映射，用于属性名和表中字段名一致的情况
> - resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况
>
> 当查询的数据为多条时，不能使用实体类作为返回值，只能使用集合，否则会抛出异常 TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值



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

当查询的数据为多条时，不能使用实体类作为返回值，否则会抛出异常 TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值



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

方式一：

```java
List<Map<String,Object>> getAllToMap();
```

```xml
<select id="getAllToMap" resultType="map">
    select * from t_user;
</select>
```

方式二：

```java
/**
* 查询所有用户信息为map集合
* @return
* 将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，并且最终要以一个map的方式返回数据，此时需要通过@MapKey注解设置   map集合的键，值是每条数据所对应的map集合
*/
@MapKey("id")
Map<String,Object> getAllToMap();
```

```xml
{3={user_sex=女, password=123, user_email=165@qq.com, user_name=李四, user_age=23, id=3}, 4={user_sex=女, password=123, user_email=165@qq.com, user_name=张三, user_age=23, id=4}, 5={user_sex=男, password=234, user_email=2313@qq.com, user_name=王五, user_age=23, id=5}}
```





## 6、特殊SQL执行

### 6.1、模糊查询









## 7、自定义映射resultMap









## 8、动态SQL









## 9、Mybatis的缓存





## 10、Mybatis的逆向工程





## 11、分页插件