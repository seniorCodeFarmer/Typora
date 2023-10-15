# 实战篇Redis




![1653056228879](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653056228879.png)



## 1、短信登录

### 1.1、导入黑马点评项目

#### 1.1.1 、导入`SQL`

#### 1.1.2、有关当前模型

- 手机或者`app`端发起请求，请求我们的`nginx`服务器，`nginx`基于七层模型走的是`HTTP`协议，可以实现基于`Lua`直接绕开`tomcat`访问`redis`，也可以作为静态资源服务器，轻松扛下上万并发， 负载均衡到下游`tomcat`服务器，打散流量，我们都知道一台4核8G的`tomcat`，在优化和处理简单业务的加持下，大不了就处理`1000`左右的并发， 经过`nginx`的负载均衡分流后，利用集群支撑起整个项目，同时`nginx`在部署了前端项目后，更是可以做到动静分离，进一步降低`tomcat`服务的压力，这些功能都得靠`nginx`起作用，所以`nginx`是整个项目中重要的一环。

- 在`tomcat`支撑起并发流量后，我们如果让`tomcat`直接去访问`Mysql`，根据经验`Mysql`企业级服务器只要上点并发，一般是`16`或`32`核心`cpu`，`32`或`64G`内存，像企业级`Mysql`加上固态硬盘能够支撑的并发，大概就是`4000起~7000`左右，上万并发， 瞬间就会让`Mysql`服务器的`cpu`，硬盘全部打满，容易崩溃，所以我们在高并发场景下，会选择使用`Mysql`集群，同时为了进一步降低`Mysql`的压力，同时增加访问的性能，我们也会加入`Redis`，同时使用`Redis`集群使得`Redis`对外提供更好的服务。


![1653059409865](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653059409865.png)

#### 1.1.3、导入后端项目

#### 1.1.4、导入前端工程

![image-20230601221012981](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230601221012981.png)

#### 1.1.5、运行前端项目

- 在`nginx`所在目录下打开一个`CMD`窗口，输入命令：


```shell
start nginx.exe
```

- 打开`chrome`浏览器，在空白页面点击鼠标右键，选择检查，即可打开开发者工具：


![image-20230601221206234](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230601221206234.png)

- 然后访问：`http://127.0.0.1:8080`，即可看到页面：


![image-20230601221247412](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230601221247412.png)



### 1.2 、基于`Session`实现登录流程

#### 1.2.1、发送短信验证码

- 用户在提交手机号后，会校验手机号是否合法，如果不合法，则要求用户重新输入手机号。

- 如果手机号合法，后台此时生成对应的验证码，同时将验证码保存到`session`，然后再通过短信的方式将验证码发送给用户。




#### 1.2.2、短信验证码登录、注册

- 用户将验证码和手机号进行输入并提交，后台拿到当前手机号和验证码，然后对用户输入的验证码进行校验，如果不一致，则无法通过校验，如果一致，则后台根据手机号查询用户，如果用户不存在，则为用户创建账号信息，保存到数据库，无论是否存在，都会将用户信息保存到`session`中，方便后续获得当前登录信息。




#### 1.2.3、校验登录状态

- 用户在请求时候，会从`cookie`中携带者`JsessionId`到后台，后台通过`JsessionId`从`session`中拿到用户信息，判断用户是否存在，如果不存在则进行拦截，如果存在，则将用户信息保存到`ThreadLocal`中，并且放行。


![1653066208144](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653066208144.png)



### 1.3 、实现发送短信验证码功能               

#### 1.3.1、页面流程

![1653067054461](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653067054461.png)

> 注：请求路径的`/api`会被`Tomcat`过滤掉，真正的请求路径是从`/user`开始的。
>



#### 1.3.2、具体代码

##### 1、发送验证码

```java
@Override
public Result sendCode(String phone, HttpSession session) {
    // 1.校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.符合，生成验证码
    String code = RandomUtil.randomNumbers(6);

    // 4.保存验证码到 session
    session.setAttribute("code",code);
    // 5.发送验证码
    log.debug("发送短信验证码成功，验证码：{}", code);
    // 返回ok
    return Result.ok(); 
}
```



##### 2、登录

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    // 1.校验手机号
    String phone = loginForm.getPhone();
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.校验验证码
    Object cacheCode = session.getAttribute("code");
    String code = loginForm.getCode();
    if(cacheCode == null || !cacheCode.toString().equals(code)){
        //3.不一致，报错
        return Result.fail("验证码错误");
    }
    //一致，根据手机号查询用户
    User user = query().eq("phone", phone).one();

    //5.判断用户是否存在
    if(user == null){
        //不存在，则创建
        user =  createUserWithPhone(phone);
    }
    //7.保存用户信息到session中
    session.setAttribute("user",user);

    return Result.ok();
}
```



### 1.4、实现登录拦截功能

#### 1.4.1、`tomcat`的运行原理

![1653068196656](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653068196656.png)

- 当用户发起请求时，会访问我们`tomcat`注册的端口（`8080`），任何程序想要运行，都需要有一个线程对当前端口号进行监听，`tomcat`也不例外，当监听线程知道用户想要和`tomcat`进行连接时，那会由监听线程创建`socket`连接，`socket`都是成对出现的，用户通过`socket`互相传递数据，当`tomcat`端的`socket`接受到数据后，此时监听线程会从`tomcat`的线程池中取出一个线程执行用户请求，在我们的服务部署到`tomcat`后，线程会找到用户想要访问的工程，然后用这个线程转发到工程中的`controller、service、dao`中，并且访问对应的`DB`，在用户执行完请求后，再统一返回，再找到`tomcat`端的`socket`，再将数据写回到用户端的`socket`，完成请求和响应。
- 通过以上讲解，我们可以得知每个用户其实对应都是去找`tomcat`线程池中的一个线程来完成工作的，使用完成后再进行回收，既然每个请求都是独立的，所以在每个用户去访问我们的工程时，我们可以使用`threadlocal`来做到线程隔离，每个线程操作自己的一份数据。




>  注：`Threadlocal`
>
> - 如果看过`ThreadLocal`的源码，你会发现在`ThreadLocal`中，无论是他的`put`方法和他的`get`方法， 都是先获得当前用户的线程，然后从线程中取出线程的成员变量`map`，只要线程不一样，`map`就不一样，所以可以通过这种方式来做到线程隔离。`ThreadLocal`称之为本地线程 。 我们可以通过`set`方法在当前线程上存储数据、通过`get`方法在当前线程上获取数据。
> - `set`方法源码分析：
>
> ~~~Java
> public void set(T value) {
>     Thread t = Thread.currentThread(); // 获取当前的线程
>     ThreadLocalMap map = getMap(t);    // 每一个线程都维护各自的一个容器（ThreadLocalMap）
>     if (map != null)
>         map.set(this, value);          // 这里的key对应的是ThreadLocal，因为我们的组件中需要传输（共享）的对象可能会有多个（不止Connection）
>     else
>         createMap(t, value);           // 默认情况下map是没有初始化的，那么第一次往其中添加数据时，会去初始化
> }
> ~~~
>
> - `get`方法源码分析：
>
> ~~~java
> public T get() {
>     Thread t = Thread.currentThread(); //获取当前的线程
>     ThreadLocalMap map = getMap(t);    //获取和这个线程（企业）相关的ThreadLocalMap（也就是工作纽带的集合）
>     if (map != null) {
>         ThreadLocalMap.Entry e = map.getEntry(this);   //this指的是ThreadLocal对象，通过它才能知道是哪一个工作纽带
>         if (e != null) {
>             @SuppressWarnings("unchecked")
>             T result = (T)e.value;     //entry.value就可以获取到工具箱了
>             return result;
>         }
>     }
>     return setInitialValue();
> }
> ~~~
>
> 

![1653068874258](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653068874258.png)



#### 1.4.2、拦截器

##### 1、拦截器代码

```Java
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
       //1.获取session
        HttpSession session = request.getSession();
        //2.获取session中的用户
        Object user = session.getAttribute("user");
        //3.判断用户是否存在
        if(user == null){
              //4.不存在，拦截，返回401状态码
              response.setStatus(401);
              return false;
        }
        //5.存在，保存用户信息到Threadlocal
        UserHolder.saveUser((User)user);
        //6.放行
        return true;
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 移除用户
        UserHolder.removeUser();
    }
}
```



##### 2、配置拦截器生效

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 登录拦截器
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns(
                        "/shop/**",
                        "/voucher/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/blog/hot",
                        "/user/code",
                        "/user/login"
                ).order(1);
        // token刷新的拦截器
        registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate)).addPathPatterns("/**").order(0);
    }
}
```



### 1.5、隐藏用户敏感信息

- 我们通过浏览器观察到此时用户的全部信息都在，这样极为不靠谱，所以我们应当在返回用户信息之前，将用户的敏感信息进行隐藏，采用的核心思路就是书写一个`UserDto`对象，这个`UserDto`对象就没有敏感信息了，我们在返回前，将有用户敏感信息的`User`对象转化成没有敏感信息的`UserDto`对象，那么就能够避免这个尴尬的问题了。
- 在登录方法处修改

```java
//7.保存用户信息到session中
session.setAttribute("user", BeanUtils.copyProperties(user,UserDTO.class));
```

- 在拦截器处：


```java
//5.存在，保存用户信息到Threadlocal
UserHolder.saveUser((UserDTO) user);
```

- 在`UserHolder`处：将`user`对象换成`UserDTO`


```java
public class UserHolder {
    private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();

    public static void saveUser(UserDTO user){
        tl.set(user);
    }

    public static UserDTO getUser(){
        return tl.get();
    }

    public static void removeUser(){
        tl.remove();
    }
}
```



### 1.6、`session`共享问题

- `Session`共享问题：多台`Tomcat`并不共享`Session`存储空间，当请求切换到不同`tomcat`服务时导致数据丢失的问题。

- 每个`tomcat`中都有一份属于自己的`session`，假设用户第一次访问第一台`tomcat`，并且把自己的信息存放到第一台服务器的`session`中，但是第二次这个用户访问到了第二台`tomcat`，那么在第二台服务器上，肯定没有第一台服务器存放的`session`，所以此时整个登录拦截功能就会出现问题，我们能如何解决这个问题呢？早期的方案是`session`拷贝，就是说虽然每个`tomcat`上都有不同的`session`，但是每当任意一台服务器的`session`修改时，都会同步给其他的`Tomcat`服务器的`session`，这样的话，就可以实现`session`的共享了。

- 但是这种方案具有两个大问题：

  - 每台服务器中都有完整的一份`session`数据，服务器压力过大。
  - `session`拷贝数据时，可能会出现延迟。

- `session`的替代方案应该满足：

  - 数据共享


    - 内存存储


    - `key、value`结构

- 所以咱们后来采用的方案都是基于`redis`来完成，我们把`session`换成`redis`，`redis`数据本身就是共享的，就可以避免`session`共享的问题了。

![image-20230615194856989](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230615194856989.png)



### 1.7、`Redis`代替`session`的业务流程

#### 1.7.1、设计`key`的结构

- 首先我们要思考一下利用`redis`来存储数据，那么到底使用哪种结构呢？由于存入的数据比较简单，我们可以考虑使用`String`，或者是使用哈希，如下图，如果使用`String`，需要注意他的`value`，多占用一点空间，如果使用哈希，则它的`value`中只会存储他数据本身，如果不是特别在意内存，其实使用`String`就可以啦。

- 保存登录的用户信息，可以使用`String`结构，以`JSON`字符串来保存，比较直观：


![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230615195135713.png)

- `Hash`结构可以将对象中的每个字段独立存储，可以针对单个字段做`CRUD`，并且内存占用更少：

![image-20230615195242099](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230615195242099.png)



#### 1.7.2、设计`key`的具体细节

- 所以我们可以使用`String`结构，就是一个简单的`key，value`键值对的方式，但是关于`key`的处理，`session`他是每个用户都有自己的`session`，但是`redis`的`key`是共享的，咱们就不能使用`code`了。

- 在设计这个`key`的时候，我们之前讲过需要满足两点：

  - `key`要具有唯一性
  - `key`要方便携带

- 如果我们采用`phone`(手机号)这个数据来存储当然是可以的，但是如果把这样的敏感数据存储到`redis`中并且从页面中带过来毕竟不太合适，所以我们在后台生成一个随机串`token`，然后让前端带来这个`token`就能完成我们的整体逻辑了。

![image-20231012170947092](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012170947092.png)



#### 1.7.3、整体访问流程

- 用户进入登录页面，输入手机号，点击发送验证码，页面提交手机号，后台校验手机号是否符合规范，如果符合，生成验证码，并将验证码作为`value`，电话号码作为`key`存入到`Redis`中，发送验证码。用户登录会去校验用户提交的手机号和验证码，是否一致，如果一致，则根据手机号查询用户信息，不存在则新建，最后将用户数据保存到`Redis`，并且生成`token`作为存储在`Redis`中用户信息的`key`，且将`token`返回给客户端。当我们校验用户是否登录时，会携带着`token`进行访问，从`Redis`中取出`token`对应的`value`，判断是否存在这个数据，如果没有则拦截，如果存在则将其保存到`ThreadLocal`中，并且放行。


![image-20230615203213809](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230615203213809.png)





### 1.8、基于`Redis`实现短信登录

**`UserServiceImpl`代码**

~~~java 
public static final Long LOGIN_CODE_TTL = 2L;

@Override
public Result sendCode(String phone, HttpSession session) {
    // 1.校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.符合生成验证码
    String code = RandomUtil.randomNumbers(6);

    // 4.保存验证码到Redis,同时设置有效期
    stringRedisTemplate.opsForValue().set(RedisConstants.LOGIN_CODE_KEY + phone,code,RedisConstants.LOGIN_CODE_TTL, TimeUnit.MINUTES);

    // 5.发送短信验证码
    log.debug("发送短信验证码成功，验证码{}",code);
    return Result.ok();
}
~~~



```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    // 1.校验手机号
    String phone = loginForm.getPhone();
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.从redis获取验证码并校验
    String cacheCode = stringRedisTemplate.opsForValue().get(LOGIN_CODE_KEY + phone);
    String code = loginForm.getCode();
    if (code == null || !cacheCode.equals(code)) {
        // 不一致，报错
        return Result.fail("验证码错误");
    }

    // 4.一致，根据手机号查询用户 select * from tb_user where phone = ?
    User user = query().eq("phone", phone).one();

    // 5.判断用户是否存在
    if (user == null) {
        // 6.不存在，创建新用户并保存
        user = createUserWithPhone(phone);
    }

    // 7.保存用户信息到 redis中
    // 7.1.随机生成token，作为登录令牌
    String token = UUID.randomUUID().toString(true);
    // 7.2.将User对象转为HashMap存储
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    Map<String, Object> userMap = BeanUtil.beanToMap(userDTO, new HashMap<>(),
                                                     CopyOptions.create()
                                                     .setIgnoreNullValue(true)
                                                     .setFieldValueEditor((fieldName, fieldValue) -> fieldValue.toString()));// 将userDTO中id值类型改为String类型
    // 7.3.存储
    String tokenKey = LOGIN_USER_KEY + token;
    stringRedisTemplate.opsForHash().putAll(tokenKey, userMap);
    // 7.4.设置token有效期
    stringRedisTemplate.expire(tokenKey, LOGIN_USER_TTL, TimeUnit.MINUTES);

    // 8.返回token
    return Result.ok(token);
}

private User createUserWithPhone(String phone) {
    User user = new User();
    user.setPhone(phone);
    user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX + RandomUtil.randomString(10));
	// 保存 User 到数据库
    save(user);
    return user;
}
```



### 1.9、解决状态登录刷新问题

#### 1.9.1、初始方案思路总结

- 在这个方案中，它确实可以使用对应路径的拦截，同时刷新登录`token`令牌的存活时间，但是现在这个拦截器他只是拦截需要被拦截的路径，假设当前用户访问了一些不需要拦截的路径，那么这个拦截器就不会生效，所以此时令牌刷新的动作实际上就不会执行，所以这个方案他是存在问题的。


![1653320822964](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653320822964.png)



####  1.9.2、优化方案

- 既然之前的拦截器无法对不需要拦截的路径生效，那么我们可以添加一个拦截器，在第一个拦截器中拦截所有的路径，把第二个拦截器做的事情放入到第一个拦截器中，同时刷新令牌，因为第一个拦截器有了`ThreadLocal`的数据，所以此时第二个拦截器只需要判断拦截器中的`user`对象是否存在即可，完成整体刷新功能。


![1653320764547](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653320764547.png)



#### 1.9.3、代码 

**`RefreshTokenInterceptor`**

```java
public class RefreshTokenInterceptor implements HandlerInterceptor {

    private StringRedisTemplate stringRedisTemplate;

    public RefreshTokenInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1.获取请求头中的token
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            return true;
        }
        // 2.基于TOKEN获取redis中的用户
        String key  = LOGIN_USER_KEY + token;
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(key);
        // 3.判断用户是否存在
        if (userMap.isEmpty()) {
            return false;
        }
        // 5.将查询到的hash数据转为UserDTO
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
        // 6.存在，保存用户信息到 ThreadLocal
        UserHolder.saveUser(userDTO);
        // 7.刷新token有效期
        stringRedisTemplate.expire(key, LOGIN_USER_TTL, TimeUnit.MINUTES);
        // 8.放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 移除用户
        UserHolder.removeUser();
    }
}
	
```

**`LoginInterceptor`**

```java
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1.判断是否需要拦截（ThreadLocal中是否有用户）
        if (UserHolder.getUser() == null) {
            // 没有，需要拦截，设置状态码
            response.setStatus(401);
            // 拦截
            return false;
        }
        // 有用户，则放行
        return true;
    }
}
```

~~~java 
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoginInterceptor())
        .excludePathPatterns(
        "/user/code",
        "/user/login",
        "/blog/hot",
        "/shop/**",
        "/shop-type/**",
        "/upload/**",
        "/voucher/**"
    ).order(1);
    registry.addInterceptor(new RefreshInterceptor(stringRedisTemplate)).addPathPatterns("/**").order(0); // 拦截所有
}
~~~





## 2、商户查询缓存

### 2.1、 什么是缓存?

- 举个例子：越野车、山地自行车、都拥有"避震器"、防止车体加速后因惯性，在酷似"U"字母的地形上飞跃，硬着陆导致的损害，像个弹簧一样。

- 同样，实际开发中，系统也需要"避震器"，防止过高的数据访问猛冲系统，导致其操作线程无法及时处理信息而瘫痪。
- 这在实际开发中对企业讲，对产品口碑，用户评价都是致命的，所以企业非常重视缓存技术。
- 缓存（`Cache`），就是数据交换的缓冲区，俗称的缓存就是缓冲区内的数据，一般从数据库中获取，存储于本地代码，例如：

```java
例1：Static final ConcurrentHashMap<K,V> map = new ConcurrentHashMap<>(); 本地用于高并发

例2：static final Cache<K,V> USER_CACHE = CacheBuilder.newBuilder().build(); 用于redis等缓存

例3：Static final Map<K,V> map =  new HashMap(); 本地缓存
```

- 由于其被`Static`修饰，所以随着类的加载而被加载到内存之中，作为本地缓存，由于其又被`final`修饰，所以其引用（例3：`map`）和对象（例3：`new HashMap()`）之间的关系是固定的，不能改变，因此不用担心赋值导致缓存失效。




####  2.1.1、为什么要使用缓存

- 一句话：因为速度快，好用。

- 缓存数据存储于代码中，而代码运行在内存中，内存的读写性能远高于磁盘，缓存可以大大降低用户访问并发量带来的服务器读写压力。

- 实际开发过程中，企业的数据量，少则几十万，多则几千万，这么大数据量，如果没有缓存来作为"避震器"，系统是几乎撑不住的，所以企业会大量运用到缓存技术，但是缓存也会增加代码复杂度和运营的成本。


