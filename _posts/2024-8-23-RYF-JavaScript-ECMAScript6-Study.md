---
layout: post
title: 阮一峰 JavaScript & ECMAScript6 学习笔记与思考
categories: [JavaScript]
description: JavaScript
keywords: JavaScript
---

## <a href="https://www.bookstack.cn/read/javascript-tutorial/README.md" target="_blank">JavaScript (ES5)</a>

Tip：以下所有大标题都设置了超链接方便查阅

### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-basic-introduction.md" target="_blank">Part 1-1 导论A</a>

JavaScript: 轻量级脚本语言 \| 嵌入式语言 \| 对象模型语言

浏览器环境提供的额外 API：浏览器控制类 \| DOM 类 \| Web 类

适用领域：浏览器平台化 \| Node 服务器端 \| 数据库操作 \| 移动平台开发 \| 应用内嵌脚本语言 \| 跨平台桌面应用

学习关键点：外部API \| 语言本身缺陷与陷阱

性能优势：语法灵活、对象 \| 编译运行 \| 事件驱动与非阻塞式设计

### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-basic-history.md" target="_blank">Part 1-2 导论B</a> 

由于保持简单，脚本语言缺少一些关键的功能，比如块级作用域、模块、子类型等，所以对于 JavaScript，需要学习各种解决问题的模式

JavaScript 编程风格是函数式编程和面向对象编程的一种混合体

#### 重要历史

2001年，JSON 格式被提出，用于取代 XML 格式进行服务器和网页之间的数据交换。JavaScript 原生支持 json，不需要额外部署代码。

2009年，Node.js 项目诞生，标志着 JavaScript 可以用于服务器端编程

2010年，三个重要的项目 NPM、BackboneJS 和 RequireJS 诞生，标志着 JavaScript 进入模块化开发的时代

2011年6月，ECMAScript 5.1 版正式发布

2012年，微软发布 TypeScript 语言，TS 是 JavaScript 的超集，在 JS 基础上添加了很多新的语法特性，主要目的是为了开发大型程序并编译为 JS 运行

2015年3月，React Native 项目发布，它将 React 框架移植到手机端，从而将 JavaScript 代码转为 iOS 的 Objective-C 或 Android 的 Java 

2015年6月，ECMAScript 6 正式发布，并且更名为“ ECMAScript 2015 ”

2017年6月，《ECMAScript 2017 标准》发布，正式引入了 async 函数，使得异步操作的写法出现了根本的变化

#### JavaScript 与 Java 的区别与联系

- JavaScript 的基本语法与对象体系是模仿 Java 设计的。但JavaScript 没有采用 Java 的静态类型

- JavaScript 的函数是一种独立的数据类型，采用基于原型对象的继承链

#### JavaScript 与 ECMAScript 的关系

- ES 是 JS 的规格，JS 是 ES 的一种实现。在日常场合这两个词可以互换

- ES 只规定 JS 的基本语法结构，与部署环境相关的标准(eg: DOM)都由其他标准规定

### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-basic-grammar.md" target="_blank">Part 1-3 基本语法</a> 

#### 一些基本知识点

分号前面可以没有任何内容，JavaScript 引擎将其视为空语句

JavaScript 的变量名区分大小写

声明变量但不赋值，则变量的值是 undefined

变量的类型没有限制，变量可以随时更改类型

var a = 1 基本等同于 a = 1，但不写 var 容易不知不觉地创建全局变量，建议总是使用var命令声明变量

使用 var 重新声明一个已经存在的变量是无效的，变量将保持第一次声明时的状态（如果重新声明时顺便赋值，则赋值成功并会覆盖之前的状态）

#### ⭐️变量提升（hoisting）

JavaScript 引擎工作时会先解析代码，获取所有被声明的变量，然后再一行一行地运行

这导致：所有变量声明语句都被提升到代码的头部，这就是变量提升（hoisting）

