## 1、关于Cookie

### 1.1、Cookie的基本概念

cookie是某些网站为了辨别用户身份，进行Session跟踪而储存在用户本地终端上的数据（通常经过加密），由用户[客户端](https://baike.baidu.com/item/客户端/101081)计算机暂时或永久保存的信息。



### 1.2、Cookie的类型

Cookie总时由用户客户端进行保存的（一般是浏览器），按其存储位置可分为：内存式Cookie和硬盘式Cookie。

内存式Cookie存储在内存中，浏览器关闭后就会消失，由于其存储时间较短，因此也被称为非持久Cookie或会话Cookie。

硬盘式Cookie保存在硬盘中，其不会随浏览器的关闭而消失，除非用户手工清理或到了过期时间。由于硬盘式Cookie存储时间是长期的，因此也被称为持久Cookie。



### 1.3、Cookie的工作原理

Cookie定义了一些HTTP请求头和HTTP响应头，通过这些HTTP头信息使服务器可以与客户端进行状态交互。

客户端请求服务器后，如果服务器需要记录用户状态，服务器会在响应信息中包含一个Set-Cookie的响应头，客户端会根据这个响应头存储Cookie信息。再次请求服务器时，客户端会在请求信息中包含一个Cookie请求头，而服务器会根据这个请求头进行用户身份、状态等较验。

![image-20230614191137729](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230614191137729.png)



下面是一个实现Cookie机制的，简单的HTTP请求过程：

- 客户端请求服务器

客户端请求IT笔录网站首页，请求头如下：

```
GET / HTTP/1.0

HOST: itbilu.com
```

- 服务器响应请求

Cookie是一种key=value形式的字符串，服务器需要记录这个客户端请求的状态，因此在响应头中包一个Set-Cookie字段。响应头如下：

```
HTTP/1.0 200 OK

Set-Cookie: UserID=itbilu; Max-Age=3600; Version=1

Content-type: text/html
```

- 再次请求时，客户端请求中会包含一个Cookie请求头

客户端会对服务器响应的Set-Cookie头信息进行存储。再次请求时，将会在请求头中包含服务器响应的Cookie信息。请求头如下：

```
GET / HTTP/1.0

HOST: itbilu.com

Cookie: UserID=itbilu
```



### 1.4、Cookie的属性设置

除了name与value之外，Cookie还具有其他几个常用的属性。每个属性对应一个getter方法与一个setter方法。Cookie类的所有属性如表下所示。

![image-20230614192110118](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230614192110118.png)

![image-20230614192129823](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230614192129823.png)

Java中把Cookie封装成了javax.servlet.http.Cookie类。每个Cookie都是该Cookie类的对象。服务器通过操作Cookie类对象对客户端Cookie进行操作。

通过request.getCookie()获取客户端提交的所有Cookie（以Cookie[]数组形式返回），通过response.addCookie(Cookiecookie)向客户端设置Cookie。

Cookie对象使用key-value属性对的形式保存用户状态，一个Cookie对象保存一个属性对，一个request或者response同时使用多个Cookie。因为Cookie类位于包javax.servlet.http.*下面，所以JSP中不需要import该类。



#### 1.4.1、Unicode编码：保存中文

中文与英文字符不同，中文属于Unicode字符，在内存中占4个字符，而英文属于ASCII字符，内存中只占2个字节。Cookie中使用Unicode字符时需要对Unicode字符进行编码，否则会乱码。

> 注：
>
> 提示：Cookie中保存中文只能编码。一般使用UTF-8编码即可。不推荐使用GBK等中文编码，因为浏览器不一定支持，而且JavaScript也不支持GBK编码。



#### 1.4.2、设置Cookie的有效期

Cookie的**maxAge**决定着Cookie的有效期，单位为秒（Second）。Cookie中通过**getMaxAge()**方法与**setMaxAge(int maxAge)**方法来读写maxAge属性。

**如果maxAge属性为正数：**表示该Cookie会在maxAge秒之后自动失效。浏览器会将maxAge为正数的Cookie持久化，即写到对应的Cookie文件中。无论客户关闭了浏览器还是电脑，只要还在maxAge秒之前，登录网站时该Cookie仍然有效。下面代码中的Cookie信息将永远有效。

```java
Cookie cookie = new Cookie("username","helloweenvsfei"); // 新建Cookie

cookie.setMaxAge(Integer.MAX_VALUE); // 设置生命周期为MAX_VALUE

response.addCookie(cookie); // 输出到客户端
```

**如果maxAge为负数：**则表示该Cookie仅在本浏览器窗口以及本窗口打开的子窗口内有效，关闭窗口后该Cookie即失效。maxAge为负数的Cookie，为临时性Cookie，不会被持久化，不会被写到Cookie文件中。Cookie信息保存在浏览器内存中，因此关闭浏览器该Cookie就消失了。Cookie默认的maxAge值为–1。

**如果maxAge为0：**则表示删除该Cookie。Cookie机制没有提供删除Cookie的方法，因此通过设置该Cookie即时失效实现删除Cookie的效果。失效的Cookie会被浏览器从Cookie文件或者内存中删除，例如：

```java
Cookie cookie = new Cookie("username","helloweenvsfei"); // 新建Cookie

cookie.setMaxAge(0); // 设置生命周期为0，不能为负数

response.addCookie(cookie); // 必须执行这一句
```

response对象提供的Cookie操作方法只有一个添加操作add(Cookie cookie)。

要想修改Cookie只能使用一个同名的Cookie来覆盖原来的Cookie，达到修改的目的。删除时只需要把maxAge修改为0即可。

> 注：
>
> 从客户端读取Cookie时，包括maxAge在内的其他属性都是不可读的，也不会被提交。浏览器提交Cookie时只会提交name与value属性。maxAge属性只被浏览器用来判断Cookie是否过期。



#### 1.4.3、修改和删除Cookie

Cookie并不提供修改、删除操作。如果要修改某个Cookie，只需要新建一个同名的Cookie，添加到response中覆盖原来的Cookie。

如果要删除某个Cookie，只需要新建一个同名的Cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie。注意是0而不是负数。负数代表其他的意义。

> 注：
>
> 修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，例如name、path、domain等，都要与原Cookie完全一样。否则，浏览器将视为两个不同的Cookie不予覆盖，导致修改、删除失败。



#### 1.4.4、设置Cookie的域名

Cookie是不可跨域名的。域名www.google.com颁发的Cookie不会被提交到域名www.baidu.com去。这是由Cookie的隐私安全机制决定的。隐私安全机制能够禁止网站非法获取其他网站的Cookie。

正常情况下，同一个一级域名下的两个二级域名如www.helloweenvsfei.com和images.helloweenvsfei.com也不能交互使用Cookie，因为二者的域名并不严格相同。如果想所有helloweenvsfei.com名下的二级域名都可以使用该Cookie，需要设置Cookie的domain参数，例如：

```java
Cookie cookie = new Cookie("time","20080808"); // 新建Cookie

cookie.setDomain(".helloweenvsfei.com"); // 设置域名

cookie.setPath("/"); // 设置路径

cookie.setMaxAge(Integer.MAX_VALUE); // 设置有效期

response.addCookie(cookie); // 输出到客户端
```

读者可以修改本机C:\WINDOWS\system32\drivers\etc下的hosts文件来配置多个临时域名，然后使用setCookie.jsp程序来设置跨域名Cookie验证domain属性。

> 注：
>
> domain参数必须以点(".")开始。另外，name相同但domain不同的两个Cookie是两个不同的Cookie。如果想要两个域名完全不同的网站共有Cookie，可以生成两个Cookie，domain属性分别为两个域名，输出到客户端。



#### 1.4.5、设置Cookie的路径

domain属性决定运行访问Cookie的域名，而path属性决定允许访问Cookie的路径（ContextPath）。例如，如果只允许/session/下的程序使用Cookie，可以这么写：

```java
Cookie cookie = new Cookie("time","20080808"); // 新建Cookie

cookie.setPath("/session/"); // 设置路径

response.addCookie(cookie); // 输出到客户端
```

设置为“/”时允许所有路径使用Cookie。path属性需要使用符号“/”结尾。name相同但path不同的两个Cookie也是两个不同的Cookie。

> 注：
>
> 页面只能获取它属于的Path的Cookie。例如/session/test/a.jsp不能获取到路径为/session/abc/的Cookie。使用时一定要注意。



#### 1.4.6、设置Cookie的安全属性

HTTP协议不仅是无状态的，而且是不安全的。使用HTTP协议的数据不经过任何加密就直接在网络上传播，有被截获的可能。使用HTTP协议传输很机密的内容是一种隐患。如果不希望Cookie在HTTP等非安全协议中传输，可以设置Cookie的secure属性为true。浏览器只会在HTTPS和SSL等安全协议中传输此类Cookie。下面的代码设置secure属性为true：

```java
Cookie cookie = new Cookie("time", "20080808"); // 新建Cookie

cookie.setSecure(true); // 设置安全属性

response.addCookie(cookie); // 输出到客户端
```

> 注：
>
> secure属性并不能对Cookie内容加密，因而不能保证绝对的安全性。如果需要高安全性，需要在程序中对Cookie内容加密、解密，以防泄密。





## 2、关于Session

Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。

> 注：
>
> 如果说Cookie机制是通过检查客户身上的“通行证”来确定客户身份的话，那么Session机制就是通过检查服务器上的“客户明细表”来确认客户身份。Session相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。
>
> Session是服务器端使用的一种记录客户端状态的机制，使用上比Cookie简单一些，相应的也增加了服务器的存储压力。



### 2.1、Session的工作原理

当程序需要为某个客户端的请求创建一个session的时候，服务器首先检查这个客户端的请求里是否已包含了一个session标识，称为session id，如果已包含一个session id则说明以前已经为此客户端创建过session，服务器就按照session id把这个session检索出来使用（如果检索不到，可能会新建一个），如果客户端请求不包含session id，则为此客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，这个session id将被在本次响应中返回给客户端保存。

如图，Session的工作原理图示：

![image-20230614221806438](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230614221806438.png)



#### 2.1.1、保存Session ID的方式

##### 1、使用Cookie进行保存

保存session id的方式可以采用cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。一般这个cookie的名字都是类似于SEEESIONID。然而，比如weblogic对于web应用程序生成的cookie，JSESSIONID=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764，它的名字就是JSESSIONID。



##### 2、URL重写

由于cookie可以被人为的禁止，必须有其他机制以便在cookie被禁止时仍然能够把session id传递回服务器。经常被使用的一种技术叫做URL重写，就是把session id直接附加在URL路径的后面。

附加方式也有两种，一种是作为URL路径的附加信息（矩阵变量，详细请看SpringBoot笔记），表现形式为：

`http://...../xxx;jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764`

另一种是作为查询字符串附加在URL后面，表现形式为：

`http://...../xxx?jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764 `

这两种方式对于用户来说是没有区别的，只是服务器在解析的时候处理的方式不同，采用第一种方式也有利于把session id的信息和正常程序参数区分开来。为了在整个交互过程中始终保持状态，就必须在每个客户端可能请求的路径后面都包含这个session id。

> 注：
>
> TOMCAT判断客户端浏览器是否支持Cookie的依据是请求中是否含有Cookie。尽管客户端可能会支持Cookie，但是由于第一次请求时不会携带任何Cookie（因为并无任何Cookie可以携带），URL地址重写后的地址中仍然会带有jsessionid。当第二次访问时服务器已经在浏览器中写入Cookie了，因此URL地址重写后的地址中就不会带有jsessionid了。



##### 3、表单隐藏字段

表单隐藏就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把session id传递回服务器。比如下面的表单：

```html
<form name="testform" action="/xxx">
<input type="text">
</form>
```

在被传递给客户端之前将被改写成： 

```html
<form name="testform" action="/xxx">
<input type="hidden" name="jsessionid" value="ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764">
<input type="text">
</form>
```

这种技术现在已较少应用，实际上这种技术可以简单的用对action应用URL重写来代替。



### 2.2、Session的常用方法

Session中包括各种方法，使用起来要比Cookie方便得多。HttpSession的常用方法如表所示。

![image-20230614223513736](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230614223513736.png)

![image-20230614223523326](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230614223523326.png)

Session对应的类为javax.servlet.http.HttpSession类。每个来访者对应一个Session对象，所有该客户的状态信息都保存在这个Session对象里。

Session对象是在客户端第一次请求服务器的时候创建的。Session也是一种key-value的属性对，通过getAttribute(String key)和setAttribute(String key，Objectvalue)方法读写客户状态信息。Servlet里通过request.getSession()方法获取该客户的Session，例如：

```java
HttpSession session = request.getSession(); // 获取Session对象

session.setAttribute("loginTime", new Date()); // 设置Session中的属性

out.println("登录时间为：" +(Date)session.getAttribute("loginTime")); // 获取Session属性
```

request还可以使用getSession(boolean create)来获取Session。区别是如果该客户的Session不存在，request.getSession()方法会返回null，而getSession(true)会先创建Session再将Session返回。

当多个客户端执行程序时，服务器会保存多个客户端的Session。获取Session的时候也不需要声明获取谁的Session。Session机制决定了当前客户只会获取到自己的Session，而不会获取到别人的Session。各客户的Session也彼此独立，互不可见。



### 2.3、Session的生命周期和有效期设置

Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session。如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session里的信息应该尽量精简。

Session在用户第一次访问服务器的时候自动创建。需要注意只有访问JSP、Servlet等程序时才会创建Session，只访问HTML、IMAGE等静态资源并不会创建Session。如果尚未生成Session，也可以使用request.getSession(true)强制生成Session。

Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃（active）”了一次。

由于会有越来越多的用户访问服务器，因此Session也会越来越多。为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。

Session的超时时间为maxInactiveInterval属性，可以通过对应的getMaxInactiveInterval()获取，通过setMaxInactiveInterval(long interval)修改。另外，通过调用Session的invalidate()方法可以使Session失效。Tomcat中Session的默认超时时间为20分钟。通过setMaxInactiveInterval(int seconds)修改超时时间。Session的超时时间也可以在web.xml中修改。例如修改为60分钟：

```xml
<session-config>
<session-timeout>60</session-timeout> <!-- 单位：分钟 -->
</session-config>
```

> 注：
>
> <session-timeout>参数的单位为分钟，而setMaxInactiveInterval(int s)单位为秒。



## 3、Session和Cookie的区别和联系

**两者的区别：**在安全和性能方面考虑

- cookie数据存放在客户端，session数据放在服务器上。
- cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应当使用session。
- session会在一定时间内保存在服务器上，当访问增多，会比较占用你服务器的性能，考虑性能应当使用cookie。
- 不同浏览器对cookie的数据大小限制不同，个数限制也不相同。单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。
- 可以考虑将登陆信息等重要信息存放为session，不重要的信息可以放在cookie中。

**两者的联系：**

- 都是用来记录用户的信息，以便让服务器分辨不同的用户。
- 可以搭配使用，但都有自己的使用局限，要考虑到安全和性能的问题。







## 4、Token

- Token 的意思是“令牌”，是服务端生成的一串字符串，作为客户端进行请求的一个标识。

- 当用户第一次登录后，服务器生成一个Token，并将此Token返回给客户端。以后客户端只需要带上这个Token前来请求数据即可，无需再带上用户名和密码。


























