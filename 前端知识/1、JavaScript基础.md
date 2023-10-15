# JavaScript 基础

## 1、JavaScript介绍

### 1.1、JavaScript是什么

#### 1、JavaScript（是什么？）

- 是一种运行在客户端（浏览器）的编程语言，实现人机交互效果。

#### 2、作用（做什么？）

- 网页特效（监听用户的一些行为让网页作出对应的反馈）
- 表单验证（针对表单数据的合法性进行判断）
- 数据交互（获取后台的数据, 渲染到前端）
- 服务端编程（node.js）

![image-20230913115915797](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913115915797.png)



#### 3、JavaScript组成（有什么？）

- ECMAScript：规定了js基础语法核心知识。
  - 比如：变量、分支语句、循环语句、对象等等
- Web APIs：
  - DOM 操作文档，比如对页面元素进行移动、大小、添加删除等操作
  - BOM 操作浏览器，比如页面弹窗，检测窗口宽度、存储数据到浏览器等等

![image-20230913120050969](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913120050969.png)



### 1.2、JavaScript 书写位置

- JavaScript 程序不能独立运行，它需要被嵌入 HTML 中，然后浏览器才能执行 JavaScript 代码。通过 `script` 标签将 JavaScript 代码引入到 HTML 中，有两种方式：

![image-20230913130729711](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913130729711.png)

#### 1、内部方式

- 通过 `script` 标签包裹 JavaScript 代码


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 引入方式</title>
    </head>
    <body>
        <!-- 内联形式：通过 script 标签包裹 JavaScript 代码 -->
        <script>
            alert('嗨，欢迎来传智播学习前端技术！')
        </script>
    </body>
</html>
```

- **规范**：script标签写在</body>上面

> 注：
>
> - 我们将 <script> 放在HTML文件的底部附近的原因是浏览器会按照代码在文件中的顺序加载 HTML。如果先加载的 JavaScript 期望修改其下方的 HTML，那么它可能由于 HTML 尚未被加载而失效。因此，将 JavaScript 代码放在 HTML页面的底部附近通常是最好的策略。



#### 2、外部形式

- 一般将 JavaScript 代码写在独立的以 .js 结尾的文件中，然后通过 `script` 标签的 `src` 属性引入。


```javascript
// demo.js
document.write('嗨，欢迎来传智播学习前端技术！')
```

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 引入方式</title>
    </head>
    <body>
        <!-- 外部形式：通过 script 的 src 属性引入独立的 .js 文件 -->
        <script src="demo.js"></script>
    </body>
</html>
```

- 如果 script 标签使用 src 属性引入了某 .js 文件，那么标签的代码会被忽略！！！如下代码所示：


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 引入方式</title>
    </head>
    <body>
        <!-- 外部形式：通过 script 的 src 属性引入独立的 .js 文件 -->
        <script src="demo.js">
            // 此处的代码会被忽略掉！！！！
            alert(666);  
        </script>
    </body>
</html>
```



#### 3、内联JavaScript

- 代码写在标签内部

~~~html
<body>
    <button onclick="alert('逗你玩~~~')">点击我月薪过万</button>
</body>
~~~





###  1.3、注释和结束符

- 通过注释可以屏蔽代码被执行或者添加备注信息，JavaScript 支持两种形式注释语法：


#### 1、单行注释

- 使用 `// ` 注释单行代码


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 注释</title>
    </head>
    <body>

        <script>
            // 这种是单行注释的语法
            // 一次只能注释一行
            // 可以重复注释
            document.write('嗨，欢迎来传智播学习前端技术！');
        </script>
    </body>
</html>
```



#### 2、多行注释

- 使用 `/* */` 注释多行代码


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 注释</title>
    </head>
    <body>

        <script>
            /* 这种的是多行注释的语法 */
            /*
    	更常见的多行注释是这种写法
    	在些可以任意换行
    	多少行都可以
      */
            document.write('嗨，欢迎来传智播学习前端技术！')
        </script>
    </body>
</html>
```

> 注：
>
> - 编辑器中单行注释的快捷键为`ctrl + /`
> - 编辑器中多行注释的快捷键为`shift + alt + A`



### 1.4、结束符

- 在 JavaScript 中`;`代表一段代码的结束，多数情况下可以省略 `;` 使用回车（enter）替代。
- 作用：使用英文的 ; 代表语句结束。
- 实际情况：实际开发中，可写可不写, 浏览器(JavaScript 引擎) 可以自动推断语句的结束位置。
- 现状：在实际开发中，越来越多的人主张，书写 JavaScript 代码时省略结束符。
- 约定：为了风格统一，结束符要么每句都写，要么每句都不写（按照团队要求）。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 结束符</title>
    </head>
    <body>

        <script> 
            alert(1);
            alert(2);
            alert(1)
            alert(2)
        </script>
    </body>
</html>
```

- 实际开发中有许多人主张书写 JavaScript 代码时省略结束符 `;`




### 1.5、输入和输出

- 输出和输入也可理解为人和计算机的交互，用户通过键盘、鼠标等向计算机输入信息，计算机处理后再展示结果给用户，这便是一次输入和输出的过程。
- 举例说明：如按键盘上的方向键，向上/下键可以滚动页面，按向上/下键这个动作叫作输入，页面发生了滚动了这便叫输出。


#### 1、输出语法

- JavaScript 可以接收用户的输入，然后再将输入的结果输出：


##### （1）语法1：

~~~html
document.write("要输出的内容")
~~~

- 作用：向body内输出内容
- 注意：如果输出的内容写的是标签，也会被解析成网页元素

##### （2）语法2：

~~~html
alert('要输出的内容')
~~~

- 作用：页面弹出警告对话框

##### （3）语法3：

~~~html
console.log('控制台打印')
~~~

- 作用：控制台输出语法，程序员调试使用



####  2、输入语法

- 向 `prompt()` 输入任意内容会以弹窗形式出现在浏览器中，一般提示用户输入一些内容。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 输入输出</title>
    </head>
    <body>

        <script> 
            // 1. 输入的任意数字，都会以弹窗形式展示
            document.write('要输出的内容')
            alert('要输出的内容');

            // 2. 以弹窗形式提示用户输入姓名，注意这里的文字使用英文的引号
            prompt('请输入您的姓名:')
        </script>
    </body>
</html>
```





## 2、变量

### 2.1、变量是什么

- 变量是计算机中用来存储数据的“容器”，它可以让计算机变得有记忆，通俗的理解变量就是使用【某个符号】来代表【某个具体的数值】（数据）


```html
<script>
    // x 符号代表了 5 这个数值
    x = 5
    // y 符号代表了 6 这个数值
    y = 6

    //举例： 在 JavaScript 中使用变量可以将某个数据（数值）记录下来！

    // 将用户输入的内容保存在 num 这个变量（容器）中
    num = prompt('请输入一数字!')

    // 通过 num 变量（容器）将用户输入的内容输出出来
    alert(num)
    document.write(num)
</script>
```



### 2.2、声明

- 声明（定义）变量有两部分构成：声明关键字、变量名（标识）


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 声明和赋值</title>
    </head>
    <body>

        <script> 
            // let 变量名
            // 声明(定义)变量有两部分构成：声明关键字、变量名（标识）
            // let 即关键字，所谓关键字是系统提供的专门用来声明（定义）变量的词语
            // age 即变量的名称，也叫标识符
            let age
        </script>
    </body>
