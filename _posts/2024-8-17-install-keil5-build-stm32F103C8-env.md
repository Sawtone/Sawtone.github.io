---
layout: post
title: STM32 | Keil5 搭建与配置 & 电赛项目总结
categories: [STM32]
description: Keil5 Arm 安装
keywords: STM32
---


# Keil5 Arm 安装、环境配置与 stm32 项目创建

## Keil5 安装，实现 Arm & C51 共存

根据：[https://blog.csdn.net/get_p_c_j/article/details/132968501](https://blog.csdn.net/get_p_c_j/article/details/132968501)

一步步做就可以实现 Arm 与 C51 在同一个 Keil5 里的共存

Tip1：下文用到的 STM32F103C8T6 是 Arm 架构，而51单片机大多为 C51 架构，，达成共存便于后续使用 Keil5 完成各项任务

Tip2：两种架构在非共存状态下会相互覆盖，不能通过安装两个不同架构的 Keil5 软件达成共存（已踩坑）

Tip3：另一个很不错的辅助资料：[https://blog.csdn.net/yc5300891/article/details/80635860](https://blog.csdn.net/yc5300891/article/details/80635860)

Tip4：官方下载地址参考：[https://blog.csdn.net/weixin_43992330/article/details/135207272](https://blog.csdn.net/weixin_43992330/article/details/135207272)

## Keil5 使用相关

软件基础使用：[https://zhuanlan.zhihu.com/p/345191075](https://zhuanlan.zhihu.com/p/345191075)

## STM32F103C8T6 开发环境配置与工程建立

首先声明：配置过程中报错太多太多了，如果不是对自己的基础要求很高的话，请直接找一个现成的 STM32 Project，然后修改内部代码直接用就行

STM32F103C8T6 芯片引脚定义与功能：[https://blog.csdn.net/weixin_60324241/article/details/136492164](https://blog.csdn.net/weixin_60324241/article/details/136492164)

理论参考：[https://blog.csdn.net/michaelchain/article/details/119628190](https://blog.csdn.net/michaelchain/article/details/119628190)

实操：[https://www.cnblogs.com/zeng-1995/p/11308622.html](https://www.cnblogs.com/zeng-1995/p/11308622.html)

     [https://blog.csdn.net/dorlolo/article/details/109181681](https://blog.csdn.net/dorlolo/article/details/109181681)
     
STM32F103C8官方包下载地址：[https://www.keil.arm.com/devices/stmicroelectronics-stm32f103c8/features/](https://www.keil.arm.com/devices/stmicroelectronics-stm32f103c8/features/)

## STLink 烧录工具配置与使用

根据：[https://zhuanlan.zhihu.com/p/576546695](https://zhuanlan.zhihu.com/p/576546695)

     [https://blog.csdn.net/qq_43064170/article/details/97155662](https://blog.csdn.net/qq_43064170/article/details/97155662)

## 部分报错与问题收集

### Error: L6218E: Undefined symbol main (referred from __rtentry2.o)

解决方案：[https://blog.csdn.net/qq_42224089/article/details/122148748](https://blog.csdn.net/qq_42224089/article/details/122148748)

### CORE/core_cm3.c(xxx): error:xxxxxxxxxxxxx

解决方案：[https://blog.csdn.net/weixin_43874303/article/details/108767842](https://blog.csdn.net/weixin_43874303/article/details/108767842)

### UTF-8 编码状态下报错

解决方案：[https://zhuanlan.zhihu.com/p/686374842](https://zhuanlan.zhihu.com/p/686374842)

notepad++ 下载地址：[https://sourceforge.net/projects/notepadplusplus.mirror](https://sourceforge.net/projects/notepadplusplus.mirror)

### 中文注释会乱码

解决方案：[https://blog.csdn.net/wuhenyouyuyouyu/article/details/121204223](https://blog.csdn.net/wuhenyouyuyouyu/article/details/121204223)

其实也是用 notepad++ 转 UTF-8，可见编码格式某些情况下还是很重要的

### Error: Flash Download failed - Could not load file 'xxxx.axf'

只是一般的文件缺失问题

解决方案：[https://blog.csdn.net/qq_39807955/article/details/103318558](https://blog.csdn.net/qq_39807955/article/details/103318558)

         [https://blog.csdn.net/weixin_43716668/article/details/128952277](https://blog.csdn.net/weixin_43716668/article/details/128952277)

### Missing Compiler Version 5（重要）

编译器版本不匹配，这个问题一些朋友也遇到过，应该发生率很高

解决方案：[https://blog.csdn.net/leveling99/article/details/129777266](https://blog.csdn.net/leveling99/article/details/129777266)

## 可能有用的内容

STM32 移植 U8g2 图形库：[https://zhuanlan.zhihu.com/p/489251923](https://zhuanlan.zhihu.com/p/489251923)

sg90 舵机接线：[https://zhuanlan.zhihu.com/p/573218204](https://zhuanlan.zhihu.com/p/573218204)

              [https://blog.csdn.net/SHRtuji/article/details/113354315](https://blog.csdn.net/SHRtuji/article/details/113354315)

## 后记

### 电赛项目总结

C51 都还没学的我，匆匆忙忙间使用 STM32 零基础完成了一项题目

过程也许存在遗憾，但努力与积累在回忆里显得异彩纷呈

成果：编号定位装置

原理图：

![stm32](/images/blog/1stm32.png)

实物图：

![stm32](/images/blog/2stm32.png)

![stm32](/images/blog/3stm32.jpg)

### 课设总结

使用 STM32 完成小项目后，反而到了 C51 的课设，这下终于小菜一碟了(

成果：温度检测系统

自己设计电路板，机构制版，自己焊接电路板，自己设计代码(泪

实物图：

![c51](/images/blog/1c51.jpg)

![c51](/images/blog/2c51.jpg)


