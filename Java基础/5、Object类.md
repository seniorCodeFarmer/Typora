## 1、介绍

- Object类是所有Java类的根父类

- 如果在类的声明中未使用 extends 关键字指明其父类，则默认父类为java.lang.Object类

- Object类中的功能具有通用性

- - 属性：无
  - 方法：equals() / toString() /getClass() / hashcode() / clone() / finalize() / wait() / notify() / notifyAll() 

- Object类只声明了一个空参的构造器

- Object类中的主要结构（图：1015）

   ![Image](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/Image.png)



## 2、使用

- 当我们输出一个对象的引用时，实际上就是调用当前对象的toString()方法。  

~~~java
System.out.println(对象引用)
~~~

- Object类中toString()方法的定义：
  - 像String、Date、File、包装类等都重写了Object中的toStirng()方法。使得在调用对象的toString()方法时，返回“实体内容”信息
  - 自定义类也可以重写toString()方法，当调用此方法时，返回对象的“实体内容”
  - 基本类型数据转换为String类型时，调用了对应包装类的toString()方法