---
layout: post
title: Linux | Windows 搭建真实 Linux 内核环境
categories: [Linux]
description: 基于 WSL2 与 xrdp 实现
keywords: Linux
---


# Windows 利用 WSL2 与 xrdp 服务搭建真实内核Linux环境(带桌面)

老样子，先贴一些官方链接：

[安装 WSL | Microsoft 官网](https://learn.microsoft.com/zh-cn/windows/wsl/install)：https://learn.microsoft.com/zh-cn/windows/wsl/install

[WSLg 教程 | Microsoft 官网](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps)：https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps

## 目录

[TOC]

### 环境介绍

GPU：LAPTOP RTX 3060

System：Windows 11

WSL ： 2.0.14.0

内置Linux内核： 5.15.133.1-1

WSLg ： 1.0.59

### 相关指令

`wsl --help` 查看 wsl 的所有指令

`wsl -l` 查看目前拥有的子系统分发

`wsl -l -v` 查看目前拥有的子系统分发及其版本

`wsl --version` 查看wsl、Linux内核、WSLg等版本号

`wsl --status`查看 wsl 各分发版目前的状态 

### 前言

在 Windows 环境下使用 Linux 系统已经是非常普遍的事情了，网上的相关攻略也有很多

大致路线可以分为以下几类：

- 土豪版：使用多台电脑，一台 Windows 一台 Linux    缺点：昂贵
- 云资源版：购置云服务器，并在云服务器下载 Linux 操作系统    缺点：昂贵，性能较差
- 大众版：使用Vmware等虚拟机软件，用存储换虚拟内核    缺点：虚拟内核不够真实
- 双系统版：在一台电脑里搭建两个操作系统，通过开关机切换    缺点：不够方便

**我们将使用的方法不在上列，而是主要依赖 WSL2 **

WSL，即适用于 Linux 的 Windows 子系统，微软在 WSL2 中预设了真实 Linux 内核，且不用像传统双系统一样反复开关机切换系统，非常方便

### 搭建 WSL 环境

#### 激活 WSL 

根据 [[安利\] WSL Linux 子系统，真香！完整实操 - 知乎](https://zhuanlan.zhihu.com/p/146545159)

使用具有管理员权限的 Shell (Windows PowerShell) 执行以下命令，即可激活 WSL (激活后需要重启系统)

`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

检验是否成功：Win + R，输入 appwiz.cpl，左上角找到“启动或关闭 Windows 功能”，会看到“适用于 Linux 的 Windows 子系统”选项处于选中状态。

如果页面显示如下，即成功

<img src="C:\Users\Sawtone\AppData\Roaming\Typora\typora-user-images\image-20240331165959021.png" alt="image-20240331165959021" style="zoom: 77%;" />

注：Hyper-V 功能我这里一直开着，不确定需不需要开，如需要，请参照以下教程自行设置：

[Win11 家庭版/专业版开启Hyper-V - 知乎](https://zhuanlan.zhihu.com/p/577980646)

#### 安装 Linux 发行版

之后我们就可以在命令提示符里用 wsl 的命令安装 Linux 发行版了，相关指令已经贴在上文

也可以自己执行`wsl --help` 来查看 wsl 的所有指令

随后自己安装想要的 Linux 发行版即可，本教程所有内容以 Ubuntu 20.04.6 LTS 为例

安装后即可通过 cmd 内的 Shell 选择器打开自己的 Linux 分发版，如下图

可以同时安装多个 Linux分发版



<img src="C:\Users\Sawtone\AppData\Roaming\Typora\typora-user-images\image-20240331165456887.png" alt="image-20240331165456887" style="zoom: 77%;" />

至此，一个具有真实内核且内存占用小的 Linux 系统就成功安装到你的电脑上了

**但** ！如果你和我一样，是一个重度 Windows 系统使用者

第一次接触 Linux 的 Shell ，完全不知道如何使用 Linux 与管理系统文件

非常非常想拥有和 Windows 一样的 Linux GUI 桌面环境体验

拿下！请继续往下看



### 搭建 Linux 桌面环境

我们将从两方面进行阐述，成功经验为后者，对过程不感兴趣的可以跳过前者

#### 使用 X server 展示桌面环境

