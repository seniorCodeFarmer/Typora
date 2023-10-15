## 1、Web APIs基本认知

### 1.1、介绍

- 严格意义上讲，我们在 JavaScript 阶段学习的知识绝大部分属于 ECMAScript 的知识体系，ECMAScript 简称 ES，它提供了一套语言标准规范，如变量、数据类型、表达式、语句、函数等语法规则都是由 ECMAScript 规定的。浏览器将 ECMAScript 大部分的规范加以实现，并且在此基础上又扩展一些实用的功能，这些被扩展出来的内容我们称为 Web APIs。


![image-20230803001139998](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230803001139998.png)

- ECMAScript 运行在浏览器中然后再结合 Web APIs 才是真正的 JavaScript，Web APIs 的核心是 DOM 和 BOM。


> - 扩展阅读：ECMAScript 规范在不断的更新中，存在多个不同的版本，早期的版本号采用数字顺序编号如 ECMAScript3、ECMAScript5，后来由于更新速度较快便采用年份做为版本号，如 ECMAScript2017、ECMAScript2018 这种格式，ECMAScript6 是 2015 年发布的，常叫做 EMCAScript2015。
>
> - 关于 JavaScript 历史的[扩展阅读](https://javascript.ruanyifeng.com/introduction/history.html)。



### 1.2、作用与分类

- 作用：就是使用 JS 去操作 html 和浏览器

- 分类：DOM (文档对象模型)、BOM（浏览器对象模型）




### 1.3、什么是DOM

- DOM（Document Object Model — 文档对象模型）是将整个 HTML 文档的每一个标签元素视为一个对象，这个对象下包含了许多的属性和方法，通过操作这些属性或者调用这些方法实现对 HTML 的动态更新，为实现网页特效以及用户交互提供技术支撑。

- 简而言之：DOM就是浏览器提供的一套专门用来操作网页内容的功能




### 1.4、DOM树

- 将 HTML 文档以树状结构直观的表现出来，我们称之为文档树或 DOM 树

- 描述网页内容关系的名词

- 作用：文档树直观的体现了标签与标签之间的关系


![image-20230803001822142](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230803001822142.png)

- DOM 节点是文档树的组成部分，**每一个节点都是一个 DOM 对象**，主要分为元素节点、属性节点、文本节点等。
  - 【元素节点】其实就是 HTML 标签，如上图中 `head`、`div`、`body` 等都属于元素节点。
  - 【属性节点】是指 HTML 标签中的属性，如上图中 `a` 标签的 `href` 属性、`div` 标签的 `class` 属性。
  - 【文本节点】是指 HTML 标签的文字内容，如 `title` 标签中的文字。
  - 【根节点】特指 `html` 标签。



### 1.5、DOM对象

- 浏览器根据 HTML 标签生成的 JS 对象

  - 所有的标签属性都可以在这个对象上面找到

  - 修改这个对象的属性会自动映射到标签身上


- DOM的核心思想

  - 把网页内容当做对象来处理


- document 对象

  - 是DOM里提供的一个对象

  - 所以它提供的属性和方法都是用来访问和操作网页内容的
    - 例：document.write()

  - 网页所有内容都在document里面



## 2、获取DOM对象

### 2.1、根据CSS选择器来获取DOM元素（重点）

#### 2.1.1、选择匹配的第一个元素

- 语法：


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

- 参数：

  - 包含一个或多个有效的CSS选择器（字符串）


- 返回值：

  - CSS选择器匹配的第一个元素，一个 HTMLElement对象

  - 如果没有匹配到，则返回null




#### 2.1.2、选择匹配的多个元素

- 语法：


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

- 参数：

  - 包含一个或多个有效的CSS选择器（字符串）


- 返回值

  - CSS选择器匹配的NodeList

  - 得到的是一个伪数组
    - 有长度、有索引号的数组
    - 但是没有 pop()、push() 等数组方法


  - 想要得到里面的每一个对象，则需要遍历（for）的方式获得


> 注：
>
> 哪怕只有一个元素，通过querySelectAll() 获取过来的也是一个伪数组，里面只有一个元素而已



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

- 还可以通过 JS 设置/修改标签元素的样式属性

  - 比如通过轮播图小圆点自动更换颜色样式

  - 点击按钮可以滚动图片，这是移动的图片的位置 left 等等




#### 4.2.1、通过 style 属性操作 CSS

- 语法：对象.style.属性 = 值

- 举例：


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
> - className是使用新值换旧值，如果需要添加一个类名样式，需要保留之前的类名样式，写法如下：
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

- 为了解决 className 容易覆盖以前的类名，我们可以通过classList方式追加和删除类名
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

- 标准属性：签天生自带的属性，比如：class、id、title 等，可以直接使用点语法操作，比如： disabled、checked、selected
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

- 定时器函数可以开启和关闭定时器

### 5.1、开启定时器

~~~javascript
setInterval(函数,间隔时间)
~~~

- 作用：每隔一段时间调用这个函数
- 间隔时间单位是毫秒

~~~javascript
function repeat() {
    console.log('你好')
}
// 每隔一秒调用repeat函数
setInterval(repeat,1000)
~~~

> 注：
>
> - 函数名字不需要加括号
> - 定时器返回的是一个 id 数字



### 5.2、关闭定时器

~~~javascript
let 变量名 = setInterval(函数,间隔时间)
clearInterval(变量名)
~~~

- 一般不会刚创建就停止，而是满足一定条件再停止

~~~javascript
let timer = setInterval(function() {
    console.log('hi~~~')
},1000)
clearInterval(timer)
~~~



### 5.3、阅读注册协议

~~~html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
        <button class="btn" disabled>我已经阅读用户协议(60)</button>
        <script>
            const btn = document.querySelector(".btn")
            let i = 5
            function fn() {
                btn.innerHTML = `我已经阅读用户协议(${i})`
                i--
                if (i === 0) {
                    btn.innerHTML = '同意'
                    btn.disabled = false
                    clearInterval(n)
                }
            }
            let n = setInterval(fn, 1000)
        </script>
    </body>
</html>
~~~



### 5.4、轮播图定时器版

~~~html
<body>
    <div class="slider">
        <div class="slider-wrapper">
            <img src="./images/slider01.jpg" alt="" />
        </div>
        <div class="slider-footer">
            <p>对人类来说会不会太超前了？</p>
            <ul class="slider-indicator">
                <li class="active"></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
            </ul>
            <div class="toggle">
                <button class="prev">&lt;</button>
                <button class="next">&gt;</button>
            </div>
        </div>
    </div>

    <script>
        // 1. 初始数据
        const sliderData = [
            { url: './images/slider01.jpg', title: '对人类来说会不会太超前了？', color: 'rgb(100, 67, 68)' },
            { url: './images/slider02.jpg', title: '开启剑与雪的黑暗传说！', color: 'rgb(43, 35, 26)' },
            { url: './images/slider03.jpg', title: '真正的jo厨出现了！', color: 'rgb(36, 31, 33)' },
            { url: './images/slider04.jpg', title: '李玉刚：让世界通过B站看到东方大国文化', color: 'rgb(139, 98, 66)' },
            { url: './images/slider05.jpg', title: '快来分享你的寒假日常吧~', color: 'rgb(67, 90, 92)' },
            { url: './images/slider06.jpg', title: '哔哩哔哩小年YEAH', color: 'rgb(166, 131, 143)' },
            { url: './images/slider07.jpg', title: '一站式解决你的电脑配置问题！！！', color: 'rgb(53, 29, 25)' },
            { url: './images/slider08.jpg', title: '谁不想和小猫咪贴贴呢！', color: 'rgb(99, 72, 114)' },
        ]

        const img = document.querySelector('.slider-wrapper img')
        const p = document.querySelector('.slider-footer p')
        let i = 0
        let n = setInterval(function () {
            i++
            if (i >= sliderData.length) {
                i = 0
            }
            // 更换图片路径
            img.src = sliderData[i].url
            // 更换文字内容
            p.innerHTML = sliderData[i].title
            // 移除其他圆点
            document.querySelector('.slider-indicator .active').classList.remove('active')
            // 填充圆点
            document.querySelector(`.slider-indicator li:nth-child(${i + 1})`).classList.add('active')
        }, 1000);

    </script>
</body>
~~~



## 6、事件

### 6.1、事件监听（绑定）

#### 6.1.1、什么是事件？

- 事件是在编程时系统内发生的**动作**或者发生的事情
- 比如用户在网页上**单击**一个按钮

#### 6.1.2、什么是事件监听？

- 就是让程序检测是否有事件产生，一旦有事件触发，就立即调用一个函数做出响应，也称为 绑定事件或者注册事件
- 比如鼠标经过显示下拉菜单，比如点击可以播放轮播图等等

#### 6.1.3、语法：

~~~html
元素对象.addEventListener('事件类型',要执行的函数)
~~~

- 事件监听三要素：
  - 事件源：那个dom元素被事件触发了，要获取dom元素
  - 事件类型：用什么方式触发，比如鼠标单击 click、鼠标经过 mouseover 等
  - 事件调用的函数：要做什么事



#### 随机点名案例

~~~html
<body>
    <h2>随机点名</h2>
    <div class="box">
        <span>名字是：</span>
        <div class="qs">这里显示姓名</div>
    </div>
    <div class="btns">
        <button class="start">开始</button>
        <button class="end">结束</button>
    </div>

    <script>
        // 数据数组
        const arr = ['马超', '黄忠', '赵云', '关羽', '张飞']
        // 获取qs
        const qs = document.querySelector(".qs")
        // 获取开始按钮
        const startbtn = document.querySelector('.btns .start')
        // 获取结束按钮
        const stopbtn = document.querySelector('.btns .end')
        let timerId = 0
        let random = 0
        startbtn.addEventListener('click', function () {
            timerId = setInterval(function () {
                // 随机数
                random = Math.floor(Math.random() * arr.length)
                qs.innerHTML = arr[random]
            }, 10)

            if (arr.length == 1) {
                startbtn.disabled = true
                stopbtn.disabled = true
            }
        })
        stopbtn.addEventListener('click', function () {
            clearInterval(timerId)
            // 点击结束按钮后，可以删除选取的数组元素
            arr.splice(random, 1)
        })

    </script>
</body>
~~~



#### 6.1.4、事件监听版本

- 发展史
  - DOM L0 ：是 DOM 的发展的第一个版本； L：level
  - DOM L1：DOM级别1 于1998年10月1日成为W3C推荐标准
  - DOM L2：使用addEventListener注册事件
  - DOM L3： DOM3级事件模块在DOM2级事件的基础上重新定义了这些事件，也添加了一些新事件类型



### 6.2、事件类型

![image-20230915101338332](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230915101338332.png)

#### 轮播图点击切换-鼠标事件

~~~html
<body>
    <div class="slider">
        <div class="slider-wrapper">
            <img src="./images/slider01.jpg" alt="" />
        </div>
        <div class="slider-footer">
            <p>对人类来说会不会太超前了？</p>
            <ul class="slider-indicator">
                <li class="active"></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
            </ul>
            <div class="toggle">
                <button class="prev">&lt;</button>
                <button class="next">&gt;</button>
            </div>
        </div>
    </div>
    <script>
        // 1. 初始数据
        const data = [
            { url: './images/slider01.jpg', title: '对人类来说会不会太超前了？', color: 'rgb(100, 67, 68)' },
            { url: './images/slider02.jpg', title: '开启剑与雪的黑暗传说！', color: 'rgb(43, 35, 26)' },
            { url: './images/slider03.jpg', title: '真正的jo厨出现了！', color: 'rgb(36, 31, 33)' },
            { url: './images/slider04.jpg', title: '李玉刚：让世界通过B站看到东方大国文化', color: 'rgb(139, 98, 66)' },
            { url: './images/slider05.jpg', title: '快来分享你的寒假日常吧~', color: 'rgb(67, 90, 92)' },
            { url: './images/slider06.jpg', title: '哔哩哔哩小年YEAH', color: 'rgb(166, 131, 143)' },
            { url: './images/slider07.jpg', title: '一站式解决你的电脑配置问题！！！', color: 'rgb(53, 29, 25)' },
            { url: './images/slider08.jpg', title: '谁不想和小猫咪贴贴呢！', color: 'rgb(99, 72, 114)' },
        ]
        // 获取元素
        const img = document.querySelector('.slider-wrapper img')
        const p = document.querySelector('.slider-footer p')
        const footer = document.querySelector('.slider-footer')
        // 1. 右按钮业务
        // 1.1 获取右侧按钮 
        const next = document.querySelector('.next')
        let i = 0  // 信号量 控制播放图片张数
        // 1.2 注册点击事件

        next.addEventListener('click', function () {
            // console.log(11)
            i++
            // 1.6判断条件  如果大于8 就复原为 0
            // if (i >= 8) {
            //   i = 0
            // }
            i = i >= data.length ? 0 : i
            // 1.3 得到对应的对象
            // console.log(data[i])
            // 调用函数
            toggle()
        })

        // 2. 左侧按钮业务
        // 2.1 获取左侧按钮 
        const prev = document.querySelector('.prev')
        // 1.2 注册点击事件
        prev.addEventListener('click', function () {
            i--
            // 判断条件  如果小于0  则爬到最后一张图片索引号是 7
            // if (i < 0) {
            //   i = 7
            // }
            i = i < 0 ? data.length - 1 : i
            // 1.3 得到对应的对象
            // console.log(data[i])
            // 调用函数
            toggle()
        })

        // 声明一个渲染的函数作为复用
        function toggle() {
            // 1.4 渲染对应的数据
            img.src = data[i].url
            p.innerHTML = data[i].title
            footer.style.backgroundColor = data[i].color
            // 1.5 更换小圆点    先移除原来的类名， 当前li再添加 这个 类名
            document.querySelector('.slider-indicator .active').classList.remove('active')
            document.querySelector(`.slider-indicator li:nth-child(${i + 1})`).classList.add('active')
        }



        // 3. 自动播放模块
        let timerId = setInterval(function () {
            // 利用js自动调用点击事件  click()  一定加小括号调用函数
            next.click()
        }, 1000)


        // 4. 鼠标经过大盒子，停止定时器
        const slider = document.querySelector('.slider')
        // 注册事件
        slider.addEventListener('mouseenter', function () {
            // 停止定时器
            clearInterval(timerId)
        })

        // 5. 鼠标离开大盒子，开启定时器
        // 注册事件
        slider.addEventListener('mouseleave', function () {
            // 停止定时器
            if (timerId) clearInterval(timerId)
            // 开启定时器
            timerId = setInterval(function () {
                // 利用js自动调用点击事件  click()  一定加小括号调用函数
                next.click()
            }, 1000)
        })
    </script>
</body>
~~~





#### 小米搜索框案例-焦点事件

~~~html
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        .mi .search {
            border: 1px solid #ff6700;
        }
    </style>
</head>

<body>
    <div class="mi">
        <input type="search" placeholder="小米笔记本">
        <ul class="result-list">
            <li><a href="#">全部商品</a></li>
            <li><a href="#">小米11</a></li>
            <li><a href="#">小米10S</a></li>
            <li><a href="#">小米笔记本</a></li>
            <li><a href="#">小米手机</a></li>
            <li><a href="#">黑鲨4</a></li>
            <li><a href="#">空调</a></li>
        </ul>
    </div>
    <script>

        // 1.获取元素
        const input = document.querySelector('[type=search]')
        const ul = document.querySelector('.result-list')
        // 2.添加事件
        input.addEventListener('focus', function () {
            input.classList.add('search')
            ul.style.display = 'block'
        })
        input.addEventListener('blur', function () {
            input.classList.remove('search')
            ul.style.display = 'none'
        })

    </script>
</body>
~~~



#### 评论字数统计-键盘、文本输入事件

~~~html
<body>
    <div class="wrapper">
        <i class="avatar"></i>
        <textarea id="tx" placeholder="发一条友善的评论" rows="2" maxlength="200"></textarea>
        <button>发布</button>
    </div>
    <div class="wrapper">
        <span class="total">0/200字</span>
    </div>
    <div class="list">
        <div class="item" style="display: none;">
            <i class="avatar"></i>
            <div class="info">
                <p class="name">清风徐来</p>
                <p class="text">大家都辛苦啦，感谢各位大大的努力，能圆满完成真是太好了[笑哭][支持]</p>
                <p class="time">2022-10-10 20:29:21</p>
            </div>
        </div>
    </div>

    <script>
        const tx = document.querySelector('#tx')
        const total = document.querySelector('.total')
        tx.addEventListener('focus', function () {
            total.style.opacity = 1
        })
        tx.addEventListener('blur', function () {
            total.style.opacity = 0
        })
        tx.addEventListener('input', function () {
            total.innerHTML = `${tx.value.length}/200`
        })
    </script>
</body>
~~~





### 6.3、事件对象

#### 6.3.1、获取事件对象

- 事件对象是什么
  - 也是个对象，这个对象里有事件触发时的相关信息
  - 例如：鼠标点击事件中，事件对象就存了鼠标点在哪个位置等信息
- 使用场景
  - 可以判断用户按下哪个键，比如按下回车键可以发布新闻
  - 可以判断鼠标点击了哪个元素，从而做相应的操作

- 语法：
  - 在事件绑定的回调函数的第一个参数就是事件对象
  - 一般命名为event、ev、e

![image-20230917121537134](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230917121537134.png)



#### 6.3.2、事件对象常用属性

- type：获取当前的事件类型
- clientX/clientY：获取光标相对于浏览器可见窗口左上角的位置
- offsetX/offsetY：获取光标相对于当前DOM元素左上角的位置
- key：用户按下的键盘键的值



#### 评论回车发布

- 需求：按下回车键盘，可以发布信息
- 分析：
  - 用到按下键盘事件 keydown 或者 keyup 都可以
  - 如果用户按下的是回车键盘，则发布信息
  - 让留言信息模块显示，把拿到的数据渲染到对应标签内部

~~~html
<body>
    <div class="wrapper">
        <i class="avatar"></i>
        <textarea id="tx" placeholder="发一条友善的评论" rows="2" maxlength="200"></textarea>
        <button>发布</button>
    </div>
    <div class="wrapper">
        <span class="total">0/200字</span>
    </div>
    <div class="list">
        <div class="item" style="display: none;">
            <i class="avatar"></i>
            <div class="info">
                <p class="name">清风徐来</p>
                <p class="text">大家都辛苦啦，感谢各位大大的努力，能圆满完成真是太好了[笑哭][支持]</p>
                <p class="time">2022-10-10 20:29:21</p>
            </div>
        </div>
    </div>
    <script>
        const tx = document.querySelector('#tx')
        const total = document.querySelector('.total')
        const item = document.querySelector('.item')
        const text = document.querySelector('.text')
        // 1. 当我们文本域获得了焦点，就让 total 显示出来
        tx.addEventListener('focus', function () {
            total.style.opacity = 1
        })
        // 2. 当我们文本域失去了焦点，就让 total 隐藏出来
        tx.addEventListener('blur', function () {
            total.style.opacity = 0
        })
        // 3. 检测用户输入
        tx.addEventListener('input', function () {
            // console.log(tx.value.length)  得到输入的长度
            total.innerHTML = `${tx.value.length}/200字`
        })

        // 4. 按下回车发布评论
        tx.addEventListener('keyup', function (e) {
            // 只有按下的是回车键，才会触发
            // console.log(e.key)
            if (e.key === 'Enter') {
                // 如果用户输入的不为空就显示和打印
                if (tx.value.trim()) {
                    // console.log(11)
                    item.style.display = 'block'
                    // console.log(tx.value)  // 用户输入的内容
                    text.innerHTML = tx.value

                }
                // 等我们按下回车，结束，清空文本域
                tx.value = ''
                // 按下回车之后，就要把 字符统计 复原
                total.innerHTML = '0/200字'
            }
        })
    </script>
</body>
~~~





### 6.4、环境对象

- **环境对象：**指的是函数内部特殊的**变量 this** ，它代表着当前函数运行时所处的环境
- **作用：**弄清楚this的指向，可以让我们代码更简洁
  - 函数的调用方式不同，this 指代的对象也不同
  - 【谁调用， this 就是谁】 是判断 this 指向的粗略规则
  - 直接调用函数，其实相当于是 window.函数，所以 this 指代 window





### 6.5、回调函数

- 如果将函数 A 做为参数传递给函数 B 时，我们称函数 A 为回调函数
- 简单理解： 当一个函数当做参数来传递给另外一个函数的时候，这个函数就是回调函数
- 常见的使用场景：使用匿名函数作为回调函数比较常见

![image-20230917150419167](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230917150419167.png)



#### Tab栏切换

- 需求：鼠标经过不同的选项卡，底部可以显示 不同的内容
- 分析：
  - 主要核心是类的切换， 设定一个当前类，可以让当前元素高亮
  - 鼠标经过当前选项卡，先移除其余元素身上的当前类，而只给当前元素添加类
  - 注意，当前类只能有一个

![image-20230917151911353](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230917151911353.png)

~~~html
<body>
    <div class="tab">
        <div class="tab-nav">
            <h3>每日特价</h3>
            <ul>
                <li><a class="active" href="javascript:;">精选</a></li>
                <li><a href="javascript:;">美食</a></li>
                <li><a href="javascript:;">百货</a></li>
                <li><a href="javascript:;">个护</a></li>
                <li><a href="javascript:;">预告</a></li>
            </ul>
        </div>
        <div class="tab-content">
            <div class="item active"><img src="./images/tab00.png" alt="" /></div>
            <div class="item"><img src="./images/tab01.png" alt="" /></div>
            <div class="item"><img src="./images/tab02.png" alt="" /></div>
            <div class="item"><img src="./images/tab03.png" alt="" /></div>
            <div class="item"><img src="./images/tab04.png" alt="" /></div>
        </div>
    </div>
    <script>
        // 1. a 模块制作 要给 5个链接绑定鼠标经过事件
        // 1.1 获取 a 元素 
        const as = document.querySelectorAll('.tab-nav a')
        // console.log(as) 
        for (let i = 0; i < as.length; i++) {
            // console.log(as[i])
            // 要给 5个链接绑定鼠标经过事件
            as[i].addEventListener('mouseenter', function () {
                // console.log('鼠标经过')
                // 排他思想  
                // 干掉别人 移除类active
                document.querySelector('.tab-nav .active').classList.remove('active')
                // 我登基 我添加类 active  this 当前的那个 a 
                this.classList.add('active')

                // 下面5个大盒子 一一对应  .item 
                // 干掉别人
                document.querySelector('.tab-content .active').classList.remove('active')
                // 对应序号的那个 item 显示 添加 active 类
                document.querySelector(`.tab-content .item:nth-child(${i + 1})`).classList.add('active')

            })
        }
    </script>
</body>
~~~





### 全选文本框

- 需求：用户点击全选，则下面复选框全部选择，取消全选则全部取消
- 分析：
  - 全选复选框点击，可以得到当前按钮的 checked
  - 把下面所有的小复选框状态checked，改为和全选复选框一致
  - 遍历下面的所有的checkbox,添加点击事件
  - 检查小复选框选中的个数，是不是等于 小复选框总的个数
  - 把结果给全选按钮
  - 利用css 复选框选择器 input:checked

~~~html
<body>
    <table>
        <tr>
            <th class="allCheck">
                <input type="checkbox" name="" id="checkAll"> <span class="all">全选</span>
            </th>
            <th>商品</th>
            <th>商家</th>
            <th>价格</th>
        </tr>
        <tr>
            <td>
                <input type="checkbox" name="check" class="ck">
            </td>
            <td>小米手机</td>
            <td>小米</td>
            <td>￥1999</td>
        </tr>
        <tr>
            <td>
                <input type="checkbox" name="check" class="ck">
            </td>
            <td>小米净水器</td>
            <td>小米</td>
            <td>￥4999</td>
        </tr>
        <tr>
            <td>
                <input type="checkbox" name="check" class="ck">
            </td>
            <td>小米电视</td>
            <td>小米</td>
            <td>￥5999</td>
        </tr>
    </table>
    
    <script>
        // 1. 获取大复选框
        const checkAll = document.querySelector('#checkAll')
        // 2. 获取所有的小复选框
        const cks = document.querySelectorAll('.ck')
        // 3. 点击大复选框  注册事件
        checkAll.addEventListener('click', function () {
            // 得到当前大复选框的选中状态
            // console.log(checkAll.checked)  // 得到 是 true 或者是 false
            // 4. 遍历所有的小复选框 让小复选框的checked  =  大复选框的 checked
            for (let i = 0; i < cks.length; i++) {
                cks[i].checked = this.checked
            }
        })
        // 5. 小复选框控制大复选框	
        for (let i = 0; i < cks.length; i++) {
            // 5.1 给所有的小复选框添加点击事件
            cks[i].addEventListener('click', function () {
                // 判断选中的小复选框个数 是不是等于  总的小复选框个数
                // 一定要写到点击里面，因为每次要获得最新的个数
                // console.log(document.querySelectorAll('.ck:checked').length)
                // console.log(document.querySelectorAll('.ck:checked').length === cks.length)
                checkAll.checked = document.querySelectorAll('.ck:checked').length === cks.length
            })
        }
    </script>
</body>
~~~





## 7、事件流

### 7.1、事件流与两个阶段说明

- 事件流指的是事件完整执行过程中的流动路径


![image-20230925203827215](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230925203827215.png)

- 说明：假设页面里有个div，当触发事件时，会经历两个阶段，分别是捕获阶段、冒泡阶段。
- 简单来说：捕获阶段是`从父到子`冒泡阶段是`从子到父`。
- 实际工作都是使用事件冒泡为主。



### 7.2、事件捕获

- 从DOM的根元素开始去执行对应的事件 (从外到里)
- 事件捕获需要写对应代码才能看到效果
- 代码：

![image-20230926000548637](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926000548637.png)

- 说明：
  - addEventListener第三个参数传入 true 代表是捕获阶段触发（很少使用）
  - 若传入false代表冒泡阶段触发，默认就是false
  - 若是用 L0 事件监听，则只有冒泡阶段，没有捕获





### 7.3、事件冒泡

- 当一个元素的事件被触发时，同样的事件将会在该元素的所有祖先元素中依次被触发。这一过程被称为事件冒泡。
- 简单理解：当一个元素触发事件后，会依次向上调用所有父级元素的同名事件。
- 事件冒泡是默认存在的。
- L2事件监听第三个参数是 false，或者默认都是冒泡。

![image-20230926000721119](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926000721119.png)





### 7.4、阻止冒泡

#### 7.4.1、阻止冒泡

- 问题：因为默认就有冒泡模式的存在，所以容易导致事件影响到父级元素。
- 需求：若想把事件就限制在当前元素内，就需要阻止事件冒泡。
- 前提：阻止事件冒泡需要拿到事件对象。
- 语法：

![image-20230926001330937](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926001330937.png)

![image-20230926001339130](https://zcw-typora.oss-cn-nanjing.aliyuncs.com/image-20230926001339130.png)

- 注意：此方法可以阻断事件流动传播，不光在冒泡阶段有效，捕获阶段也有效。



#### 7.4.2、阻止默认行为

- 我们某些情况下需要阻止默认行为的发生，比如阻止链接的跳转，表单域的跳转
- 语法：

~~~javascript
e.preventDefault()
~~~

~~~html 
<form action="http://www.baidu.com">
    <input type="submit" value="提交">
</form>
<script>
    const form = document.querySelector('form')
    form.addEventListener('click',function(e) {
        // 阻止表单默认提交行为
        e.preventDefault()
    })
</script>
~~~



### 7.5、解绑事件

#### 7.5.1、on 事件

- on 事件方式，直接使用`null`覆盖就可以实现事件的解绑
- 语法：

~~~JavaScript
// 绑定事件
btn.onclick = function() {
    alert('点击了')
}
// 解绑事件
btn.onclick = null
~~~



#### 7.5.2、addEventListener 事件

- addEventListener 方式，必须使用：`removeEventListener(事件类型,事件处理函数,[获取捕获或者冒泡阶段])`
- 例如：

~~~javascript
function fn(){
    alert('点击了')
}
// 绑定事件
btn.addEventListener('click',fn)
// 解绑事件
btn.removeEventListener('click',fn)
~~~

- 注意：匿名函数无法被解绑



#### 7.5.3、鼠标经过事件的区别

- 鼠标经过事件：
  - mouseover 和 mouseout 会有冒泡效果
  - mouseenter 和 mouseleave 没有冒泡效果（推荐）



#### 7.5.4、两种注册事件的区别

- 传统 on 注册（L0）
  - 同一个对象，后面注册的事件会覆盖前面注册（同一个事件）
  - 直接使用null覆盖偶就可以实现事件的解绑
  - 都是冒泡阶段执行的
- 事件监听注册（L2）
  - 语法: addEventListener(事件类型, 事件处理函数, 是否使用捕获)
  - 后面注册的事件不会覆盖前面注册的事件（同一个事件）
  - 可以通过第三个参数去确定是在冒泡或者捕获阶段执行
  - 必须使用removeEventListener(事件类型, 事件处理函数, 获取捕获或者冒泡阶段)
  - 匿名函数无法被解绑





















