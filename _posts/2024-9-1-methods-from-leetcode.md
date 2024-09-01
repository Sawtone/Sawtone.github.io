---
layout: post
title: C++ JavaScript | Leetcode 刷题过程中遇到的相关方法和知识点(连载)
categories: [C++, JavaScript]
description: C++, JavaScript
keywords: C++, JavaScript
---

# C++ 和 JavaScript 基础并不算很好，将每天刷题过程中遇到的方法和新知识记录在此，帮助记忆

## <a href="https://leetcode.cn/problems/running-sum-of-1d-array/" target="_blank">Easy | 1480.一维数组的动态和 | 散题</a>

```
• 数组
C++: std::vector = {} 

JavaScript: var arr=new array() 或 var arr=[]

【延伸】
vector 的特点：标准模板库容器、动态调整大小、连续内存、堆内存存储
-----------------------------------------------------------------------------------------
• 数组长度
C++: size_t length = vector.size()

JavaScript: var length = array.length
-----------------------------------------------------------------------------------------
• 添加数组元素
C++: vector.push_back(newElem)

JavaScript: array.push(newElem)
-----------------------------------------------------------------------------------------
【注意】
对于 C++ 的 vector，如果一开始创建的是空向量，则不能使用索引进行赋值，只能使用 vector.push_back 方法
eg: vector<int> results; vector[2] = 10; // Error
而对于 JavaScript 的 array，即使创建的是空数组，JavaScript 也会自动扩展数组的长度以适应新的索引
但还是建议使用 array.push 方法，这样更清晰明确，不容易出错
-----------------------------------------------------------------------------------------
• 无符号整数类型
C++： size_t length = nums.size()

JavaScript： 无

【延伸】
32位系统上 size_t 通常是 unsigned int；64位系统上，它通常是 unsigned long long
size_t 类型变量常用来表示 数组或容器的索引和大小、操作内存时内存块的大小、循环计数
std::vector::size()、std::string::length() 等标准库函数都返回 size_t 类型
```

## <a href="https://leetcode.cn/problems/make-a-square-with-the-same-color/" target="_blank">Easy | 3127.构造相同颜色的正方形 | 2024.8.31 每日一题</a>

```
• 增强遍历
C++: for (const auto& elem : container) {}

JavaScript：for (const elem of container) {} 与 for (const key in container) {}

【注意】
C++ 有 auto(自动类型推导) 和 &(引用)
而JavaScript 是动态类型语言，本身自带 "auto"，对象和数组按引用传递，原始类型按值传递

【延伸】
JavaScript 中，for...of 循环用于遍历可迭代对象（例如数组、字符串、Set、Map 等）的值
而 for...in 循环用于遍历对象的可枚举属性，或遍历数组的索引，而不是遍历值
-----------------------------------------------------------------------------------------
• 二维数组
C++： vector<vector<int>> vectorPlus = {}

JavaScript：let matrix = []
```

## <a href="https://leetcode.cn/problems/running-sum-of-1d-array/" target="_blank">Easy | 1450.在既定时间做作业的学生人数 | 2024.9.1 每日一题</a>