</html>
```

- 关键字是 JavaScript 中内置的一些英文词汇（单词或缩写），它们代表某些特定的含义，如 `let` 的含义是声明变量的，看到 `let`  后就可想到这行代码的意思是在声明变量，如 `let age;` 
- `let` 和 `var` 都是 JavaScript 中的声明变量的关键字，推荐使用 `let` 声明变量！！！



### 2.3、赋值

- 声明（定义）变量相当于创造了一个空的“容器”，通过赋值向这个容器中添加数据。
- 简单点，也可以声明变量的时候直接完成赋值操作，这种操作也称为变量初始化。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 声明和赋值</title>
    </head>
    <body>

        <script> 
            // 声明(定义)变量有两部分构成：声明关键字、变量名（标识）
            // let 即关键字，所谓关键字是系统提供的专门用来声明（定义）变量的词语
            // age 即变量的名称，也叫标识符
            let age
            // 赋值，将 18 这个数据存入了 age 这个“容器”中
            age = 18
            // 这样 age 的值就成了 18
            document.write(age)

            // 也可以声明和赋值同时进行
            let str = 'hello world!'
            alert(str);
        </script>
    </body>
</html>
```



### 2.4、更新变量

- 变量赋值后，还可以通过简单地给它一个不同的值来更新它。

~~~html
let age = 18
age = 19
~~~

> 注：
>
> - let 不允许多次声明一个变量。



### 2.5、声明多个变量

- 语法：多个变量中间用逗号隔开。

~~~html
let age = 19,uname = 'pink'
~~~



### 2.6、关键字

- JavaScript 使用专门的关键字 `let` 和 `var` 来声明（定义）变量，在使用时需要注意一些细节：

- 以下是使用 `let` 时的注意事项：
  - 允许声明和赋值同时进行
  - 不允许重复声明
  - 允许同时声明多个变量并赋值
  - JavaScript 中内置的一些关键字不能被当做变量名
- 以下是使用 `var` 时的注意事项：
  - 可以先使用再声明（不合理）
  - 允许声明和赋值同时进行
  - 允许重复声明
  - 允许同时声明多个变量并赋值
- 大部分情况使用 `let` 和 `var` 区别不大，但是 `let` 相较 `var` 更严谨，因此推荐使用 `let`，后期会更进一步介绍二者间的区别。



### 2.7、变量名命名规则

- 关于变量的名称（标识符）有一系列的规则需要遵守：
  - 只能是字母、数字、下划线、$，且不能能数字开头
  - 字母区分大小写，如 Age 和 age 是不同的变量
  - JavaScript 内部已占用于单词（关键字或保留字）不允许使用
  - 尽量保证变量具有一定的语义，见字知义

> 注：所谓关键字是指 JavaScript 内部使用的词语，如 `let` 和`var`，保留字是指 JavaScript 内部目前没有使用的词语，但是将来可能会使用词语。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 变量名命名规则</title>
    </head>
    <body>

        <script> 
            let age = 18 // 正确
            let age1 = 18 // 正确
            let _age = 18 // 正确

            // let 1age = 18; // 错误，不可以数字开头
            let $age = 18 // 正确
            let Age = 24 // 正确，它与小写的 age 是不同的变量
            // let let = 18; // 错误，let 是关键字
            let int = 123 // 不推荐，int 是保留字
        </script>
    </body>
</html>
```



## 3、常量

- 概念：使用 const 声明的变量称为“常量”。

- 使用场景：当某个变量永远不会改变的时候，就可以使用 const 来声明，而不是let。

- 命名规范：和变量一致。


~~~javascript
const PI = 3.14
~~~

>注： 
>
>- 常量不允许重新赋值，声明的时候必须赋值（初始化）



## 4、数据类型

- 计算机程序可以处理大量的数据，为了方便数据的管理，将数据分成了不同的类型：

> 注：
>
> - 通过 typeof 关键字检测数据类型

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 数据类型</title>
    </head>
    <body>

        <script> 
            // 检测 1 是什么类型数据，结果为 number
            document.write(typeof 1)
        </script>
    </body>
</html>
```



- JS 数据类型整体分为两大类：
  - 基本数据类型
  - 引用数据类型

![image-20230913135821500](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913135821500.png)



### 4.1、基本数据类型

#### 4.1.1、数字型

- 即我们数学中学习到的数字，可以是整数、小数、正数、负数


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 数据类型</title>
    </head>
    <body>

        <script> 
            let score = 100 // 正整数
            let price = 12.345 // 小数
            let temperature = -40 // 负数

            document.write(typeof score) // 结果为 number
            document.write(typeof price) // 结果为 number
            document.write(typeof temperature) // 结果为 number
        </script>
    </body>
</html>
```

- JavaScript 中的数值类型与数学中的数字是一样的，分为正数、负数、小数等。


> 注：
>
> - JS 是弱数据类型，变量到底属于那种类型，只有赋值之后，我们才能确认
> - Java是强数据类型，例如：int a = 3，必须是整数

- NaN 代表一个计算错误。它是一个不正确的或者一个未定义的数学操作所得到的结果

~~~html
console.log('老师' - 2) // NaN
~~~

- NaN 是粘性的。任何对 NaN 的操作都会返回 NaN

~~~html 
console.log(NaN - 2) // NaN
~~~





#### 4.1.2、字符串类型

- 通过单引号（ `''`） 、双引号（ `""`）或反引号包裹的数据都叫字符串，单引号和双引号没有本质上的区别，推荐使用单引号。
- 注意事项：
  - 无论单引号或是双引号必须成对使用
  - 单引号/双引号可以互相嵌套，但是不以自已嵌套自已（口诀：外双内单，或者外单内双）
  - 必要时可以使用转义符 `\`，输出单引号或双引号

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 数据类型</title>
    </head>
    <body>

        <script> 
            let user_name = '小明' // 使用单引号
            let gender = "男" // 使用双引号
            let str = '123' // 看上去是数字，但是用引号包裹了就成了字符串了
            let str1 = '' // 这种情况叫空字符串

            documeent.write(typeof user_name) // 结果为 string
            documeent.write(typeof gender) // 结果为 string
            documeent.write(typeof str) // 结果为 string
        </script>
    </body>
</html>
```

- 字符串拼接：
  - 场景：+ 运算符可以实现字符串的拼接
  - 口诀：数字相加，字符相连

~~~html
documeent.write('我叫' + '刘德华')
let uname = '刘德华'
let song = '忘情水'
documeent.write(uname + song)
~~~



**模板字符串**

- 使用场景
  - 拼接字符串和变量
  - 在没有它之前，要拼接变量比较麻烦

~~~html
document.write('大家好，我叫' + name + '，今年' + age + '岁')
~~~

- 语法
  - `` (反引号)
  - 在英文输入模式下按键盘的tab键上方那个键（1左边那个键）
  - 内容拼接变量时，用 ${ } 包住变量

~~~html
document.write(`大家好，我叫${name}，今年${age}岁`)
~~~





#### 4.1.3、布尔类型

- 表示肯定或否定时在计算机中对应的是布尔类型数据。
- 它有两个固定的值 `true` 和 `false`，表示肯定的数据用 `true`，表示否定的数据用 `false`。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 数据类型</title>
    </head>
    <body>

        <script> 
            //  pink老师帅不帅？回答 是 或 否
            let isCool = true // 是的，摔死了！
            isCool = false // 不，套马杆的汉子！

            document.write(typeof isCool) // 结果为 boolean
        </script>
    </body>