![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20220523214414123.png)



#### 2.1.2、如何使用缓存

- 实际开发中，会构筑多级缓存来使系统运行速度进一步提升，例如：本地缓存与`redis`中的缓存并发使用。

- 浏览器缓存：主要是存在于浏览器端的缓存。

- 应用层缓存：可以分为`tomcat`本地缓存，比如之前提到的`map`，或者是使用`redis`作为缓存。

- 数据库缓存：在数据库中有一片空间是`buffer pool`，增改查数据都会先加载到`mysql`的缓存中。
- `CPU`缓存：当代计算机最大的问题是`cpu`性能提升了，但内存读写速度没有跟上，所以为了适应当下的情况，增加了`cpu`的`L1，L2，L3`级的缓存。

![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20220523212915666.png)





### 2.2、添加商户缓存

- 在我们查询商户信息时，我们是直接操作从数据库中去进行查询的，大致逻辑是这样，直接查询数据库那肯定慢咯，所以我们需要增加缓存。


```java
@GetMapping("/{id}")
public Result queryShopById(@PathVariable("id") Long id) {
    //这里是直接查询数据库
    return shopService.queryById(id);
}
```

#### 2.2.1 、缓存模型和思路

- 标准的操作方式就是查询数据库之前先查询缓存，如果缓存数据存在，则直接从缓存中返回，如果缓存数据不存在，再查询数据库，然后将数据存入`redis`。


![1653322097736](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653322097736.png)

#### 2.1.2、代码如下

- 代码思路：如果缓存有，则直接返回，如果缓存不存在，则查询数据库，然后存入`redis`缓存。


![1653322190155](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653322190155.png)

~~~Java
public Result queryById(Long id) {
    // 1.从redis中查询商铺
    String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);

    // 2.判断是否命中
    if (StrUtil.isNotBlank(shopJson)) {
        // 3.存在，直接返回
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    }

    // 4.未命中，查询数据库
    Shop shop = getById(id);

    // 5.不存在，返回错误
    if (shop == null) {
        return Result.fail("店铺不存在！");
    }

    // 6.存在，写入Redis
    String jsonStr = JSONUtil.toJsonStr(shop);
    stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,jsonStr);

    // 7.返回
    return Result.ok(shop);
}
~~~

![image-20231012171137623](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012171137623.png)



### 2.3、缓存更新策略

- 缓存更新是`redis`为了节约内存而设计出来的一个东西，主要是因为内存数据宝贵，当我们向`redis`插入太多数据，此时就可能会导致缓存中的数据过多，所以`redis`会对部分数据进行更新，或者把他叫为淘汰更合适。

- 内存淘汰：`redis`自动进行，当`redis`内存达到咱们设定的`max-memery`的时候，会自动触发淘汰机制，淘汰掉一些不重要的数据（可以自己设置策略方式）。

- 超时剔除：当我们给`redis`设置了过期时间`ttl`之后，`redis`会将超时的数据进行删除，方便咱们继续使用缓存。

- 主动更新：我们可以手动调用方法把缓存删掉，通常用于解决缓存和数据库不一致问题。

![1653322506393](.\Redis实战篇.assets\1653322506393.png)



#### 2.3.1 、数据库缓存不一致解决方案

- 由于我们的缓存的数据源来自于数据库，而数据库的数据是会发生变化的，因此，如果当数据库中数据发生变化，而缓存却没有同步，此时就会有一致性问题存在，其后果是：用户使用缓存中的过时数据，就会产生类似多线程数据安全问题，从而影响业务，产品口碑等。怎么解决呢？有如下几种方案：
  - `Cache Aside Pattern`人工编码方式：缓存调用者在更新完数据库后再去更新缓存，也称之为双写方案。
  - `Read/Write Through Pattern`：由系统本身完成，数据库与缓存的问题交由系统本身去处理。
  - `Write Behind Caching Pattern`：调用者只操作缓存，其他线程去异步处理数据库，实现最终一致。

![1653322857620](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653322857620.png)



#### 2.3.2 、数据库和缓存不一致采用什么方案

- 综合考虑使用方案一，但是方案一调用者如何处理呢？这里有几个问题：

  - 删除缓存还是更新缓存？

    - 更新缓存：每次更新数据库都更新缓存，无效写操作较多，并且存在较大的线程安全问题。

    * 删除缓存：更新数据库时让缓存失效，查询时再更新缓存。

  - 如何保证缓存与数据库的操作的同时成功或失败？ 

    * 单体系统，将缓存与数据库操作放在一个事务。

    * 分布式系统，利用`TCC`等分布式事务方案。

  - 先操作缓存还是先操作数据库？

    * 先删除缓存，再操作数据库。

    * 先操作数据库，再删除缓存。

- 如果采用第一个方案，那么假设我们每次操作数据库后，都操作缓存，但是中间如果没有人查询，那么这个更新动作实际上只有最后一次生效，中间的更新动作意义并不大，我们可以把缓存删除，等待再次查询时，将查询的数据存入缓存。

- 应该具体先操作缓存还是先操作数据库，我们应当是先操作数据库，再删除缓存，原因在于，如果你选择第一种方案，在两个线程并发来访问时，假设线程`1`先来，他先把缓存删了，此时线程`2`过来，他查询缓存数据并不存在，此时他写入缓存，当他写入缓存后，线程`1`再执行更新动作时，实际上写入的就是旧的数据，新的数据被旧数据覆盖了。

- 所以最终采用的方案是先更新数据库再删除缓存的双写并使用超时剔除兜底的方案。

![1653323595206](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653323595206.png)



#### 2.3.3、实现商铺和缓存与数据库双写一致

- 修改`ShopController`中的业务逻辑，满足下面的需求：

  - 根据`id`查询店铺时，如果缓存未命中，则查询数据库，将数据库结果写入缓存，并设置超时时间。
  - 根据`id`修改店铺时，先修改数据库，再删除缓存。
  - 设置`redis`缓存时添加过期时间：

![1653325871232](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653325871232.png)

- 代码分析：通过之前的淘汰，我们确定了采用删除策略，来解决双写问题，当我们修改了数据之后，然后把缓存中的数据进行删除，查询时发现缓存中没有数据，则会从`mysql`中加载最新的数据，从而避免数据库和缓存不一致的问题。


![1653325929549](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653325929549.png)

~~~java
@Override
public Result updateByIdAndCache(Shop shop) {
    Long id = shop.getId();
    if (id == null) {
        return Result.fail("id不存在");
    }

    // 更新数据库
    updateById(shop);
    // 删除缓存
    stringRedisTemplate.delete(RedisConstants.CACHE_SHOP_KEY + id);
    return Result.ok();
}
~~~



### 2.4、缓存穿透问题

#### 2.4.1、缓存穿透

- 缓存穿透是指客户端请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库。



#### 2.4.2、解决方案

- 常见的解决方案有两种：

##### 1、缓存空对象

- 当我们客户端访问不存在的数据时，先请求`redis`，但是此时`redis`中没有数据，此时会访问到数据库，但是数据库中也没有数据，这个数据穿透了缓存，直击数据库，我们都知道数据库能够承载的并发不如`redis`这么高，如果大量的请求同时过来访问这种不存在的数据，这些请求就都会访问到数据库，简单的解决方案就是哪怕这个数据在数据库中也不存在，我们也把这个数据存入到`redis`中去，这样，下次用户过来访问这个不存在的数据，那么在`redis`中也能找到这个数据就不会进入到数据库了。 
  - 优点：实现简单，维护方便。
  - 缺点：额外的内存消耗、可能造成短期的不一致。



##### 2、布隆过滤

- 布隆过滤器其实采用的是哈希思想来解决这个问题，通过一个庞大的二进制数组，走哈希思想去判断当前这个要查询的这个数据是否存在，如果布隆过滤器判断存在，则放行，这个请求会去访问`redis`，哪怕此时`redis`中的数据过期了，但是数据库中一定存在这个数据，在数据库中查询出来这个数据后，再将其放入到`redis`中，假设布隆过滤器判断这个数据不存在，则直接返回。这种方式优点在于节约内存空间，存在误判，误判原因在于：布隆过滤器走的是哈希思想，只要是哈希思想，就可能存在哈希冲突。

  * 优点：内存占用较少，没有多余`key`。

  * 缺点：实现复杂、存在误判可能。

![1653326156516](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653326156516.png)



#### 2.4.3、编码解决店铺查询的缓存穿透问题

- 在原来的逻辑中，我们如果发现这个数据在`mysql`中不存在，直接就返回`404`了，这样是会存在缓存穿透问题的。

- 现在的逻辑中：如果这个数据不存在，我们不会返回`404`，还是会把这个数据写入到`Redis`中，并且将`value`设置为空，当再次发起查询时，我们如果发现命中之后，判断这个`value`是否是`null`，如果是`null`，则是之前写入的数据，证明是缓存穿透数据，如果不是，则直接返回数据。


![1653327124561](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653327124561.png)

~~~java
public Result queryById(Long id) {
    // 1.从redis中查询商铺
    String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);

    // 2.判断是否命中
    if (StrUtil.isNotBlank(shopJson)) {
        // 3.存在，直接返回
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    }
    // 解决缓存穿透问题
    if (shopJson != null) { // 如果查询的店铺缓存不存在，则shopJson第一次是null，以后都是空串
        return Result.fail("店铺不存在！");
    }

    // 4.未命中，查询数据库
    Shop shop = getById(id);

    // 5.数据库中也不存在,写入空值
    if (shop == null) {
        //将空串写入redis
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,"",RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);
        return Result.fail("店铺不存在！");
    }

    // 6.存在，写入Redis
    String jsonStr = JSONUtil.toJsonStr(shop);
    stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,jsonStr,RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);

    // 7.返回
    return Result.ok(shop);
}
~~~



#### 2.4.4、总结

- 缓存穿透产生的原因是什么？

  * 用户请求的数据在缓存中和数据库中都不存在，不断发起这样的请求，给数据库带来巨大压力。

- 缓存穿透的解决方案有哪些？

  * 缓存`null`值
  
  
    * 布隆过滤
  
  
    * 增强`id`的复杂度，避免被猜测`id`规律
  
  
    * 做好数据的基础格式校验
  
  
    * 加强用户权限校验
  
  
    * 做好热点参数的限流
  




### 2.5 、缓存雪崩问题

- 缓存雪崩是指在同一时段大量的缓存`key`同时失效或者`Redis`服务宕机，导致大量请求到达数据库，带来巨大压力。

- 解决方案：

  * 给不同的`Key`的`TTL`添加随机值

  * 利用`Redis`集群提高服务的可用性

  * 给缓存业务添加降级限流策略

  * 给业务添加多级缓存


![1653327884526](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653327884526.png)



### 2.6、缓存击穿问题

#### 2.6.1、缓存击穿

- 缓存击穿问题也叫热点`Key`问题，就是一个被高并发访问并且缓存重建业务较复杂的`key`突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击。

- 常见的解决方案有两种：

  * 互斥锁

  * 逻辑过期

- 逻辑分析：假设线程`1`在查询缓存之后，本来应该去查询数据库，然后把这个数据重新加载到缓存的，此时只要线程`1`走完这个逻辑，其他线程就都能从缓存中加载这些数据了，但是假设在线程`1`没有走完的时候，后续的线程`2`，线程`3`，线程`4`同时过来访问当前这个方法， 那么这些线程都不能从缓存中查询到数据，那么他们就会同一时刻来访问查询缓存，都没查到，接着同一时间去访问数据库，同时的去执行数据库代码，对数据库访问压力过大。

![1653328022622](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653328022622.png)



#### 2.6.2、解决方案

##### 1、互斥锁

- 互斥锁来解决：因为锁能实现互斥性。假设线程过来，只能一个人一个人的来访问数据库，从而避免对于数据库访问压力过大，但这也会影响查询的性能，因为此时会让查询的性能从并行变成了串行，我们可以采用`tryLock`方法 + `double check`来解决这样的问题。
- 假设现在线程`1`过来访问，他查询缓存没有命中，但是此时他获得到了锁的资源，那么线程`1`就会一个人去执行逻辑，假设现在线程`2`过来，线程`2`在执行过程中，并没有获得到锁，那么线程`2`就可以进行到休眠，直到线程`1`把锁释放后，线程`2`获得到锁，然后再来执行逻辑，此时就能够从缓存中拿到数据了。

![1653328288627](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653328288627.png)



##### 2、逻辑过期

- 方案分析：我们之所以会出现这个缓存击穿问题，主要原因是在于我们对`key`设置了过期时间，假设我们不设置过期时间，其实就不会有缓存击穿的问题，但是不设置过期时间，这样数据不就一直占用我们内存了吗，我们可以采用逻辑过期方案。

- 我们把过期时间设置在`redis`的`value`中，注意：这个过期时间并不会直接作用于`redis`，而是我们后续通过逻辑去处理。假设线程`1`去查询缓存，然后从`value`中判断出来当前的数据已经过期了，此时线程`1`去获得互斥锁，那么其他线程会进行阻塞，获得了锁的线程它会开启一个新的线程去进行以前的重构数据的逻辑，直到新开的线程完成这个逻辑后，才释放锁， 而线程`1`直接进行返回，假设现在线程`3`过来访问，由于线程`2`持有着锁，所以线程`3`无法获得锁，线程`3`也直接返回数据，只有等到新开的线程`2`把重建数据构建完后，其他线程才能走返回正确的数据。

- 这种方案巧妙在于，异步的构建缓存，缺点在于在构建完缓存之前，返回的都是脏数据。


![1653328663897](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653328663897.png)

![image-20231012171331890](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012171331890.png)



#### 2.6.3、方案对比

- 互斥锁方案：由于保证了互斥性，所以数据一致，且实现简单，因为仅仅只需要加一把锁而已，也没其他的事情需要操心，所以没有额外的内存消耗，缺点在于有锁就有死锁问题的发生，且只能串行执行性能肯定受到影响。

- 逻辑过期方案：线程读取过程中不需要等待，性能好，有一个额外的线程持有锁去进行重构数据，但是在重构数据完成前，其他的线程只能返回之前的数据，且实现起来麻烦。

![1653357522914](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653357522914.png)



#### 2.6.4、利用互斥锁解决缓存击穿问题

- 核心思路：相较于原来从缓存中查询不到数据后直接查询数据库而言，现在的方案是进行查询之后，如果从缓存没有查询到数据，则进行互斥锁的获取，获取互斥锁后，判断是否获得到了锁，如果没有获得到，则休眠，过一会再进行尝试，直到获取到锁为止，才能进行查询。

- 如果获取到了锁的线程，再去进行查询，查询后将数据写入`redis`缓存，再释放锁，返回数据，利用互斥锁就能保证只有一个线程去执行操作数据库的逻辑，防止缓存击穿。


![](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653357860001.png)



**操作锁的代码：**

- 核心思路就是利用`redis`的`setnx`方法来表示获取锁，该方法含义是`redis`中如果没有这个`key`，则插入成功，返回`1`，在`stringRedisTemplate`中返回`true`，  如果有这个`key`则插入失败，则返回`0`，在`stringRedisTemplate`返回`false`，我们可以通过`true`，或者是`false`，来表示是否有线程成功插入`key`，成功插入的`key`的线程我们认为他就是获得到锁的线程。


```java
/**
 * 尝试获取锁，set成功就是获取锁，其他线程不能对此key做set操作
 *
 * @param key
 * @return
 */
private boolean tryLock(String key) {
    Boolean isSuccess = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
    return BooleanUtil.isTrue(isSuccess);
}

/**
 * 解锁
 *
 * @param key
 */
private void unlock(String key) {
    stringRedisTemplate.delete(key);
}
```

**操作代码：**

```java
/**
 * 缓存击穿互斥锁解决方法
 *
 * @param id
 * @return
 */
public Shop queryWithMutex(Long id) {
    // 1.从redis中查询商铺
    String shopJson = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);

    // 2.判断是否命中
    if (StrUtil.isNotBlank(shopJson)) {
        // 3.存在，直接返回
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return shop;
    }
    // 解决缓存穿透问题
    if (shopJson != null) {
        return null;
    }

    // 4.实现缓存重建
    Shop shop = null;
    try {
        // 4.1.获取互斥锁
        boolean tryLock = tryLock(RedisConstants.LOCK_SHOP_KEY + id);
        // 4.2.判断是否获取锁
        if (!tryLock) {
            // 4.3. 不成功，休眠并重试
            Thread.sleep(50);
            return queryWithMutex(id); // 这里return是要线程结束此方法的执行，重新开始调用
        }
        // 4.4.成功，根据id查询数据库
        shop = getById(id);
        // 模拟重建延时
        Thread.sleep(200);
        // 5.不存在,写入空值
        if (shop == null) {
            //将空串写入redis
            stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id, "", RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);
            return null;
        }

        // 6.将商铺数据写入Redis
        String jsonStr = JSONUtil.toJsonStr(shop);
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id, jsonStr, RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    } finally {
        // 7.释放锁
        unlock(RedisConstants.LOCK_SHOP_KEY + id);
    }

    // 8.返回
    return shop;
}
```



####  2.6.5 、利用逻辑过期解决缓存击穿问题

- 前提：理解一点：使用逻辑过期解决缓存击穿问题是基于热点`key`问题的，在使用之前我们需要对热点`key`数据进行预热。
- 需求：修改根据`id`查询商铺的业务，基于逻辑过期方式来解决缓存击穿问题。
- 思路分析：当用户开始查询`redis`时，判断是否命中，如果没有命中则直接返回空数据，不查询数据库（不是热点`key`），而一旦命中后，将`value`取出，判断`value`中的过期时间是否过期，如果没有过期，则直接返回`redis`中的数据，如果过期，则在开启独立线程后直接返回之前的数据，独立线程去重构数据，重构完成后释放互斥锁。

![1653360308731](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653360308731.png)

- 如何封装数据：因为现在`redis`中存储的数据的`value`需要带上过期时间，此时要么你去修改原来的实体类，要么你封装数据和过期时间。


##### 1、步骤一

- 新建一个实体类，我们采用第二个方案，这个方案，对原来代码没有侵入性。


```java
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}
```

##### 2、步骤二

- 在`ShopServiceImpl`新增此方法，利用单元测试进行缓存预热。


![1653360807133](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653360807133.png)

- 在测试类中

![1653360864839](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653360864839.png)



##### 3、步骤三

- **`ShopServiceImpl`**

```java
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);
public Shop queryWithLogicalExpire( Long id ) {
    String key = CACHE_SHOP_KEY + id;
    // 1.从redis查询商铺缓存
    String json = stringRedisTemplate.opsForValue().get(key);
    // 2.判断是否存在
    if (StrUtil.isBlank(json)) {
        // 3.不存在，直接返回
        return null;
    }
    // 4.命中，需要先把json反序列化为对象
    RedisData redisData = JSONUtil.toBean(json, RedisData.class);
    Shop shop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);
    LocalDateTime expireTime = redisData.getExpireTime();
    // 5.判断是否过期
    if(expireTime.isAfter(LocalDateTime.now())) {
        // 5.1.未过期，直接返回店铺信息
        return shop;
    }
    // 5.2.已过期，需要缓存重建
    // 6.缓存重建
    // 6.1.获取互斥锁
    String lockKey = LOCK_SHOP_KEY + id;
    boolean isLock = tryLock(lockKey);
    // 6.2.判断是否获取锁成功
    if (isLock){
        CACHE_REBUILD_EXECUTOR.submit( ()->{
            try{
                //重建缓存
                this.saveShop2Redis(id,20L);
            }catch (Exception e){
                throw new RuntimeException(e);
            }finally {
                unlock(lockKey);
            }
        });
    }
    // 6.4.返回过期的商铺信息
    return shop;
}
```



### 2.7、封装Redis工具类

