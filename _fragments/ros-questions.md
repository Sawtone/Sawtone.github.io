---
layout: fragment
title: ROS 相关问题汇总与解答
tags: [ROS]
categories: [ROS]
description: 安装教程
keywords: ROS
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## ROS 换源问题

官方指定的源有时候不行，需要更换其他源

寻找优秀源可根据：https://blog.csdn.net/YMMMAR/article/details/122142925

Tip：链接非换源方法，换源方法百度即可

## sudo apt update 出错

具体报错：E: Malformed entry 1 in list file /etc/apt/sources.list.d/ros-latest.list (Component)

根据：https://blog.csdn.net/weixin_43728093/article/details/124242972

直接根据系统版本修改源列表即可，非常有效

## sudo apt-get update 出错

具体报错：

E: 无法获得锁 /var/lib/dpkg/lock-frontend - open (11: 资源暂时不可用)

E: 无法获取 dpkg 前端锁 (/var/lib/dpkg/lock-frontend)，是否有其他进程正占用它？

根据：https://blog.csdn.net/diaodaa/article/details/104516036

执行教程操作即可，但如果执行后仍然出错，则需要多次执行

Tip：我自己解决的时候同种情况重复出现了多次，重复解决多次后便没有再次遇到。

## ROS 小乌龟不动

根据：https://blog.csdn.net/shuishou1666/article/details/134285067

自查问题即可

## XTDrone 官方文档

https://www.yuque.com/xtdrone

## Gazebo 官方文档

https://classic.gazebosim.org/

----------------------------------

其余待补充