</html>
```



#### 4.1.4、undefined

- 未定义是比较特殊的类型，只有一个值 undefined。
- 只声明变量，不赋值的情况下，变量的默认值为 undefined，一般很少【直接】为某个变量赋值为 undefined。

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 数据类型</title>
    </head>
    <body>

        <script> 
            // 只声明了变量，并末赋值
            let tmp;
            document.write(typeof tmp) // 结果为 undefined
        </script>
    </body>
</html>
```

- 工作中的使用场景：
  - 我们开发中经常声明一个变量，等待传送过来的数据。
  - 如果我们不知道这个数据是否传递过来，此时我们可以通过检测这个变量是不是undefined，就判断用户是否有数据传递过来。

> 注：
>
> - JavaScript 中变量的值决定了变量的数据类型。



#### 4.1.5、null （空类型）

- JavaScript 中的 null 仅仅是一个代表“无”、“空”或“值未知”的特殊值

~~~html
let obj = null
console.log(obj) // null
~~~

- null 和 undefined 的区别
  - undefined：表示声明了一个变量但没有赋值
  - null：表示给变量赋值了，但是内容为空

- null 开发中的使用场景
  - 官方解释：把 null 作为尚未创建的对象
  - 大白话： 将来有个变量里面存放的是一个对象，但是对象还没创建好，可以先给个null





#### 4.1.6、控制台输出语句和检测数据类型

##### 1、控制台输出语句

![image-20230913151318897](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913151318897.png)

- 控制台语句经常用于测试结果来使用。
- 可以看出数字型和布尔型颜色为蓝色，字符串和undefined颜色为灰色



##### 2、通过 typeof 关键字检测数据类型

- typeof 运算符可以返回被检测的数据类型。它支持两种语法形式：

  - 作为运算符： typeof x （常用的写法）

  - 函数形式： typeof(x)

- 换言之，有括号和没有括号，得到的结果是一样的，所以我们直接使用运算符的写法。

![image-20230913151444907](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913151444907.png)





## 5、类型转换

- JavaScript是弱数据类型： JavaScript也不知道变量到底属于那种数据类型，只有赋值了才清楚。
- 在 JavaScript 中数据被分成了不同的类型，如数值、字符串、布尔值、undefined，在实际编程的过程中，不同数据类型之间存在着转换的关系。
- 坑： 使用表单、prompt 获取过来的数据默认是字符串类型的，此时就不能直接简单的进行加法运算。

![image-20230913151541430](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913151541430.png)

- 此时需要转换变量的数据类型。
- 通俗来说，就是把一种数据类型的变量转换成我们需要的数据类型。



### 5.1、隐式转换

- 某些运算符被执行时，系统内部自动将数据类型进行转换，这种转换称为隐式转换。
- 规则：
  - \+ 号两边只要有一个是字符串，都会把另外一个转成字符串
  - 除了 + 以外的算术运算符，比如 -、*、/ 等都会把数据转成数字类型
- 缺点：
  - 转换类型不明确，靠经验才能总结
- 小技巧：
  - \+ 号作为正号解析可以转换成数字型
  - 任何数据和字符串相加结果都是字符串

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 隐式转换</title>
    </head>
    <body>
        <script> 
            let num = 13 // 数值
            let num2 = '2' // 字符串

            // 结果为 132
            // 原因是将数值 num 转换成了字符串，相当于 '13'
            // 然后 + 将两个字符串拼接到了一起
            console.log(num + num2)

            // 结果为 11
            // 原因是将字符串 num2 转换成了数值，相当于 2
            // 然后数值 13 减去 数值 2
            console.log(num - num2)

            let a = prompt('请输入一个数字')
            let b = prompt('请再输入一个数字')

            alert(a + b);
        </script>
    </body>
</html>
```

![image-20230913151952071](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913151952071.png)

> 注：
>
> - 数据类型的隐式转换是 JavaScript 的特征，后续学习中还会遇到，目前先需要理解什么是隐式转换。





### 5.2、显式转换

- 编写程序时过度依靠系统内部的隐式转换是不严禁的，因为隐式转换规律并不清晰，大多是靠经验总结的规律。为了避免因隐式转换带来的问题，通常根逻辑需要对数据进行显示转换。


**Number(数据)**

- 转成数字类型
- 如果字符串内容里有非数字，转换失败时结果为 NaN（Not a Number）即不是一个数字
- NaN也是number类型的数据，代表非数字

**parseInt(数据)**

- 只保留整数

**parseFloat(数据)**

- 可以保留小数

**转换为字符型**

- String(数据)
- 变量.toString(进制)

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 隐式转换</title>
    </head>
    <body>
        <script>
            let t = '12'
            let f = 8

            // 显式将字符串 12 转换成数值 12
            t = Number(t)

            // 检测转换后的类型
            // console.log(typeof t);
            console.log(t + f) // 结果为 20

            // 并不是所有的值都可以被转成数值类型
            let str = 'hello'
            // 将 hello 转成数值是不现实的，当无法转换成
            // 数值时，得到的结果为 NaN （Not a Number）
            console.log(Number(str))
        </script>
    </body>
</html>
```






## 6、运算符

### 6.1、算术运算符

- 数字是用来计算的，比如：乘法 * 、除法 / 、加法 + 、减法 - 等等，所以经常和算术运算符一起。

- 算术运算符：也叫数学运算符，主要包括加、减、乘、除、取余（求模）等


| 运算符 | 作用                                                 |
| ------ | ---------------------------------------------------- |
| +      | 求和                                                 |
| -      | 求差                                                 |
| *      | 求积                                                 |
| /      | 求商                                                 |
| **%**  | 取模（取余数），开发中经常用于作为某个数字是否被整除 |

> 注：
>
> - 在计算失败时，显示的结果是 NaN （not a number）

```javascript
// 算术运算符
console.log(1 + 2 * 3 / 2) //  4 
let num = 10
console.log(num + 10)  // 20
console.log(num + num)  // 20

// 1. 取模(取余数)  使用场景：  用来判断某个数是否能够被整除
console.log(4 % 2) //  0  
console.log(6 % 3) //  0
console.log(5 % 3) //  2
console.log(3 % 5) //  3

// 2. 注意事项 : 如果我们计算失败，则返回的结果是 NaN (not a number)
console.log('pink老师' - 2)
console.log('pink老师' * 2)
console.log('pink老师' + 2)   // pink老师2
```



### 6.2、赋值运算符

- 赋值运算符：对变量进行赋值的运算符

-  =  将等号右边的值赋予给左边，要求左边必须是一个容器


| 运算符 | 作用     |
| ------ | -------- |
| +=     | 加法赋值 |
| -+     | 减法赋值 |
| *=     | 乘法赋值 |
| /=     | 除法赋值 |
| %=     | 取余赋值 |

```javascript
<script>
let num = 1
// num = num + 1
// 采取赋值运算符
// num += 1
num += 3
console.log(num)
</script>
```



### 6.3、自增/自减运算符

| 符号 | 作用 | 说明                       |
| ---- | ---- | -------------------------- |
| ++   | 自增 | 变量自身的值加1，例如: x++ |
| --   | 自减 | 变量自身的值减1，例如: x-- |

- ++在前和++在后在单独使用时二者并没有差别，而且一般开发中我们都是独立使用
- ++在后（后缀式）我们会使用更多

> 注：
>
> - 只有变量能够使用自增和自减运算符
> - ++、-- 可以在变量前面也可以在变量后面，比如: x++  或者  ++x 

```javascript
<script>
    // let num = 10
    // num = num + 1
    // num += 1
    // // 1. 前置自增
    // let i = 1
    // ++i
    // console.log(i)

    // let i = 1
    // console.log(++i + 1)
    // 2. 后置自增
    // let i = 1
    // i++
    // console.log(i)
    // let i = 1
    // console.log(i++ + 1)

    // 了解 
    let i = 1
console.log(i++ + ++i + i)
</script>
```



