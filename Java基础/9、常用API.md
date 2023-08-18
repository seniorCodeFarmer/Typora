## 1、字符串相关类：String

### 1.1、String的特性

- String类：代表字符串。java程序中的所有字符串字面值（如“abc”）都作为此类的实例实现。
- String类是一个final类，不可被继承，代表不可变的字符序列。
- String内部定义了final char[] value（Java8之前及Java8版本）用于存储字符串数据，String对象的字符内容是存储在一个字符数组value[]中的。
- String类实现了Serializable接口，表示字符串是支持序列化的。
- 实现了Comparable接口，表示String可以比较大小。

![image-20230313140702098](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313140702098.png)

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```



### 1.2、理解String的不可变性

- 当对字符串重新赋值时，需要重新指定内存区域赋值，不能对原有的value进行赋值。
- 当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能对原有的value进行赋值。

- 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能对原有的value进行修改。
- String字符串的存储：通过字面量的方式（区别于new）给一个字符串赋值，此时的字符串值声明在字符串常量池中（方法区的字符串常量池）。

- 字符串常量池中是不会存储相同内容的字符串的。

```java
public void test1() {
    String s1 = "abc";
    String s2 = "abc";
    System.out.println(s1 == s2);//true
    
    s1 = "hello";

    System.out.println(s1 == s2);//false

    System.out.println(s1);//hello
    System.out.println(s2);//abc

    String s3 = "abc";
    s3 += "def";
    System.out.println(s3);//abcdef
    System.out.println(s2);//abc

    String s4 = "abc";
    String s5 = s4.replace("a", "m");
    System.out.println(s4);//abc
    System.out.println(s5);//mbc
}
```



### 1.3、String的实例化方式

- 方式一：通过字面量定义的方式：此时声明在方法区中的字符串常量池中

- 方式二：通过new+构造器的方式：此时声明在堆中（凡是new的结构都是在堆中）

![image-20230315092202984](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230315092202984.png)

```java
    public void test2(){
        //通过字面量定义的方式：此时的s1和s2的数据javaEE声明在方法区中的字符串常量池中。
        String s1 = "javaEE";
        String s2 = "javaEE";
        //通过new + 构造器的方式:此时的s3和s4保存的地址值，是数据在堆空间中开辟空间以后对应的地址值。
        String s3 = new String("javaEE");
        String s4 = new String("javaEE");

        System.out.println(s1 == s2);//true
        System.out.println(s1 == s3);//false
        System.out.println(s1 == s4);//false
        System.out.println(s3 == s4);//false

        System.out.println("***********************");
        Person p1 = new Person("Tom",12);
        Person p2 = new Person("Tom",12);

        System.out.println(p1.name.equals(p2.name));//true
        System.out.println(p1.name == p2.name);//true

        p1.name = "Jerry";
        System.out.println(p2.name);//Tom
    }
```

![image-20230315092225013](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230315092225013.png)



### 1.4、String不同拼接操作的对比

- 常量与常量的拼接结果在常量池，且常量池中不会存在相同内容的常量
- 只要其中一个是变量，结果就在堆中
- 如果拼接的结果调用intern()方法，返回值就在常量池中

```java
   public void test3(){
        String s1 = "javaEE";
        String s2 = "hadoop";

        String s3 = "javaEEhadoop";
        String s4 = "javaEE" + "hadoop";
        String s5 = s1 + "hadoop";
        String s6 = "javaEE" + s2;
        String s7 = s1 + s2;

        System.out.println(s3 == s4);//true
        System.out.println(s3 == s5);//false
        System.out.println(s3 == s6);//false
        System.out.println(s3 == s7);//false
        System.out.println(s5 == s6);//false
        System.out.println(s5 == s7);//false
        System.out.println(s6 == s7);//false

        String s8 = s6.intern();//返回值得到的s8使用的常量值中已经存在的“javaEEhadoop”
        System.out.println(s3 == s8);//true
    }