- 基于`StringRedisTemplate`封装一个缓存工具类，满足下列需求：

  * 方法1：将任意`Java`对象序列化为`json`并存储在`string`类型的`key`中，并且可以设置`TTL`过期时间。

  * 方法2：将任意`Java`对象序列化为`json`并存储在`string`类型的`key`中，并且可以设置逻辑过期时间，用于处理缓存击穿问题。

  * 方法3：根据指定的`key`查询缓存，并反序列化为指定类型，利用缓存空值的方式解决缓存穿透问题。

  * 方法4：根据指定的`key`查询缓存，并反序列化为指定类型，需要利用逻辑过期解决缓存击穿问题。

- 将逻辑进行封装：

```java
@Slf4j
@Component
public class CacheClient {

    private final StringRedisTemplate stringRedisTemplate;

    private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

    public CacheClient(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    public void set(String key, Object value, Long time, TimeUnit unit) {
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value), time, unit);
    }

    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit unit) {
        // 设置逻辑过期
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
        // 写入Redis
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
    }

    public <R,ID> R queryWithPassThrough(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit){
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isNotBlank(json)) {
            // 3.存在，直接返回
            return JSONUtil.toBean(json, type);
        }
        // 判断命中的是否是空值
        if (json != null) {
            // 返回一个错误信息
            return null;
        }

        // 4.不存在，根据id查询数据库
        R r = dbFallback.apply(id);
        // 5.不存在，返回错误
        if (r == null) {
            // 将空值写入redis
            stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
            // 返回错误信息
            return null;
        }
        // 6.存在，写入redis
        this.set(key, r, time, unit);
        return r;
    }

    public <R, ID> R queryWithLogicalExpire(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isBlank(json)) {
            // 3.存在，直接返回
            return null;
        }
        // 4.命中，需要先把json反序列化为对象
        RedisData redisData = JSONUtil.toBean(json, RedisData.class);
        R r = JSONUtil.toBean((JSONObject) redisData.getData(), type);
        LocalDateTime expireTime = redisData.getExpireTime();
        // 5.判断是否过期
        if(expireTime.isAfter(LocalDateTime.now())) {
            // 5.1.未过期，直接返回店铺信息
            return r;
        }
        // 5.2.已过期，需要缓存重建
        // 6.缓存重建
        // 6.1.获取互斥锁
        String lockKey = LOCK_SHOP_KEY + id;
        boolean isLock = tryLock(lockKey);
        // 6.2.判断是否获取锁成功
        if (isLock){
            // 6.3.成功，开启独立线程，实现缓存重建
            CACHE_REBUILD_EXECUTOR.submit(() -> {
                try {
                    // 查询数据库
                    R newR = dbFallback.apply(id);
                    // 重建缓存
                    this.setWithLogicalExpire(key, newR, time, unit);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }finally {
                    // 释放锁
                    unlock(lockKey);
                }
            });
        }
        // 6.4.返回过期的商铺信息
        return r;
    }

    public <R, ID> R queryWithMutex(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String shopJson = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isNotBlank(shopJson)) {
            // 3.存在，直接返回
            return JSONUtil.toBean(shopJson, type);
        }
        // 判断命中的是否是空值
        if (shopJson != null) {
            // 返回一个错误信息
            return null;
        }

        // 4.实现缓存重建
        // 4.1.获取互斥锁
        String lockKey = LOCK_SHOP_KEY + id;
        R r = null;
        try {
            boolean isLock = tryLock(lockKey);
            // 4.2.判断是否获取成功
            if (!isLock) {
                // 4.3.获取锁失败，休眠并重试
                Thread.sleep(50);
                return queryWithMutex(keyPrefix, id, type, dbFallback, time, unit);
            }
            // 4.4.获取锁成功，根据id查询数据库
            r = dbFallback.apply(id);
            // 5.不存在，返回错误
            if (r == null) {
                // 将空值写入redis
                stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
                // 返回错误信息
                return null;
            }
            // 6.存在，写入redis
            this.set(key, r, time, unit);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }finally {
            // 7.释放锁
            unlock(lockKey);
        }
        // 8.返回
        return r;
    }

    private boolean tryLock(String key) {
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return BooleanUtil.isTrue(flag);
    }

    private void unlock(String key) {
        stringRedisTemplate.delete(key);
    }
}
```

- 在`ShopServiceImpl`中：


```java
@Resource
private CacheClient cacheClient;

 @Override
    public Result queryById(Long id) {
        // 解决缓存穿透
        Shop shop = cacheClient
                .queryWithPassThrough(CACHE_SHOP_KEY, id, Shop.class, this::getById, CACHE_SHOP_TTL, TimeUnit.MINUTES);

        // 互斥锁解决缓存击穿
        // Shop shop = cacheClient
        //         .queryWithMutex(CACHE_SHOP_KEY, id, Shop.class, this::getById, CACHE_SHOP_TTL, TimeUnit.MINUTES);

        // 逻辑过期解决缓存击穿
        // Shop shop = cacheClient
        //         .queryWithLogicalExpire(CACHE_SHOP_KEY, id, Shop.class, this::getById, 20L, TimeUnit.SECONDS);

        if (shop == null) {
            return Result.fail("店铺不存在！");
        }
        // 7.返回
        return Result.ok(shop);
    }
```





## 3、优惠卷秒杀

### 3.1 、全局唯一`ID`

- 每个店铺都可以发布优惠券：


![1653362612286](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653362612286.png)

- 当用户抢购时，就会生成订单并保存到`tb_voucher_order`这张表中，而订单表如果使用数据库自增`ID`就存在一些问题：

  * `id`的规律性太明显

  * 受单表数据量的限制


- 场景分析一：如果我们的`id`具有太明显的规则，用户或者说商业对手很容易猜测出来我们的一些敏感信息，比如商城在一天时间内，卖出了多少单，这明显不合适。

- 场景分析二：随着我们商城规模越来越大，`mysql`的单表的容量不宜超过`500W`，数据量过大之后，我们要进行拆库拆表，但拆分表了之后，他们从逻辑上讲他们是同一张表，所以他们的`id`是不能一样的， 于是乎我们需要保证`id`的唯一性。

- 全局`ID`生成器，是一种在分布式系统下用来生成全局唯一`ID`的工具，一般要满足下列特性：


![1653363100502](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653363100502.png)

- 为了增加`ID`的安全性，我们可以不直接使用`Redis`自增的数值，而是拼接一些其它信息：


![1653363172079](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653363172079.png)

- `ID`的组成部分：符号位：`1bit`，永远为`0`。
- 时间戳：`31bit`，以秒为单位，可以使用`69`年。

- 序列号：`32bit`，秒内的计数器，支持每秒产生`2^32`个不同`ID`。




### 3.2、`Redis`实现全局唯一`ID`

```java
@Component
public class RedisIdWorker {
    /**
     * 开始时间戳
     */
    private static final long BEGIN_TIMESTAMP = 1640995200L;
    /**
     * 序列号的位数
     */
    private static final int COUNT_BITS = 32;

    private StringRedisTemplate stringRedisTemplate;

    public RedisIdWorker(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    public long nextId(String keyPrefix) {
        // 1.生成时间戳
        LocalDateTime now = LocalDateTime.now();
        long nowSecond = now.toEpochSecond(ZoneOffset.UTC);
        long timestamp = nowSecond - BEGIN_TIMESTAMP;

        // 2.生成序列号
        // 2.1.获取当前日期，精确到天
        String date = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        // 2.2.自增长
        long count = stringRedisTemplate.opsForValue().increment("icr:" + keyPrefix + ":" + date);

        // 3.拼接并返回
        return timestamp << COUNT_BITS | count;
    }
}
```

- 测试类：

```java
@Test
void testIdWorker() throws InterruptedException {
    CountDownLatch latch = new CountDownLatch(300);

    Runnable task = () -> {
        for (int i = 0; i < 100; i++) {
            long id = redisIdWorker.nextId("order");
            System.out.println("id = " + id);
        }
        latch.countDown();
    };
    long begin = System.currentTimeMillis();
    for (int i = 0; i < 300; i++) {
        es.submit(task);
    }
    latch.await();
    long end = System.currentTimeMillis();
    System.out.println("time = " + (end - begin));
}
```

> 注：关于`Countdownlatch`
>
> - `Countdownlatch`名为信号枪：主要的作用是同步协调在多线程的等待于唤醒问题。
>
> - 我们如果没有`CountDownLatch`，那么由于程序是异步的，当异步程序没有执行完时，主线程就已经执行完了，然后我们期望的是分线程全部走完之后，主线程再走，所以我们此时需要使用到`CountDownLatch`。
>
> - `CountDownLatch`中有两个最重要的方法：
>   - `countDown`
>   - `await`
> - `await`方法是阻塞方法，我们担心分线程没有执行完时，`main`线程就先执行，所以使用`await`可以让`main`线程阻塞，那么什么时候`main`线程不再阻塞呢？当`CountDownLatch`内部维护的变量变为`0`时，就不再阻塞，直接放行，那么什么时候`CountDownLatch`维护的变量变为0 呢，我们只需要调用一次`countDown`，内部变量就减少`1`，我们让分线程和变量绑定，执行完一个分线程就减少一个变量，当分线程全部走完，`CountDownLatch`维护的变量就是`0`，此时`await`就不再阻塞，统计出来的时间也就是所有分线程执行完后的时间。



### 3.3、添加优惠卷

- 每个店铺都可以发布优惠券，分为平价券和特价券。平价券可以任意购买，而特价券需要秒杀抢购：


![1653365145124](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653365145124.png)

- `tb_voucher`：优惠券的基本信息，优惠金额、使用规则等。
- `tb_seckill_voucher`：优惠券的库存、开始抢购时间，结束抢购时间。特价优惠券才需要填写这些信息。
- 平价卷由于优惠力度并不是很大，所以是可以任意领取，而代金券由于优惠力度大，所以像第二种卷，就得限制数量，从表结构上也能看出，特价卷除了具有优惠卷的基本信息以外，还具有库存，抢购时间，结束时间等等字段。
- 新增普通卷代码：`VoucherController`


```java
@PostMapping
public Result addVoucher(@RequestBody Voucher voucher) {
    voucherService.save(voucher);
    return Result.ok(voucher.getId());
}
```

- 新增秒杀卷代码：`VoucherController`、`VoucherServiceImpl`

```java
@PostMapping("seckill")
public Result addSeckillVoucher(@RequestBody Voucher voucher) {
    voucherService.addSeckillVoucher(voucher);
    return Result.ok(voucher.getId());
}
```

```java
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);
    // 保存秒杀库存到Redis中
    stringRedisTemplate.opsForValue().set(SECKILL_STOCK_KEY + voucher.getId(), voucher.getStock().toString());
}
```



### 3.4、实现秒杀下单

- 下单核心思路：当我们点击抢购时，会触发右侧的请求，我们只需要编写对应的`controller`即可。


![1653365839526](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653365839526.png)

- 秒杀下单应该思考的内容，下单时需要判断两点：

  * 秒杀是否开始或结束，如果尚未开始或已经结束则无法下单。

  * 库存是否充足，不足则无法下单。

- 下单核心逻辑分析：

  - 当用户开始进行下单，我们应当去查询优惠卷信息，查询到优惠卷信息，判断是否满足秒杀条件。
- 比如时间是否充足，如果时间充足，则进一步判断库存是否足够，如果两者都满足，则扣减库存，创建订单，然后返回订单`id`，如果有一个条件不满足则直接结束。


![1653366238564](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653366238564.png)

- `VoucherOrderServiceImpl`

```java
@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀尚未开始！");
    }
    // 3.判断秒杀是否已经结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 已经结束
        return Result.fail("秒杀已经结束！");
    }
    // 4.判断库存是否充足
    if (voucher.getStock() < 1) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    //5，扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock= stock -1")
            .eq("voucher_id", voucherId).update();
    if (!success) {
        //扣减库存
        return Result.fail("库存不足！");
    }
    //6.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    // 6.1.订单id
    long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);
    // 6.2.用户id
    Long userId = UserHolder.getUser().getId();
    voucherOrder.setUserId(userId);
    // 6.3.代金券id
    voucherOrder.setVoucherId(voucherId);
    save(voucherOrder);

    return Result.ok(orderId);
}
```



### 3.5、库存超卖问题分析

- 有关超卖问题分析：在我们原有代码中是这么写的


```java
if (voucher.getStock() < 1) {
    // 库存不足
    return Result.fail("库存不足！");
}
//5，扣减库存
boolean success = seckillVoucherService.update()
    .setSql("stock= stock -1")
    .eq("voucher_id", voucherId).update();
if (!success) {
    //扣减库存
    return Result.fail("库存不足！");
}
```

- 假设线程`1`过来查询库存，判断出来库存大于`1`，正准备去扣减库存，但是还没有来得及去扣减，此时线程`2`过来，线程`2`也去查询库存，发现这个数量一定也大于`1`，那么这两个线程都会去扣减库存，最终多个线程相当于一起去扣减库存，此时就会出现库存的超卖问题。


![1653368335155](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653368335155.png)

- 超卖问题是典型的多线程安全问题，针对这一问题的常见解决方案就是加锁，而对于加锁，我们通常有两种解决方案：


![1653368562591](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653368562591.png)

##### 1、悲观锁

- 悲观锁可以实现对于数据的串行化执行，比如`sync`，和`lock`都是悲观锁的代表，同时，悲观锁中又可以再细分为公平锁，非公平锁，可重入锁，等等。




##### 2、乐观锁

- 乐观锁：会有一个版本号，每次操作数据会对版本号`+1`，再提交回数据时，会去校验是否比之前的版本大`1 `，如果大`1`，则进行操作成功，这套机制的核心逻辑在于，如果在操作过程中，版本号只比原来大`1`，那么就意味着操作过程中没有人对他进行过修改，他的操作就是安全的，如果不大`1`，则数据被修改过，当然乐观锁还有一些变种的处理方式比如`cas`。

- 乐观锁的典型代表：就是`cas`，利用`cas`进行无锁化机制加锁，`var5`是操作前读取的内存值，`while`中的`var1+var2`是预估值，如果预估值`==`内存值，则代表中间没有被人修改过，此时就将新值去替换内存值。
- 其中`do while`是为了在操作失败时，再次进行自旋操作，即把之前的逻辑再操作一次。

```java
int var5;
do {
    var5 = this.getIntVolatile(var1, var2);
} while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

return var5;
```



##### 3、使用方式

- 课程中的使用方式是没有像`cas`一样带自旋的操作，也没有对`version`的版本号`+1`，它的操作逻辑是在操作时，对版本号进行`+1`操作，然后要求`version`如果是`1`的情况下，才能操作，那么第一个线程在操作后，数据库中的`version`变成了`2`，但是它自己满足`version = 1`，所以没有问题，此时线程`2`执行，线程`2`最后也需要加上条件`version = 1`，但是现在由于线程`1`已经操作过了，所以线程`2`操作时就不满足`version=1`的条件了，所以线程`2`无法执行成功。


![1653369268550](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653369268550.png)



### 3.6 、乐观锁解决超卖问题

#### 3.6.1、修改代码方案一

- `VoucherOrderServiceImpl`在扣减库存时，改为：

```java
boolean success = seckillVoucherService.update()
            .setSql("stock= stock -1") //set stock = stock -1
            .eq("voucher_id", voucherId).eq("stock",voucher.getStock()).update(); //where id = ？ and stock = ?
```

- 以上逻辑的核心含义是：只要我扣减库存时的库存和之前我查询到的库存是一样的，就意味着没有人在中间修改过库存，那么此时就是安全的，但是以上这种方式通过测试发现会有很多失败的情况，失败的原因在于：在使用乐观锁过程中假设`100`个线程同时都拿到了`100`的库存，然后大家一起去进行扣减，但是`100`个人中只有`1`个人扣减成功，其他的线程在准备扣减时，发现库存已经被修改过了，所以此时其他线程都会失败。




#### 3.6.2、修改代码方案二

- 之前的方式要修改前后都保持一致，但是这样我们分析过，成功的概率太低，所以我们的乐观锁需要变一下，改成`stock`大于`0`即可。


```java
boolean success = seckillVoucherService.update()
            .setSql("stock= stock -1")
            .eq("voucher_id", voucherId).update().gt("stock",0).update(); //where id = ? and stock > 0
```



**知识小扩展：**

- 针对`cas`中的自旋压力过大，我们可以使用`Longaddr`这个类去解决。

- `Java8`提供的一个对`AtomicLong`改进后的一个类，`LongAdder`。
- 大量线程并发更新一个原子性的时候，天然的问题就是自旋，会导致并发性问题，当然这也比我们直接使用`sync`来的好。所以利用这么一个类，`LongAdder`来进行优化。

- 如果获取某个值，则会对`cell`和`base`的值进行递增，最后返回一个完整的值。


![1653370271627](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653370271627.png)



### 3.7、优惠券秒杀一人一单

- 需求：修改秒杀业务，要求同一个优惠券，一个用户只能下一单。

- 现在的问题在于：

  - 优惠卷是为了引流，但是目前的情况是，一个人可以无限制的抢这个优惠卷，所以我们应当增加一层逻辑，让一个用户只能下一个单，而不是让一个用户下多个单。
- 具体操作逻辑如下：比如时间是否充足，如果时间充足，则进一步判断库存是否足够，然后再根据优惠卷`id`和用户`id`查询是否已经下过这个订单，如果下过这个订单，则不再下单，否则进行下单。


![1653371854389](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653371854389.png)

- 初步代码：增加一人一单逻辑`VoucherOrderServiceImpl`

```java
@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀尚未开始！");
    }
    // 3.判断秒杀是否已经结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀已经结束！");
    }
    // 4.判断库存是否充足
    if (voucher.getStock() < 1) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    // 5.一人一单逻辑
    // 5.1.用户id
    Long userId = UserHolder.getUser().getId();
    int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
    // 5.2.判断是否存在
    if (count > 0) {
        // 用户已经购买过了
        return Result.fail("用户已经购买过一次！");
    }

    //6，扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock = stock -1")
            .eq("voucher_id", voucherId).update();
    if (!success) {
        //扣减库存
        return Result.fail("库存不足！");
    }
    //7.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    // 7.1.订单id
    long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);

    voucherOrder.setUserId(userId);
    // 7.3.代金券id
    voucherOrder.setVoucherId(voucherId);
    save(voucherOrder);

    return Result.ok(orderId);
}
```

- 存在问题：现在的问题还是和之前一样，并发过来，查询数据库，都不存在订单，所以我们还是需要加锁，但是乐观锁比较适合更新数据，而现在是插入数据，所以我们需要使用悲观锁操作。
- 注意：在这里提到了非常多的问题，我们需要慢慢的来思考，首先我们的初始方案是封装了一个`createVoucherOrder`方法，同时为了确保它线程安全，在方法上添加了一把`synchronized`锁。

```java
@Transactional
public synchronized Result createVoucherOrder(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    // 5.1.查询订单
    int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
    // 5.2.判断是否存在
    if (count > 0) {
        // 用户已经购买过了
        return Result.fail("用户已经购买过一次！");
    }

    // 6.扣减库存
    boolean success = seckillVoucherService.update()
        .setSql("stock = stock - 1") // set stock = stock - 1
        .eq("voucher_id", voucherId).gt("stock", 0) // where id = ? and stock > 0
        .update();
    if (!success) {
        // 扣减失败
        return Result.fail("库存不足！");
    }

    // 7.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    // 7.1.订单id
    long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);
    // 7.2.用户id
    voucherOrder.setUserId(userId);
    // 7.3.代金券id
    voucherOrder.setVoucherId(voucherId);
    save(voucherOrder);

    // 7.返回订单id
    return Result.ok(orderId);
}
```

- 但是这样添加锁，锁的粒度太粗了，在使用锁过程中，控制锁粒度是一个非常重要的事情，因为如果锁的粒度太大，会导致每个线程进来都会锁住，所以我们需要去控制锁的粒度，以下这段代码需要修改为：

