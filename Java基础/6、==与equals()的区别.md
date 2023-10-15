## 1、== 的使用

- 可以使用在基本数据类型和引用数据类型变量中。
- 如果比较的是基本数据类型变量，比较两个变量保存的数据是否相等，不一定类型要相同。
- 如果比较的是引用数据类型变量，比较两个对象地址值是否相同，即两个引用是否指向同一个对象实体。
- 补充：== 符号使用时，必须保证符号左右两边的变量类型一致，否则编译不通过。（都是基本数据类型或都是引用数据类型）

~~~Java
int i = 10；
int j = 10;
double d = 10.0;
System.out.println(i == j);//true
System.out.println(i == d);//true

char c = 10;
System.out.println(i == c);//true

char c1 = 'A';
char c2 = 65;
System.out.println(c1 == c2);//true
~~~



## 2、equals() 的使用

- 是一个方法，不是运算符
- 只能适用于引用数据类型
- Object类中equals()方法的定义：

~~~Java
public boolean equals(Object obj) {
    if (this == obj)
        return true;
    if (obj == null)
        return false;
    if (getClass() != obj.getClass())
        return false;
    TestCustomer other = (TestCustomer) obj;
    if (age != other.age)
        return false;
    if (name == null) {
        if (other.name != null)
            return false;
    } else if (!name.equals(other.name))
        return false;
    return true;
}
~~~

- 说明：Object类中定义的equals()方法和 == 的作用是相同的，比较两个对象的地址值是否相同，即两个引用是否指向同一个对象实体。

- 重写

~~~Java
public boolean equals(Object obj) {
    if(this == obj) {
        return true;
    }
    if(obj instanceof TestCustomer) { //判断obj是否是TestCustomer类的实例，是继续判断，否则不用继续判断
        TestCustomer test = (TestCustomer) obj; //obj向下转型
        if(this.age == test.age &&  this.name.equals(test.name)) {
            return true;
        }else {
            return false;
        }
    }else {
        return false;
    }
}
~~~

- 像String、Date、File、包装类等都重写了Object类中的equals()方法。重写以后，比较的不是两个引用的地址是否相同，而是比较两个对象的“实体内容”是否相同 。
- 通常情况下，我们自定义的类如果使用equals()的话，通常是比较两个对象的“实体内容”是否相同。那么，我们就需要对Object类中的equals()进行重写。
- 重写的原则：比较两个实体对象内容是否相同。
- 当要进行比较时，基本数据类型用 ==，引用数据类型用equals()方法

![image-20230903232135580](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903232135580.png)

![image-20230903232215514](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230903232215514.png)



## 3、hashCode 与 equals 两者直接之间的关系

- 如果两个对象相同（即用equals比较返回true），那么它们的hashCode值一定要相同！！！
- 如果两个对象不相同（即用equals比较返回false），那么它们的hashCode值可能相同也可能不相同。
- 如果两个对象的haseCode相同（存在哈希冲突），那么它们可能相同也可能不同（即equals 比较可能是true也可能是false）。
- 如果两个对象的haseCode不同，那么它们肯定不同（即用equals比较返回false）