### 6.4、比较运算符

- 使用场景：比较两个数据大小、是否相等，根据比较结果返回一个布尔值（true / false）


| 运算符 | 作用                                   |
| ------ | -------------------------------------- |
| >      | 左边是否大于右边                       |
| <      | 左边是否小于右边                       |
| >=     | 左边是否大于或等于右边                 |
| <=     | 左边是否小于或等于右边                 |
| ===    | 左右两边是否`类型`和`值`都相等（重点） |
| ==     | 左右两边`值`是否相等                   |
| !=     | 左右值不相等                           |
| !==    | 左右两边是否不全等                     |

```javascript
<script>
  console.log(3 > 5)
  console.log(3 >= 3)
  console.log(2 == 2)
  // 比较运算符有隐式转换 把'2' 转换为 2  双等号 只判断值
  console.log(2 == '2')  // true
  // console.log(undefined === null)
  // === 全等 判断 值 和 数据类型都一样才行
  // 以后判断是否相等 请用 ===  
  console.log(2 === '2')
  console.log(NaN === NaN) // NaN 不等于任何人，包括他自己
  console.log(2 !== '2')  // true  
  console.log(2 != '2') // false 
  console.log('-------------------------')
  console.log('a' < 'b') // true
  console.log('aa' < 'ab') // true
  console.log('aa' < 'aac') // true
  console.log('-------------------------')
</script>
```

- 字符串比较，是比较的字符对应的ASCII码
  - 从左往右依次比较
  - 如果第一位一样再比较第二位，以此类推
  - 比较的少，了解即可
- NaN不等于任何值，包括它本身
  - 涉及到"NaN“ 都是false
- 尽量不要比较小数，因为小数有精度问题
- 不同类型之间比较会发生隐式转换
  - 最终把数据隐式转换转成number类型再比较
  - 所以开发中，如果进行准确的比较我们更喜欢 === 或者 !==





### 6.5、逻辑运算符

- 使用场景：可以把多个布尔值放到一起运算，最终返回一个布尔值


| 符号 | 名称   | 日常读法 | 特点                       | 口诀           |
| ---- | ------ | -------- | -------------------------- | -------------- |
| &&   | 逻辑与 | 并且     | 符号两边有一个假的结果为假 | 一假则假       |
| \|\| | 逻辑或 | 或者     | 符号两边有一个真的结果为真 | 一真则真       |
| !    | 逻辑非 | 取反     | true变false  false变true   | 真变假，假变真 |

| A     | B     | A && B | A \|\| B | !A    |
| ----- | ----- | ------ | -------- | ----- |
| false | false | false  | false    | true  |
| false | true  | false  | true     | true  |
| true  | false | false  | true     | false |
| true  | true  | true   | true     | false |

```javascript
<script>
    // 逻辑与 一假则假
    console.log(true && true)
    console.log(false && true)
    console.log(3 < 5 && 3 > 2)
    console.log(3 < 5 && 3 < 2)
    console.log('-----------------')
    // 逻辑或 一真则真
    console.log(true || true)
    console.log(false || true)
    console.log(false || false)
    console.log('-----------------')
    // 逻辑非  取反
    console.log(!true)
    console.log(!false)

    console.log('-----------------')

    let num = 6
    console.log(num > 5 && num < 10)
    console.log('-----------------')
  </script>
```



### 6.6、运算符优先级

![image-20230913154506831](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913154506831.png)

- 一元运算符里面的逻辑非优先级很高
- 逻辑与比逻辑或优先级高



## 7、语句

### 7.1、表达式和语句

- 表达式：表达式是可以被求值的代码，JavaScript 引擎会将其计算出一个结果。
- 语句：语句是一段可以执行的代码。

![67101792498](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1671017924981.png)



### 7.2、分支语句

- 分支语句可以根据条件判定真假，来选择性的执行想要的代码

- 分支语句包含：
  - if分支语句（重点）
  - 三元运算符
  - switch语句

#### 7.2.1、if 分支语句

- 语法：


~~~javascript
if(条件表达式) {
    // 满足条件要执行的语句
}
~~~

- 小括号内的条件结果是布尔值，为 true 时，进入大括号里执行代码；为false，则不执行大括号里面代码

- 小括号内的结果若不是布尔类型时，会发生类型转换为布尔值，类似Boolean()

- 如果大括号只有一个语句，大括号可以省略，但是，俺们不提倡这么做~


~~~javascript
<script>
    // 单分支语句
    // if (false) {
    //   console.log('执行语句')
    // }
    // if (3 > 5) {
    //   console.log('执行语句')
    // }
    // if (2 === '2') {
    //   console.log('执行语句')
    // }
    //  1. 除了0 所有的数字都为真
    //   if (0) {
    //     console.log('执行语句')
    //   }
    // 2.除了 '' 所有的字符串都为真 true
    // if ('pink老师') {
    //   console.log('执行语句')
    // }
    // if ('') {
    //   console.log('执行语句')
    // }
    // // if ('') console.log('执行语句')

    // 1. 用户输入
    let score = +prompt('请输入成绩')
    // 2. 进行判断输出
    if (score >= 700) {
      alert('恭喜考入黑马程序员')
    }
    console.log('-----------------')

  </script>
~~~



#### 7.2.2、if双分支语句

- 如果有两个条件的时候，可以使用 if else 双分支语句


~~~javascript
if (条件表达式){
  // 满足条件要执行的语句
} else {
  // 不满足条件要执行的语句
}
~~~

- 例如：


~~~javascript
 <script>
    // 1. 用户输入
    let uname = prompt('请输入用户名:')
    let pwd = prompt('请输入密码:')
    // 2. 判断输出
    if (uname === 'pink' && pwd === '123456') {
      alert('恭喜登录成功')
    } else {
      alert('用户名或者密码错误')
    }
  </script>
~~~



#### 7.2.3、if 多分支语句

- 使用场景： 适合于有多个条件的时候


~~~javascript
 <script>
    // 1. 用户输入
    let score = +prompt('请输入成绩：')
    // 2. 判断输出
    if (score >= 90) {
      alert('成绩优秀，宝贝，你是我的骄傲')
    } else if (score >= 70) {
      alert('成绩良好，宝贝，你要加油哦~~')
    } else if (score >= 60) {
      alert('成绩及格，宝贝，你很危险~')
    } else {
      alert('成绩不及格，宝贝，我不想和你说话，我只想用鞭子和你说话~')
    }
  </script>
~~~



#### 7.2.4、三元运算符（三元表达式）

- **使用场景**： 一些简单的双分支，可以使用  三元运算符（三元表达式），写起来比 if  else双分支 更简单

- **符号**：? 与 : 配合使用

- 语法：


~~~javascript
条件 ? 表达式1 ： 表达式2
~~~

- 例如：


~~~javascript
// 三元运算符（三元表达式）
// 1. 语法格式
// 条件 ? 表达式1 : 表达式2 

// 2. 执行过程 
// 2.1 如果条件为真，则执行表达式1
// 2.2 如果条件为假，则执行表达式2

// 3. 验证
// 5 > 3 ? '真的' : '假的'
console.log(5 < 3 ? '真的' : '假的')

// let age = 18 
// age = age + 1
//  age++

// 1. 用户输入 
let num = prompt('请您输入一个数字:')
// 2. 判断输出- 小于10才补0
// num = num < 10 ? 0 + num : num
num = num >= 10 ? num : 0 + num
alert(num)
~~~