```



### 1.5、常用方法

#### 1.5.1、常用方法一

- int length()：返回字符串长度：return value.length
- char charAt(int index)：返回某索引处的字符 return value[index]
- boolean isEmpty()：判断是否是空字符串：return value.length == 0
- String toLowerCase()：使用默认语言环境，将String中的所有字符转换成小写
- String toUpperCase()：使用默认语言环境，将String中的所有字符转换成大写
- String trim()：返回字符的副本，忽略前导空白和尾部空白
- boolean equals(Object obj)：比较字符串的内容是否相同
- boolean equalsIgnoreCase(String anotherString)：与equals方法类似，但忽略大小写
- String concat(String str)：将指定字符串连接到此字符串的结尾，等价于用“+”
- int compareTo(String anotherString)：比较两个字符串的大小，如果为负数，则当前字符串小，如果为正数，则当前字符串大，如果相等，则大小一样
- String substring(int beginIndex)：返回一个新的字符串，它是此字符串的从beginIndex开始截取
- String substring(int beginIndex,int endIndex)：返回一个新字符串，它是此字符串从beginIndex开始截取到endIndex，左闭右开

```java
    public void test1() {
        String s1 = "HelloWorld";
        System.out.println(s1.length());//10
        System.out.println(s1.charAt(0));//H
        System.out.println(s1.charAt(9));//d
//        System.out.println(s1.charAt(10));//StringIndexOutOfBoundsException
//        s1 = "";
        System.out.println(s1.isEmpty());// false

        String s2 = s1.toLowerCase();
        System.out.println(s1);//s1不可变的，仍然为原来的字符串HelloWorld
        System.out.println(s2);//改成小写以后的字符串helloworld

        String s3 = "   he  llo   world   ";
        String s4 = s3.trim();
        System.out.println("-----" + s3 + "-----");//-----   he  llo   world   -----
        System.out.println("-----" + s4 + "-----");//-----he  llo   world-----
    }

    public void test2() {
        String s1 = "HelloWorld";
        String s2 = "helloworld";
        System.out.println(s1.equals(s2)); // false
        System.out.println(s1.equalsIgnoreCase(s2)); //true

        String s3 = "abc";
        String s4 = s3.concat("def");
        System.out.println(s4);//abcdef

        String s5 = "abc";
        String s6 = new String("abe");
        System.out.println(s5.compareTo(s6));//涉及到字符串排序 -2 s5比s6小

        String s7 = "北京尚硅谷教育";
        String s8 = s7.substring(2);
        System.out.println(s7);//北京尚硅谷教育
        System.out.println(s8);//"尚硅谷教育"

        String s9 = s7.substring(2, 5);
        System.out.println(s9);//"尚硅谷"
    }
```



#### 1.5.2、常用方法二

- boolean endsWith(String suffix)：测试此字符串是否以指定的后缀结束
- boolean startsWith(String prefix)：测试此字符串是否以指定的前缀开始
- boolean startsWith(String prefix,int toffset)：测试此字符串从指定索引开始的子字符串是否以指定的前缀开始
- boolean contains(CharSequence s)：当且仅当此字符串包含指定的char值序列时，返回true 
- int indexOf(String str)：返回指定子字符串在此字符串中第一次出现处的索引
- int indexOf(String str,int fromIndex)：返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始找
- int lastIndexOf(String str)：返回指定子字符串在此字符串中最右边出现处的索引，从后往前找
- int lastIndexOf(String str,int fromIndex)：返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索

> 注：
>
> indexOf和lastIndexOf方法如果未找到都是返回-1

```java
public void test3(){
        String str1 = "hellowworld";
        boolean b1 = str1.endsWith("rld");
        System.out.println(b1);//true

        boolean b2 = str1.startsWith("He");
        System.out.println(b2);//false

        boolean b3 = str1.startsWith("ll",2);
        System.out.println(b3);//true

        String str2 = "wor";
        System.out.println(str1.contains(str2));//true

        System.out.println(str1.indexOf("lol")); //-1

        System.out.println(str1.indexOf("lo",5)); //-1

        String str3 = "hellorworld";

        System.out.println(str3.lastIndexOf("or")); //7
        System.out.println(str3.lastIndexOf("or",6)); //4

        //什么情况下，indexOf(str)和lastIndexOf(str)返回值相同？
        //情况一：存在唯一的一个str。情况二：不存在str
    }
```



#### 1.5.3、常用方法三

- 替换：

- - String replace(char oldChar,char newChar)：返回一个新的字符串，它是通过用newChar替换此字符串中出现的所有oldChar得到的
  - String replace(CharSequence target,CharSequence replacement)：使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串
  - String replaceAll(String regex,String replacement)：使用给定的replacement替换此字符串所有匹配给定的正则表达式的子字符串
  - String replaceFirst(String regex,String replacement)：使用给定的replacement替换此字符串匹配给定的正则表达式的第一个子字符串

- 匹配：

- - boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式

- 切片：

- - String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串，并将切下来的各部分字符串返回为字符串数组
  - String[] split(String regex,int limit)：根据匹配给定的正则表达式来拆分此字符串，最多不超过limit个，如果超过了，剩下的全部放到最后一个元素中

```java
public void test4(){
        String str1 = "北京尚硅谷教育北京";
        String str2 = str1.replace('北', '东');

        System.out.println(str1);//北京尚硅谷教育北京
        System.out.println(str2);//东京尚硅谷教育东京

        String str3 = str1.replace("北京", "上海");
        System.out.println(str3);//上海尚硅谷教育上海

        System.out.println("*************************");
        String str = "12hello34world5java7891mysql456";
        //把字符串中的数字替换成,，如果结果中开头和结尾有，的话去掉
        String string = str.replaceAll("\\d+", ",").replaceAll("^,|,$", "");
        System.out.println(string);//hello,world,java,mysql

        System.out.println("*************************");
        str = "12345";
        //判断str字符串中是否全部有数字组成，即有1-n个数字组成
        boolean matches = str.matches("\\d+");
        System.out.println(matches);//true
        String tel = "0571-4534289";
        //判断这是否是一个杭州的固定电话
        boolean result = tel.matches("0571-\\d{7,8}");
        System.out.println(result);//true

        System.out.println("*************************");
        str = "hello|world|java";
        String[] strs = str.split("\\|");
        for (int i = 0; i < strs.length; i++) {
            System.out.println(strs[i]);//hello   world    java
        }
        System.out.println();
        str2 = "hello.world.java";
        String[] strs2 = str2.split("\\.");
        for (int i = 0; i < strs2.length; i++) {
            System.out.println(strs2[i]);//hello   world    java
        }
    }
