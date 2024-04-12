---
layout: post
title: PCB | Altium Designer 软件使用笔记
categories: [PCB]
description: 电路原理图与 PCB 版图设计
keywords: PCB
---

# Altium Designer 20 软件使用笔记，主要基于教师讲解与个人使用

### 第一章 Altium Designer使用基础

##### Altium Designer文档资料的管理方式

Project Group -> Project -> Many Files

不要设置 Free Document (会编译不成功)

##### 创建新的设计项目

File -> New -> Project 建立工程

1、 创建PCB项目 .PrjPCB

2、 Schematic -> 原理图文件 创建原理图文件 .SchDoc  

3、 PCB -> PCB版图文件 创建PCB文件 .PcbDoc

##### 如何显示Project、Navigator、Properties、Components等面板

可以用图钉图标控制两侧面板的浮窗模式开关

View -> Panels (或软件右下角 Panels) 可以打开被关闭的面板

左侧一般有四个面板，分别是：

Project: 管理项目文件  Navigator  PCB   PCB Filter

右侧一般有两个面板，分别是：

Components  Properties 

##### 开启已有工程及文件（2种方法）

1.双击

2.File -> Open Project Group / Open Project / Open

##### 保存多个修改文件

\1. Ctrl + S

\2. File -> Save / Save As / Save Project As / Save Project Group As

退出的时候系统也会询问

##### 关闭工程及文件（5种方法）

File -> Close 

Project -> Close Project Document / Close Project

Windows -> Close Documents / Close All

右键 Project面板的文件 or 右键 文件标签

##### Altium Designer 的窗口界面

![ad-window](/images/blog/ad-window.png)

### 第二章 Altium Designer 原理图绘制基础

##### Altium Designer 的原理图编辑器

原理图只关注电器连接正确与否，与器件大小形状无关

#### 工作窗口的放大、缩小和相关操作

#####  画面的放大和缩小（4种方法）

View -> Zoom in / Zoom Out

放大镜图标

Ctrl + roll

PageUp / PageDown

##### 显示整张图纸(适配大小)

View -> Fit Document

#####  显示图纸中的全部元件(适配大小)

View -> Fit All Objects 或 点击图标

#####  放大画面的选定区域（2种）

View -> Area / Around Point / 点击图标

#####  显示画面的刷新

End / F5 刷新