Tip：只有声明语句会得到提升，单独的赋值语句不会被提升

#### JavaScript 保留字

arguments、break、case、catch、class、const、continue、debugger、default、delete、do、else、enum、eval、export、extends、false、finally、for、function、if、implements、import、in、instanceof、interface、let、new、null、package、private、protected、public、return、static、super、switch、this、throw、true、try、typeof、var、void、while、with、yield

#### ⭐️注释陷阱（HTML）

JavaScript 可以兼容 HTML 注释，即 \<!- - 和 - -\> 也可作为单行注释

但 - -> 只有在行首，才会被当成单行注释，否则会被当作运算

eg： n - -> 0 实际上是 n- - > 0 

#### 区块

对于 var 命令来说，JavaScript 的区块不构成单独的作用域，与不使用区块的情况没有任何区别

在 JavaScript 中单独使用区块并不常见，区块往往用来构成其他更复杂的语法结构，比如for、if、while、function 等

#### 基本结构

if、if...else、if...else if、switch、for、while、do...while 同 C语言

值得注意的是：(条件) ? 表达式1 : 表达式2，三元运算符，如果条件为真，返回 表达式1 的值

#### ⭐️标签（破环定位符）

标签相当于跳出循环的定位符，与 break 和 continue 配合使用，可跳出特定的循环

- 与 break 搭配：

```JavaScript
Circle1:
    for (var i = 0; i < 3; i++){
        for (var j = 0; j < 3; j++){        
            if (i === 1 && j === 1) {            
                break Circle1;              
            }          
            console.log('i = ' + i + ',' + 'j = ' + j);         
        }    
    }  
/*
We will get:
i = 0, j = 0
i = 0, j = 1
i = 0, j = 2
i = 1, j = 0
*/
```


- 与 普通代码块 搭配：

```JavaScript
testPart: {
    console.log(1);
    break testPart;
    console.log('本行不会输出');
}
console.log(2);
/*
We will get:
1
2
*/
```


- 与 continue 搭配：

```JavaScript
Circle1:
    for (var i = 0; i < 3; i++){
        for (var j = 0; j < 3; j++){
            if (i === 1 && j === 1){
                continue Circle1;
            } 
            console.log('i = ' + i + ',' + 'j = ' + j);
        }
    }
/*
We will get:
i = 0, j = 0
i = 0, j = 1
i = 0, j = 2
i = 1, j = 0
i = 2, j = 0
i = 2, j = 1
i = 2, j = 2
*/
```



### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-genera.md" target="_blank">Part 2-1 数据类型概述</a>

七种数据类型：
- 原始类型：数值(number)、字符串(string)、布尔值(boolean)
- 合成类型：对象(object)
- 特殊类型：undefined、null
- ES6新增：Symbol

其中对象又可分为三种：狭义对象(object)、数组(array)、函数(function)

#### 确定数据类型(typeof)

Tip:instanceof 运算符 与 Object.prototype.toString 方法 也可以确定数据类型，此处先不展开

数据类型 | typeof()结果
-------|----------
数值 | number
字符串 | string
布尔值 | boolean
函数 | function
undefined | undefined
null  | ⭐️object
对象 | object
{} | object
空数组[] | object

Tip：instanceof 运算符可以区分空数组与普通对象

⭐️思考：

利用独一无二的 typeof(undefined)=undefined

我们可以效仿 C++、Python 异常处理来处理未定义导致的异常

与其发生：

```JavaScript
if (v) {
  console.log('OK');
}
// Error: v is not defined
```

不妨写成：

```JavaScript
Circle1:
if (v) {
  if(typeof v === "undefined"){
  console.log('oops');
  break Circle1; // 运用标签阻止正常结果输出
  }
  console.log('OK');
}
```
### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types--null-undefined-boolean.md" target="_blank">Part 2-2 null、undefined、boolean 详解</a>













----------------------------------

其余待补充