```



### 1.6、String与char[]之间的转换

- String --> char[]：调用String的toCharArray()
- char[] --> String：调用String的构造器

```java
    public void test2(){
        String str1 = "abc123";  //题目： a21cb3

        char[] charArray = str1.toCharArray();
        for (int i = 0; i < charArray.length; i++) {
            System.out.print(charArray[i]);//abc123

        char[] arr = new char[]{'h','e','l','l','o'};
        String str2 = new String(arr);
        System.out.println(str2);//hello
    }
```



### 1.7、String与byte[]之间的转换

- String --> byte[]：调用String的getBytes[]
- byte[] --> String：调用String的构造器 

```java
    public void test3() throws UnsupportedEncodingException {
        String str1 = "abc123中国";
        byte[] bytes = str1.getBytes();//使用默认的字符集，进行编码。
        System.out.println(Arrays.toString(bytes));//[97, 98, 99, 49, 50, 51, -28, -72, -83, -27, -101, -67]

        byte[] gbks = str1.getBytes("gbk");//使用gbk字符集进行编码。
        System.out.println(Arrays.toString(gbks));//[97, 98, 99, 49, 50, 51, -42, -48, -71, -6]

        System.out.println("******************");

        String str2 = new String(bytes);//使用默认的字符集，进行解码。
        System.out.println(str2);//abc123中国

        String str3 = new String(gbks);
        System.out.println(str3);//出现乱码。原因：编码集和解码集不一致！

        String str4 = new String(gbks, "gbk");
        System.out.println(str4);//没有出现乱码。原因：编码集和解码集一致！
    }
```





## **2、StringBuffer和StringBuilder的使用**

### 2.1、String、StringBuffer、StringBuilder三者的异同

- String：不可变的字符序列；底层使用char[]存储 ，效率最低
- StringBuffer：可变的字符序列；线程安全，效率低；底层使用char[]存储；StringBuffer继承了AbstractStringBuilder
- StringBuilder：可变的字符序列；线程不安全，效率高，jdk5.0新增的；底层使用char[]存储 ；StringBuffer继承了AbstractStringBuilder

> 注：
>
> 面试题：为什么String不可变，StringBuffer和StringBuilder可变？
>
> 因为String底层的char[]是使用final修饰的，final修饰的字符串常量会直接放到常量池中



### 2.2、源码分析

#### 2.2.1、StringBuilder

StringBuilder类的继承关系：

![image-20230313150645870](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313150645870.png)



```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
    char[] value;

    /**
     * The count is the number of characters used.
     */
    int count;

    /**
     * This no-arg constructor is necessary for serialization of subclasses.
     */
    AbstractStringBuilder() {
    }

    /**
     * Creates an AbstractStringBuilder of the specified capacity.
     */
    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
    }
}
```

```java
public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{

    /** use serialVersionUID for interoperability */
    static final long serialVersionUID = 4383685877147921099L;

    /**
     * Constructs a string builder with no characters in it and an
     * initial capacity of 16 characters.
     */
    public StringBuilder() {
        // StringBuilder默认初始大小为16
        super(16);
    }
}
```

> 注：
>
> StringBuilder默认初始长度为16



#### 2.2.2、StringBuffer

StringBuffer类的继承关系：

![image-20230313150553045](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230313150553045.png)



> 注：
>
> StringBuilder和StringBuffer底层结构基本相同，默认初始长度都是16
>
> 默认情况下，扩容为原来容量的2倍 + 2，同时将原有数组中的元素复制到新的数组中
>
> 指导意义：开发中建议大家使用：StringBuffer(int capacity) 或 StringBuilder(int capacity)

```java
private void ensureCapacityInternal(int minimumCapacity) {
        // overflow-conscious code
        if (minimumCapacity - value.length > 0) {
            value = Arrays.copyOf(value,
                    newCapacity(minimumCapacity));
        }
}

