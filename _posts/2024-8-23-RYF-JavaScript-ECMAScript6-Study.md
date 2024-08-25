---
layout: post
title: 阮一峰 JavaScript & ECMAScript6 学习笔记与思考
categories: [JavaScript]
description: JavaScript
keywords: JavaScript
---

# 写点关于《阮一峰 JavaScript 教程》《阮一峰 ECMAScript6 标准入门教程》的阅读笔记与感悟

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

2009年，Node.js 项目诞生，标志着 JavaScript 可以用于服务器端编程

2011年6月，ECMAScript 5.1 版正式发布

2012年，微软发布 TypeScript 语言，TS 是 JavaScript 的超集，在 JS 基础上添加了很多新的语法特性，主要目的是为了开发大型程序并编译为 JS 运行

2015年3月，React Native 项目发布，它将 React 框架移植到手机端，从而将 JavaScript 代码转为 iOS 的 Objective-C 或 Android 的 Java 

2015年6月，ECMAScript 6 正式发布，并且更名为“ ECMAScript 2015 ”

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

声明变量但不赋值，则变量的值是 `undefined`

变量的类型没有限制，变量可以随时更改类型

`var a = 1` 基本等同于 `a = 1`，但不写 `var` 容易不知不觉地创建全局变量，建议总是使用 `var` 命令声明变量

使用 `var` 重新声明一个已经存在的变量是无效的，变量将保持第一次声明时的状态（如果重新声明时顺便赋值，则赋值成功并会覆盖之前的状态）

#### ⭐️变量提升（hoisting）

JavaScript 引擎工作时会先解析代码，获取所有被声明的变量，然后再一行一行地运行

这导致：所有变量声明语句都被提升到代码的头部，这就是变量提升（hoisting）

Tip：只有声明语句会得到提升，单独的赋值语句不会被提升

#### JavaScript 保留字

`arguments、break、case、catch、class、const、continue、debugger、default、delete、do、else、enum、eval、export、extends、false、finally、for、function、if、implements、import、in、instanceof、interface、let、new、null、package、private、protected、public、return、static、super、switch、this、throw、true、try、typeof、var、void、while、with、yield`

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

标签相当于跳出循环的定位符，与 `break` 和 `continue` 配合使用，可跳出特定的循环

- 与 `break` 搭配：

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


- 与 `continue` 搭配：

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
- 特殊类型：`undefined`、`null`
- ES6新增：Symbol

其中对象又可分为三种：狭义对象(object)、数组(array)、函数(function)

#### 确定数据类型(typeof)

Tip:`instanceof` 运算符 与 `Object.prototype.toString` 方法 也可以确定数据类型，此处先不展开

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

Tip：`instanceof` 运算符可以区分空数组与普通对象

⭐️思考：

利用独一无二的 `typeof(undefined) = undefined`

我们可以效仿 C++、Python 异常处理机制来处理未定义导致的异常

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

#### 浅析 undefined 与 null 

相等运算符(==)会直接报告 `undefined` 与 `null` 相等

`null` 转为数值是 `0` ，不利于实现错误处理机制，故设计 `undefined` 并使其数值为 `NaN` 

返回undefined的典型场景 | 代码  | undefined
-------|----------|----------
变量已声明，但未赋值 | var i | i 
调用函数未提供应有参数 | function f(x) {return x;} | x
对象的某个属性没有赋值 | var o = new Object( ) | o.p
函数没有返回值时 | function f( ) { }  | f( )

#### true & false

会被转换为 `false` 的六个值：undefined、null、false、0、NaN、空字符串(""、'')

其余值都被视为 `true`（⭐️包括空数组[ ]和空对象{ }）

### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-number.md" target="_blank">Part 2-3 数值与相关方法</a>

JavaScript 内所有数字均以 64 位浮点数形式储存，1 与 1.0 严格相等(===)

Tip：某些运算只有整数才能完成，此时 JavaScript 会自动把 64 位浮点数转成 32 位整数，然后再进行运算

浮点数不精确，所以要注意：

```JavaScript
(0.3 - 0.2) === (0.2 - 0.1)
// false
```
#### 范围与精度

JavaScript 64 位浮点数采用国际标准 IEEE 754

- 第 1 位：符号位，0 表示正数，1 表示负数
- 第 2 位到第 12 位（共 11 位）：指数部分  
- 第 13 位到第 64 位（共 52 位）：小数部分（即有效数字）