#### 7.2.4、switch语句（了解）

- 使用场景： 适合于有多个条件的时候，也属于分支语句，大部分情况下和 if多分支语句功能相同

- 注意：
  - switch case语句一般用于等值判断， if适合于区间判断
  - switchcase一般需要配合break关键字使用，没有break会造成case穿透
  - if 多分支语句开发要比switch更重要，使用也更多

- 例如：


~~~javascript
// switch分支语句
// 1. 语法
// switch (表达式) {
//   case 值1:
//     代码1
//     break

//   case 值2:
//     代码2
//     break
//   ...
//   default:
//     代码n
// }

<script>
  switch (2) {
    case 1:
    console.log('您选择的是1')
    break  // 退出switch
    case 2:
    console.log('您选择的是2')
    break  // 退出switch
    case 3:
    console.log('您选择的是3')
    break  // 退出switch
    default:
    console.log('没有符合条件的')
  }
</script>
~~~

- 释义
  - 找到跟小括号里数据**全等**的case值，并执行里面对应的代码。
  - 若没有全等 === 的则执行default里的代码。
  - 例：数据若跟值2全等，则执行代码2。

**if 多分支语句和 switch的区别：**

- 共同点
  - 都能实现多分支选择， 多选1 
  - 大部分情况下可以互换

- 区别：
  - switch…case语句通常处理case为比较**确定值**的情况，而if…else…语句更加灵活，通常用于**范围判断**(大于，等于某个范围)。
  - switch 语句进行判断后直接执行到程序的语句，效率更高，而if…else语句有几种判断条件，就得判断多少次。
  - switch 一定要注意 必须是 ===  全等，一定注意 数据类型，同时注意break否则会有穿透效果。
  - 结论：
    - 当分支比较少时，if…else语句执行效率高。
    - 当分支比较多时，switch语句执行效率高，而且结构更清晰。



#### 7.2.5、断点调试

- **作用：**学习时可以帮助更好的理解代码运行，工作时可以更快找到bug
- 浏览器打开调试界面
  - 按F12打开开发者工具
  - 点到源代码一栏 （ sources ）
  - 选择代码文件

- **断点：**在某句代码上加的标记就叫断点，当程序执行到这句有标记的代码时会暂停下来



### 7.3、循环语句

- 使用场景：重复执行 指定的一段代码，比如我们想要输出10次 '我学的很棒'


#### 7.3.1、while循环

- while :  在…. 期间， 所以 while循环 就是在满足条件期间，重复执行某些代码。
- 语法：

~~~javascript
while (条件表达式) {
   // 循环体    
}
~~~

- 释义：
  - 跟if语句很像，都要满足小括号里的条件为true才会进入循环体执行代码
  - while大括号里代码执行完毕后不会跳出，而是继续回到小括号里判断条件是否满足，若满足又执行大括号里的代码，然后再回到小括号判断条件，直到括号内条件不满足，即跳出
- 例如：

~~~javascript
// while循环: 重复执行代码

// 1. 需求: 利用循环重复打印3次 '月薪过万不是梦，毕业时候见英雄'
let i = 1
while (i <= 3) {
  document.write('月薪过万不是梦，毕业时候见英雄~<br>')
  i++   // 这里千万不要忘了变量自增否则造成死循环
}
~~~

- 循环三要素：
  - 初始值 （经常用变量）
  - 终止条件
  - 变量的变化量

- 例如：


~~~javascript
<script>
  // // 1. 变量的起始值
  // let i = 1
  // // 2. 终止条件
  // while (i <= 3) {
  //   document.write('我要循环三次 <br>')
  //   // 3. 变量的变化量
  //   i++
  // }
  // 1. 变量的起始值
  let end = +prompt('请输入次数:')
let i = 1
// 2. 终止条件
while (i <= end) {
  document.write('我要循环三次 <br>')
  // 3. 变量的变化量
  i++
}

</script>
~~~





#### 7.3.2、中止循环

- `break` ：中止整个循环，一般用于结果已经得到，后续的循环不需要的时候可以使用（提高效率）  
- `continue`：中止本次循环，一般用于排除或者跳过某一个选项的时候

~~~javascript
<script>
    // let i = 1
    // while (i <= 5) {
    //   console.log(i)
    //   if (i === 3) {
    //     break  // 退出循环
    //   }
    //   i++

    // }


    let i = 1
    while (i <= 5) {
      if (i === 3) {
        i++
        continue
      }
      console.log(i)
      i++

    }
  </script>
~~~



#### 7.3.3、无限循环

- while(true) 来构造“无限”循环，需要使用break退出循环。（常用）

- for(;;) 也可以来构造“无限”循环，同样需要使用break退出循环。


~~~javascript
// 无限循环  
// 需求： 页面会一直弹窗询问你爱我吗？
// (1). 如果用户输入的是 '爱'，则退出弹窗
// (2). 否则一直弹窗询问

// 1. while(true) 无限循环
// while (true) {
//   let love = prompt('你爱我吗?')
//   if (love === '爱') {
//     break
//   }
// }

// 2. for(;;) 无限循环
for (; ;) {
    let love = prompt('你爱我吗?')
    if (love === '爱') {
        break
    }
}
~~~



#### 7.3.4、for语句

- `for` 是 JavaScript 提供的另一种循环控制的话句，它和 `while` 只是语法上存在差异。

##### 1、for语句的基本使用

- 实现循环的 3 要素

```html
<script>
    // 1. 语法格式
    // for(起始值; 终止条件; 变化量) {
    //   // 要重复执行的代码
    // }

    // 2. 示例：在网页中输入标题标签
    // 起始值为 1
    // 变化量 i++
    // 终止条件 i <= 6
    for(let i = 1; i <= 6; i++) {
        document.write(`<h${i}>循环控制，即重复执行<h${i}>`)
    }
</script>
```

- 变化量和死循环，`for` 循环和 `while` 一样，如果不合理设置增量和终止条件，便会产生死循环。
- 跳出和终止循环

```html
<script>
    // 1. continue 
    for (let i = 1; i <= 5; i++) {
        if (i === 3) {
            continue  // 结束本次循环，继续下一次循环
        }
        console.log(i)
    }
    // 2. break
    for (let i = 1; i <= 5; i++) {
        if (i === 3) {
            break  // 退出结束整个循环
        }
        console.log(i)
    }
</script>
```

- 结论：

  - `JavaScript` 提供了多种语句来实现循环控制，但无论使用哪种语句都离不开循环的3个特征，即起始值、变化量、终止条件，做为初学者应着重体会这3个特征，不必过多纠结三种语句的区别。

  - 起始值、变化量、终止条件，由开发者根据逻辑需要进行设计，规避死循环的发生。

  - 当如果明确了循环的次数的时候推荐使用`for`循环,当不明确循环的次数的时候推荐使用`while`循环


>注：
>
>- `for` 的语法结构更简洁，故 `for` 循环的使用频次会更多。



##### 2、循环嵌套

- 利用循环的知识来对比一个简单的天文知识，我们知道地球在自转的同时也在围绕太阳公转，如果把自转和公转都看成是循环的话，就相当于是循环中又嵌套了另一个循环。


![universe](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/universe.gif)

- 实际上 JavaScript 中任何一种循环语句都支持循环的嵌套，如下代码所示：


![64791826139](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1647918261399.png)

```javascript
// 1. 外面的循环 记录第n天 
for (let i = 1; i < 4; i++) {
    document.write(`第${i}天 <br>`)
    // 2. 里层的循环记录 几个单词
    for (let j = 1; j < 6; j++) {
        document.write(`记住第${j}个单词<br>`)
    }
}
```

