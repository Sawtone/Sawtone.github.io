---
layout: post
title: Linux | Windows 搭建真实 Linux 内核环境
categories: [Linux, WSL]
description: 基于 WSL2 与 xrdp 实现
keywords: Linux, WSL
---


# Windows 利用 WSL2 与 xrdp 服务搭建真实内核 Linux 环境(include桌面)

老样子，先贴一些官方链接：

[安装 WSL](https://learn.microsoft.com/zh-cn/windows/wsl/install)：https://learn.microsoft.com/zh-cn/windows/wsl/install

[WSLg 教程](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps)：https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps

## 目录

见页面右侧

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

`wsl --shutdown` 彻底关闭 wsl 各分发版 

`wsl --unregister <发行版名称>` 卸载某个发行版

`wsl --set-default-version 2` 启用 WSL2 功能并设置为默认

`wsl --set-version <发行版名称> 2` 已安装的发行版转换为WSL2

`echo $DISPLAY` 检查 X server 的环境变量


### 前言

在 Windows 环境下使用 Linux 系统已经是非常普遍的事情了，网上的相关攻略也有很多

大致路线可以分为以下几类：

- 土豪版：使用多台电脑，一台 Windows 一台 Linux
  缺点：昂贵
- 云资源版：购置云服务器，并在云服务器下载 Linux 操作系统
  缺点：昂贵，性能较差
- 大众版：使用Vmware等虚拟机软件，用存储换虚拟内核
  缺点：虚拟内核不够真实
- 双系统版：在一台电脑里搭建两个操作系统，通过开关机切换
  缺点：不够方便

**我们将使用的方法不在上列，而是主要依赖 WSL2**

WSL，即适用于 Linux 的 Windows 子系统，微软在 WSL2 中预设了真实 Linux 内核，这使我们不用像传统双系统一样反复开关机切换系统，非常方便


### 搭建 WSL 环境

#### 激活 WSL 

根据 [WSL Linux 子系统，真香！完整实操 - 知乎](https://zhuanlan.zhihu.com/p/146545159)

使用具有管理员权限的 Shell (Windows PowerShell) 执行以下命令，即可激活 WSL (激活后需要重启系统)

`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

检验是否成功：Win + R，输入 appwiz.cpl，左上角找到“启动或关闭 Windows 功能”，会看到“适用于 Linux 的 Windows 子系统”选项处于选中状态。

如果页面显示如下，即成功

![article-300word](/images/blog/activate-wsl.png)

注：Hyper-V 功能我这里一直开着，不确定需不需要开，如需要，请参照以下教程自行设置：

[Win11 家庭版/专业版开启Hyper-V - 知乎](https://zhuanlan.zhihu.com/p/577980646)

#### 安装 Linux 发行版

之后我们就可以在命令提示符里用 wsl 的命令安装 Linux 发行版了，相关指令已经贴在上文

也可以自己执行`wsl --help` 来查看 wsl 的所有指令

随后自己安装想要的 Linux 发行版即可，本教程所有内容以 Ubuntu 20.04.6 LTS 为例

安装后即可通过 cmd 内的 Shell 选择器打开自己的 Linux 分发版，如下图

可以同时安装多个 Linux分发版

![article-300word](/images/blog/choose-shell.png)

至此，一个具有真实内核且内存占用小的 Linux 系统就成功安装到你的电脑上了

**但** ！如果你和我一样，是一个重度 Windows 系统使用者

第一次接触 Linux 的 Shell ，完全不知道如何使用 Linux 与管理系统文件

非常非常想拥有和 Windows 一样的 Linux GUI 桌面环境体验

拿下！请继续往下看



### 搭建 Linux 桌面环境

我们将从三方面进行阐述，成功经验为后者 ( xrdp + XFCE4 ) ，对过程不感兴趣的可以跳过前两者

**特此说明：前两者 ( WSLg 与 X server ) 效果较差且过程较乱，如无经验，请勿尝试**

**只有最后一种方法是成功的！！！！！！**

#### 使用 WSLg 显示桌面环境

从Windows 10的一个更新开始，Microsoft引入了对Linux GUI应用程序的原生支持，即WSLg

因为WSL 2可以使用内置的 WSLg 来显示桌面环境，并且 WSLg 不需要设置 `DISPLAY` 变量

##### 确保环境正确

通过`wsl -l -v` 查看目前拥有的子系统分发及其版本，确保 WSL 版本为 2

##### 安装桌面环境

如确定版本正确，则可直接安装想要的桌面环境：

```
# 更新包列表，确保安装的是软件的最新版本

sudo apt update

# 较低配置(注重资源占用和性能)

XFCE4: sudo apt install xfce4
LXQt: sudo apt install lxqt

# 中等配置(提供介于现代与传统之间的体验)

MATE： sudo apt install mate-desktop-environment
Cinnamon： sudo apt install cinnamon-desktop-environment

# 较高配置(现代且功能丰富)

GNOME: sudo apt install ubuntu-desktop -y
KDE Plasma: sudo apt install kde-plasma-desktop

# KDE安装还有一种：sudo apt install kubuntu-desktop （但建议先用上面的）
```

安装后，运行对应的启动指令后， WSLg 会直接渲染并显示桌面环境

比如：我这里 KDE Plasma 5 的启动指令是 `startplasma-x11`

我尝试了资源占用最高的 KDE Plasma 桌面环境， WSLg 能够以几乎 0 延迟的状态运行桌面环境

可以看出 Microsoft 对于真实 Linux 内核和 Linux GUI 的显示还是下足功夫的

##### 局限性

**但！** 为什么 WSLg 被我视作失败经验呢，事实上，它具有一个致命缺陷：

WSLg 显示的所有内容都是一个 **独！立！的！** Windows 窗口

这意味着，桌面环境的桌面会始终全屏显示，这直接导致：

作为 Panel 存在的资源栏以及你打开的任何其他窗口(如 Linux Bash)

在你单击桌面区域后，都会隐藏，而你需要点 Win 键之后通过 Windows 资源栏重新返回

这无疑会造成极其难受的使用体验

截止本文发布时间，我依旧没找到有效的解决办法

问题在于 WSLg 的默认显示设置是 Multiple Windows (多窗口) 且无法更改

如果有修改办法欢迎指出与评论，毕竟 WSLg 使用体验真的很好

#### 使用 X server 显示桌面环境

##### 为什么使用 X server

要想不借助 WSLg 显示 Linux GUI，我们需要下载X服务器，如 VcXsrv 或 Xming 等

X server 是 Linux 与其他类 UNIX 服务器系统中的 显示服务器，用于监听 X client 发送来的图形界面显示请求，并且将图形界面绘制并显示在屏幕上 

虽然我们是显示 Windows 的 WSL 产生的内容，但也要通过 X server 来完成过程

##### 局限性与优势

这一过程具有不可忽视的缺点：数据需要经过 X server 才能正确显示，当资源占用过大便会出现卡顿

尤其我们并非使用原生 Linux 环境，在多种情况下的延迟会更大

我使用多种 X server 测试了多个桌面环境，发现都具有不同程度的卡顿

但 X server 可以有效解决 WSLg 存在的窗口显示问题，因为 X server 提供了窗口显示的模式选择

##### X server 页面讲解

 此处以 VcXsrv 为例， Xming 同理

![article-300word](/images/blog/X-server.png)

四个模式分别对应不同的窗口显示模式：

Multiple windows：多窗口，每个窗口都是一个独立的 Windows 窗口

Full screen： 全屏显示，完全遮罩 Windows 界面

One large window：大窗口显示，所有窗口包含在一个最大化的 Windows 窗口里

One window without titlebar： 相比于大窗口显示，不包括最上面的边栏

**个人比较推荐后两种**

而 Display number 对应你的 `DISPLAY` 变量中最后一位数，如果这里选择 0

那你的 `DISPLAY` 变量就以 `.0` 结尾

##### 下载与安装 X server

- 下载并安装VcXsrv：https://sourceforge.net/projects/vcxsrv/
- 下载并安装Xming：https://sourceforge.net/projects/xming/

##### 禁用 WSLg

在实践过程中，我一度为此困扰许久

我首先使用 X server 创建桌面环境，但始终未发现 WSLg 是自动运行且优先级高于 X server

导致我将所有 WSLg 出现的问题都认为是 X server 出现的问题

这太荒谬了！！！浪费了我好久！！！！！！

所以我将首先告诉你，首先应当禁用 WSLg

```
# 在 Windows Shell 里输入以下代码以创建 .wslconfig 文件
# 注意： .wslconfig是文件拓展名, 而不是文件名

notepad "$env:USERPROFILE/.wslconfig
```

这将在 C:/User/***YourUserName*** 地址下创建 .wslconfig 文件

接下来请在空白文件里写入：

```
[wsl2]
guiApplications=false
```

保存文件并关闭，输入 `wsl --shutdown` 重启 WSL，完成 WSLg 的禁用

##### 设置环境变量

接下来，我们需要在 Linux 发行版中设置环境变量

打开 Linux 发行版终端，然后添加以下内容到 `.bashrc` 文件末尾：

我使用的文本编辑器是 nano ，终端是 bash ，所以我运行 `nano .bashrc`

```
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0
export LIBGL_ALWAYS_INDIRECT=1
export LIBGL_ALWAYS_SOFTWARE=1
export XDG_RUNTIME_DIR=/run/user/$(id -u)
```

保存文件并退出，然后运行 `source ~/.bashrc `，更新对文件作出的更改

##### 确保 X server 相关设置正确

- 确保 Disable access control 被选择为选中，这将允许所有客户端连接，排除因权限不足造成的问题
- 确保 Windows 防火墙没有阻止 X server 软件的活动 (在 Windows 安全中心中查看)
- 启动时最好以管理员权限运行 X server

##### 检验 X server 安装是否成功 

```
sudo apt install x11-apps -y

xeyes
```

如果显示两个小眼睛 (可以百度xeyes效果) ，即代表设置成功

##### 报错后可能的解决办法

使用xauth管理X11授权

```
# 安装xauth

sudo apt update
sudo apt install xauth -y

# 生成空的授权文件

touch ~/.Xauthority

```

然后生成授权信息并添加到授权文件中：

```
xauth generate $DISPLAY . trusted
```

一定要确保`DISPLAY`环境变量设置正确

```
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0
```

最后在Windows命令行中运行`wsl --shutdown`，重启 WSL



#### 使用 Xrdp 服务搭建桌面环境（成功）

##### 什么是 Xrdp

xrdp 是微软 RDP（远程桌面协议）服务器的免费开源实现，可让微软 Windows 以外的操作系统（如 Linux 和 BSD 风格的操作系统）提供功能完备的 RDP 兼容远程桌面体验。

据开发者称，该协议可与 rdesktop、FreeRDP 和微软自己的远程桌面客户端一起使用。

##### 创建服务

运行下述指令下载 Xrdp 与桌面环境

```
sudo apt-get install xrdp
sudo apt-get install xubuntu-desktop
```

运行如下指令，创建或进入该文件 ( nano 文本编辑器)

```
sudo nano ~/.xsession 
```

在文件内写入如下内容，确保打开的是 XFCE4 桌面

```
xfce4-session
```

不安装其他桌面的原因是， Xrdp 依旧会有延时，本人尝试了 KDE Plasma 发现依然有较低的延迟

欢迎大家一试，其实延迟也不算大(

##### 查看地址

运行 `ip addr` 查看 WSL Xrdp 服务的地址

格式为 xxx.xxx.xxx.xxx (xxx 指代两位数或三位数)

两个优质的教程，可参考：

[玩转 WSL 并配置Linux下的开发调试环境(Linux初学者福音) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/49227132) 

[wsl中的Ubuntu安装远程桌面 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/675456855#VNC)

修改端口可参考： [wsl中的Ubuntu安装远程桌面 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/675456855#VNC)

注：我没修改端口，可以正常使用

##### 连接 Linux 桌面环境

得到地址后，在 wsl 内输入 `sudo service xrdp restart` 启动服务，并保持窗口不关闭

打开 windows 远程连接功能，输入地址，直接远程连接

在进去的页面里选择 Xorg 即可，用户名与密码即 WSL 的用户名与密码

其他远程连接设置随个人喜好而定

**至此，全文结束**



放个效果图吧，图片中是 XFCE4 桌面环境，非常轻量化的一种

该 Linux 桌面是全屏沉浸式显示，但最上面有远程连接的 Panel，点击即可窗口化或最小化

**登录界面：**

![article-300word](/images/blog/login-linux.png)

**桌面：**

![article-300word](/images/blog/xfce4-linux.png)



### 附录

#### 壁纸挑选(doge

[Awesome Wallpapers - wallhaven.cc](https://wallhaven.cc/)

#### 他人成功经验（未复现成功）

[使用WSL2G在Windows11上运行KDE Plasma5！！_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV14z4y1n7ZP/?spm_id_from=333.337.search-card.all.click&vd_source=a8494e1b1a50d7a1518fb0e65ad50c50)

[WSL2 里轻轻松松运行 plasma(KDE)桌面_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1xL4y1i7oo/?vd_source=a8494e1b1a50d7a1518fb0e65ad50c50)

[Ubuntu 20.04 Desktop GUI On WSL 2 On Surface Pro (most-useful.com)](https://www.most-useful.com/ubuntu-20-04-desktop-gui-on-wsl-2-on-surface-pro-4.html#use-xdmcp-instead-of-vnc)

#### 一些其他有用的链接

[WSL2 基于WSLg运行Clion等GUI应用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/428957165)

[快速理解 X server, DISPLAY 与 X11 Forwarding_xserver-CSDN博客](https://blog.csdn.net/weixin_41973774/article/details/132511452)

[【Linux】关于 X11 GUI 转发和 WSLg 的坑 - 掘金 (juejin.cn)](https://juejin.cn/post/7006357005954711588)

[Windows 10 LTSC WSL2 + WSLg 环境配置 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/593196112)

[Unable to start X server in WSL2 · Issue #1172 · microsoft/wslg (github.com)](https://github.com/microsoft/wslg/issues/1172)

[WSL2中的高级设置配置wsl.conf 和 .wslconfig-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2154076)

[如何在WSL2开启GUI（通过X11-forwarding） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/613532987)

[Win10子系统 (WSL) Ubuntu 20.04 LTS 安装ROS - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/354217111)

[Linux中的DISPLAY环境变量相关问题 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/661457667)

[win10+WSL+安装VcXsrv Windows X Server - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/128507562)

[windows下Xming调用linux的图形界面-CSDN博客](https://blog.csdn.net/qq_36293096/article/details/103871875)

[Wsl2 Ubuntu20.04安装图形化界面_environment variable xdg_session_path not set. is -CSDN博客](https://blog.csdn.net/IPGsoz/article/details/116017749)

[X Server 的优化_xserver composite-CSDN博客](https://blog.csdn.net/teethfairy/article/details/7750395)

#### 云服务器使用桌面

服务器一般不用桌面，用的话要用 VNC 远程连接，又卡又不好看，不建议使用

[如何在Linux系统的ECS实例中安装图形界面-阿里云帮助中心 (aliyun.com)](https://help.aliyun.com/zh/ecs/installing-a-graphical-desktop-environment-for-a-linux-instance)



### 后记

#### 使用

Xrdp 连接的延时较低，能够满足日常需要

配置好之后，打开的流程就是在 Bash 里运行  `sudo service xrdp restart` ，然后直接双击远程连接的 .rdp 就行(自己保存一个，很方便)

文件交互可以直接通过文件夹进行，只需要在 WSL 内置的文件夹系统里预设一个文件夹，然后在Linux桌面里放个快捷方式即可

退出建议使用 Linux 桌面环境的 Log out，不建议直接点 Windows 窗口的关闭

#### 创作

这次 WSL 安装 Linux 桌面环境的探索早于3月初完成，但我拖了好久才完成此文

以 WSL + Xrdp 的形式搭建 Linux 环境不仅节省了大量存储空间，还确保了 Linux 内核的真实性

我认为是相当有意义的一件事

对 WSLg 和 X server 的试错成本很高，但这同样是一笔宝贵的经验，希望这些 被我撞过的南墙 能够帮到其他人

***Come on bro, Let me save ur day :D***

如果对你有帮助的话，可以给 Blog 的 Github 项目地址点个 **Star** ，万分感谢！

项目地址：https://github.com/Sawtone/Sawtone.github.io

欢迎在 评论区 或 vx 交流 ~