小数部分 53 个二进制位

运算精度范围为 $[-2^{53},2^{53}]$

$2^{53}$ = 9,007,199,254,740,992，共 16 位

指数部分 11 个二进制位，最大值为 $2^{11}-1$ = 2047，分出 1023 表示负数，则正区间边界为1024，负区间边界为1023 + 小数部分52 = 1075

数值表示范围为 $(-2^{1075},2^{1024})$

等于或超出两侧边界会发生 正溢出/负溢出

正溢出(overflow)返回 `Infinity`，负溢出(underflow)返回 `0` 

Tip：Number 对象的 `MAX_VALUE` 和 `MIN_VALUE` 属性可返回具体的最大值和最小值

#### 科学记数法

以下两种情况，JavaScript 会自动将数值转为科学计数法表示：

- 小数点前的数字多于21位
- 小数点后的零多于5个

#### 进制

- 八进制：有前缀 `0o` 或 `0O` 的数值，或者有前导 0 且只用到 0-7 的数值
- 十六进制：有前缀 `0x` 或 `0X` 的数值
- 二进制：有前缀 `0b` 或 `0B` 的数值

如果八进制、十六进制、二进制的数值里面出现不属于该进制的数字，会报错

Tip: 只有前导 0 且只用到 0-7 的数值会破例被视为八进制，这种表示方法在处理时很容易造成混乱，ES5 的严格模式和 ES6 已经废除了这种表示法，但是浏览器为了兼容以前的代码，目前还继续支持这种表示法。

#### 特殊数值

**正零与负零**

JavaScript 内部实际上存在 `+0` 和 `-0`，它们严格相等(===)

`+0` 或 `-0` 当作分母时，返回的值是不相等的，其他几乎所有场合，它们都被视为正常的 0

Tip：不相等是因为除以 `+0` 得到 `+Infinity`，除以 `-0` 得到 `-Infinity`

**NaN(Not a Number)**

`NaN` 不是一个独立的数据类型，属于数值(number)

`NaN` 主要出现在以下三种场合：

- 将字符串解析成数字出错 eg：`5 - 'apple' = NaN`
- 数学函数错误运算 eg：`Math.acos(2) Math.log(-1) Math.sqrt(-1) // NaN`
- `0 / 0 = NaN`

运算规则大体如下：

- `NaN` 不等于任何值，包括它本身
- `NaN` 与任何数（包括它自己）的运算，得到的都是 `NaN`
- `NaN` 在布尔运算时被当作 `false`
- 数组查找索引方法 `indexOf()` 底层使用严格相等，类似方法对 `NaN` 不成立

**+Infinity & -Infinity**

`Infinity` 不是一个独立的数据类型，属于数值(number)

`Infinity` 主要出现在以下两种场合：

- 数值太大 eg：`Math.pow(2, 1024) // Infinity`
- 非 0 数值除以 0 eg：`1 / 0 = Infinity` \| `1 / -0 = -Infinity`

`+Infinity` 大于 `NaN` 以外一切数值，`-Infinity` 小于 `NaN` 以外一切数值

运算规则大体如下：

- `Infinity` 与 `NaN` 作比较，总是返回 `false`
- 加减乘除基本运算符合数学中无限的定义
- `0 * Infinity = NaN` \| `0 / Infinity = 0` \| `Infinity / 0 = Infinity`
- `Infinity + Infinity = Infinity` \| `Infinity * Infinity = Infinity`
- `Infinity - Infinity = NaN` \| `Infinity / Infinity = NaN`
- `Infinity` 与 `null` 计算时，`null` 会转成 `0`，等同于与 `0` 的计算
- `Infinity` 与 `undefined` 计算，总是返回 `NaN`

Tip: 数值正溢出(overflow)、负溢出(underflow)、除以 0、被 0 除都不会报错，所以单纯的数学运算很难出现 ERROR


#### 数值相关全局方法

**parseInt()**




**parseFloat()**




**isNaN()**




**isFinite()**







### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-string.md" target="_blank">Part 2-4 字符串与字符集</a>





### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-object.md" target="_blank">Part 2-5 对象与属性</a>




### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-function.md" target="_blank">Part 2-6 函数——属性、方法、作用域、闭包</a>




### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-array.md" target="_blank">Part 2-7 数组</a>



### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-operators-arithmetic.md" target="_blank">Part 3-1 算术运算符</a>



----------------------------------

其余待补充