- 记住，外层循环循环一次，里层循环循环全部


##### 3、倒三角

~~~javascript
// 外层打印几行
for (let i = 1; i <= 5; i++) {
    // 里层打印几个星星
    for (let j = 1; j <= i; j++) {
        document.write('★')
    }
    document.write('<br>')
}
~~~

 ![64791867895](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1647918678956.png)



##### 4、九九乘法表

- 样式css


~~~css
span {
    display: inline-block;
    width: 100px;
    padding: 5px 10px;
    border: 1px solid pink;
    margin: 2px;
    border-radius: 5px;
    box-shadow: 2px 2px 2px rgba(255, 192, 203, .4);
    background-color: rgba(255, 192, 203, .1);
    text-align: center;
    color: hotpink;
}
~~~

- javascript 


~~~javascript
 // 外层打印几行
for (let i = 1; i <= 9; i++) {
    // 里层打印几个星星
    for (let j = 1; j <= i; j++) {
        // 只需要吧 ★ 换成  1 x 1 = 1   
        document.write(`
		<div> ${j} x ${i} = ${j * i} </div>
     `)
    }
    document.write('<br>')
}
~~~

![64791873467](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1647918734677.png)



## 8、综合案例-ATM存取款机



![67101878155](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/1671018781557.png)



分析：

①：提示输入框写到循环里面（无限循环）

②：用户输入4则退出循环 break

③：提前准备一个金额预先存储一个数额 money

④：根据输入不同的值，做不同的操作

​     (1)  取钱则是减法操作， 存钱则是加法操作，查看余额则是直接显示金额

​     (2) 可以使用 if else if 多分支 来执行不同的操作

完整代码：

~~~javascript
<script>
  // 1. 开始循环 输入框写到 循环里面
  // 3. 准备一个总的金额
  let money = 100
while (true) {
  let re = +prompt(`
请您选择操作：
1.存钱
2.取钱
3.查看余额
4.退出
`)
  // 2. 如果用户输入的 4 则退出循环， break  写到if 里面，没有写到switch里面， 因为4需要break退出循环
  if (re === 4) {
    break
  }
  // 4. 根据输入做操作
  switch (re) {
    case 1:
      // 存钱
      let cun = +prompt('请输入存款金额')
      money = money + cun
      break
      case 2:
      // 存钱
      let qu = +prompt('请输入取款金额')
      money = money - qu
      break
      case 3:
      // 存钱
      alert(`您的银行卡余额是${money}`)
      break
  }
}
</script>
~~~







## 9、数组

### 9.1、数组是什么

- **数组：**(Array)是一种可以按顺序保存数据的数据类型

- **使用场景：**如果有多个数据可以用数组保存起来，然后放到一个变量中，管理非常方便



### 9.2、数组的基本使用

#### 9.2.1、定义数组和数组单元

```html
<script>
  // 1. 语法，使用 [] 来定义一个空数组
  // 定义一个空数组，然后赋值给变量 classes
  // let classes = [];

  // 2. 定义非空数组
  let classes = ['小明', '小刚', '小红', '小丽', '小米']
</script>
```

- 通过 `[]` 定义数组，数据中可以存放真正的数据，如小明、小刚、小红等这些都是数组中的数据，我们这些数据称为数组单元，数组单元之间使用英文逗号分隔。




#### 92.2.、访问数组和数组索引

- 使用数组存放数据并不是最终目的，关键是能够随时的访问到数组中的数据（单元）。其实 JavaScript 为数组中的每一个数据单元都编了号，通过数据单元在数组中的编号便可以轻松访问到数组中的数据单元了。
- 我们将数据单元在数组中的编号称为索引值，也有人称其为下标。
- 索引值实际是按着数据单元在数组中的位置依次排列的，注意是从` 0` 开始的


```html
<script>
  let classes = ['小明', '小刚', '小红', '小丽', '小米']
  
  // 1. 访问数组，语法格式为：变量名[索引值]
  document.write(classes[0]) // 结果为：小明
  document.write(classes[1]) // 结果为：小刚
  document.write(classes[4]) // 结果为：小米
  
  // 2. 通过索引值还可以为数组单重新赋值
  document.write(classes[3]) // 结果为：小丽
  // 重新为索引值为 3 的单元赋值
  classes[3] = '小小丽'
  document.wirte(classes[3]); // 结果为： 小小丽
</script>
```



#### 9.2.3、数据单元值类型

- 数组做为数据的集合，它的单元值可以是任意数据类型


```html
<script>
  // 6. 数组单值类型可以是任意数据类型

  // a) 数组单元值的类型为字符类型
  let list = ['HTML', 'CSS', 'JavaScript']
  // b) 数组单元值的类型为数值类型
  let scores = [78, 84, 70, 62, 75]
  // c) 混合多种类型
  let mixin = [true, 1, false, 'hello']
</script>
```



#### 9.2.4、数组长度属性

- 重申一次，数组在 JavaScript 中并不是新的数据类型，它属于对象类型。


```html
<script>
  // 定义一个数组
  let arr = ['html', 'css', 'javascript']
  // 数组对应着一个 length 属性，它的含义是获取数组的长度
  console.log(arr.length) // 3
</script>
```



### 9.3、操作数组

- 数组做为对象数据类型，不但有 `length` 属性可以使用，还提供了许多方法：
  - push：动态向数组的尾部添加一个单元
  - unshit：动态向数组头部添加一个单元
  - pop：删除最后一个单元
  - shift：删除第一个单元
  - splice：动态删除任意单元

- 使用以上4个方法时，都是直接在原数组上进行操作，即成功调任何一个方法，原数组都跟着发生相应的改变。并且在添加或删除单元时 `length` 并不会发生错乱。


```html
<script>
    // 定义一个数组
    let arr = ['html', 'css', 'javascript']

    // 1. push 动态向数组的尾部添加一个单元
    arr.push('Nodejs')
    console.log(arr)
    arr.push('Vue')

    // 2. unshit 动态向数组头部添加一个单元
    arr.unshift('VS Code')
    console.log(arr)

    // 3. splice 动态删除任意单元
    arr.splice(2, 1) // 从索引值为2的位置开始删除1个单元
    console.log(arr)

    // 4. pop 删除最后一个单元
    arr.pop()
    console.log(arr)

    // 5. shift 删除第一个单元
    arr.shift()
    console.log(arr)
</script>
```





## 10、函数

### 10.1、声明和调用

- 函数可以把具有相同或相似逻辑的代码“包裹”起来，通过函数调用执行这些被“包裹”的代码逻辑，这么做的优势是有利于精简代码方便复用。


#### 1、声明（定义）

声明（定义）一个完整函数包括关键字、函数名、形式参数、函数体、返回值5个部分

![image-20230913171023375](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913171023375.png)

- 函数名命名规范
  - 和变量命名基本一致
  - 尽量小驼峰式命名法
  - 前缀应该为动词
  - 命名建议：常用动词约定



#### 2、调用

- 声明（定义）的函数必须调用才会真正被执行，使用 `()` 调用函数。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 声明和调用</title>
    </head>
    <body>
        <script>
            // 声明（定义）了最简单的函数，既没有形式参数，也没有返回值
            function sayHi() {
                console.log('嗨~')
            }
            // 函数调用，这些函数体内的代码逻辑会被执行
            // 函数名()

            sayHi()
            // 可以重复被调用，多少次都可以
            sayHi()
        </script>
    </body>
