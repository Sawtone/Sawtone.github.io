---
layout: fragment
title: 日常使用遇到的一些 markdown 知识点
tags: [markdown, uncompleted]
categories: [markdown, uncompleted]
description: markdown
keywords: markdown, uncompleted
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---


# 从未系统学习过 markdown 这门工具性质的语言，借着使用 markdown 做笔记与写博客的机会，记录一些知识点与体会

## 超链接将默认以新标签页打开

写 Blog 时偶然发现，默认形式设置的 markdown 超链接是没有任何属性的

单击超链接会导致当前页面直接跳转为目标页面，不利于正常浏览

**修改：**

我将原来的

```
[Sawtone's Blog](sawtone.github.io) 
```

修改为 

```
<a href="sawtone.github.io" target="_blank">Sawtone's Blog</a>
```

这样，超链接便具有了 target="_blank" 属性

解决了读者需要 Ctrl + 单击 或者右键选择新标签页打开 的不便利问题