private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int newCapacity = (value.length << 1) + 2;
        if (newCapacity - minCapacity < 0) {
            newCapacity = minCapacity;
        }
        return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
            ? hugeCapacity(minCapacity)
            : newCapacity;
}
```



### 2.3、StringBuffer的常用方法

- StringBuffer append(xxx)：提供了很多的append()方法，用于进行字符串拼接
- StringBuffer delete(int start,int end)：删除指定位置的内容
- StringBuffer replace(int start,int end,String str)：把[start,end)位置替换为str
- StringBuffer insert(int offset,xxx)：在指定位置插入xxx
- StringBuffer reverse()：把当前字符序列逆转
- public int indexOf(String str)：返回子字符串在字符串中首次出现的位置
- public String substring(int start,int end)：返回一个从start开始到end索引结束的左闭右开区间的子字符串
- public int length()：返回字符串的长度
- public char charAt(int n)：返回指定索引的字符
- public void setCharAt(int n,char ch)：设置指定索引位置的字符



### 2.4、总结

- 增：append(xxx)，可以使用链式结构
- 删：delete(int start,int end)
- 改：setCharAt(int n,char ch) / replace(int start,int end,String str)
- 查：charAt(int n)
- 插：insert(int offset,xxx)
- 长度：length()
- 遍历：for + charAt() / toString()





## 3、Date类（java.util.Date）

### 3.1、两个构造器的使用

- 构造器一：Date() 创建一个对应当前时间的Date对象
- 构造器二：Date(long l) 创建指定毫秒数的Date对象



### 3.2、两个方法的使用

- toString()：显示当前的年、月、日、时、分、秒
- getTime()：获取当前Date对象对应的毫秒数（时间戳）



### 3.3、java.sql.Date

对应着数据库中的日期类型的变量

![image-20230315092745291](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230315092745291.png)



如何实例化：java.sql.Date() date = new java.sql.Date(long l);

如何将java.util.Date对象转换为java.sql.Date

```java
/**
* JDK 8之前日期和时间的API测试
*
* @author shkstart
* @create 2019 下午 4:30
*/
public class DateTimeTest {
    /*
    java.util.Date类
           |---java.sql.Date类
    3. java.sql.Date对应着数据库中的日期类型的变量
        >如何实例化
        >如何将java.util.Date对象转换为java.sql.Date对象
     */
    @Test
    public void test2(){
        //构造器一：Date()：创建一个对应当前时间的Date对象
        Date date1 = new Date();
        System.out.println(date1.toString());//Sat Feb 16 16:35:31 GMT+08:00 2019

        System.out.println(date1.getTime());//1550306204104

        //构造器二：创建指定毫秒数的Date对象
        Date date2 = new Date(155030620410L);
        System.out.println(date2.toString());

        //创建java.sql.Date对象
        java.sql.Date date3 = new java.sql.Date(35235325345L);
        System.out.println(date3);//1971-02-13

        //如何将java.util.Date对象转换为java.sql.Date对象
        //情况一：
//        Date date4 = new java.sql.Date(2343243242323L);
//        java.sql.Date date5 = (java.sql.Date) date4;
        //情况二：
        Date date6 = new Date();
        java.sql.Date date7 = new java.sql.Date(date6.getTime());
    }

    //1.System类中的currentTimeMillis()
    @Test
    public void test1(){
        long time = System.currentTimeMillis();
        //返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差。
        //称为时间戳
        System.out.println(time);
    }
}
```



### 3.4、SimpleDateFormat的使用

SimpleDateFormat对日期Date类的格式化和解析

Date类的API不易于国际化，大部分被废弃了，java.text.SimpleDateFormat类是一个不与语言环境有关的方式来格式化和解析日期的具体类。

它允许进行格式化：日期→文本、解析：文本→日期

格式化：格式化成日期字符串

- SimpleDateFormat：默认的模式和语言环境创建对象
- public SimpleDateFormat(String pattern)：该构造方法可以用参数pattern指定的格式创建一个对象，该对象调用
- public String format(Date date)：方法格式化时间对象date

解析：

- public Date parse(String source)：从给定字符串的开始解析文本，以生成一个日期

```java
public void testSimpleDateFormat() throws ParseException {
        //实例化SimpleDateFormat:使用默认的构造器
        SimpleDateFormat sdf = new SimpleDateFormat();

        //格式化：日期 --->字符串
        Date date = new Date();
        System.out.println(date);

        String format = sdf.format(date);
        System.out.println(format);
 
        //解析：格式化的逆过程，字符串 ---> 日期
        String str = "19-12-18 上午11:43";
        Date date1 = sdf.parse(str);
        System.out.println(date1);

        //*************按照指定的方式格式化和解析：调用带参的构造器*****************
//        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyyy.MMMMM.dd GGG hh:mm aaa");
        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        //格式化
        String format1 = sdf1.format(date);
        System.out.println(format1);//2019-02-18 11:48:27
        //解析:要求字符串必须是符合SimpleDateFormat识别的格式(通过构造器参数体现),
        //否则，抛异常
        Date date2 = sdf1.parse("2020-02-18 11:48:27");
        System.out.println(date2);
    }