- `intern()`这个方法是从常量池中拿到数据，如果我们直接使用`userId.toString()`它拿到的对象实际上是`new`出来不同的对象，我们使用锁必须保证锁必须是同一把，所以我们需要使用`intern()`方法。

```java
@Transactional
public  Result createVoucherOrder(Long voucherId) {
	Long userId = UserHolder.getUser().getId();
	synchronized(userId.toString().intern()){
         // 5.1.查询订单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        // 5.2.判断是否存在
        if (count > 0) {
            // 用户已经购买过了
            return Result.fail("用户已经购买过一次！");
        }

        // 6.扣减库存
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1") // set stock = stock - 1
                .eq("voucher_id", voucherId).gt("stock", 0) // where id = ? and stock > 0
                .update();
        if (!success) {
            // 扣减失败
            return Result.fail("库存不足！");
        }

        // 7.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        // 7.1.订单id
        long orderId = redisIdWorker.nextId("order");
        voucherOrder.setId(orderId);
        // 7.2.用户id
        voucherOrder.setUserId(userId);
        // 7.3.代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);

        // 7.返回订单id
        return Result.ok(orderId);
    }
}
```

- 但是以上代码还是存在问题，问题的原因在于当前方法被`spring`的事务控制，如果你在方法内部加锁，可能会导致当前方法事务还没有提交，但是锁已经释放也会导致问题，所以我们选择将当前方法整体包裹起来，确保事务不会出现问题，如下：

- 在`seckillVoucher`方法中，添加以下逻辑，这样就能保证事务的特性，同时也控制了锁的粒度。


![1653373434815](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653373434815.png)

- 但是以上做法依然有问题，因为你调用的方法，其实是`this.`的方式调用的，这样会使`Spring`事务失效，事务想要生效，还得利用代理对象来调用，所以这个地方，我们需要获得原始的事务对象， 来操作事务。


![1653383810643](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653383810643.png)





### 3.8、集群环境下的并发问题

- 通过加锁可以解决在单机情况下的一人一单安全问题，但是在集群模式下就不行了。
- 我们将服务启动两份，端口分别为`8081`和`8082`：

![1653373887844](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653373887844.png)

- 然后修改`nginx`的`conf`目录下的`nginx.conf`文件，配置反向代理和负载均衡：

![1653373908620](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653373908620.png)

- 有关锁失效原因分析：
  - 由于现在我们部署了多个`tomcat`，每个`tomcat`都有一个属于自己的`jvm`，那么假设在服务器`A`的`tomcat`内部，有两个线程，这两个线程由于使用的是同一份代码，那么他们的锁对象是同一个，是可以实现互斥的，但是如果现在是服务器`B`的`tomcat`内部，又有两个线程，但是他们的锁对象写的虽然和服务器`A`一样，但是锁对象却不是同一个，所以线程`3`和线程`4`可以实现互斥，但是却无法和线程`1`和线程`2`实现互斥，这就是集群环境下，`sync`锁失效的原因，在这种情况下，我们就需要使用分布式锁来解决这个问题。

![1653374044740](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653374044740.png)



## 4、分布式锁

### 4.1 、基本原理和实现方式对比

- 分布式锁：满足分布式系统或集群模式下多进程可见并且互斥的锁。

- 分布式锁的核心思想就是让大家都使用同一把锁，只要大家使用的是同一把锁，那么我们就能锁住线程，不让线程进行，让程序串行执行，这就是分布式锁的核心思路。


![1653374296906](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653374296906.png)

- 那么分布式锁他应该满足一些什么样的条件呢？
  - 可见性：多个线程都能看到相同的结果，注意：这个地方说的可见性并不是并发编程中指的内存可见性，只是说多个进程之间都能感知到变化的意思。

  - 互斥：互斥是分布式锁的最基本的条件，使得程序串行执行。

  - 高可用：程序不易崩溃，时时刻刻都保证较高的可用性。

  - 高性能：由于加锁本身就让性能降低，所有对于分布式锁本身需要他就较高的加锁性能和释放锁性能。

  - 安全性：安全也是程序中必不可少的一环。



![1653381992018](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653381992018.png)

- 常见的分布式锁有三种：

  - `Mysql`：`Mysql`本身就带有锁机制，但是由于`Mysql`性能本身一般，所以采用分布式锁的情况下，其实使用`Mysql`作为分布式锁比较少见。


  - `Redis`：`Redis`作为分布式锁是非常常见的一种使用方式，现在企业级开发中基本都使用`redis`或者`zookeeper`作为分布式锁，利用`setnx`这个方法，如果插入`key`成功，则表示获得到了锁，如果有人插入成功，其他人插入失败则表示无法获得到锁，利用这套逻辑来实现分布式锁。

  - `Zookeeper`：`zookeeper`也是企业级开发中较好的一个实现分布式锁的方案，由于本套视频并不讲解`zookeeper`的原理和分布式锁的实现，所以不过多阐述。

![1653382219377](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653382219377.png)



### 4.2 、`Redis`分布式锁的实现核心思路

- 实现分布式锁时需要实现的两个基本方法：

  * 获取锁：

    * 互斥：确保只能有一个线程获取锁
    * 非阻塞：尝试一次，成功返回true，失败返回false


~~~shell
# 添加锁，NX是互斥，EX是设置超时时间
SET lock thread1 NX EX 10
~~~

* 释放锁：

  * 手动释放
  * 超时释放：获取锁时添加一个超时时间


~~~shell
# 释放锁，删除即可
DEL key
~~~

- 核心思路：
  - 我们利用`redis`的`setNx`方法，当有多个线程进入时，我们就利用该方法，第一个线程进入时，`redis`中就有这个`key`了，返回了`1`，如果结果是`1`，则表示他抢到了锁，那么他去执行业务，然后再删除锁，退出锁逻辑，没有抢到锁的哥们，等待一定时间后重试即可。

![1653382830810](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653382830810.png)



### 4.3、实现分布式锁版本一

* 加锁逻辑：锁的基本接口

![1656079017728](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1656079017728.png)

- `SimpleRedisLock`：利用`setnx`方法进行加锁，同时增加过期时间，防止死锁，此方法可以保证加锁和增加过期时间具有原子性。

```java
private String name;                                                          

private StringRedisTemplate stringRedisTemplate;                              

private static final String KEY_PREFIX = "lock:";                             

public SimpleRedisLock(String name, StringRedisTemplate stringRedisTemplate) {
    this.name = name;                                                         
    this.stringRedisTemplate = stringRedisTemplate;                           
}                                                                             
@Override
public boolean tryLock(long timeoutSec) {
    // 获取线程标示
    String threadId = Thread.currentThread().getId()
        // 获取锁
        Boolean success = stringRedisTemplate.opsForValue()
        .setIfAbsent(KEY_PREFIX + name, threadId + "", timeoutSec, TimeUnit.SECONDS);
    return Boolean.TRUE.equals(success);
}
```

* 释放锁逻辑：`SimpleRedisLock`，释放锁，防止删除别人的锁

```java
public void unlock() {
    //通过del删除锁
    stringRedisTemplate.delete(KEY_PREFIX + name);
}
```

* 修改业务代码

```java
  @Override
    public Result seckillVoucher(Long voucherId) {
        // 1.查询优惠券
        SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
        // 2.判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
            // 尚未开始
            return Result.fail("秒杀尚未开始！");
        }
        // 3.判断秒杀是否已经结束
        if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
            // 尚未开始
            return Result.fail("秒杀已经结束！");
        }
        // 4.判断库存是否充足
        if (voucher.getStock() < 1) {
            // 库存不足
            return Result.fail("库存不足！");
        }
        Long userId = UserHolder.getUser().getId();
        //创建锁对象(新增代码)
        SimpleRedisLock lock = new SimpleRedisLock("order:" + userId, stringRedisTemplate);
        //获取锁对象
        boolean isLock = lock.tryLock(1200);
		//加锁失败
        if (!isLock) {
            return Result.fail("不允许重复下单");
        }
        try {
            //获取代理对象(事务)
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            return proxy.createVoucherOrder(voucherId);
        } finally {
            //释放锁
            lock.unlock();
        }
    }
```



### 4.4、Redis分布式锁误删情况说明

- 持有锁的线程在锁的内部出现了阻塞，导致他的锁自动释放，这时其他线程，线程`2`来尝试获得锁，就拿到了这把锁，然后线程`2`在持有锁执行过程中，线程`1`反应过来，继续执行，而线程`1`执行过程中，走到了删除锁逻辑，此时就会把本应该属于线程`2`的锁进行删除，这就是误删别人锁的情况说明。

- 解决方案：解决方案就是在每个线程释放锁的时候，去判断一下当前这把锁是否属于自己，如果属于自己，则不进行锁的删除，假设还是上边的情况，线程`1`卡顿，锁自动释放，线程`2`进入到锁的内部执行逻辑，此时线程`1`反应过来，然后删除锁，但是线程`1`，一看当前这把锁不是属于自己，于是不进行删除锁逻辑，当线程`2`走到删除锁逻辑时，如果没有卡过自动释放锁的时间点，则判断当前这把锁是属于自己的，于是删除这把锁。


![1653385920025](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653385920025.png)



### 4.5、解决Redis分布式锁误删问题

- 需求：修改之前的分布式锁实现，满足：在获取锁时存入线程标示（可以用UUID表示）

- 在释放锁时先获取锁中的线程标示，判断是否与当前线程标示一致

  * 如果一致则释放锁

  * 如果不一致则不释放锁

- 核心逻辑：在存入锁时，放入自己线程的标识，在删除锁时，判断当前这把锁的标识是不是自己存入的，如果是，则进行删除，如果不是，则不进行删除。

![1653387398820](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653387398820.png)

- 具体代码如下：加锁


```java
private static final String ID_PREFIX = UUID.randomUUID().toString(true) + "-";
@Override
public boolean tryLock(long timeoutSec) {
   // 获取线程标示
   String threadId = ID_PREFIX + Thread.currentThread().getId();
   // 获取锁
   Boolean success = stringRedisTemplate.opsForValue()
                .setIfAbsent(KEY_PREFIX + name, threadId, timeoutSec, TimeUnit.SECONDS);
   return Boolean.TRUE.equals(success);
}
```

- 释放锁


```java
public void unlock() {
    // 获取线程标示
    String threadId = ID_PREFIX + Thread.currentThread().getId();
    // 获取锁中的标示
    String id = stringRedisTemplate.opsForValue().get(KEY_PREFIX + name);
    // 判断标示是否一致
    if(threadId.equals(id)) {
        // 释放锁
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```

- 有关代码实操说明：
  - 在我们修改完此处代码后，我们重启工程，然后启动两个线程，第一个线程持有锁后，手动释放锁，第二个线程 此时进入到锁内部，再放行第一个线程，此时第一个线程由于锁的value值并非是自己，所以不能释放锁，也就无法删除别人的锁，此时第二个线程能够正确释放锁，通过这个案例初步说明我们解决了锁误删的问题。



### 4.6 、分布式锁的原子性问题

- 更为极端的误删逻辑说明：

- 线程`1`现在持有锁之后，在执行业务逻辑过程中，他正准备删除锁，而且已经走到了条件判断的过程中，比如他已经拿到了当前这把锁确实是属于他自己的，正准备删除锁，但是此时他阻塞了（例如：JVM垃圾回收）然后锁超时了，那么此时线程`2`进来，但是线程`1`他会接着往后执行，当他卡顿结束后，他直接就会执行删除锁那行代码，相当于条件判断并没有起到作用，这就是删锁时的原子性问题，之所以有这个问题，是因为线程`1`的拿锁，比锁，删锁，实际上并不是原子性的，我们要防止刚才的情况发生，


![1653387764938](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653387764938.png)



### 4.7、`Lua`脚本解决多条命令原子性问题

- `Redis`提供了`Lua`脚本功能，在一个脚本中编写多条Redis命令，确保多条命令执行时的原子性。Lua是一种编程语言，它的基本语法大家可以参考网站：`https://www.runoob.com/lua/lua-tutorial.html`，这里重点介绍`Redis`提供的调用函数，我们可以使用`lua`去操作`redis`，又能保证他的原子性，这样就可以实现拿锁比锁删锁是一个原子性动作了，作为`Java`程序员这一块并不作一个简单要求，并不需要大家过于精通，只需要知道他有什么作用即可。
- 这里重点介绍Redis提供的调用函数，语法如下： 

```lua
redis.call('命令名称', 'key', '其它参数', ...)
```

- 例如，我们要执行`set name jack`，则脚本是这样：


```lua
# 执行 set name jack
redis.call('set', 'name', 'jack')
```

- 例如，我们要先执行`set name Rose`，再执行`get name`，则脚本如下：


```lua
# 先执行 set name jack
redis.call('set', 'name', 'Rose')
# 再执行 get name
local name = redis.call('get', 'name')
# 返回
return name
```

- 写好脚本以后，需要用`Redis`命令来调用脚本，调用脚本的常见命令如下：


![1653392181413](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653392181413.png)

- 例如，我们要执行`redis.call('set', 'name', 'jack')`这个脚本，语法如下：


![1653392218531](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653392218531.png)

- 如果脚本中的`key、value`不想写死，可以作为参数传递。`key`类型参数会放入`KEYS`数组，其它参数会放入`ARGV`数组，在脚本中可以从`KEYS`和`ARGV`数组获取这些参数：


![1653392438917](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653392438917.png)

- 接下来我们来回一下我们释放锁的逻辑：释放锁的业务流程是这样的

  - 获取锁中的线程标示
  
  
    - 判断是否与指定的标示（当前线程标示）一致
  
  
    - 如果一致则释放锁（删除）
  
  
    - 如果不一致则什么都不做
  
  
  - 如果用`Lua`脚本来表示则是这样的：最终我们操作`redis`的拿锁比锁删锁的`lua`脚本就会变成这样：
  


```lua
-- 这里的 KEYS[1] 就是锁的key，这里的ARGV[1] 就是当前线程标示
-- 获取锁中的标示，判断是否与当前线程标示一致
if (redis.call('GET', KEYS[1]) == ARGV[1]) then
  -- 一致，则删除锁
  return redis.call('DEL', KEYS[1])
end
-- 不一致，则直接返回
return 0
```



### 4.8、利用`Java`代码调用`Lua`脚本改造分布式锁

- `lua`脚本本身并不需要大家花费太多时间去研究，只需要知道如何调用，大致是什么意思即可，所以在笔记中并不会详细的去解释这些`lua`表达式的含义。
- 我们的`RedisTemplate`中，可以利用`execute`方法去执行`lua`脚本，参数对应关系就如下图：


![1653393304844](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653393304844.png)

**Java代码**

```java
private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;
    static {
        UNLOCK_SCRIPT = new DefaultRedisScript<>();
        UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
        UNLOCK_SCRIPT.setResultType(Long.class);
    }

public void unlock() {
    // 调用lua脚本
    stringRedisTemplate.execute(
            UNLOCK_SCRIPT,
            Collections.singletonList(KEY_PREFIX + name),
            ID_PREFIX + Thread.currentThread().getId());
}
// 经过以上代码改造后，我们就能够实现 拿锁比锁删锁的原子性动作了~
```



### 4.9、总结：

- 基于`Redis`的分布式锁实现思路：

  * 利用`set nx ex`获取锁，并设置过期时间，保存线程标示

  * 释放锁时先判断线程标示是否与自己一致，一致则删除锁
  * 特性：
    * 利用`set nx`满足互斥性
    * 利用`set ex`保证故障时锁依然能释放，避免死锁，提高安全性
    * 利用`Redis`集群保证高可用和高并发特性


- 笔者总结：我们一路走来，利用添加过期时间，防止死锁问题的发生，但是有了过期时间之后，可能出现误删别人锁的问题，这个问题我们开始是利用删之前通过拿锁，比锁，删锁这个逻辑来解决的，也就是删之前判断一下当前这把锁是否是属于自己的，但是现在还有原子性问题，也就是我们没法保证拿锁比锁删锁是一个原子性的动作，最后通过`lua`表达式来解决这个问题

- 但是目前还剩下一个问题锁不住，什么是锁不住呢，你想一想，如果当过期时间到了之后，我们可以给他续期一下，比如续个`30s`，就好像是网吧上网， 网费到了之后，然后说，来，网管，再给我来`10`块的，是不是后边的问题都不会发生了，那么续期问题怎么解决呢，可以依赖于我们接下来要学习`redission`啦。


- 测试逻辑：
  - 第一个线程进来，得到了锁，手动删除锁，模拟锁超时了，其他线程会执行lua来抢锁，当第一天线程利用lua删除锁时，lua能保证他不能删除他的锁，第二个线程删除锁时，利用lua同样可以保证不会删除别人的锁，同时还能保证原子性。




## 5、分布式锁-`redission`

### 5.1 、分布式锁-`redission`功能介绍

- 基于`setnx`实现的分布式锁存在下面的问题：

  - 重入问题：重入问题是指获得锁的线程可以再次进入到相同的锁的代码块中，可重入锁的意义在于防止死锁，比如`HashTable`这样的代码中，他的方法都是使用`synchronized`修饰的，假如他在一个方法内，调用另一个方法，那么此时如果是不可重入的，不就死锁了吗？所以可重入锁他的主要意义是防止死锁，我们的`synchronized`和`Lock`锁都是可重入的。

  - 不可重试：是指目前的分布式只能尝试一次，我们认为合理的情况是：当线程在获得锁失败后，他应该能再次尝试获得锁。

  - 超时释放：我们在加锁时增加了过期时间，这样的我们可以防止死锁，但是如果卡顿的时间超长，虽然我们采用了`lua`表达式防止删锁的时候，误删别人的锁，但是毕竟没有锁住，有安全隐患。

  - 主从一致性：如果`Redis`提供了主从集群，当我们向集群写数据时，主机需要异步的将数据同步给从机，而万一在同步过去之前，主机宕机了，就会出现死锁问题。

![1653546070602](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653546070602.png)

- 那么什么是`Redission`呢

  - `Redisson`是一个在`Redis`的基础上实现的`Java`驻内存数据网格（`In-Memory Data Grid`）。它不仅提供了一系列的分布式的`Java`常用对象，还提供了许多分布式服务，其中就包含了各种分布式锁的实现。

  - `Redission`提供了分布式锁的多种多样的功能：

![1653546736063](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653546736063.png)



### 5.2、分布式锁-`Redission`快速入门

- 引入依赖：


```xml
<dependency>
	<groupId>org.redisson</groupId>
	<artifactId>redisson</artifactId>
	<version>3.13.6</version>
</dependency>
```

- 配置`Redisson`客户端：


```java
@Configuration
public class RedissonConfig {
    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        config.useSingleServer().setAddress("redis://192.168.150.101:6379")
            .setPassword("123321");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}
```

- 如何使用`Redission`的分布式锁


```java
@Resource
private RedissionClient redissonClient;

@Test
void testRedisson() throws Exception{
    //获取锁(可重入)，指定锁的名称
    RLock lock = redissonClient.getLock("anyLock");
    //尝试获取锁，参数分别是：获取锁的最大等待时间(期间会重试)，锁自动释放时间，时间单位
    boolean isLock = lock.tryLock(1,10,TimeUnit.SECONDS);
    //判断获取锁成功
    if(isLock){
        try{
            System.out.println("执行业务");          
        }finally{
            //释放锁
            lock.unlock();
        }
        
    }
}
```

- 在`VoucherOrderServiceImpl`，注入`RedissonClient`


