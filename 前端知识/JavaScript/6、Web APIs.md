## 1、Web APIs基本认知

### 1.1、介绍

严格意义上讲，我们在 JavaScript 阶段学习的知识绝大部分属于 ECMAScript 的知识体系，ECMAScript 简称 ES，它提供了一套语言标准规范，如变量、数据类型、表达式、语句、函数等语法规则都是由 ECMAScript 规定的。浏览器将 ECMAScript 大部分的规范加以实现，并且在此基础上又扩展一些实用的功能，这些被扩展出来的内容我们称为 Web APIs。

![image-20230803001139998](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230803001139998.png)

ECMAScript 运行在浏览器中然后再结合 Web APIs 才是真正的 JavaScript，Web APIs 的核心是 DOM 和 BOM。

> 扩展阅读：ECMAScript 规范在不断的更新中，存在多个不同的版本，早期的版本号采用数字顺序编号如 ECMAScript3、ECMAScript5，后来由于更新速度较快便采用年份做为版本号，如 ECMAScript2017、ECMAScript2018 这种格式，ECMAScript6 是 2015 年发布的，常叫做 EMCAScript2015。
>
> 关于 JavaScript 历史的[扩展阅读](https://javascript.ruanyifeng.com/introduction/history.html)。



### 1.2、作用与分类

作用：就是使用 JS 去操作 html 和浏览器

分类：DOM (文档对象模型)、BOM（浏览器对象模型）



### 1.3、什么是DOM

DOM（Document Object Model — 文档对象模型）是将整个 HTML 文档的每一个标签元素视为一个对象，这个对象下包含了许多的属性和方法，通过操作这些属性或者调用这些方法实现对 HTML 的动态更新，为实现网页特效以及用户交互提供技术支撑。

简而言之：DOM就是浏览器提供的一套专门用来操作网页内容的功能



### 1.4、DOM树

将 HTML 文档以树状结构直观的表现出来，我们称之为文档树或 DOM 树

描述网页内容关系的名词

作用：文档树直观的体现了标签与标签之间的关系

![image-20230803001822142](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230803001822142.png)



DOM 节点是文档树的组成部分，**每一个节点都是一个 DOM 对象**，主要分为元素节点、属性节点、文本节点等。

- 【元素节点】其实就是 HTML 标签，如上图中 `head`、`div`、`body` 等都属于元素节点。
- 【属性节点】是指 HTML 标签中的属性，如上图中 `a` 标签的 `href` 属性、`div` 标签的 `class` 属性。
- 【文本节点】是指 HTML 标签的文字内容，如 `title` 标签中的文字。
- 【根节点】特指 `html` 标签。



### 1.5、DOM对象

浏览器根据 HTML 标签生成的 JS 对象

- 所有的标签属性都可以在这个对象上面找到
- 修改这个对象的属性会自动映射到标签身上

DOM的核心思想

- 把网页内容当做对象来处理

document 对象

- 是DOM里提供的一个对象
- 所以它提供的属性和方法都是用来访问和操作网页内容的
  - 例：document.write()
- 网页所有内容都在document里面



## 2、获取DOM对象

### 2.1、根据CSS选择器来获取DOM元素（重点）

#### 2.1.1、选择匹配的第一个元素

语法：

~~~html
<body>
    <div id="nav" class="box">nihao</div>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
    <script>
        // 获取第一个元素
        const firstDiv = document.querySelector('div')
        // const firstDiv = document.querySelector('.box')
        // const firstDiv = document.querySelector('#nav')
        console.dir(firstDiv) // 新版Chrome浏览器使用log无法打印对象
        const firstLi = document.querySelector('ul li:first-child')
        console.dir(firstLi)
    </script>
</body>
~~~

参数：

- 包含一个或多个有效的CSS选择器（字符串）

返回值：

- CSS选择器匹配的第一个元素，一个 HTMLElement对象
- 如果没有匹配到，则返回null



#### 2.1.2、选择匹配的多个元素

语法：

~~~html
<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>2</li>
    </ul>
    <script>
        const lis = document.querySelectorAll('ul li')
        console.dir(lis)
    </script>
</body>
~~~

参数：

- 包含一个或多个有效的CSS选择器（字符串）

返回值

- CSS选择器匹配的NodeList
- 得到的是一个伪数组
  - 有长度、有索引号的数组
  - 但是没有 pop()、push() 等数组方法

- 想要得到里面的每一个对象，则需要遍历（for）的方式获得

> 注：
>
> 哪怕只有一个元素，通过querySelectAll() 获取过来的也是一个**伪数组**，里面只有一个元素而已



## 3、操作元素内容

### 3.1、元素 innerText 属性

- 将文本内容添加/更新到任意标签位置
- 显示纯文本，不解析标签

~~~html
<body>
    <div class="box">我是内容</div>
    <script>
        const box = document.querySelector('.box')
        box.innerText = "我是一个盒子"
    </script>
</body>
~~~



### 3.2、元素 innerHTML属性

- 将文本内容添加/更新到任意标签位置
- 会解析标签，多标签建议使用模板字符

~~~html
<body>
    <div class="box">我是内容</div>
    <script>
        const box = document.querySelector('.box')
        // box.innerText = "我是一个盒子"
        box.innerHTML = "<strong>我是一个盒子</strong>"
    </script>
</body>
~~~



## 4、操作元素属性

### 4.1、操作元素常用属性

- 还可以通过 JS 设置/修改标签元素属性，比如通过 src 更换图片
- 最常见的属性比如： href、title、src 等
- **语法：**对象.属性

~~~html
<body>
    <img src="./images/1.webp"></img>
<script>
    function getRandom(M, N) {
        return Math.floor(Math.random() * (M - N + 1)) + N
    }
    const random = getRandom(1, 6)
    console.dir(random)
    const img = document.querySelector('img')
    img.src = `./images/${random}.webp`
</script>
</body>
~~~



### 4.2、操作元素样式属性

还可以通过 JS 设置/修改标签元素的样式属性

- 比如通过轮播图小圆点自动更换颜色样式
- 点击按钮可以滚动图片，这是移动的图片的位置 left 等等



#### 4.2.1、通过 style 属性操作 CSS

语法：对象.style.属性 = 值

举例：

~~~html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .box {
      width: 200px;
      height: 300px;
      background-color: pink;
    }
  </style>
</head>

<body>
  <div class="box"></div>
  <script>
    const box = document.querySelector('.box')
    box.style.width = '400px'
    box.style.height = '600px'
    box.style.border = '2px solid blue'
    box.style.borderTop = '2px solid red'
  </script>
</body>
~~~

> 注：
>
> - 修改样式通过style属性引出
> - 如果属性有-连接符，需要转换为小驼峰命名法
> - 赋值的时候，需要的时候不要忘记加css单位



#### 4.2.2、通过类名（className）操作CSS

- 如果修改的样式比较多，直接通过style属性修改比较繁琐，我们可以通过借助于css类名的形式
- 语法：

~~~html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box {
            width: 300px;
            height: 300px;
            background-color: blue;
            border: 2px solid red;
            margin: 100px auto;
            padding: 10px;
        }
    </style>
</head>
<body>
    <div></div>
    <script>
        const div = document.querySelector('div')
        div.className = 'box'
    </script>
</body>
~~~

> 注：
>
> - 由于class是关键字, 所以使用className去代替
> - className是使用新值换旧值, 如果需要添加一个类名样式，需要保留之前的类名样式，写法如下：
>
> ~~~html
> <body>
>     <div class="nav"></div>
>     <script>
>         const div = document.querySelector('div')
>         div.className = 'nav box'
>     </script>
> </body>
> ~~~



#### 4.2.3、通过classList操作CSS

- 为了解决className 容易覆盖以前的类名，我们可以通过classList方式追加和删除类名
- 语法：

~~~html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box {
            width: 200px;
            height: 200px;
            color: black;
        }
        .active {
            width: 200px;
            height: 200px;
            background-color: red;
        }
    </style>
</head>

<body>
    <div class="box">文字</div>
    <script>
        const div = document.querySelector('.box')
        // 添加
        div.classList.add('active')
        // 移除
        div.classList.remove('box')
        // 切换 -- 有就不加，没有就加
        div.classList.toggle('box')
    </script>
</body>
~~~



### 4.3、操作表单元素属性

- 表单很多情况，也需要修改属性，比如点击眼睛，可以看到密码，本质是把表单类型转换为文本框
- 正常的有属性有取值的，跟其他的标签属性没有任何区别
- 表单属性中添加就有效果，移除就没有效果，一律使用布尔值表示，如果为 true 代表添加了该属性，如果是 false 代表移除了该属性
- 比如： disabled、checked、selected

~~~html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
        <input type="text" value="请输入">
        <button disabled>按钮</button>
        <input type="checkbox" name="" id="" class="agree">
        <script>
            // 1. 获取元素
            let input = document.querySelector('input')
            // 2. 取值或者设置值  得到input里面的值可以用 value
            // console.log(input.value)
            input.value = '小米手机'
            input.type = 'password'

            // 2. 启用按钮
            let btn = document.querySelector('button')
            // disabled 不可用   =  false  这样可以让按钮启用
            btn.disabled = false
            // 3. 勾选复选框
            let checkbox = document.querySelector('.agree')
            checkbox.checked = false
        </script>
    </body>
</html>
~~~



### 4.4、自定义属性

- **标准属性:** 标签天生自带的属性，比如：class、id、title 等，可以直接使用点语法操作，比如： disabled、checked、selected
- 自定义属性：
  - 在html5中推出来了专门的data-自定义属性
  - 在标签上一律以data-开头
  - 在 DOM 对象上一律以 dataset 对象方式获取

~~~html
<body>
    <div data-id="1"></div>
    <script>
        const div = document.querySelector('div')
        console.dir(div.dataset.id)
    </script>
</body>
~~~

​	

## 5、定时器-间歇函数





















