```



### 3.5、Calender日历类（抽象类）的使用

Calendar是一个抽象基类，主要用于完成日期字段之间相互操作的功能

```java
public void testCalendar(){
        //1.实例化
        //方式一：创建其子类（GregorianCalendar）的对象
        //方式二：调用其静态方法getInstance()
        Calendar calendar = Calendar.getInstance();
//        System.out.println(calendar.getClass());

        //2.常用方法
        //get()
        int days = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(days);
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));

        //set()
        //calendar可变性
        calendar.set(Calendar.DAY_OF_MONTH,22);
        days = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(days);

        //add()
        calendar.add(Calendar.DAY_OF_MONTH,-3);
        days = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(days);

        //getTime():日历类---> Date
        Date date = calendar.getTime();
        System.out.println(date);

        //setTime():Date ---> 日历类
        Date date1 = new Date();
        calendar.setTime(date1);
        days = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(days);
    }
```



#### 3.5.1、实例化

- 方式一：创建其子类（GregorianCalendar）的对象
- 方式二：调用其静态方法getInstance()

````java
Calendar calendar = Calendar.getInstance();
````



#### 3.5.2、常用方法

一个Calendar的实例是系统时间的抽象表示，通过get(int field)方法来取得想要的时间信息。比如YEAR、MONTH、DAY_OF_WEEK、HOUR_OF_DAY、MINUTE、SECOND

- get()

```java
int days = calendar.get(Calendar.DAY_OF_MONTH);
System.out.println(days);
System.out.println(calendar.get(Calendar.DAY_OF_YEAR));
```

- set()

```java
//calendar可变性
calendar.set(Calendar.DAY_OF_MONTH,22);
days = calendar.get(Calendar.DAY_OF_MONTH);
System.out.println(days); // 22
```

- add()

```java
calendar.add(Calendar.DAY_OF_MONTH,-3);
days = calendar.get(Calendar.DAY_OF_MONTH);
System.out.println(days);
```

- getTime()：日历类 —> Date

```java
Date date = calendar.getTime();
System.out.println(date);
```

- setTime()：Date —> 日历类

```java
Date date1 = new Date();
calendar.setTime(date1);
days = calendar.get(Calendar.DAY_OF_MONTH);
System.out.println(days);
```

> 注：
>
> 获取月份时：一月是0，二月是1，以此类推，12月是11
>
> 获取星期时：周日是1，周二是2，。。。周六是7



### 3.6、JDK8中新日期时间API

#### 3.6.1、新日期时间API出现的背景

如果我们可以跟别人说：“我们在 1502643933071见面，别晚了!”那么就再简单不过了。但是我们希望时间与昼夜和四季有关，于是事情就变复杂了。JDK 1.0中包含了一个java.util.Date类，但是它的大多数方法已经在JDK 1.1引入Calendar类之后被弃用了。而Calendar并不比Date好多少。它们面临的问题是：

- 可变性：像日期和时间这样的类应该是不可变的。
- 偏移性：Date中的年份是从1900年开始的，而月份都是从0开始。
- 格式化：格式化只对Date有用，Calendar则不行。此外，它们也不是线程安全的；不能处理闰秒等。

第三次引入的API是成功的，并且Java 8中引入的java.time API已经纠正了过去的缺陷，将来很长一段时间内它都会为我们服务。

Java8吸收了Joda-Time的精华，以一个新的开始为Java创建优秀的API。新的java.time 中包含了所有关于本地日期（LocalDate） 、本地时间（LocalTime）、本地日期时间（LocalDateTime）、时区（ZonedDateTime）和持续时间（Duration）的类。历史悠久的Date类新增了tolnstant() 方法，用于把Date转换成新的表示形式。这些新增的本地化时间日期API大大简化了日期时间和本地化的管理。

####  3.6.2、新日期时间API

LocalDate、 LocalTime、 LocalDateTime类是其中较重要的几个类，它们的实例是**不可变**的对象，分别表示使用IS0-8601日历系统的日期、时间、日期和时间。它们提供了简单的本地日期或时间，并不包含当前的时间信息，也不包含与时区相关的信息。

- LocalDate代表IOS格式(yyyy-MM-dd) 的日期,可以存储生日、纪念日等日期。
- LocalTime表示一个时间，而不是日期。
- LocalDateTime是用来表示日期和时间的，这是一个最常用的类之一。

>  注：IS0-8601日历系统是国际标准化组织制定的现代公民的日期和时间的表示法，也就是公历。

```java
/**
*LocalDate、LocalTime、LocalDateTime 的使用
*说明：
*1.LocalDateTime相较于LocalDate、LocalTime，使用频率要高
*2.类似于Calendar
*/
public void test1(){
        //now():获取当前的日期、时间、日期+时间
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();

        System.out.println(localDate); // 2023-03-15
        System.out.println(localTime); // 09:48:25.624
        System.out.println(localDateTime);// 2023-03-15T09:48:25.624

        //of():设置指定的年、月、日、时、分、秒。没有偏移量
        LocalDateTime localDateTime1 = LocalDateTime.of(2020, 10, 6, 13, 23, 43);
        System.out.println(localDateTime1); // 2020-10-06T13:23:43

        //getXxx()：获取相关的属性
        System.out.println(localDateTime.getDayOfMonth()); // 15
        System.out.println(localDateTime.getDayOfWeek()); // WEDNESDAY
        System.out.println(localDateTime.getMonth()); // MARCH
        System.out.println(localDateTime.getMonthValue()); // 3
        System.out.println(localDateTime.getMinute()); // 48

        //体现不可变性
        //withXxx():设置相关的属性
        LocalDate localDate1 = localDate.withDayOfMonth(22);
        System.out.println(localDate); // 2023-03-15
        System.out.println(localDate1); // 2023-03-22

        LocalDateTime localDateTime2 = localDateTime.withHour(4);
        System.out.println(localDateTime); // 2023-03-15T09:48:25.624
        System.out.println(localDateTime2); // 2023-03-15T04:48:25.624

        //不可变性
        LocalDateTime localDateTime3 = localDateTime.plusMonths(3);
        System.out.println(localDateTime);// 2023-03-15T09:48:25.624
        System.out.println(localDateTime3);// 2023-06-15T09:48:25.624

        LocalDateTime localDateTime4 = localDateTime.minusDays(6);
        System.out.println(localDateTime);// 2023-03-15T09:48:25.624
        System.out.println(localDateTime4);// 2023-03-09T09:48:25.624
    }