```java
@Resource
private RedissonClient redissonClient;

@Override
public Result seckillVoucher(Long voucherId) {
        // 1.查询优惠券
        SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
        // 2.判断秒杀是否开始
        if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
            // 尚未开始
            return Result.fail("秒杀尚未开始！");
        }
        // 3.判断秒杀是否已经结束
        if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
            // 尚未开始
            return Result.fail("秒杀已经结束！");
        }
        // 4.判断库存是否充足
        if (voucher.getStock() < 1) {
            // 库存不足
            return Result.fail("库存不足！");
        }
        Long userId = UserHolder.getUser().getId();
        //创建锁对象 这个代码不用了，因为我们现在要使用分布式锁
        //SimpleRedisLock lock = new SimpleRedisLock("order:" + userId, stringRedisTemplate);
        RLock lock = redissonClient.getLock("lock:order:" + userId);
        //获取锁对象
        boolean isLock = lock.tryLock();
       
		//加锁失败
        if (!isLock) {
            return Result.fail("不允许重复下单");
        }
        try {
            //获取代理对象(事务)
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            return proxy.createVoucherOrder(voucherId);
        } finally {
            //释放锁
            lock.unlock();
        }
 }
```



### 5.3、分布式锁-`redission`可重入锁原理

- 在`Lock`锁中，他是借助于底层的一个`voaltile`的一个`state`变量来记录重入的状态的，比如当前没有人持有这把锁，那么`state=0`，假如有人持有这把锁，那么`state=1`，如果持有这把锁的人再次持有这把锁，那么`state`就会`+1` ，如果是对于`synchronized`而言，他在`c`语言代码中会有一个`count`，原理和`state`类似，也是重入一次就加`1`，释放一次就减`1`，直到减少成`0`时，表示当前这把锁没有被人持有。  

- 在`redission`中，我们的也支持可重入锁。

![1653548087334](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653548087334.png)

- 在分布式锁中，采用`hash`结构用来存储锁，其中大`key`表示这把锁是否存在，用小`key`表示当前这把锁被哪个线程持有，所以接下来我们一起分析一下当前的这个`lua`表达式。

![image-20231012072653633](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012072653633.png)

![image-20231012072729042](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012072729042.png)

- 这个地方一共有`3`个参数：

  - `KEYS[1]`：锁名称

  - `ARGV[1]`：线程唯一标识

  - `ARGV[2]`：锁的自动释放时间

- `exists`：判断数据是否存在。

- `redis.call('hset', KEYS[1], ARGV[2], 1)`：此时它就开始往`redis`里边去写数据 ，写成一个`hash`结构。

- 如果当前这把锁存在，则第一个条件不满足，再判断`redis.call('hexists', KEYS[1], ARGV[2]) == 1`。此时需要通过大`key`+小`key`判断当前这把锁是否是属于自己的，如果是自己的，则进行`redis.call('hincrby', KEYS[1], ARGV[2], 1)`，将当前这个锁的`value`进行`+1`，`redis.call('pexpire', KEYS[1], ARGV[1])`，然后再对其设置过期时间，如果以上两个条件都不满足，则表示当前这把锁抢锁失败，最后返回`pttl`，即为当前这把锁的失效时间。

- 如果小伙帮们看了前边的源码， 你会发现他会去判断当前这个方法的返回值是否为`null`，如果是`null`，则对应则前两个`if`对应的条件，退出抢锁逻辑，如果返回的不是`null`，即走了第三个分支，在源码处会进行`while(true)`的自旋抢锁。

```lua
"if (redis.call('exists', KEYS[1]) == 0) then " +
                  "redis.call('hset', KEYS[1], ARGV[2], 1); " +
                  "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                  "return nil; " +
              "end; " +
              "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                  "redis.call('hincrby', KEYS[1], ARGV[2], 1); " +
                  "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                  "return nil; " +
              "end; " +
              "return redis.call('pttl', KEYS[1]);"
```

~~~lua
if (redis.call('hexists', KEYS[1], ARGV[3]) == 0) then " +
                        "return nil;" +
                        "end; " +
                        "local counter = redis.call('hincrby', KEYS[1], ARGV[3], -1); " +
                        "if (counter > 0) then " +
                        "redis.call('pexpire', KEYS[1], ARGV[2]); " +
                        "return 0; " +
                        "else " +
                        "redis.call('del', KEYS[1]); " +
                        "redis.call('publish', KEYS[2], ARGV[1]); " +
                        "return 1; " +
                        "end; " +
                        "return nil;",
~~~





### 5.4、分布式锁-`redission`锁重试和`WatchDog`机制

![image-20231012084903236](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012084903236.png)

~~~java 
class RedissonLock {
    @Override
    public boolean tryLock(long waitTime, TimeUnit unit) throws InterruptedException {
        return tryLock(waitTime, -1, unit);
    }

    @Override
    public boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException {
        long time = unit.toMillis(waitTime);
        long current = System.currentTimeMillis();
        long threadId = Thread.currentThread().getId();
        Long ttl = tryAcquire(waitTime, leaseTime, unit, threadId);
    }
}
~~~

~~~java 
// WatchdogTimeout 30秒
class RedissonLock {
    private <T> RFuture<Long> tryAcquireAsync(long waitTime, long leaseTime, TimeUnit unit, long threadId) {
        if (leaseTime != -1) {
            return tryLockInnerAsync(waitTime, leaseTime, unit, threadId, RedisCommands.EVAL_LONG);
        }
        RFuture<Long> ttlRemainingFuture = tryLockInnerAsync(waitTime,
                                                             commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout(),
                                                             TimeUnit.MILLISECONDS, threadId, RedisCommands.EVAL_LONG); 
        ttlRemainingFuture.onComplete((ttlRemaining, e) -> {
            if (e != null) {
                return;
            }

            // lock acquired
            if (ttlRemaining == null) {
                scheduleExpirationRenewal(threadId);
            }
        });
        return ttlRemainingFuture;
    }
}
~~~

~~~java
    <T> RFuture<T> tryLockInnerAsync(long waitTime, long leaseTime, TimeUnit unit, long threadId, RedisStrictCommand<T> command) {
        internalLockLeaseTime = unit.toMillis(leaseTime);

        return evalWriteAsync(getName(), LongCodec.INSTANCE, command,
                "if (redis.call('exists', KEYS[1]) == 0) then " +
                        "redis.call('hincrby', KEYS[1], ARGV[2], 1); " +
                        "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                        "return nil; " +
                        "end; " +
                        "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                        "redis.call('hincrby', KEYS[1], ARGV[2], 1); " +
                        "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                        "return nil; " +
                        "end; " +
                        "return redis.call('pttl', KEYS[1]);", // 返回锁的剩余有效期
                Collections.singletonList(getName()), internalLockLeaseTime, getLockName(threadId));
    }
~~~

~~~java
@Override
public boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException {
    long time = unit.toMillis(waitTime);
    long current = System.currentTimeMillis();
    long threadId = Thread.currentThread().getId();
    Long ttl = tryAcquire(waitTime, leaseTime, unit, threadId);
    // lock acquired
    if (ttl == null) { // ttl == null 意味着Lua脚本中前两个条件执行成功要么是首次获取锁成功，要么是重入锁成功
        return true;
    }

    time -= System.currentTimeMillis() - current; // 剩余等待时间
    if (time <= 0) {
        acquireFailed(waitTime, unit, threadId);
        return false;
    }

    current = System.currentTimeMillis();
    RFuture<RedissonLockEntry> subscribeFuture = subscribe(threadId); // 订阅其他线程释放锁的消息
    if (!subscribeFuture.await(time, TimeUnit.MILLISECONDS)) { // 等待消息，当且仅当在指定时间内完成返回true，在这里指的是在剩余等待时间内，Future等待到了消息，就是true
        if (!subscribeFuture.cancel(false)) {
            subscribeFuture.onComplete((res, e) -> {
                if (e == null) {
                    unsubscribe(subscribeFuture, threadId); // 取消订阅
                }
            });
        }
        acquireFailed(waitTime, unit, threadId);
        return false;
    }

    try {
        time -= System.currentTimeMillis() - current; // 剩余等待时间
        if (time <= 0) {
            acquireFailed(waitTime, unit, threadId);
            return false;
        }

        while (true) { // 重试
            long currentTime = System.currentTimeMillis();
            ttl = tryAcquire(waitTime, leaseTime, unit, threadId);
            // lock acquired
            if (ttl == null) {
                return true;
            }

            time -= System.currentTimeMillis() - currentTime;
            if (time <= 0) {
                acquireFailed(waitTime, unit, threadId);
                return false;
            }

            // waiting for message
            currentTime = System.currentTimeMillis();
            if (ttl >= 0 && ttl < time) { 
                subscribeFuture.getNow().getLatch().tryAcquire(ttl, TimeUnit.MILLISECONDS);
            } else {
                subscribeFuture.getNow().getLatch().tryAcquire(time, TimeUnit.MILLISECONDS);
            }

            time -= System.currentTimeMillis() - currentTime;
            if (time <= 0) {
                acquireFailed(waitTime, unit, threadId);
                return false;
            }
        }
    } finally {
        unsubscribe(subscribeFuture, threadId);
    }
    //        return get(tryLockAsync(waitTime, leaseTime, unit));
}
~~~

- 释放锁时发布一条消息

![image-20231012090500407](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012090500407.png)

- 抢锁过程中，获得当前线程，通过`tryAcquire`进行抢锁，该抢锁逻辑和之前逻辑相同：

  - 先判断当前这把锁是否存在，如果不存在，插入一把锁，返回`null`。

  - 判断当前这把锁是否是属于当前线程，如果是，则返回`null`。

- 所以如果返回是`null`，则代表着当前已经抢锁完毕，或者可重入完毕，但是如果以上两个条件都不满足，则进入到第三个条件，返回的是锁的失效时间，可以自行往下翻一点点，你能发现有个`while( true) `再次进行`tryAcquire`进行抢锁。

```java
long threadId = Thread.currentThread().getId();
Long ttl = tryAcquire(-1, leaseTime, unit, threadId);
// lock acquired
if (ttl == null) {
    return;
}
```

- 接下来会有一个条件分支，因为`lock`方法有重载方法，一个是带参数，一个是不带参数，如果带参数传入的值是`-1`，如果传入参数，则`leaseTime`是他本身，所以如果传入了参数，此时`leaseTime != -1`则会进去抢锁，抢锁的逻辑就是之前说的那三个逻辑：


```java
if (leaseTime != -1) {
    return tryLockInnerAsync(waitTime, leaseTime, unit, threadId, RedisCommands.EVAL_LONG);
}
```

- 如果是没有传入时间，则此时也会进行抢锁， 而且抢锁时间是默认看门狗时间 `commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout()`

- `ttlRemainingFuture.onComplete((ttlRemaining, e)`这句话相当于对以上抢锁进行了监听，也就是说当上边抢锁完毕后，此方法会被调用，具体调用的逻辑就是去后台开启一个线程，进行续约逻辑，也就是看门狗线程。

```java
RFuture<Long> ttlRemainingFuture = tryLockInnerAsync(waitTime,
                                        commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout(),
                                        TimeUnit.MILLISECONDS, threadId, RedisCommands.EVAL_LONG);
ttlRemainingFuture.onComplete((ttlRemaining, e) -> {
    if (e != null) {
        return;
    }

    // lock acquired
    if (ttlRemaining == null) { // 剩余有效期等于null，获取锁成功
        scheduleExpirationRenewal(threadId); // 自动更新续约时间
    }
});
return ttlRemainingFuture;
```

~~~java
private void scheduleExpirationRenewal(long threadId) {
    ExpirationEntry entry = new ExpirationEntry();
    ExpirationEntry oldEntry = EXPIRATION_RENEWAL_MAP.putIfAbsent(getEntryName(), entry);// 一个RedissonLock对象可以创建出很多锁的实例，每个锁的实例都会有自己的名字，所以一个锁对应一个entry
    if (oldEntry != null) {
        oldEntry.addThreadId(threadId); // 不同线程不可能在同一时间拿到同一把锁
    } else {
        entry.addThreadId(threadId);
        renewExpiration(); // 更新有效期
    }
}
protected String getEntryName() { // 当前锁的名称
    return entryName;
}
~~~

```java
private void renewExpiration() {
    ExpirationEntry ee = EXPIRATION_RENEWAL_MAP.get(getEntryName());
    if (ee == null) {
        return;
    }

    Timeout task = commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
        @Override
        public void run(Timeout timeout) throws Exception {
            ExpirationEntry ent = EXPIRATION_RENEWAL_MAP.get(getEntryName());
            if (ent == null) {
                return;
            }
            Long threadId = ent.getFirstThreadId();
            if (threadId == null) {
                return;
            }

            RFuture<Boolean> future = renewExpirationAsync(threadId); // 更新有效期
            future.onComplete((res, e) -> {
                if (e != null) {
                    log.error("Can't update lock " + getName() + " expiration", e);
                    return;
                }

                if (res) {
                    // reschedule itself
                    renewExpiration(); // 递归
                }
            });
        }
    }, internalLockLeaseTime / 3, TimeUnit.MILLISECONDS);

    ee.setTimeout(task);
}

protected RFuture<Boolean> renewExpirationAsync(long threadId) {
    return evalWriteAsync(getName(), LongCodec.INSTANCE, RedisCommands.EVAL_BOOLEAN,
                          "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                          "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                          "return 1; " +
                          "end; " +
                          "return 0;",
                          Collections.singletonList(getName()),
                          internalLockLeaseTime, getLockName(threadId));
}
```

- 此逻辑就是续约逻辑，注意看`commandExecutor.getConnectionManager().newTimeout(TimerTask task,long delay,TimeUnit unit)`此方法`Method(new TimerTask() {},参数2 ，参数3)`。
- 指的是：通过参数`2`，参数`3`去描述什么时候去做参数`1`的事情，现在的情况是：`10s`之后去做参数`1`的事情，因为锁的失效时间是`30s`，当`10s`之后，此时这个`timeTask`就触发了，它就去进行续约，把当前这把锁续约成`30s`，如果操作成功，那么此时就会递归调用自己，再重新设置一个`timeTask()`，于是再过`10s`后又再设置一个`timerTask`，完成不停的续约。
- 那么大家可以想一想，假设我们的线程出现了宕机他还会续约吗？当然不会，因为没有人再去调用`renewExpiration`这个方法，所以等到时间之后自然就释放了。

~~~java 
@Override
public RFuture<Void> unlockAsync(long threadId) {
    RPromise<Void> result = new RedissonPromise<Void>();
    RFuture<Boolean> future = unlockInnerAsync(threadId);

    future.onComplete((opStatus, e) -> {
        cancelExpirationRenewal(threadId); // 取消更新有效期

        if (e != null) {
            result.tryFailure(e);
            return;
        }

        if (opStatus == null) {
            IllegalMonitorStateException cause = new IllegalMonitorStateException("attempt to unlock lock, not locked by current thread by node id: "
                                                                                  + id + " thread-id: " + threadId);
            result.tryFailure(cause);
            return;
        }

        result.trySuccess(null);
    });

    return result;
}

void cancelExpirationRenewal(Long threadId) {
        ExpirationEntry task = EXPIRATION_RENEWAL_MAP.get(getEntryName());
        if (task == null) {
            return;
        }
        
        if (threadId != null) {
            task.removeThreadId(threadId);
        }

        if (threadId == null || task.hasNoThreads()) {
            Timeout timeout = task.getTimeout();
            if (timeout != null) {
                timeout.cancel();
            }
            EXPIRATION_RENEWAL_MAP.remove(getEntryName());
        }
    }
~~~

![image-20231012093831418](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012093831418.png)



> 注：`Redisson`分布式锁原理：
>
> - 可重入：利用`hash`结构记录线程`id`和重入次数。
> - 可重试：利用信号量和`PubSub`功能实现等待、唤醒、获取锁失败的重试机制。
> - 超时续约：利用`watchDog`，每隔一段时间（`releaseTime / 3`），重置超时时间。



### 5.5、分布式锁-`redission`锁的`MutiLock`原理

- 为了提高`redis`的可用性，我们会搭建集群或者主从，现在以主从为例。

- 此时我们去写命令，写在主机上， 主机会将数据同步给从机，但是假设在主机还没有来得及把数据写入到从机去的时候，此时主机宕机，哨兵会发现主机宕机，并且选举一个`slave`变成`master`，而此时新的`master`中实际上并没有锁信息，此时锁信息就已经丢掉了。


![1653553998403](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653553998403.png)

- 为了解决这个问题，`redission`提出来了`MutiLock`锁，使用这把锁咱们就不使用主从了，每个节点的地位都是一样的， 这把锁加锁的逻辑需要写入到每一个主从节点上，只有所有的服务器都写入成功，此时才是加锁成功，假设现在某个节点挂了，那么它去获得锁的时候，只要有一个节点拿不到，都不能算是加锁成功，就保证了加锁的可靠性。


![1653554055048](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653554055048.png)

- 那么`MutiLock`加锁原理是什么呢？笔者画了一幅图来说明：

- 当我们去设置了多个锁时，`redission`会将多个锁添加到一个集合中，然后用`while`循环去不停去尝试拿锁，但是会有一个总共的加锁时间，这个时间是用需要加锁的个数 * `1500ms` ，假设有`3`个锁，那么时间就是`4500ms`，假设在这`4500ms`内，所有的锁都加锁成功， 那么此时才算是加锁成功，如果在`4500ms`有线程加锁失败，则会再次去进行重试。


![1653553093967](.\Redis实战篇.assets\1653553093967.png)

~~~Java
@Override
public boolean tryLock(long waitTime, long leaseTime, TimeUnit unit) throws InterruptedException {
    long newLeaseTime = -1;
    if (leaseTime != -1) {
        if (waitTime == -1) {
            newLeaseTime = unit.toMillis(leaseTime);
        } else {
            newLeaseTime = unit.toMillis(waitTime)*2;
        }
    }

    long time = System.currentTimeMillis();
    long remainTime = -1; // 剩余等待时间
    if (waitTime != -1) {
        remainTime = unit.toMillis(waitTime);
    }
    long lockWaitTime = calcLockWaitTime(remainTime);

    int failedLocksLimit = failedLocksLimit();
    List<RLock> acquiredLocks = new ArrayList<>(locks.size());
    for (ListIterator<RLock> iterator = locks.listIterator(); iterator.hasNext();) {
        RLock lock = iterator.next();
        boolean lockAcquired;
        try {
            if (waitTime == -1 && leaseTime == -1) {
                lockAcquired = lock.tryLock();
            } else {
                long awaitTime = Math.min(lockWaitTime, remainTime);
                lockAcquired = lock.tryLock(awaitTime, newLeaseTime, TimeUnit.MILLISECONDS);
            }
        } catch (RedisResponseTimeoutException e) {
            unlockInner(Arrays.asList(lock));
            lockAcquired = false;
        } catch (Exception e) {
            lockAcquired = false;
        }

        if (lockAcquired) {
            acquiredLocks.add(lock);
        } else {
            if (locks.size() - acquiredLocks.size() == failedLocksLimit()) {
                break;
            }

            if (failedLocksLimit == 0) {
                unlockInner(acquiredLocks);
                if (waitTime == -1) {
                    return false;
                }
                failedLocksLimit = failedLocksLimit();
                acquiredLocks.clear();
                // reset iterator
                while (iterator.hasPrevious()) {
                    iterator.previous();
                }
            } else {
                failedLocksLimit--;
            }
        }

        if (remainTime != -1) {
            remainTime -= System.currentTimeMillis() - time;
            time = System.currentTimeMillis();
            if (remainTime <= 0) {
                unlockInner(acquiredLocks);
                return false;
            }
        }
    }

    if (leaseTime != -1) {
        List<RFuture<Boolean>> futures = new ArrayList<>(acquiredLocks.size());
        for (RLock rLock : acquiredLocks) {
            RFuture<Boolean> future = ((RedissonLock) rLock).expireAsync(unit.toMillis(leaseTime), TimeUnit.MILLISECONDS);
            futures.add(future);
        }

        for (RFuture<Boolean> rFuture : futures) {
            rFuture.syncUninterruptibly();
        }
    }

    return true;
}
~~~



