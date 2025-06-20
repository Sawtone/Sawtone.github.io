---
layout: post
title: JavaScript | 阮一峰 JavaScript & ECMAScript6 学习笔记与思考
categories: [JavaScript, 连载]
description: JavaScript, 连载
keywords: JavaScript, uncompleted
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

#### ⭐️思考：

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

作用A：将字符串转为整数

特点：
- 字符串头部的空格会被自动去除
- 参数不是字符串会先转为字符串，可能导致意外
- 转换过程为单个字符依次进行，遇到不能转为数字的字符，则停止并返回已经转好的部分 eg: `arseInt('12.34') = 12`
- 如果字符串的第一个字符不能转化为数字就返回 `NaN` (正负号不算) eg:`parseInt('+') = NaN` `parseInt('+1') = 1`
- 字符串以 0x 或 0X 开头会被按照十六进制数解析，以 0 开头会被按照十进制解析
- 会自动转为科学计数法的数字会被以科学计数法的表示方法视为字符串，从而导致错误 eg: `parseInt(1000000000000000000000.5) = parseInt('1e+21') = 1`     

作用B：接受第二个参数(2到36之间)，表示被解析的值的进制，返回该值对应的十进制数

特点：
- 参数不是字符串会先转为字符串，可能导致意外
- 默认情况下第二个参数为 10，即默认是十进制转十进制
- 如果第二个参数不是数值，会被自动转为一个整数
- 参数必须在2到36之间，否则返回 `NaN`
- 如果第二个参数是 0、`undefined` 和 `null` ，则直接忽略
- 如果字符串包含对于指定进制无意义的字符，则从最高位开始转换，返回可以转换的数值；如果最高位无法转换，直接返回 `NaN`

**parseFloat()**

作用：将字符串转为浮点数

特点：
- 字符串头部的空格会被自动去除
- 参数不是字符串会先转为字符串，可能导致意外
- 转换过程为单个字符依次进行，遇到不能转为浮点数的字符，则停止并返回已经转好的部分
- 如果字符串符合科学计数法，则进行相应转换 eg: `parseFloat('314e-2') = 3.14`
- 如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回 `NaN`
- 空字符串将被转为 `NaN`

**isNaN()**

作用：判断一个值是否为 `NaN`

特点：
- 参数不是数值会先转为数值
- 字符串、对象、数组转换数值的结果是 `NaN`，`isNaN` 方法将返回 `true`
- 空数组和只有一个数值成员的数组，`isNaN` 方法返回 `false` (因为这些数组能被 `Number()` 转为数值)

总结：
- 使用 `isNaN()` 前最好先判断数据类型，使用 `return typeof value === 'number' && isNaN(value)` 判断 `NaN`
- 判断 `NaN` 最好使用其特征——不等于本身，即使用 `return value !== value` 判断 `NaN`

**isFinite()**

作用：返回一个布尔值，判断某个值是否为正常的数值

特点：
- `Infinity`、`-Infinity`、`NaN` 和 `undefined` 将返回 `false`
- 其他数值全部返回 `true`

### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-string.md" target="_blank">Part 2-4 字符串与字符集</a>

单引号字符串内可使用双引号，双引号字符串内可使用单引号

单引号字符串内部使用单引号 或 双引号字符串内使用双引号，需要转义内部的引号

⭐️ HTML 的属性使用双引号，所以 JavaScript 的字符串一般只使用单引号

如果字符串必须分成多行书写，有两种办法：
- 在除了末尾行的每一行的尾部使用反斜杠，且反斜杠后必须直接换行
- 使用 `+` 连接字符串的每一行，但每行的内容必须都是单独的字符串

#### 反思

原书介绍了一种利用多行注释输出多行字符串的方法：

```JavaScript
console.log((function () { /*
line 1
line 2
line 3
*/})
    .toString().split('\n').slice(1, -1).join('\n')
);
/*
We will get:
"line 1
line 2
line 3"
*/
```

⭐️ 网页控制台环境：
直接将原书中的代码输入 IDE 后，没有得到任何有效输出

输入网页控制台后，却能够得到正确输出

与同学讨论后，发现需要在 IDE 里添加 console.log，而网页控制台因为具有 `>` 可以直接实现输出

这里值得注意的还有：

如果在控制台使用没有 `console.log` 的代码，得到输出如下：

```
'line 1\nline 2\nline 3
```

而使用 `console.log` 将得到：

```
line 1
line 2
line 3
```

可见，网页控制台 `>` 出来的东西是一整个字符串，换行符等会以原始形式展示，而自己 log 的会把换行符变成可视的

原理暂不细究


Tip：如下，模板字符串(Template Literals)也可以做到多行输出

```JavaScript
(function () {
return `
line 1
line 2
line 3
`;
}).toString().split('\n').slice(1, -1).join('\n')

```


### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-object.md" target="_blank">Part 2-5 对象与属性</a>

#### 属性

属性(property)：即键名，当属性的值为函数，该属性被叫做方法

对象的所有键名都是字符串(ES6 又引入 Symbol 值作为键名)，如果键名是数值会被自动转为字符串

不符合变量命名规则的键名可以通过添加引号设置为字符串防止报错

对象的属性之间用逗号分隔，最后一个属性后面可以加逗号，也可以不加

#### 对象引用