```



#### 3.6.3、瞬时：Instant

Instant：时间线上的一个瞬时点。这可能被用来记录应用程序中的事件时间戳。

在处理时间和日期的时候，我们通常会想到年，月，日，时，分，秒。然而，这只是时间的一个模型，是面向人类的。第二种通用模型是面向机器的，或者说是连续的。在此模型中，时间线中的一个点表示为一个很大的数，这有利于计算机处理。在UNIX中，这个数从1970年开始，以秒为的单位；同样的，在Java中 ,也是从1970年开始，但以毫秒为单位。

java.time包通过值类型Instant提供机器视图，不提供处理人类意义上的时间单位。Instant表示时间线上的一点， 而不需要任何上下文信息，例如，时区。概念上讲，它只是简单的表示自1970年1月1日0时0分0秒(UTC)开始的秒数。因为java.time包是基于纳秒计算的，所以Instant的精度可以达到纳秒级。

>  注：(1 ns= 10^-9s) 1秒= 1000毫秒=10^6微秒=10^9纳秒

```java
public void test2(){
        //now():获取本初子午线对应的标准时间，英国伦敦时间
        Instant instant = Instant.now();
        System.out.println(instant);//2023-03-15T01:52:55.960Z

        //添加时间的偏移量
        OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
        System.out.println(offsetDateTime);//2023-03-15T09:52:55.960+08:00

        //toEpochMilli():获取自1970年1月1日0时0分0秒（UTC）开始的毫秒数，类似于Date类的getTime()
        long milli = instant.toEpochMilli();
        System.out.println(milli);// 1678845175960

        //ofEpochMilli():通过给定的毫秒数，获取Instant实例，类似于Date(long millis)
        Instant instant1 = Instant.ofEpochMilli(1550475314878L);
        System.out.println(instant1);// 2019-02-18T07:35:14.878Z
}
```



#### 3.6.4、格式化与解析日期或时间

```java
/*
    DateTimeFormatter：格式化或解析日期、时间
    类似于SimpleDateFormat
*/
    @Test
    public void test3(){
//        方式一：预定义的标准格式。如：ISO_LOCAL_DATE_TIME;ISO_LOCAL_DATE;ISO_LOCAL_TIME
        DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
        //格式化:日期-->字符串
        LocalDateTime localDateTime = LocalDateTime.now();
        String str1 = formatter.format(localDateTime);
        System.out.println(localDateTime);
        System.out.println(str1);//2019-02-18T15:42:18.797

        //解析：字符串 -->日期
        TemporalAccessor parse = formatter.parse("2019-02-18T15:42:18.797");
        System.out.println(parse);

//      方式二：
//      本地化相关的格式。如：ofLocalizedDateTime()
//      FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT :适用于LocalDateTime
        DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
        //格式化
        String str2 = formatter1.format(localDateTime);
        System.out.println(str2);//2019年2月18日 下午03时47分16秒

//      本地化相关的格式。如：ofLocalizedDate()
//      FormatStyle.FULL / FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT : 适用于LocalDate
        DateTimeFormatter formatter2 = DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM);
        //格式化
        String str3 = formatter2.format(LocalDate.now());
        System.out.println(str3);//2019-2-18