> 注：
>
> - 不可重入`Redis`分布式锁：
>   - 原理：利用`setnx`的互斥性；利用`ex`避免死锁，释放锁时判断线程标识。
>   - 缺陷：不可重入、无法重试、锁超时失效。
> - 可重入的`Redis`分布式锁：
>   - 原理：利用`hash`结构，记录线程标识和重入次数；利用`watchDog`延续锁时间；利用信号量控制锁重试等待。
>   - 缺陷：`redis`宕机引起锁失效问题。
> - `Redisson`的`multiLock`：
>   - 原理：多个独立的`Redis`节点，必须在所有节点都获取重入锁，才算获取锁成功。
>   - 缺陷：运维成本高，实现复杂。



## 6、秒杀优化

### 6.1、秒杀优化-异步秒杀思路

- 我们来回顾一下下单流程：当用户发起请求，此时会请求`nginx`，`nginx`会访问到`tomcat`，而`tomcat`中的程序，会进行串行操作，分成如下几个步骤：

  - 查询优惠卷


  - 判断秒杀库存是否足够


  - 查询订单

  - 校验是否是一人一单

  - 扣减库存


  - 创建订单

- 在这六步操作中，又有很多操作是要去操作数据库的，而且还是一个线程串行执行， 这样就会导致我们的程序执行的很慢，所以我们需要异步程序执行，那么如何加速呢？
- 比如，我们可以不可以使用异步编排来做，或者说我开启`N`多线程，`N`多个线程，一个线程执行查询优惠卷，一个执行判断扣减库存，一个去创建订单等等，然后再统一做返回，这种做法和课程中有哪种好呢？答案是课程中的好，因为如果你采用我刚说的方式，如果访问的人很多，那么线程池中的线程可能一下子就被消耗完了，而且你使用上述方案，最大的特点在于，你觉得时效性会非常重要，但是你想想是吗？并不是，比如我只要确定他能做这件事，然后我后边慢慢做就可以了，我并不需要他一口气做完这件事，所以我们应当采用的是课程中，类似消息队列的方式来完成我们的需求，而不是使用线程池或者是异步编排的方式来完成这个需求。


![1653560986599](.\Redis实战篇.assets\1653560986599.png)

- 优化方案：我们将耗时比较短的逻辑判断放入到`redis`中，比如是否库存足够，比如是否一人一单，这样的操作，只要这种逻辑可以完成，就意味着我们是一定可以下单完成的，我们只需要进行快速的逻辑判断，根本就不用等下单逻辑走完，我们直接给用户返回成功， 再在后台开一个线程，后台线程慢慢的去执行`queue`里边的消息，这样程序不就超级快了吗？而且也不用担心线程池消耗殆尽的问题，因为这里我们的程序中并没有手动使用任何线程池，当然这里边有两个难点：

  - 第一个难点是我们怎么在`redis`中去快速校验一人一单，还有库存判断。

  - 第二个难点是由于我们校验和`tomct`下单是两个线程，那么我们如何知道到底哪个单他最后是否成功，或者是下单完成，为了完成这件事我们在`redis`操作完之后，我们会将一些信息返回给前端，同时也会把这些信息丢到异步`queue`中去，后续操作中，可以通过这个`id`来查询我们`tomcat`中的下单逻辑是否完成了。


![1653561657295](.\Redis实战篇.assets\1653561657295.png)

- 我们现在来看看整体思路：当用户下单之后，判断库存是否充足只需要到`redis`中去根据`key`找对应的`value`是否大于`0`即可，如果不充足，则直接结束，如果充足，继续在`redis`中判断用户是否可以下单，如果`set`集合中没有这个用户，说明它可以下单，可以扣减库存，并将`userId`和优惠卷存入到`redis`中，并且返回`0`，整个过程需要保证是原子性的，我们可以使用`lua`来操作。

- 当以上判断逻辑走完之后，我们可以判断当前`redis`中返回的结果是否是`0` ，如果是`0`，则表示可以下单，则将之前说的信息存入到到`queue`中去，然后返回，然后再来个线程异步的下单，前端可以通过返回的订单`id`来判断是否下单成功。


![1653562234886](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653562234886.png)





### 6.2、秒杀优化-`Redis`完成秒杀资格判断

- 需求：

  * 新增秒杀优惠券的同时，将优惠券信息保存到`Redis`中


  * 基于`Lua`脚本，判断秒杀库存、一人一单，决定用户是否抢购成功


  * 如果抢购成功，将优惠券`id`和用户`id`封装后存入阻塞队列


  * 开启线程任务，不断从阻塞队列中获取信息，实现异步下单功能

    ![1656080546603](.\Redis实战篇.assets\1656080546603.png)


- `VoucherServiceImpl`

```java
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);
    // 保存秒杀库存到Redis中
    //SECKILL_STOCK_KEY 这个变量定义在RedisConstans中
    //private static final String SECKILL_STOCK_KEY ="seckill:stock:"
    stringRedisTemplate.opsForValue().set(SECKILL_STOCK_KEY + voucher.getId(), voucher.getStock().toString());
}
```

~~~
http://localhost:8081/voucher/seckill
~~~

~~~json
{
    "shopId": 1,
    "title": "100元代金券",
    "subTitle": "周一至周五均可使用",
    "rules": "全场通用\\n无需预约\\n可无限叠加\\不兑现、不找零\\n仅限堂食",
    "payValue": 8000,
    "actualValue": 10000,
    "type": 1,
    "stock": 100,
    "beginTime": "2023-08-11T10:09:12",
    "endTime": "2023-11-11T10:09:12"
}
~~~

- 完整`lua`表达式

```lua
-- 1.参数列表
-- 1.1.优惠券id
local voucherId = ARGV[1]
-- 1.2.用户id
local userId = ARGV[2]
-- 1.3.订单id
local orderId = ARGV[3]

-- 2.数据key
-- 2.1.库存key
local stockKey = 'seckill:stock:' .. voucherId
-- 2.2.订单key
local orderKey = 'seckill:order:' .. voucherId

-- 3.脚本业务
-- 3.1.判断库存是否充足 get stockKey
if(tonumber(redis.call('get', stockKey)) <= 0) then
    -- 3.2.库存不足，返回1
    return 1
end
-- 3.2.判断用户是否下单 SISMEMBER orderKey userId
if(redis.call('sismember', orderKey, userId) == 1) then
    -- 3.3.存在，说明是重复下单，返回2
    return 2
end
-- 3.4.扣库存 incrby stockKey -1
redis.call('incrby', stockKey, -1)
-- 3.5.下单（保存用户）sadd orderKey userId
redis.call('sadd', orderKey, userId)
-- 3.6.发送消息到队列中， XADD stream.orders * k1 v1 k2 v2 ...
redis.call('xadd', 'stream.orders', '*', 'userId', userId, 'voucherId', voucherId, 'id', orderId)
return 0
```

- 当以上`lua`表达式执行完毕后，剩下的就是根据步骤`3`,`4`来执行我们接下来的任务了。

- `VoucherOrderServiceImpl`

```java
@Override
public Result seckillVoucher(Long voucherId) {
    //获取用户
    Long userId = UserHolder.getUser().getId();
    long orderId = redisIdWorker.nextId("order");
    // 1.执行lua脚本
    Long result = stringRedisTemplate.execute(
            SECKILL_SCRIPT,
            Collections.emptyList(),
            voucherId.toString(), userId.toString(), String.valueOf(orderId)
    );
    int r = result.intValue();
    // 2.判断结果是否为0
    if (r != 0) {
        // 2.1.不为0 ，代表没有购买资格
        return Result.fail(r == 1 ? "库存不足" : "不能重复下单");
    }
    //TODO 保存阻塞队列
    // 3.返回订单id
    return Result.ok(orderId);
}
```

![image-20231012170801246](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012170801246.png)

 ![image-20231012170753582](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20231012170753582.png)



### 6.3、秒杀优化-基于阻塞队列实现秒杀优化

- `VoucherOrderServiceImpl`
- 修改下单动作，现在我们去下单时，是通过lua表达式去原子执行判断逻辑，如果判断我出来不为`0`，则要么是库存不足，要么是重复下单，返回错误信息，如果是`0`，则把下单的逻辑保存到队列中去，然后异步执行。

```java
//异步处理线程池
private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();

//在类初始化之后执行，因为当这个类初始化好了之后，随时都是有可能要执行的
@PostConstruct
private void init() {
   SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
}
// 用于线程池处理的任务
// 当初始化完毕后，就会去从对列中去拿信息
 private class VoucherOrderHandler implements Runnable{

        @Override
        public void run() {
            while (true){
                try {
                    // 1.获取队列中的订单信息
                    VoucherOrder voucherOrder = orderTasks.take();
                    // 2.创建订单
                    handleVoucherOrder(voucherOrder);
                } catch (Exception e) {
                    log.error("处理订单异常", e);
                }
          	 }
        }
     
       private void handleVoucherOrder(VoucherOrder voucherOrder) {
            //1.获取用户
            Long userId = voucherOrder.getUserId();
            // 2.创建锁对象 
            RLock redisLock = redissonClient.getLock("lock:order:" + userId);
            // 3.尝试获取锁
            boolean isLock = redisLock.lock();
            // 4.判断是否获得锁成功
            if (!isLock) {
                // 获取锁失败，直接返回失败或者重试
                log.error("不允许重复下单！");
                return;
            }
            try {
				//注意：由于是spring的事务是放在threadLocal中，此时的是多线程，事务会失效
                proxy.createVoucherOrder(voucherOrder);
            } finally {
                // 释放锁
                redisLock.unlock();
            }
    }
     //a
	private BlockingQueue<VoucherOrder> orderTasks =new  ArrayBlockingQueue<>(1024 * 1024);

    @Override
    public Result seckillVoucher(Long voucherId) {
        Long userId = UserHolder.getUser().getId();
        long orderId = redisIdWorker.nextId("order");
        // 1.执行lua脚本
        Long result = stringRedisTemplate.execute(
                SECKILL_SCRIPT,
                Collections.emptyList(),
                voucherId.toString(), userId.toString(), String.valueOf(orderId)
        );
        int r = result.intValue();
        // 2.判断结果是否为0
        if (r != 0) {
            // 2.1.不为0 ，代表没有购买资格
            return Result.fail(r == 1 ? "库存不足" : "不能重复下单");
        }
        VoucherOrder voucherOrder = new VoucherOrder();
        // 2.3.订单id
        long orderId = redisIdWorker.nextId("order");
        voucherOrder.setId(orderId);
        // 2.4.用户id
        voucherOrder.setUserId(userId);
        // 2.5.代金券id
        voucherOrder.setVoucherId(voucherId);
        // 2.6.放入阻塞队列
        orderTasks.add(voucherOrder);
        //3.获取代理对象
         proxy = (IVoucherOrderService)AopContext.currentProxy();
        //4.返回订单id
        return Result.ok(orderId);
    }
     
      @Transactional
    public  void createVoucherOrder(VoucherOrder voucherOrder) {
        Long userId = voucherOrder.getUserId();
        // 5.1.查询订单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherOrder.getVoucherId()).count();
        // 5.2.判断是否存在
        if (count > 0) {
            // 用户已经购买过了
           log.error("用户已经购买过了");
           return ;
        }

        // 6.扣减库存
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1") // set stock = stock - 1
                .eq("voucher_id", voucherOrder.getVoucherId()).gt("stock", 0) // where id = ? and stock > 0
                .update();
        if (!success) {
            // 扣减失败
            log.error("库存不足");
            return ;
        }
        save(voucherOrder);
 
    }

```

> 总结：秒杀业务的优化思路是什么？
>
> - 先利用`Redis`完成库存余量、一人一单判断，完成抢单业务。
> - 再将下单业务放入阻塞队列，利用独立线程异步下单。
>
> 基于阻塞队列的异步秒杀存在哪些问题？
>
> - 内存限制问题。
> - 数据安全问题。





## 7、`Redis`消息队列

### 7.1、`Redis`消息队列-认识消息队列

- 什么是消息队列：字面意思就是存放消息的队列。最简单的消息队列模型包括3个角色：

  * 消息队列：存储和管理消息，也被称为消息代理（Message Broker）

  * 生产者：发送消息到消息队列

  * 消费者：从消息队列获取消息并处理消息


![1653574849336](.\Redis实战篇.assets\1653574849336.png)

使用队列的好处在于 **解耦：**所谓解耦，举一个生活中的例子就是：快递员(生产者)把快递放到快递柜里边(Message Queue)去，我们(消费者)从快递柜里边去拿东西，这就是一个异步，如果耦合，那么这个快递员相当于直接把快递交给你，这事固然好，但是万一你不在家，那么快递员就会一直等你，这就浪费了快递员的时间，所以这种思想在我们日常开发中，是非常有必要的。

这种场景在我们秒杀中就变成了：我们下单之后，利用redis去进行校验下单条件，再通过队列把消息发送出去，然后再启动一个线程去消费这个消息，完成解耦，同时也加快我们的响应速度。

这里我们可以使用一些现成的mq，比如kafka，rabbitmq等等，但是呢，如果没有安装mq，我们也可以直接使用redis提供的mq方案，降低我们的部署和学习成本。



### 7.2 Redis消息队列-基于List实现消息队列

**基于List结构模拟消息队列**

消息队列（Message Queue），字面意思就是存放消息的队列。而Redis的list数据结构是一个双向链表，很容易模拟出队列效果。

队列是入口和出口不在一边，因此我们可以利用：LPUSH 结合 RPOP、或者 RPUSH 结合 LPOP来实现。
不过要注意的是，当队列中没有消息时RPOP或LPOP操作会返回null，并不像JVM的阻塞队列那样会阻塞并等待消息。因此这里应该使用BRPOP或者BLPOP来实现阻塞效果。

![1653575176451](.\Redis实战篇.assets\1653575176451.png)

基于List的消息队列有哪些优缺点？
优点：

* 利用Redis存储，不受限于JVM内存上限
* 基于Redis的持久化机制，数据安全性有保证
* 可以满足消息有序性

缺点：

* 无法避免消息丢失
* 只支持单消费者



### 7.3 Redis消息队列-基于PubSub的消息队列

PubSub（发布订阅）是Redis2.0版本引入的消息传递模型。顾名思义，消费者可以订阅一个或多个channel，生产者向对应channel发送消息后，所有订阅者都能收到相关消息。

 SUBSCRIBE channel [channel] ：订阅一个或多个频道
 PUBLISH channel msg ：向一个频道发送消息
 PSUBSCRIBE pattern[pattern] ：订阅与pattern格式匹配的所有频道

![1653575506373](.\Redis实战篇.assets\1653575506373.png)

基于PubSub的消息队列有哪些优缺点？
优点：

* 采用发布订阅模型，支持多生产、多消费

缺点：

* 不支持数据持久化
* 无法避免消息丢失
* 消息堆积有上限，超出时数据丢失



### 7.4 Redis消息队列-基于Stream的消息队列

Stream 是 Redis 5.0 引入的一种新数据类型，可以实现一个功能非常完善的消息队列。

发送消息的命令：

![1653577301737](.\Redis实战篇.assets\1653577301737.png)

例如：

![1653577349691](.\Redis实战篇.assets\1653577349691.png)

读取消息的方式之一：XREAD

![1653577445413](.\Redis实战篇.assets\1653577445413.png)

例如，使用XREAD读取第一个消息：

![1653577643629](.\Redis实战篇.assets\1653577643629.png)

XREAD阻塞方式，读取最新的消息：

![1653577659166](.\Redis实战篇.assets\1653577659166.png)

在业务开发中，我们可以循环的调用XREAD阻塞方式来查询最新消息，从而实现持续监听队列的效果，伪代码如下

![1653577689129](.\Redis实战篇.assets\1653577689129.png)

注意：当我们指定起始ID为$时，代表读取最新的消息，如果我们处理一条消息的过程中，又有超过1条以上的消息到达队列，则下次获取时也只能获取到最新的一条，会出现漏读消息的问题

STREAM类型消息队列的XREAD命令特点：

* 消息可回溯
* 一个消息可以被多个消费者读取
* 可以阻塞读取
* 有消息漏读的风险



### 7.5 Redis消息队列-基于Stream的消息队列-消费者组

消费者组（Consumer Group）：将多个消费者划分到一个组中，监听同一个队列。具备下列特点：

![1653577801668](.\Redis实战篇.assets\1653577801668.png)

创建消费者组：
![1653577984924](.\Redis实战篇.assets\1653577984924.png)
key：队列名称
groupName：消费者组名称
ID：起始ID标示，$代表队列中最后一个消息，0则代表队列中第一个消息
MKSTREAM：队列不存在时自动创建队列
其它常见命令：

 **删除指定的消费者组**

```java
XGROUP DESTORY key groupName
```

 **给指定的消费者组添加消费者**

```java
XGROUP CREATECONSUMER key groupname consumername
```

 **删除消费者组中的指定消费者**

```java
XGROUP DELCONSUMER key groupname consumername
```

从消费者组读取消息：

```java
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] ID [ID ...]
```

* group：消费组名称
* consumer：消费者名称，如果消费者不存在，会自动创建一个消费者
* count：本次查询的最大数量
* BLOCK milliseconds：当没有消息时最长等待时间
* NOACK：无需手动ACK，获取到消息后自动确认
* STREAMS key：指定队列名称
* ID：获取消息的起始ID：

">"：从下一个未消费的消息开始
其它：根据指定id从pending-list中获取已消费但未确认的消息，例如0，是从pending-list中的第一个消息开始

消费者监听消息的基本思路：

![1653578211854](.\Redis实战篇.assets\1653578211854.png)STREAM类型消息队列的XREADGROUP命令特点：

* 消息可回溯
* 可以多消费者争抢消息，加快消费速度
* 可以阻塞读取
* 没有消息漏读的风险
* 有消息确认机制，保证消息至少被消费一次

最后我们来个小对比

![1653578560691](.\Redis实战篇.assets\1653578560691.png)

### 7.6 基于Redis的Stream结构作为消息队列，实现异步秒杀下单

需求：

* 创建一个Stream类型的消息队列，名为stream.orders
* 修改之前的秒杀下单Lua脚本，在认定有抢购资格后，直接向stream.orders中添加消息，内容包含voucherId、userId、orderId
* 项目启动时，开启一个线程任务，尝试获取stream.orders中的消息，完成下单\

修改lua表达式,新增3.6 

![1656082824939](.\Redis实战篇.assets\1656082824939.png)

VoucherOrderServiceImpl

```java
private class VoucherOrderHandler implements Runnable {

    @Override
    public void run() {
        while (true) {
            try {
                // 1.获取消息队列中的订单信息 XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
                List<MapRecord<String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                    Consumer.from("g1", "c1"),
                    StreamReadOptions.empty().count(1).block(Duration.ofSeconds(2)),
                    StreamOffset.create("stream.orders", ReadOffset.lastConsumed())
                );
                // 2.判断订单信息是否为空
                if (list == null || list.isEmpty()) {
                    // 如果为null，说明没有消息，继续下一次循环
                    continue;
                }
                // 解析数据
                MapRecord<String, Object, Object> record = list.get(0);
                Map<Object, Object> value = record.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);
                // 3.创建订单
                createVoucherOrder(voucherOrder);
                // 4.确认消息 XACK
                stringRedisTemplate.opsForStream().acknowledge("s1", "g1", record.getId());
            } catch (Exception e) {
                log.error("处理订单异常", e);
                //处理异常消息
                handlePendingList();
            }
        }
    }

    private void handlePendingList() {
        while (true) {
            try {
                // 1.获取pending-list中的订单信息 XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 0
                List<MapRecord<String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                    Consumer.from("g1", "c1"),
                    StreamReadOptions.empty().count(1),
                    StreamOffset.create("stream.orders", ReadOffset.from("0"))
                );
                // 2.判断订单信息是否为空
                if (list == null || list.isEmpty()) {
                    // 如果为null，说明没有异常消息，结束循环
                    break;
                }
                // 解析数据
                MapRecord<String, Object, Object> record = list.get(0);
                Map<Object, Object> value = record.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);
                // 3.创建订单
                createVoucherOrder(voucherOrder);
                // 4.确认消息 XACK
                stringRedisTemplate.opsForStream().acknowledge("s1", "g1", record.getId());
            } catch (Exception e) {
                log.error("处理pendding订单异常", e);
                try{
                    Thread.sleep(20);
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }
    }
}

```