```JavaScript
// 不同变量名指向同一对象，它们都是此对象的引用，指向同一个内存地址
var o1 = {};
var o2 = o1;
// 修改其中一个变量，会影响到其他所有变量
o1.a = 1;
o2.a     // 1
// 取消某一个变量对于原对象的引用，不会影响到另一个变量
o1 = 1;
o2     // { a: 1 }
Tip：如果是变量，结果如下，这说明变量只是值的拷贝，不指向内存地址
var x = 1;
var y = x;
x = 2;
y // 1
```

#### 对象与代码块

```
// 如下代码具有歧义，不确定是对象还是代码块，通常 JS 将这种情况解释为代码块
{ console.log(123) } // 123

// 如果想解释为对象，最好在大括号前加上圆括号(圆括号内只能是表达式)
({ foo: 123 })

// 如果分别将两种传入 eval 方法
eval('{foo: 123}') // 123 (foo 被视为标签
eval('({foo: 123})') // {foo: 123}
```

#### 对属性操作

```JavaScript
// 读取属性
// 使用方括号运算符的话，键名必须放在引号里
var obj = {
  p: 'Hello World',
  6: 'ok'
  0.7: 'float'
};
obj.p // "Hello World"
obj['p'] // "Hello World"

// 方括号内部可使用表达式
obj[3 + 3] // "ok"

// 数字键名必须使用方括号运算符，使用点运算会和小数点混淆
// 数字键名可以不加引号，因为会自动转换
obj[0.7] // "float"

```

```JavaScript
// 属性赋值
// JS 允许属性“后绑定”，可以在任意时刻新增属性

// 属性查看
var obj = {
  key1: 1,
  key2: 2
};
Object.keys(obj); // ['key1', 'key2']

// 属性删除
// delete 命令用于删除对象的属性，删除成功后返回 true
// delete 命令只能删除对象本身的属性，无法删除继承的属性
// 原书勘误：不是["p"]，应当是[ 'p' ]

// 注意：
// delete 删除一个不存在的属性不会报错，这表明不能通过 delete 判断属性是否存在
// 只有当属性存在且不可配置的情况下，delete 命令会返回false

// 判断属性是否存在
// in 运算符不能识别属性是自带的还是继承的
// 对象的 hasOwnProperty 方法可以判断是否为对象自身属性
var obj = { p: 1 };
'p' in obj // true
'toString' in obj // true
if ('toString' in obj) {
  console.log(obj.hasOwnProperty('toString')); // false
}

// 遍历属性
// for...in 循环可以遍历一个对象的全部属性
// 它只能遍历可遍历的属性
// 它不仅遍历对象自身的属性，还遍历继承的属性

//with语句
用不着

```

### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-function.md" target="_blank">Part 2-6 函数——属性、方法、作用域、闭包</a>

调用函数时，要使用圆括号运算符

没有返回值一般返回 `undefined`

```
// 第一等公民（first-class citizen）：
// JS 将函数看作一种与其它值地位相同的值，凡是可以使用值的地方，就能使用函数
// 函数只是一个可以执行的值，此外并无特殊之处
// 由于函数与其他数据类型地位平等，所以在 JavaScript 语言中又称函数为第一等公民

function add(x, y) {
  return x + y;
}
function a(op){
  return op;
}
a(add)(1, 1)  // 2

// a(add) 的结果是 add 函数本身
// a(add)(1, 1) 等效于 add(1, 1)
```

#### 函数书写方式

直接书写

```
function print(s) {
  console.log(s);
}
```

将匿名函数赋值给变量
```
// 这个匿名函数又称函数表达式
// 如果匿名函数具有名字，则该函数名只在函数体内部有效
// 这种方式相当于变量赋值，末尾需要加分号
var print = function(s) {
  console.log(s);
};
```

Function 构造函数

```
// 非常不直观，几乎无人使用
var add = new Function(
  'x',
  'y',
  'return x + y'
);
```

#### 函数声明的变量提升

```
// 函数的声明也具有变量提升特性
f(); // true
function f() {}

// 但如果使用赋值方式定义函数，就会报错
// 因为变量的声明被提升了，但变量的赋值没有提升，函数还未传入
f(); // Error
var f = function (){};

// 如果重复声明
// 正常情况下后者保留
function f() {
  console.log(1);
}
f() // 2
function f() {
  console.log(2);
}
f() // 2

// 函数表达式赋值的情况下优先保留
// 因为赋值反而被放在最后，覆盖了被提升的函数声明
var f = function () {
  console.log('1');
}
function f() {
  console.log('2');
}
f() // 1

```

#### 函数的属性与方法

name 属性：
```
function f1() {}
f1.name // "f1"

// 如果函数是函数表达式赋值得来的话
// 匿名函数返回变量名
var f2 = function () {};
f2.name // "f2"
// 具名函数返回函数名
// 真正的函数名还是变量名，这个函数名只能在函数体内使用
var f3 = function myName() {};
f3.name // 'myName'
```

length 属性：
```
// 返回函数预期传入的参数个数，即函数定义之中的参数个数
function f(a, b) {}
f.length // 2
// length 可以判断定义时和调用时参数的差异，以便实现面向对象的“方法重载”（overload）
```

toString() 方法：
```
// 函数的 toString() 方法以字符串的形式返回函数的源码
对于原生函数，toString()方法返回function (){[native code]}







```






### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-types-array.md" target="_blank">Part 2-7 数组</a>



### <a href="https://www.bookstack.cn/read/javascript-tutorial/docs-operators-arithmetic.md" target="_blank">Part 3-1 算术运算符</a>



----------------------------------

其余待补充