//       重点： 方式三：自定义的格式。如：ofPattern(“yyyy-MM-dd hh:mm:ss”)
        DateTimeFormatter formatter3 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        //格式化
        String str4 = formatter3.format(LocalDateTime.now());
        System.out.println(str4);//2019-02-18 03:52:09

        //解析
        TemporalAccessor accessor = formatter3.parse("2019-02-18 03:52:09");
        System.out.println(accessor);
    }
```



#### 3.6.5、其他时间API

Zoneld：该类中包含了所有的时区信息，一个时区的ID，如Europe/Paris。

ZonedDate Time：一个在IS0-8601日历系统时区的日期时间，如2007-12-03T 10:15:30+01:00 Europe/Paris。

- 其中每个时区都对应着ID，地区ID都为“{区域}{城市}”的格式，例如：Asia/Shanghai等。

Clock：使用时区提供对当前即时、日期和时间的访问的时钟。

持续时间：Duration， 用于计算两个“时间”间隔。

日期间隔：Period，用于计算两个“日期”间隔。

TemporalAdjuster：时间校正器。有时我们可能需要获取例如:将日期调整到“下一个工作日”等操作。

TemporalAdjusters：该类通过静态方法(firstDayOfXxx()/lastDayOfXxx()/nextXxx()提供了大量的常用TemporalAdjuster的实现。



#### 3.6.6、与传统日期处理的转换

![image-20230315095543084](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230315095543084.png)





## 4、Java比较器

在Java中经常会涉及到对象数组的排序问题，那么就涉及到对象之间的比较问题

Java实现对象排序的有两种

- 自然排序：java.lang.Comparable
- 定制排序：java.util.Comparator



### 4.1、说明

Java中的对象，正常情况下，只能进行比较：== 或 != 。不能使用 > 或 < 的，但是在开发场景中，我们需要对多个对象进行排序，言外之意，就需要比较对象的大小。如何实现？使用两个接口中的任何一个：Comparable 或 Comparator



### 4.2、Comparable接口的使用（自然排序）

像String、包装类等实现了Comparable接口，重写了compareTo(obj)方法，给出了比较两个对象大小的方式，进行了从小到大的排列。

重写compareTo(obj)的规则：

- 如果当前对象this大于形参对象obj，则返回正整数
- 如果当前对象this小于形参对象obj，则返回负整数
- 如果当前对象this等于形参对象obj，则返回零

对于自定义类来说，如果需要排序，我们可以让自定义类实现Comparable接口，重写compareTo(obj)方法。在compareTo(obj)方法中指明如何排序。

```java
public class CompareTest {
    @Test
    public void test1(){
        String[] arr = new String[]{"AA","CC","KK","MM","GG","JJ","DD"};
        //Arrays.sort(arr);

        System.out.println(Arrays.toString(arr));
    }