## 8、达人探店

### 8.1、达人探店-发布探店笔记

发布探店笔记

探店笔记类似点评网站的评价，往往是图文结合。对应的表有两个：
tb_blog：探店笔记表，包含笔记中的标题、文字、图片等
tb_blog_comments：其他用户对探店笔记的评价

**具体发布流程**

![1653578992639](.\Redis实战篇.assets\1653578992639.png)

上传接口

```java
@Slf4j
@RestController
@RequestMapping("upload")
public class UploadController {

    @PostMapping("blog")
    public Result uploadImage(@RequestParam("file") MultipartFile image) {
        try {
            // 获取原始文件名称
            String originalFilename = image.getOriginalFilename();
            // 生成新文件名
            String fileName = createNewFileName(originalFilename);
            // 保存文件
            image.transferTo(new File(SystemConstants.IMAGE_UPLOAD_DIR, fileName));
            // 返回结果
            log.debug("文件上传成功，{}", fileName);
            return Result.ok(fileName);
        } catch (IOException e) {
            throw new RuntimeException("文件上传失败", e);
        }
    }

}
```

注意：同学们在操作时，需要修改SystemConstants.IMAGE_UPLOAD_DIR 自己图片所在的地址，在实际开发中图片一般会放在nginx上或者是云存储上。

BlogController

```java
@RestController
@RequestMapping("/blog")
public class BlogController {

    @Resource
    private IBlogService blogService;

    @PostMapping
    public Result saveBlog(@RequestBody Blog blog) {
        //获取登录用户
        UserDTO user = UserHolder.getUser();
        blog.setUpdateTime(user.getId());
        //保存探店博文
        blogService.saveBlog(blog);
        //返回id
        return Result.ok(blog.getId());
    }
}
```

### 8.2 达人探店-查看探店笔记

实现查看发布探店笔记的接口

![1653579931626](.\Redis实战篇.assets\1653579931626.png)

实现代码：

BlogServiceImpl

```java
@Override
public Result queryBlogById(Long id) {
    // 1.查询blog
    Blog blog = getById(id);
    if (blog == null) {
        return Result.fail("笔记不存在！");
    }
    // 2.查询blog有关的用户
    queryBlogUser(blog);
  
    return Result.ok(blog);
}
```

### 8.3 达人探店-点赞功能

初始代码

```java
@GetMapping("/likes/{id}")
public Result queryBlogLikes(@PathVariable("id") Long id) {
    //修改点赞数量
    blogService.update().setSql("liked = liked +1 ").eq("id",id).update();
    return Result.ok();
}
```

问题分析：这种方式会导致一个用户无限点赞，明显是不合理的

造成这个问题的原因是，我们现在的逻辑，发起请求只是给数据库+1，所以才会出现这个问题

![1653581590453](.\Redis实战篇.assets\1653581590453.png)

完善点赞功能

需求：

* 同一个用户只能点赞一次，再次点击则取消点赞
* 如果当前用户已经点赞，则点赞按钮高亮显示（前端已实现，判断字段Blog类的isLike属性）

实现步骤：

* 给Blog类中添加一个isLike字段，标示是否被当前用户点赞
* 修改点赞功能，利用Redis的set集合判断是否点赞过，未点赞过则点赞数+1，已点赞过则点赞数-1
* 修改根据id查询Blog的业务，判断当前登录用户是否点赞过，赋值给isLike字段
* 修改分页查询Blog业务，判断当前登录用户是否点赞过，赋值给isLike字段

为什么采用set集合：

因为我们的数据是不能重复的，当用户操作过之后，无论他怎么操作，都是

具体步骤：

1、在Blog 添加一个字段

```java
@TableField(exist = false)
private Boolean isLike;
```

2、修改代码

```java
 @Override
    public Result likeBlog(Long id){
        // 1.获取登录用户
        Long userId = UserHolder.getUser().getId();
        // 2.判断当前登录用户是否已经点赞
        String key = BLOG_LIKED_KEY + id;
        Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
        if(BooleanUtil.isFalse(isMember)){
             //3.如果未点赞，可以点赞
            //3.1 数据库点赞数+1
            boolean isSuccess = update().setSql("liked = liked + 1").eq("id", id).update();
            //3.2 保存用户到Redis的set集合
            if(isSuccess){
                stringRedisTemplate.opsForSet().add(key,userId.toString());
            }
        }else{
             //4.如果已点赞，取消点赞
            //4.1 数据库点赞数-1
            boolean isSuccess = update().setSql("liked = liked - 1").eq("id", id).update();
            //4.2 把用户从Redis的set集合移除
            if(isSuccess){
                stringRedisTemplate.opsForSet().remove(key,userId.toString());
            }
        }
```

### 8.4 达人探店-点赞排行榜

在探店笔记的详情页面，应该把给该笔记点赞的人显示出来，比如最早点赞的TOP5，形成点赞排行榜：

之前的点赞是放到set集合，但是set集合是不能排序的，所以这个时候，咱们可以采用一个可以排序的set集合，就是咱们的sortedSet

![1653805077118](.\Redis实战篇.assets\1653805077118.png)

我们接下来来对比一下这些集合的区别是什么

所有点赞的人，需要是唯一的，所以我们应当使用set或者是sortedSet

其次我们需要排序，就可以直接锁定使用sortedSet啦

![1653805203758](.\Redis实战篇.assets\1653805203758.png)

修改代码

BlogServiceImpl

点赞逻辑代码

```java
   @Override
    public Result likeBlog(Long id) {
        // 1.获取登录用户
        Long userId = UserHolder.getUser().getId();
        // 2.判断当前登录用户是否已经点赞
        String key = BLOG_LIKED_KEY + id;
        Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
        if (score == null) {
            // 3.如果未点赞，可以点赞
            // 3.1.数据库点赞数 + 1
            boolean isSuccess = update().setSql("liked = liked + 1").eq("id", id).update();
            // 3.2.保存用户到Redis的set集合  zadd key value score
            if (isSuccess) {
                stringRedisTemplate.opsForZSet().add(key, userId.toString(), System.currentTimeMillis());
            }
        } else {
            // 4.如果已点赞，取消点赞
            // 4.1.数据库点赞数 -1
            boolean isSuccess = update().setSql("liked = liked - 1").eq("id", id).update();
            // 4.2.把用户从Redis的set集合移除
            if (isSuccess) {
                stringRedisTemplate.opsForZSet().remove(key, userId.toString());
            }
        }
        return Result.ok();
    }


    private void isBlogLiked(Blog blog) {
        // 1.获取登录用户
        UserDTO user = UserHolder.getUser();
        if (user == null) {
            // 用户未登录，无需查询是否点赞
            return;
        }
        Long userId = user.getId();
        // 2.判断当前登录用户是否已经点赞
        String key = "blog:liked:" + blog.getId();
        Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
        blog.setIsLike(score != null);
    }
```

点赞列表查询列表

BlogController

```java
@GetMapping("/likes/{id}")
public Result queryBlogLikes(@PathVariable("id") Long id) {

    return blogService.queryBlogLikes(id);
}
```

BlogService

```java
@Override
public Result queryBlogLikes(Long id) {
    String key = BLOG_LIKED_KEY + id;
    // 1.查询top5的点赞用户 zrange key 0 4
    Set<String> top5 = stringRedisTemplate.opsForZSet().range(key, 0, 4);
    if (top5 == null || top5.isEmpty()) {
        return Result.ok(Collections.emptyList());
    }
    // 2.解析出其中的用户id
    List<Long> ids = top5.stream().map(Long::valueOf).collect(Collectors.toList());
    String idStr = StrUtil.join(",", ids);
    // 3.根据用户id查询用户 WHERE id IN ( 5 , 1 ) ORDER BY FIELD(id, 5, 1)
    List<UserDTO> userDTOS = userService.query()
            .in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list()
            .stream()
            .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
            .collect(Collectors.toList());
    // 4.返回
    return Result.ok(userDTOS);
}
```

## 9、好友关注

### 9.1 好友关注-关注和取消关注

针对用户的操作：可以对用户进行关注和取消关注功能。

![1653806140822](.\Redis实战篇.assets\1653806140822.png)

实现思路：

需求：基于该表数据结构，实现两个接口：

* 关注和取关接口
* 判断是否关注的接口

关注是User之间的关系，是博主与粉丝的关系，数据库中有一张tb_follow表来标示：

![1653806253817](.\Redis实战篇.assets\1653806253817.png)

注意: 这里需要把主键修改为自增长，简化开发。

FollowController

```java
//关注
@PutMapping("/{id}/{isFollow}")
public Result follow(@PathVariable("id") Long followUserId, @PathVariable("isFollow") Boolean isFollow) {
    return followService.follow(followUserId, isFollow);
}
//取消关注
@GetMapping("/or/not/{id}")
public Result isFollow(@PathVariable("id") Long followUserId) {
      return followService.isFollow(followUserId);
}
```

FollowService

```java
取消关注service
@Override
public Result isFollow(Long followUserId) {
        // 1.获取登录用户
        Long userId = UserHolder.getUser().getId();
        // 2.查询是否关注 select count(*) from tb_follow where user_id = ? and follow_user_id = ?
        Integer count = query().eq("user_id", userId).eq("follow_user_id", followUserId).count();
        // 3.判断
        return Result.ok(count > 0);
    }

 关注service
 @Override
    public Result follow(Long followUserId, Boolean isFollow) {
        // 1.获取登录用户
        Long userId = UserHolder.getUser().getId();
        String key = "follows:" + userId;
        // 1.判断到底是关注还是取关
        if (isFollow) {
            // 2.关注，新增数据
            Follow follow = new Follow();
            follow.setUserId(userId);
            follow.setFollowUserId(followUserId);
            boolean isSuccess = save(follow);

        } else {
            // 3.取关，删除 delete from tb_follow where user_id = ? and follow_user_id = ?
            remove(new QueryWrapper<Follow>()
                    .eq("user_id", userId).eq("follow_user_id", followUserId));

        }
        return Result.ok();
    }
```

### 9.2 好友关注-共同关注

想要去看共同关注的好友，需要首先进入到这个页面，这个页面会发起两个请求

1、去查询用户的详情

2、去查询用户的笔记

以上两个功能和共同关注没有什么关系，大家可以自行将笔记中的代码拷贝到idea中就可以实现这两个功能了，我们的重点在于共同关注功能。

![1653806706296](.\Redis实战篇.assets\1653806706296.png)

```java
// UserController 根据id查询用户
@GetMapping("/{id}")
public Result queryUserById(@PathVariable("id") Long userId){
	// 查询详情
	User user = userService.getById(userId);
	if (user == null) {
		return Result.ok();
	}
	UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
	// 返回
	return Result.ok(userDTO);
}

// BlogController  根据id查询博主的探店笔记
@GetMapping("/of/user")
public Result queryBlogByUserId(
		@RequestParam(value = "current", defaultValue = "1") Integer current,
		@RequestParam("id") Long id) {
	// 根据用户查询
	Page<Blog> page = blogService.query()
			.eq("user_id", id).page(new Page<>(current, SystemConstants.MAX_PAGE_SIZE));
	// 获取当前页数据
	List<Blog> records = page.getRecords();
	return Result.ok(records);
}
```

接下来我们来看看共同关注如何实现：

需求：利用Redis中恰当的数据结构，实现共同关注功能。在博主个人页面展示出当前用户与博主的共同关注呢。

当然是使用我们之前学习过的set集合咯，在set集合中，有交集并集补集的api，我们可以把两人的关注的人分别放入到一个set集合中，然后再通过api去查看这两个set集合中的交集数据。

![1653806973212](.\Redis实战篇.assets\1653806973212.png)

我们先来改造当前的关注列表

改造原因是因为我们需要在用户关注了某位用户后，需要将数据放入到set集合中，方便后续进行共同关注，同时当取消关注时，也需要从set集合中进行删除

FollowServiceImpl

```java
@Override
public Result follow(Long followUserId, Boolean isFollow) {
    // 1.获取登录用户
    Long userId = UserHolder.getUser().getId();
    String key = "follows:" + userId;
    // 1.判断到底是关注还是取关
    if (isFollow) {
        // 2.关注，新增数据
        Follow follow = new Follow();
        follow.setUserId(userId);
        follow.setFollowUserId(followUserId);
        boolean isSuccess = save(follow);
        if (isSuccess) {
            // 把关注用户的id，放入redis的set集合 sadd userId followerUserId
            stringRedisTemplate.opsForSet().add(key, followUserId.toString());
        }
    } else {
        // 3.取关，删除 delete from tb_follow where user_id = ? and follow_user_id = ?
        boolean isSuccess = remove(new QueryWrapper<Follow>()
                .eq("user_id", userId).eq("follow_user_id", followUserId));
        if (isSuccess) {
            // 把关注用户的id从Redis集合中移除
            stringRedisTemplate.opsForSet().remove(key, followUserId.toString());
        }
    }
    return Result.ok();
}
```

**具体的关注代码：**

FollowServiceImpl

```java
@Override
public Result followCommons(Long id) {
    // 1.获取当前用户
    Long userId = UserHolder.getUser().getId();
    String key = "follows:" + userId;
    // 2.求交集
    String key2 = "follows:" + id;
    Set<String> intersect = stringRedisTemplate.opsForSet().intersect(key, key2);
    if (intersect == null || intersect.isEmpty()) {
        // 无交集
        return Result.ok(Collections.emptyList());
    }
    // 3.解析id集合
    List<Long> ids = intersect.stream().map(Long::valueOf).collect(Collectors.toList());
    // 4.查询用户
    List<UserDTO> users = userService.listByIds(ids)
            .stream()
            .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
            .collect(Collectors.toList());
    return Result.ok(users);
}
```

### 9.3 好友关注-Feed流实现方案

当我们关注了用户后，这个用户发了动态，那么我们应该把这些数据推送给用户，这个需求，其实我们又把他叫做Feed流，关注推送也叫做Feed流，直译为投喂。为用户持续的提供“沉浸式”的体验，通过无限下拉刷新获取新的信息。

对于传统的模式的内容解锁：我们是需要用户去通过搜索引擎或者是其他的方式去解锁想要看的内容

![1653808641260](.\Redis实战篇.assets\1653808641260.png)



对于新型的Feed流的的效果：不需要我们用户再去推送信息，而是系统分析用户到底想要什么，然后直接把内容推送给用户，从而使用户能够更加的节约时间，不用主动去寻找。

![1653808993693](.\Redis实战篇.assets\1653808993693.png)

Feed流的实现有两种模式：

Feed流产品有两种常见模式：
Timeline：不做内容筛选，简单的按照内容发布时间排序，常用于好友或关注。例如朋友圈

* 优点：信息全面，不会有缺失。并且实现也相对简单
* 缺点：信息噪音较多，用户不一定感兴趣，内容获取效率低

智能排序：利用智能算法屏蔽掉违规的、用户不感兴趣的内容。推送用户感兴趣信息来吸引用户

* 优点：投喂用户感兴趣信息，用户粘度很高，容易沉迷
* 缺点：如果算法不精准，可能起到反作用
  本例中的个人页面，是基于关注的好友来做Feed流，因此采用Timeline的模式。该模式的实现方案有三种：

我们本次针对好友的操作，采用的就是Timeline的方式，只需要拿到我们关注用户的信息，然后按照时间排序即可

，因此采用Timeline的模式。该模式的实现方案有三种：

* 拉模式
* 推模式
* 推拉结合

**拉模式**：也叫做读扩散

该模式的核心含义就是：当张三和李四和王五发了消息后，都会保存在自己的邮箱中，假设赵六要读取信息，那么他会从读取他自己的收件箱，此时系统会从他关注的人群中，把他关注人的信息全部都进行拉取，然后在进行排序

优点：比较节约空间，因为赵六在读信息时，并没有重复读取，而且读取完之后可以把他的收件箱进行清楚。

缺点：比较延迟，当用户读取数据时才去关注的人里边去读取数据，假设用户关注了大量的用户，那么此时就会拉取海量的内容，对服务器压力巨大。

![1653809450816](.\Redis实战篇.assets\1653809450816.png)



**推模式**：也叫做写扩散。

推模式是没有写邮箱的，当张三写了一个内容，此时会主动的把张三写的内容发送到他的粉丝收件箱中去，假设此时李四再来读取，就不用再去临时拉取了

优点：时效快，不用临时拉取

缺点：内存压力大，假设一个大V写信息，很多人关注他， 就会写很多分数据到粉丝那边去

![1653809875208](.\Redis实战篇.assets\1653809875208.png)

**推拉结合模式**：也叫做读写混合，兼具推和拉两种模式的优点。

推拉模式是一个折中的方案，站在发件人这一段，如果是个普通的人，那么我们采用写扩散的方式，直接把数据写入到他的粉丝中去，因为普通的人他的粉丝关注量比较小，所以这样做没有压力，如果是大V，那么他是直接将数据先写入到一份到发件箱里边去，然后再直接写一份到活跃粉丝收件箱里边去，现在站在收件人这端来看，如果是活跃粉丝，那么大V和普通的人发的都会直接写入到自己收件箱里边来，而如果是普通的粉丝，由于他们上线不是很频繁，所以等他们上线时，再从发件箱里边去拉信息。

![1653812346852](.\Redis实战篇.assets\1653812346852.png)

### 9.4 好友关注-推送到粉丝收件箱

需求：

* 修改新增探店笔记的业务，在保存blog到数据库的同时，推送到粉丝的收件箱
* 收件箱满足可以根据时间戳排序，必须用Redis的数据结构实现
* 查询收件箱数据时，可以实现分页查询

Feed流中的数据会不断更新，所以数据的角标也在变化，因此不能采用传统的分页模式。

传统了分页在feed流是不适用的，因为我们的数据会随时发生变化

假设在t1 时刻，我们去读取第一页，此时page = 1 ，size = 5 ，那么我们拿到的就是10~6 这几条记录，假设现在t2时候又发布了一条记录，此时t3 时刻，我们来读取第二页，读取第二页传入的参数是page=2 ，size=5 ，那么此时读取到的第二页实际上是从6 开始，然后是6~2 ，那么我们就读取到了重复的数据，所以feed流的分页，不能采用原始方案来做。

![1653813047671](.\Redis实战篇.assets\1653813047671.png)

Feed流的滚动分页

我们需要记录每次操作的最后一条，然后从这个位置开始去读取数据

举个例子：我们从t1时刻开始，拿第一页数据，拿到了10~6，然后记录下当前最后一次拿取的记录，就是6，t2时刻发布了新的记录，此时这个11放到最顶上，但是不会影响我们之前记录的6，此时t3时刻来拿第二页，第二页这个时候拿数据，还是从6后一点的5去拿，就拿到了5-1的记录。我们这个地方可以采用sortedSet来做，可以进行范围查询，并且还可以记录当前获取数据时间戳最小值，就可以实现滚动分页了

![1653813462834](.\Redis实战篇.assets\1653813462834.png)

核心的意思：就是我们在保存完探店笔记后，获得到当前笔记的粉丝，然后把数据推送到粉丝的redis中去。

