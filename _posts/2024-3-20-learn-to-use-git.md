---
layout: post
title: Git | Git 使用笔记(持续学习)
categories: [Git]
description: 记录学习与使用 Git 的一些心得
keywords: Git
---

# Git 使用笔记，记录学习与使用 Git 的一些心得

### 下载 Git 客户端

直接访问 [https://git-scm.com/downloads](https://link.zhihu.com/?target=https%3A//git-scm.com/downloads) 即可



### 从 GitHub 下载代码

cmd 运行 `git clone xxx.git` 即可



### 向 GitHub 上传代码

#### 前期准备

##### 设置SSH key

打开电脑路径 C:/User/***YourUserName***/.ssh 并查看是否有 `id_rsa .pub` 文件

若没有，则需要在 cmd 内运行：

```powershell
ssh-keygen -t rsa -C "email"  # email为你在github上注册的邮箱
```

##### 向 GitHub 添加 SSH keys

步骤：右上角头像 -> Settings -> SSH and GPG Keys -> New SSH Key

打开后 `Title` 可以随便写，`Key` 内需要输入 `id_rsa .pub` 文件的全部内容

最后点击 `Add SSH Key`，结束

#### 具体操作

##### 调出 Git Bash

找到你想要上传的文件夹，如 D:/Test

右键此文件夹，点击显示更多选项，选择 Open Git Bash Here

##### 按顺序输入若干命令

1. `git init`   

   作用：在此文件夹生成一个.git隐藏文件

2. `git add . `   

   作用：将文件添加到暂存区（staging area）

   Tip：`add` 后的  ` .`   代表提交此文件夹全部目录，将  ` .`  替换为目录内某文件名可以上传目录内指定文件

3. `git status `  

   作用：查看上传状态 (非必选) 

4. `git commit -m "ok"`  

   作用：将所有已经通过 git add 添加到暂存区的更改提交到仓库，-m "ok" 是这次提交的注释

5. `git remote add origin https://xxx@xxx/test.git`

   作用：添加一个新的远程仓库，prigin 表示引用，后面的地址需于 GitHub 仓库中获取（下图黑色部分）

   ![image-20240419185140255](C:\Users\Sawtone\AppData\Roaming\Typora\typora-user-images\image-20240419185140255.png)

6. `git push origin master`

   作用：将本地的 `master` 分支的所有提交推送到远程仓库中的同名分支上

⭐至此，你已经能够在 GitHub 上查看上传的文件了

一些报错可以参考：

[上传本地文件（夹）到GitHub和更新仓库文件 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/136355306)

[解决'origin' does not appear to be a git repository - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/333241228)

[git 操作报错(fatal: ‘xxx’ does not appear to be a git repository)_does not appear to be a git repository fatal: coul-CSDN博客](https://blog.csdn.net/cyqzy/article/details/122037120)

[解决git push 错误error: src refspec master does not match any. error: failed to push some refs to_error: src refspec master does not match any error-CSDN博客](https://blog.csdn.net/wenb1bai/article/details/89363711)



### 更新修改过的 GitHub 仓库

##### 调出 Git Bash

找到你想要上传的文件夹，如 D:/Test

右键此文件夹，点击显示更多选项，选择 Open Git Bash Here

##### 按顺序输入若干命令

1. `git status`

   作用：显示当前工作目录和暂存区的状态，可以列出：

   未跟踪的文件：还没有被 Git 跟踪的文件。

   已修改的文件：已经被 Git 跟踪但是自上次提交后被修改的文件。

   已暂存的文件：已经使用 `git add` 添加到暂存区，准备下次提交的文件。

2. `git add -A`

   附加参数 -A ，可将工作目录中的所有变化（包括新文件、修改过的文件和删除的文件）添加到暂存区

3. `git commit -a -m "ok"`

   附加参数 -a ，可自动把所有已跟踪（已在Git索引中的）文件的修改添加到暂存区

   Tip：此命令不会提交未跟踪（进入暂存区）的新文件，因为 -a 不包括未跟踪文件

4. `git push origin master -f`

   附加参数 -f / --force，可强制推送，用于在远程分支的当前状态与你的不一致时覆盖它。

   Tip：危险操作，可能导致远程仓库中其他人的提交丢失，特别是在协作环境中。

​		  在团队协作中，推荐先进行 git pull 来同步远程仓库的更改，再进行推送操作。

⭐至此，你已经能够在 GitHub 上查看更新后的仓库文件了





### 删除 GitHub 仓库中的部分文件夹









### Git 常用命令与参数

#### git init

作用：初始化一个新的 Git 仓库 或 重新初始化一个现有的仓库

关键词：**初始化**

|        指定参数        | Git 会执行的操作 |
| :--------------------: | :--------------: |
|        不带参数        |                  |
|        `--bare`        |                  |
|     `-q / --quiet`     |                  |
|    `--template=xxx`    |                  |
| `--initial-branch=xxx` |                  |
|       `--shared`       |                  |

#### git commit

作用：保存已通过 `git add` 添加到暂存区的更改到本地仓库历史记录

关键词：**提交**

常用参数：

|     指定参数     |                       Git 会执行的操作                       |
| :--------------: | :----------------------------------------------------------: |
|     不带参数     | 会打开配置的的默认文本编辑器（如 vim 或 nano）让你输入提交信息 |
|    `-m “abc”`    |        直接将此行命令附加的信息（“abc”）视为提交信息         |
|   `-a / --all`   | 自动把所有已跟踪的文件的改动加入到提交中，即使它们还没有被添加到暂存区 |
|    `--amend`     | 若提交后发现错误 可用 `git add` 添加更改 再调用此命令修正此前最后一次提交信息 |
| `-s / --signoff` | 在提交消息的末尾添加一行签名，作为提交者同意开源项目中的贡献者协议的一种方式 |
|   `--no-edit`    | 与 `--amend` 一起使用时，允许修改最后一次提交而不更改提交信息 |
|  `-p / -patch`   |   启动一个交互式界面，允许选择将哪些更改加入到暂存区或提交   |