    //Goods对象必须实现Comparable接口
    @Test
    public void test2() {
        Goods[] arr = new Goods[5];
        arr[0] = new Goods("lenovoMouse",34);
        arr[1] = new Goods("dellMouse",43);
        arr[2] = new Goods("xiaomiMouse",12);
        arr[3] = new Goods("huaweiMouse",65);
        arr[4] = new Goods("microsoftMouse",43);

        Arrays.sort(arr);

        System.out.println(Arrays.toString(arr));
    }
}
```



### 4.3、Comparator接口的使用（定制排序）

背景：当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用 Comparator 的对象来排序

重写compare(Object o1,Object o2)方法，比较o1和o2的大小：

- 如果方法返回正整数，则表示o1大于o2
- 如果返回0，表示相等
- 返回负整数，表示o1小于o2

可以将Comparator传递给sort方法（如Collections.sort或Arrays.sort），从而允许在排序顺序上实现精准控制

还可以使用Comparator来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序

```java
    @Test
    public void test3(){
        String[] arr = new String[]{"AA","CC","KK","MM","GG","JJ","DD"};
        Arrays.sort(arr,new Comparator(){
            //按照字符串从大到小的顺序排列
            @Override
            public int compare(Object o1, Object o2) {
                if(o1 instanceof String && o2 instanceof  String){
                    String s1 = (String) o1;
                    String s2 = (String) o2;
                    return -s1.compareTo(s2);
                }
//                return 0;
                throw new RuntimeException("输入的数据类型不一致");
            }
        });
        System.out.println(Arrays.toString(arr));
    }

    @Test
    public void test4(){
        Goods[] arr = new Goods[6];
        arr[0] = new Goods("lenovoMouse",34);
        arr[1] = new Goods("dellMouse",43);
        arr[2] = new Goods("xiaomiMouse",12);
        arr[3] = new Goods("huaweiMouse",65);
        arr[4] = new Goods("huaweiMouse",224);
        arr[5] = new Goods("microsoftMouse",43);

        Arrays.sort(arr, new Comparator() {
            //指明商品比较大小的方式:按照产品名称从低到高排序,再按照价格从高到低排序
            @Override
            public int compare(Object o1, Object o2) {
                if(o1 instanceof Goods && o2 instanceof Goods){
                    Goods g1 = (Goods)o1;
                    Goods g2 = (Goods)o2;
                    if(g1.getName().equals(g2.getName())){
                        return -Double.compare(g1.getPrice(),g2.getPrice());
                    }else{
                        return g1.getName().compareTo(g2.getName());
                    }
                }
                throw new RuntimeException("输入的数据类型不一致");
            }
        });

        System.out.println(Arrays.toString(arr));
    }
}
```



### 4.4、Comparable接口与Comparator的使用的对比

Comparable接口的方式一旦一定，保证Comparable接口实现类的对象在任何位置都可以比较大小

Comparator接口属于临时性的比较



## 5、System类

System类代表系统，系统级的很多属性和控制方法都放置在该类的内部。该类位于java.lang包。

由于该类的构造器是private的，所以无法创建该类的对象，也就是无法实例化该类。其内部的成员变量和成员方法都是static的，所以也可以很方便的进行调用。

成员变量

- System类内部包含in、out和err三个成员变量，分别代表标准输入流(键盘输入)，标准输出流(显示器)和标准错误输出流(显示器)。

成员方法

- native long currentTimeMillis()：该方法的作用是返回当前的计算机时间，时间的表达格式为当前计算机时间和GMT时间(格林威治时间)1970年1月1号0时0分0秒所差的毫秒数。
- void exit(int status)：该方法的作用是退出程序。其中status的值为0代表正常退出，非零代表异常退出。使用该方法可以在图形界面编程中实现程序的退出功能等。
- void gc()：该方法的作用是请求系统进行垃圾回收。至于系统是否立刻回收，则取决于系统中垃圾回收算法的实现以及系统执行时的情况。
- String getProperty(String key)：该方法的作用是获得系统中属性名为key的属性对应的值。系统中常见的属性名以及属性的作用如下表所示。

![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)



## 6、Math类

java.lang.Math提供了一系列静态方法用于科学计算。其方法的参数和返回值类型一般为double型。

- abs——绝对值
- acos、asin、atan、cos、sin、tan——三角函数
- sqrt——平方根
- pow(double a,doble b) ——a的b次幂
- log——自然对数
- exp——e为底指数
- max(double a,double b)
- min(double a,double b)
- random()——返回0.0到1.0的随机数
- long round(double a)—— double 型数据a转换为long型(四舍五入)
- toDegrees(double angrad)——弧度- ->角度
- toRadians(double angdeg)——角度- ->弧度



## 7、BigInteger 和 BigDecimal

### 7.1、BigInteger

Integer类作为int的包装类，能存储的最大整型值为2^31 - 1（2,147,483,647），Long类也是有限的，最大为2^63 - 1。如果要表示再大的整数，不管是基本数据类型还是他们的包装类都无能为力，更不用说进行运算了。

java.math包的BigInteger可以表不可变的任意大小的整数。BigInteger提供所有Java的基本整数操作符的对应物，并提供java.lang.Math的所有相关方法。另外，BigInteger 还提供以下运算：模算术、GCD计算、质数测试、素数生成、位操作以及一些其他操作。

构造器

- BigInteger(String val)：根据字符串构建BigInteger对象

常用方法

- public BigInteger abs()：返回此BigInteger的绝对值的BigInteger。
- BigInteger add(BigInteger val) ：返回其值为(this + val)的BigInteger
- BigInteger subtract(BigInteger val) ：返回其值为(this - val)的BigInteger
- BigInteger multiply(BigInteger val) ：返回其值为(this * val)的BigInteger
- BigInteger divide(BigInteger val) ：返回其值为(this / val)的BigInteger。整数相除只保留整数部分。
- BigInteger remainder(BigInteger val)：返回其值为(this % val)的BigInteger。
- BigInteger] divideAndRemainder(BigInteger val)：返回包含(this/ val)后跟(this % val)的两个BigInteger的数组。
- BigInteger pow(int exponent) ：返回其值为(this^exponent) 的BigInteger。



### 7.2、BigDecimal

一般的Float类和Double类可以用来做科学计算或工程计算，但在商业计算中，要求数字精度比较高，故用到java.math.BigDecimal类。

BigDecimal类支持不可变的、任意精度的有符号十进制定点数。

构造器

- public BigDecimal(double val)
- public BigDecimal(String val)

常用方法

- public BigDecimal add(BigDecimal augend)
- public BigDecimal subtract(BigDecimal subtrahend)
- public BigDecimal multiply(BigDecimal multiplicand)
- public BigDecimal divide(BigDecimal divisor, int scale, int roundingMode)

























































































































































