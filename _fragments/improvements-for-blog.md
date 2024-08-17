---
layout: fragment
title: 我对 Blog 作出的一些改进
tags: [Blog]
categories: [Blog]
description: Blog
keywords: Blog
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 超链接将默认以新标签页打开

写 Blog 时偶然发现，为方便点击设置的 markdown 超链接是没有任何属性的

也就是说，单击超链接会直接在当前页面跳转到目标页面🥲

那么如何修改其默认属性为新标签页跳转呢

将原来的

\[sawtone.github.io\]\(sawtone.github.io\) 

修改为 

\<a href\="sawtone.github.io" target\="_blank"\>sawtone.github.io\</a\>

这样，超链接便具有了 target="_blank" 属性

读者再也不用担心正在看的博客被跳转了

解决了读者需要 Ctrl + 单击 或者右键选择新标签页打开超链接的不便利问题

这或许就是设计产品是需要考虑许多问题之一吧

Tip: 书写时顺便复习到 markdown 的转义字符" \ "，好久没写了居然想着 markdown 怎么打注释🥲


----------------------------------

其余待补充