</html>
```

> 注：
>
> - 函数名的命名规则与变量是一致的，并且尽量保证函数名的语义。

- 小案例： 小星星


~~~javascript
<script>
        // 函数声明
        function sayHi() {
            // document.write('hai~')
            document.write(`*<br>`)
            document.write(`**<br>`)
            document.write(`***<br>`)
            document.write(`****<br>`)
            document.write(`*****<br>`)
            document.write(`******<br>`)
            document.write(`*******<br>`)
            document.write(`********<br>`)
            document.write(`*********<br>`)
        }
        // 函数调用
        sayHi()
        sayHi()
        sayHi()
        sayHi()
        sayHi()
</script>
~~~



#### 3、函数传参

- 通过向函数传递参数，可以让函数更加灵活多变，参数可以理解成是一个变量。

- 声明（定义）一个功能为打招呼的函数

  - 传入数据列表

  - 声明这个函数需要传入几个数据

  - 多个数据用逗号隔开


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 函数参数</title>
    </head>
    <body>

        <script>
            // 声明（定义）一个功能为打招呼的函数
            // function sayHi() {
            //   console.log('嗨~')
            // }
            // 调用函数
            // sayHi()


            // 这个函数似乎没有什么价值，除非能够向不同的人打招呼
            // 这就需要借助参数来实现了
            function sayHi(name) {
                // 参数 name 可以被理解成是一个变量
                console.log(name)
                console.log('嗨~' + name)
            }

            // 调用 sayHi 函数，括号中多了 '小明'
            // 这时相当于为参数 name 赋值了
            sayHi('小明')// 结果为 小明

            // 再次调用 sayHi 函数，括号中多了 '小红'
            // 这时相当于为参数 name 赋值了
            sayHi('小红') // 结果为 小红
        </script>
    </body>
</html>
```

- 总结：

  - 声明（定义）函数时的形参没有数量限制，当有多个形参时使用 `,` 分隔

  - 调用函数传递的实参要与形参的顺序一致



#### 4、形参和实参

- 形参：声明函数时写在函数名右边小括号里的叫形参（形式上的参数）

- 实参：调用函数时写在函数名右边小括号里的叫实参（实际上的参数）

- 形参可以理解为是在这个函数内声明的变量（比如 num1 = 10）实参可以理解为是给这个变量赋值

- 开发中尽量保持形参和实参个数一致


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 函数参数</title>
    </head>
    <body>
        <script>
            // 声明（定义）一个计算任意两数字和的函数
            // 形参 x 和 y 分别表示任意两个数字，它们是两个变量
            function count(x, y) {
                console.log(x + y);
            }
            // 调用函数，传入两个具体的数字做为实参
            // 此时 10 赋值给了形参 x
            // 此时 5  赋值给了形参 y
            count(10, 5); // 结果为 15
        </script>
    </body>
</html>
```

- 形参：可以看做变量，但是如果一个变量不给值，默认是什么？
  - undefined
- 但是如果做用户不输入实参，刚才的案例，则出现 undefined + undefined 结果是什么？
  - NaN
- 我们可以改进下，用户不输入实参，可以给 **形参默认值**，可以默认为 0, 这样程序更严谨，可以如下操作：

![image-20230913171650557](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913171650557.png)

- 说明：这个默认值只会在缺少实参参数传递时才会被执行，所以有参数会优先执行传递过来的实参，否则默认为undefined



#### 5、返回值

- 函数的本质是封装（包裹），函数体内的逻辑执行完毕后，函数外部如何获得函数内部的执行结果呢？要想获得函数内部逻辑的执行结果，需要通过 `return` 这个关键字，将内部执行结果传递到函数外部，这个被传递到外部的结果就是返回值。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 函数返回值</title>
    </head>
    <body>

        <script>
            // 定义求和函数
            function count(a, b) {
                let s = a + b
                // s 即为 a + b 的结果
                // 通过 return 将 s 传递到外部
                return s
            }

            // 调用函数，如果一个函数有返回值
            // 那么可将这个返回值赋值给外部的任意变量
            let total = count(5, 12)
        </script>
    </body>
</html>
```

- 总结：
  - 在函数体中使用 return 关键字能将内部的执行结果交给函数外部使用
  - 函数内部只能出现1 次 return，并且 return 下一行代码不会再被执行，所以 return 后面的数据不要换行写
  - return会立即结束当前函数
  - 函数可以没有return，这种情况默认返回值为 undefined



> 注：
>
> - 两个相同的函数后面的会覆盖前面的函数
> - 在Javascript中实参的个数和形参的个数可以不一致
>   - 如果形参过多会自动填上undefined (了解即可)
>   - 如果实参过多那么多余的实参会被忽略 (函数内部有一个arguments，里面装着所有的实参)
> - 函数一旦碰到return就不会在往下执行了 函数的结束用return





#### 6、作用域

- 通常来说，一段程序代码中所用到的名字并不总是有效和可用的，而限定这个名字的可用性的代码范围就是这个名字的作用域。

- 作用域的使用提高了程序逻辑的局部性，增强了程序的可靠性，减少了名字冲突。


![image-20230913172324083](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913172324083.png)

- 在JavaScript中，根据作用域的不同，变量可以分为：

![image-20230913172430200](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913172430200.png)

>注：
>
>- 如果函数内部，变量没有声明，直接赋值，也当全局变量看，但是强烈不推荐
>- 但是有一种情况，函数内部的形参可以看做是局部变量。



**变量的访问原则**

- 只要是代码，就至少有一个作用域
- 写在函数内部的局部作用域
- 如果函数中还有函数，那么在这个作用域中就又可以诞生一个作用域
- 访问原则：在能够访问到的情况下先局部， 局部没有在找全局



#### 7、匿名函数

- 函数可以分为具名函数和匿名函数

- 匿名函数：没有名字的函数，无法直接使用。

![image-20230913172803146](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913172803146.png)

- 函数表达式
  - 将匿名函数赋值给一个变量，并且通过变量名称进行调用 我们将这个称为**函数表达式**

~~~javascript
// 声明
let fn = function() { 
   console.log('函数表达式')
}
// 调用
fn()
~~~

- 立即执行函数

~~~javascript
(function(){ xxx  })();
(function(){xxxx}());
~~~

>注：
>
>- 无需调用，立即执行，其实本质已经调用了
>- 多个立即执行函数之间用分号隔开





## 11、对象

- 对象是 JavaScript 数据类型的一种，之前已经学习了数值类型、字符串类型、布尔类型、undefined。对象数据类型可以被理解成是一种数据集合。它由属性和方法两部分构成。

### 11.1、对象是什么

- 对象（object）：JavaScript里的一种数据类型
- 可以理解为是一种无序的数据集合， 注意数组是有序的数据集合
- 用来描述某个事物，例如描述一个人
  - 人有姓名、年龄、性别等信息、还有吃饭睡觉打代码等功能
  - 如果用多个变量保存则比较散，用对象比较统一



### 11.2、语法

- 声明一个对象类型的变量与之前声明一个数值或字符串类型的变量没有本质上的区别。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象语法</title>
    </head>
    <body>

        <script>
            // 声明字符串类型变量
            let str = 'hello world!'

            // 声明数值类型变量
            let num = 199

            // 声明对象类型变量，使用一对花括号
            // user 便是一个对象了，目前它是一个空对象
            let user = {}
            
            let person = new Object()
        </script>
    </body>
