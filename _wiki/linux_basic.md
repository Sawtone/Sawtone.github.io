---
layout: wiki
title: linux-Basic
cate1: Linux
cate2: 
description: linux-Basic
keywords: Linux
---

## 一些基本命令

### WSL

`wsl --help` 查看 wsl 的所有指令

`wsl -l` 查看目前拥有的子系统分发

`wsl -l -v` 查看目前拥有的子系统分发及其版本

`wsl --version` 查看wsl、Linux内核、WSLg等版本号

`wsl --status` 查看 wsl 各分发版目前的状态 

`wsl --shutdown` 彻底关闭 wsl 包括各分发版 


### 重置密码 (wsl) ：

(1) 输入`wsl -u root` 以root用户启动WSL

(2) 输入`passwd 用户名` 为该用户设置新密码

如果是原生 linux ，就直接在 root 环境下 `passwd 用户名` 即可