```java
@Override
public Result saveBlog(Blog blog) {
    // 1.获取登录用户
    UserDTO user = UserHolder.getUser();
    blog.setUserId(user.getId());
    // 2.保存探店笔记
    boolean isSuccess = save(blog);
    if(!isSuccess){
        return Result.fail("新增笔记失败!");
    }
    // 3.查询笔记作者的所有粉丝 select * from tb_follow where follow_user_id = ?
    List<Follow> follows = followService.query().eq("follow_user_id", user.getId()).list();
    // 4.推送笔记id给所有粉丝
    for (Follow follow : follows) {
        // 4.1.获取粉丝id
        Long userId = follow.getUserId();
        // 4.2.推送
        String key = FEED_KEY + userId;
        stringRedisTemplate.opsForZSet().add(key, blog.getId().toString(), System.currentTimeMillis());
    }
    // 5.返回id
    return Result.ok(blog.getId());
}
```

### 9.5好友关注-实现分页查询收邮箱

需求：在个人主页的“关注”卡片中，查询并展示推送的Blog信息：

具体操作如下：

1、每次查询完成后，我们要分析出查询出数据的最小时间戳，这个值会作为下一次查询的条件

2、我们需要找到与上一次查询相同的查询个数作为偏移量，下次查询时，跳过这些查询过的数据，拿到我们需要的数据

综上：我们的请求参数中就需要携带 lastId：上一次查询的最小时间戳 和偏移量这两个参数。

这两个参数第一次会由前端来指定，以后的查询就根据后台结果作为条件，再次传递到后台。

![1653819821591](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653819821591.png)

一、定义出来具体的返回值实体类

```java
@Data
public class ScrollResult {
    private List<?> list;
    private Long minTime;
    private Integer offset;
}
```

BlogController

注意：RequestParam 表示接受url地址栏传参的注解，当方法上参数的名称和url地址栏不相同时，可以通过RequestParam 来进行指定

```java
@GetMapping("/of/follow")
public Result queryBlogOfFollow(
    @RequestParam("lastId") Long max, @RequestParam(value = "offset", defaultValue = "0") Integer offset){
    return blogService.queryBlogOfFollow(max, offset);
}
```

BlogServiceImpl

```java
@Override
public Result queryBlogOfFollow(Long max, Integer offset) {
    // 1.获取当前用户
    Long userId = UserHolder.getUser().getId();
    // 2.查询收件箱 ZREVRANGEBYSCORE key Max Min LIMIT offset count
    String key = FEED_KEY + userId;
    Set<ZSetOperations.TypedTuple<String>> typedTuples = stringRedisTemplate.opsForZSet()
        .reverseRangeByScoreWithScores(key, 0, max, offset, 2);
    // 3.非空判断
    if (typedTuples == null || typedTuples.isEmpty()) {
        return Result.ok();
    }
    // 4.解析数据：blogId、minTime（时间戳）、offset
    List<Long> ids = new ArrayList<>(typedTuples.size());
    long minTime = 0; // 2
    int os = 1; // 2
    for (ZSetOperations.TypedTuple<String> tuple : typedTuples) { // 5 4 4 2 2
        // 4.1.获取id
        ids.add(Long.valueOf(tuple.getValue()));
        // 4.2.获取分数(时间戳）
        long time = tuple.getScore().longValue();
        if(time == minTime){
            os++;
        }else{
            minTime = time;
            os = 1;
        }
    }
	os = minTime == max ? os : os + offset;
    // 5.根据id查询blog
    String idStr = StrUtil.join(",", ids);
    List<Blog> blogs = query().in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list();

    for (Blog blog : blogs) {
        // 5.1.查询blog有关的用户
        queryBlogUser(blog);
        // 5.2.查询blog是否被点赞
        isBlogLiked(blog);
    }

    // 6.封装并返回
    ScrollResult r = new ScrollResult();
    r.setList(blogs);
    r.setOffset(os);
    r.setMinTime(minTime);

    return Result.ok(r);
}
```

## 10、附近商户

### 10.1、附近商户-GEO数据结构的基本用法

GEO就是Geolocation的简写形式，代表地理坐标。Redis在3.2版本中加入了对GEO的支持，允许存储地理坐标信息，帮助我们根据经纬度来检索数据。常见的命令有：

* GEOADD：添加一个地理空间信息，包含：经度（longitude）、纬度（latitude）、值（member）
* GEODIST：计算指定的两个点之间的距离并返回
* GEOHASH：将指定member的坐标转为hash字符串形式并返回
* GEOPOS：返回指定member的坐标
* GEORADIUS：指定圆心、半径，找到该圆内包含的所有member，并按照与圆心之间的距离排序后返回。6.以后已废弃
* GEOSEARCH：在指定范围内搜索member，并按照与指定点之间的距离排序后返回。范围可以是圆形或矩形。6.2.新功能
* GEOSEARCHSTORE：与GEOSEARCH功能一致，不过可以把结果存储到一个指定的key。 6.2.新功能

### 10.2、 附近商户-导入店铺数据到GEO

具体场景说明：

![1653822036941](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653822036941.png)



当我们点击美食之后，会出现一系列的商家，商家中可以按照多种排序方式，我们此时关注的是距离，这个地方就需要使用到我们的GEO，向后台传入当前app收集的地址(我们此处是写死的) ，以当前坐标作为圆心，同时绑定相同的店家类型type，以及分页信息，把这几个条件传入后台，后台查询出对应的数据再返回。

![1653822021827](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653822021827.png)

我们要做的事情是：将数据库表中的数据导入到redis中去，redis中的GEO，GEO在redis中就一个menber和一个经纬度，我们把x和y轴传入到redis做的经纬度位置去，但我们不能把所有的数据都放入到menber中去，毕竟作为redis是一个内存级数据库，如果存海量数据，redis还是力不从心，所以我们在这个地方存储他的id即可。

但是这个时候还有一个问题，就是在redis中并没有存储type，所以我们无法根据type来对数据进行筛选，所以我们可以按照商户类型做分组，类型相同的商户作为同一组，以typeId为key存入同一个GEO集合中即可

代码

HmDianPingApplicationTests

```java
@Test
void loadShopData() {
    // 1.查询店铺信息
    List<Shop> list = shopService.list();
    // 2.把店铺分组，按照typeId分组，typeId一致的放到一个集合
    Map<Long, List<Shop>> map = list.stream().collect(Collectors.groupingBy(Shop::getTypeId));
    // 3.分批完成写入Redis
    for (Map.Entry<Long, List<Shop>> entry : map.entrySet()) {
        // 3.1.获取类型id
        Long typeId = entry.getKey();
        String key = SHOP_GEO_KEY + typeId;
        // 3.2.获取同类型的店铺的集合
        List<Shop> value = entry.getValue();
        List<RedisGeoCommands.GeoLocation<String>> locations = new ArrayList<>(value.size());
        // 3.3.写入redis GEOADD key 经度 纬度 member
        for (Shop shop : value) {
            // stringRedisTemplate.opsForGeo().add(key, new Point(shop.getX(), shop.getY()), shop.getId().toString());
            locations.add(new RedisGeoCommands.GeoLocation<>(
                    shop.getId().toString(),
                    new Point(shop.getX(), shop.getY())
            ));
        }
        stringRedisTemplate.opsForGeo().add(key, locations);
    }
}
```

### 10.3 附近商户-实现附近商户功能

SpringDataRedis的2.3.9版本并不支持Redis 6.2提供的GEOSEARCH命令，因此我们需要提示其版本，修改自己的POM

第一步：导入pom

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <exclusions>
        <exclusion>
            <artifactId>spring-data-redis</artifactId>
            <groupId>org.springframework.data</groupId>
        </exclusion>
        <exclusion>
            <artifactId>lettuce-core</artifactId>
            <groupId>io.lettuce</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>2.6.2</version>
</dependency>
<dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <version>6.1.6.RELEASE</version>
</dependency>
```

第二步：

ShopController

```java
@GetMapping("/of/type")
public Result queryShopByType(
        @RequestParam("typeId") Integer typeId,
        @RequestParam(value = "current", defaultValue = "1") Integer current,
        @RequestParam(value = "x", required = false) Double x,
        @RequestParam(value = "y", required = false) Double y
) {
   return shopService.queryShopByType(typeId, current, x, y);
}
```

ShopServiceImpl

```java
@Override
    public Result queryShopByType(Integer typeId, Integer current, Double x, Double y) {
        // 1.判断是否需要根据坐标查询
        if (x == null || y == null) {
            // 不需要坐标查询，按数据库查询
            Page<Shop> page = query()
                    .eq("type_id", typeId)
                    .page(new Page<>(current, SystemConstants.DEFAULT_PAGE_SIZE));
            // 返回数据
            return Result.ok(page.getRecords());
        }

        // 2.计算分页参数
        int from = (current - 1) * SystemConstants.DEFAULT_PAGE_SIZE;
        int end = current * SystemConstants.DEFAULT_PAGE_SIZE;

        // 3.查询redis、按照距离排序、分页。结果：shopId、distance
        String key = SHOP_GEO_KEY + typeId;
        GeoResults<RedisGeoCommands.GeoLocation<String>> results = stringRedisTemplate.opsForGeo() // GEOSEARCH key BYLONLAT x y BYRADIUS 10 WITHDISTANCE
                .search(
                        key,
                        GeoReference.fromCoordinate(x, y),
                        new Distance(5000),
                        RedisGeoCommands.GeoSearchCommandArgs.newGeoSearchArgs().includeDistance().limit(end)
                );
        // 4.解析出id
        if (results == null) {
            return Result.ok(Collections.emptyList());
        }
        List<GeoResult<RedisGeoCommands.GeoLocation<String>>> list = results.getContent();
        if (list.size() <= from) {
            // 没有下一页了，结束
            return Result.ok(Collections.emptyList());
        }
        // 4.1.截取 from ~ end的部分
        List<Long> ids = new ArrayList<>(list.size());
        Map<String, Distance> distanceMap = new HashMap<>(list.size());
        list.stream().skip(from).forEach(result -> {
            // 4.2.获取店铺id
            String shopIdStr = result.getContent().getName();
            ids.add(Long.valueOf(shopIdStr));
            // 4.3.获取距离
            Distance distance = result.getDistance();
            distanceMap.put(shopIdStr, distance);
        });
        // 5.根据id查询Shop
        String idStr = StrUtil.join(",", ids);
        List<Shop> shops = query().in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list();
        for (Shop shop : shops) {
            shop.setDistance(distanceMap.get(shop.getId().toString()).getValue());
        }
        // 6.返回
        return Result.ok(shops);
    }
```



## 11、用户签到

#### 11.1、用户签到-BitMap功能演示

我们针对签到功能完全可以通过mysql来完成，比如说以下这张表

![1653823145495](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653823145495.png)

用户一次签到，就是一条记录，假如有1000万用户，平均每人每年签到次数为10次，则这张表一年的数据量为 1亿条

每签到一次需要使用（8 + 8 + 1 + 1 + 3 + 1）共22 字节的内存，一个月则最多需要600多字节

我们如何能够简化一点呢？其实可以考虑小时候一个挺常见的方案，就是小时候，咱们准备一张小小的卡片，你只要签到就打上一个勾，我最后判断你是否签到，其实只需要到小卡片上看一看就知道了

我们可以采用类似这样的方案来实现我们的签到需求。

我们按月来统计用户签到信息，签到记录为1，未签到则记录为0.

把每一个bit位对应当月的每一天，形成了映射关系。用0和1标示业务状态，这种思路就称为位图（BitMap）。这样我们就用极小的空间，来实现了大量数据的表示

Redis中是利用string类型数据结构实现BitMap，因此最大上限是512M，转换为bit则是 2^32个bit位。

![1653824498278](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653824498278.png)

BitMap的操作命令有：

* SETBIT：向指定位置（offset）存入一个0或1
* GETBIT ：获取指定位置（offset）的bit值
* BITCOUNT ：统计BitMap中值为1的bit位的数量
* BITFIELD ：操作（查询、修改、自增）BitMap中bit数组中的指定位置（offset）的值
* BITFIELD_RO ：获取BitMap中bit数组，并以十进制形式返回
* BITOP ：将多个BitMap的结果做位运算（与 、或、异或）
* BITPOS ：查找bit数组中指定范围内第一个0或1出现的位置

#### 11.2 、用户签到-实现签到功能

需求：实现签到接口，将当前用户当天签到信息保存到Redis中

思路：我们可以把年和月作为bitMap的key，然后保存到一个bitMap中，每次签到就到对应的位上把数字从0变成1，只要对应是1，就表明说明这一天已经签到了，反之则没有签到。

我们通过接口文档发现，此接口并没有传递任何的参数，没有参数怎么确实是哪一天签到呢？这个很容易，可以通过后台代码直接获取即可，然后到对应的地址上去修改bitMap。

![1653833970361](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653833970361.png)

**代码**

UserController

```java
 @PostMapping("/sign")
 public Result sign(){
    return userService.sign();
 }
```

UserServiceImpl

```java
@Override
public Result sign() {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.获取日期
    LocalDateTime now = LocalDateTime.now();
    // 3.拼接key
    String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = USER_SIGN_KEY + userId + keySuffix;
    // 4.获取今天是本月的第几天
    int dayOfMonth = now.getDayOfMonth();
    // 5.写入Redis SETBIT key offset 1
    stringRedisTemplate.opsForValue().setBit(key, dayOfMonth - 1, true);
    return Result.ok();
}
```

#### 11.3 用户签到-签到统计

**问题1：**什么叫做连续签到天数？
从最后一次签到开始向前统计，直到遇到第一次未签到为止，计算总的签到次数，就是连续签到天数。

![1653834455899](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653834455899.png)

Java逻辑代码：获得当前这个月的最后一次签到数据，定义一个计数器，然后不停的向前统计，直到获得第一个非0的数字即可，每得到一个非0的数字计数器+1，直到遍历完所有的数据，就可以获得当前月的签到总天数了

**问题2：**如何得到本月到今天为止的所有签到数据？

  BITFIELD key GET u[dayOfMonth] 0

假设今天是10号，那么我们就可以从当前月的第一天开始，获得到当前这一天的位数，是10号，那么就是10位，去拿这段时间的数据，就能拿到所有的数据了，那么这10天里边签到了多少次呢？统计有多少个1即可。

**问题3：如何从后向前遍历每个bit位？**

注意：bitMap返回的数据是10进制，哪假如说返回一个数字8，那么我哪儿知道到底哪些是0，哪些是1呢？我们只需要让得到的10进制数字和1做与运算就可以了，因为1只有遇见1 才是1，其他数字都是0 ，我们把签到结果和1进行与操作，每与一次，就把签到结果向右移动一位，依次内推，我们就能完成逐个遍历的效果了。

需求：实现下面接口，统计当前用户截止当前时间在本月的连续签到天数

有用户有时间我们就可以组织出对应的key，此时就能找到这个用户截止这天的所有签到记录，再根据这套算法，就能统计出来他连续签到的次数了



![1653835784444](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653835784444.png)

代码

**UserController**

```java
@GetMapping("/sign/count")
public Result signCount(){
    return userService.signCount();
}
```

**UserServiceImpl**

```java
@Override
public Result signCount() {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.获取日期
    LocalDateTime now = LocalDateTime.now();
    // 3.拼接key
    String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = USER_SIGN_KEY + userId + keySuffix;
    // 4.获取今天是本月的第几天
    int dayOfMonth = now.getDayOfMonth();
    // 5.获取本月截止今天为止的所有的签到记录，返回的是一个十进制的数字 BITFIELD sign:5:202203 GET u14 0
    List<Long> result = stringRedisTemplate.opsForValue().bitField(
            key,
            BitFieldSubCommands.create()
                    .get(BitFieldSubCommands.BitFieldType.unsigned(dayOfMonth)).valueAt(0)
    );
    if (result == null || result.isEmpty()) {
        // 没有任何签到结果
        return Result.ok(0);
    }
    Long num = result.get(0);
    if (num == null || num == 0) {
        return Result.ok(0);
    }
    // 6.循环遍历
    int count = 0;
    while (true) {
        // 6.1.让这个数字与1做与运算，得到数字的最后一个bit位  // 判断这个bit位是否为0
        if ((num & 1) == 0) {
            // 如果为0，说明未签到，结束
            break;
        }else {
            // 如果不为0，说明已签到，计数器+1
            count++;
        }
        // 把数字右移一位，抛弃最后一个bit位，继续下一个bit位
        num >>>= 1;
    }
    return Result.ok(count);
}
```

#### 11.4 额外加餐-关于使用bitmap来解决缓存穿透的方案

回顾**缓存穿透**：

发起了一个数据库不存在的，redis里边也不存在的数据，通常你可以把他看成一个攻击

解决方案：

* 判断id<0

* 如果数据库是空，那么就可以直接往redis里边把这个空数据缓存起来

第一种解决方案：遇到的问题是如果用户访问的是id不存在的数据，则此时就无法生效

第二种解决方案：遇到的问题是：如果是不同的id那就可以防止下次过来直击数据

所以我们如何解决呢？

我们可以将数据库的数据，所对应的id写入到一个list集合中，当用户过来访问的时候，我们直接去判断list中是否包含当前的要查询的数据，如果说用户要查询的id数据并不在list集合中，则直接返回，如果list中包含对应查询的id数据，则说明不是一次缓存穿透数据，则直接放行。

![1653836416586](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653836416586.png)

现在的问题是这个主键其实并没有那么短，而是很长的一个 主键

哪怕你单独去提取这个主键，但是在11年左右，淘宝的商品总量就已经超过10亿个

所以如果采用以上方案，这个list也会很大，所以我们可以使用bitmap来减少list的存储空间

我们可以把list数据抽象成一个非常大的bitmap，我们不再使用list，而是将db中的id数据利用哈希思想，比如：

id % bitmap.size  = 算出当前这个id对应应该落在bitmap的哪个索引上，然后将这个值从0变成1，然后当用户来查询数据时，此时已经没有了list，让用户用他查询的id去用相同的哈希算法， 算出来当前这个id应当落在bitmap的哪一位，然后判断这一位是0，还是1，如果是0则表明这一位上的数据一定不存在，  采用这种方式来处理，需要重点考虑一个事情，就是误差率，所谓的误差率就是指当发生哈希冲突的时候，产生的误差。



![1653836578970](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653836578970.png)



## 12、UV统计

### 12.1 、UV统计-HyperLogLog

首先我们搞懂两个概念：

* UV：全称Unique Visitor，也叫独立访客量，是指通过互联网访问、浏览这个网页的自然人。1天内同一个用户多次访问该网站，只记录1次。
* PV：全称Page View，也叫页面访问量或点击量，用户每访问网站的一个页面，记录1次PV，用户多次打开页面，则记录多次PV。往往用来衡量网站的流量。

通常来说UV会比PV大很多，所以衡量同一个网站的访问量，我们需要综合考虑很多因素，所以我们只是单纯的把这两个值作为一个参考值

UV统计在服务端做会比较麻烦，因为要判断该用户是否已经统计过了，需要将统计过的用户信息保存。但是如果每个访问的用户都保存到Redis中，数据量会非常恐怖，那怎么处理呢？

Hyperloglog(HLL)是从Loglog算法派生的概率算法，用于确定非常大的集合的基数，而不需要存储其所有值。相关算法原理大家可以参考：https://juejin.cn/post/6844903785744056333#heading-0
Redis中的HLL是基于string结构实现的，单个HLL的内存**永远小于16kb**，**内存占用低**的令人发指！作为代价，其测量结果是概率性的，**有小于0.81％的误差**。不过对于UV统计来说，这完全可以忽略。

![1653837988985](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653837988985.png)

### 12.2 UV统计-测试百万数据的统计

测试思路：我们直接利用单元测试，向HyperLogLog中添加100万条数据，看看内存占用和统计效果如何

![1653838053608](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1653838053608.png)

经过测试：我们会发生他的误差是在允许范围内，并且内存占用极小 