</html>
```



### 11.3、属性和访问

- 数据描述性的信息称为属性，如人的姓名、身高、年龄、性别等，一般是名词性的。
  - 属性都是成对出现的，包括属性名和值，它们之间使用英文 `:` 分隔
  - 多个属性之间使用英文 `,` 分隔
  - 属性就是依附在对象上的变量
  - 属性名可以使用 `""` 或 `''`，一般情况下省略，除非名称遇到特殊符号如空格、中横线等

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象语法</title>
    </head>
    <body>

        <script>
            // 通过对象描述一个人的数据信息
            // person 是一个对象，它包含了一个属性 name
            // 属性都是成对出现的，属性名 和 值，它们之间使用英文 : 分隔
            let person = {
                name: '小明', // 描述人的姓名
                age: 18, // 描述人的年龄
                stature: 185, // 描述人的身高
                gender: '男', // 描述人的性别
            }
        </script>
    </body>
</html>
```

- 声明对象，并添加了若干属性后，可以使用 `.` 或 `[]` 获得对象中属性对应的值，我称之为属性访问。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象语法</title>
    </head>
    <body>

        <script>
            // 通过对象描述一个人的数据信息
            // person 是一个对象，它包含了一个属性 name
            // 属性都是成对出现的，属性名 和 值，它们之间使用英文 : 分隔
            let person = {
                name: '小明', // 描述人的姓名
                age: 18, // 描述人的年龄
                stature: 185, // 描述人的身高
                gender: '男', // 描述人的性别
            };

            // 访问人的名字
            console.log(person.name) // 结果为 小明
            // 访问人性别
            console.log(person.gender) // 结果为 男
            // 访问人的身高
            console.log(person['stature']) // 结果为 185
            // 或者
            console.log(person.stature) // 结果同为 185
        </script>
    </body>
</html>
```

- 扩展：也可以动态为对象添加属性，动态添加与直接定义是一样的，只是语法上更灵活。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象语法</title>
    </head>
    <body>

        <script>
            // 声明一个空的对象（没有任何属性）
            let user = {}
            // 动态追加属性
            user.name = '小明'
            user['age'] = 18

            // 动态添加与直接定义是一样的，只是语法上更灵活
        </script>
    </body>
</html>
```

- 扩展：可以动态删除对象中的属性

~~~html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象语法</title>
    </head>
    <body>
        <script>
            let person = {
                name: '小明', // 描述人的姓名
                age: 18, // 描述人的年龄
                stature: 185, // 描述人的身高
                gender: '男', // 描述人的性别
            };
            delete person.gender // 删除 gender 属性
        </script>
    </body>
</html>
~~~

- 扩展：对于多词属性或则 - 等属性，点操作就不能用了
  - 我们可以采取： 对象[‘属性’] 方式， 单引号和双引号都阔以
  - 也可以用于其他正常属性
  - [] 语法里面的值如果不添加引号，默认会当成变量解析

![image-20230913174343209](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230913174343209.png)



### 11.4、方法和调用

- 数据行为性的信息称为方法，如跑步、唱歌等，一般是动词性的，其本质是函数。
  - 方法是由方法名和函数两部分构成，它们之间使用 : 分隔
  - 多个属性之间使用英文 `,` 分隔
  - 方法是依附在对象中的函数
  - 方法名可以使用 `""` 或 `''`，一般情况下省略，除非名称遇到特殊符号如空格、中横线等

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象方法</title>
    </head>
    <body>

        <script>
            // 方法是依附在对象上的函数
            let person = {
                name: '小红',
                age: 18,
                // 方法是由方法名和函数两部分构成，它们之间使用 : 分隔
                singing: function () {
                    console.log('两只老虎，两只老虎，跑的快，跑的快...')
                },
                run: function () {
                    console.log('我跑的非常快...')
                }
            }
        </script>
    </body>
</html>
```

- 声明对象，并添加了若干方法后，可以使用 `.` 或 `[]` 调用对象中函数，我称之为方法调用，也可以添加形参和实参。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象方法</title>
    </head>
    <body>

        <script>
            // 方法是依附在对象上的函数
            let person = {
                name: '小红',
                age: 18,
                // 方法是由方法名和函数两部分构成，它们之间使用 : 分隔
                singing: function () {
                    console.log('两只老虎，两只老虎，跑的快，跑的快...')
                },
                run: function () {
                    console.log('我跑的非常快...')
                }
            }

            // 调用对象中 singing 方法
            person.singing()
            // 调用对象中的 run 方法
            person.run()

        </script>
    </body>
</html>
```

- 扩展：也可以动态为对象添加方法，动态添加与直接定义是一样的，只是语法上更灵活。


```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>JavaScript 基础 - 对象方法</title>
    </head>
    <body>

        <script>
            // 声明一个空的对象（没有任何属性，也没有任何方法）
            let user = {}
            // 动态追加属性
            user.name = '小明'
            user.['age'] = 18

            // 动态添加方法
            user.move = function () {
                console.log('移动一点距离...')
            }

        </script>
    </body>
</html>
```

>  注：
>
> - 无论是属性或是方法，同一个对象中出现名称一样的，后面的会覆盖前面的。
> - null 也是 JavaScript 中数据类型的一种，通常只用它来表示不存在的对象。使用 typeof 检测类型它的类型时，结果为 `object`。



### 11.5、遍历对象

~~~javascript
let obj = {
    uname: 'pink'
}
for(let k in obj) {
    // k 属性名  字符串  带引号    obj.'uname'     k ===  'uname'
    // obj[k]  属性值    obj['uname']   obj[k]
    console.log(k) // 打印属性名
    console.log(obj[k]) // 打印属性值
}
~~~

- 一般不用这种方式遍历数组、主要是用来遍历对象
- for in语法中的 k 是一个变量，在循环的过程中依次代表对象的属性名
- 由于 k 是变量, 所以必须使用 [ ] 语法解析
- 一定记住： k是获得对象的属性名， 对象名[k]是获得属性值



## 12、内置对象

- 回想一下我们曾经使用过的 `console.log`，`console`其实就是 JavaScript 中内置的对象，该对象中存在一个方法叫 `log`，然后调用 `log` 这个方法，即 `console.log()`。
- 除了 `console` 对象外，JavaScritp 还有其它的内置的对象

### 12.1、Math

- `Math` 是 JavaScript 中内置的对象，称为数学对象，这个对象下即包含了属性，也包含了许多的方法。

**属性**

- Math.PI，获取圆周率

```javascript
// 圆周率
console.log(Math.PI);
```

**方法**

- Math.random，生成 0 到 1 间的随机数

```javascript
// 0 ~ 1 之间的随机数, 包含 0 不包含 1
Math.random()
```

- Math.ceil，数字向上取整

```javascript
// 舍弃小数部分，整数部分加1
Math.ceil(3.4)
```

- Math.floor，数字向下取整

```javascript
// 舍弃小数部分，整数部分不变
Math.floor(4.68)
```

- Math.round，四舍五入取整

```javascript
// 取整，四舍五入原则
Math.round(5.46539)
Math.round(4.849)
```

- Math.max，在一组数中找出最大的

```javascript
// 找出最大值
Math.max(10, 21, 7, 24, 13)
```

- Math.min，在一组数中找出最小的

```javascript
// 找出最小值
Math.min(24, 18, 6, 19, 21)
```

- Math.pow，幂方法

```javascript
// 求某个数的多少次方
Math.pow(4, 2) // 求 4 的 2 次方
Math.pow(2, 3) // 求 2 的 3 次方
```

- Math.sqrt，平方根

```javascript
// 求某数的平方根
Math.sqrt(16)
```

- 数学对象提供了比较多的方法，这里不要求强记，通过演示数学对象的使用，加深对对象的理解。









































