---
layout: post
title: AI | InternLM2-Chat-7B 搭建与基于 Windows 的大模型量化方法
categories: [AI, InternLM]
description: 搭建InternLM的一些感想
keywords: AI, InternLM
---

# Windows+LAPTOP 本地搭建 InternLM2-Chat-7B(4bit量化)

[InternLM's GitHub](https://github.com/InternLM)：https://github.com/InternLM

[InternLM2 项目地址](https://github.com/InternLM/InternLM)：https://github.com/InternLM/InternLM

[InternLM's HuggingFace](https://huggingface.co/internlm)：https://huggingface.co/internlm

InternLM 多项指标表现优秀，觉得不错欢迎Star~

## 目录

本来Typora生成了一份，后面想起博客有了，大家可以直接用屏幕右边博客那个。

### 环境介绍

GPU：LAPTOP RTX 3060  (6GB GDDR6 + 7.9GB 共享GPU)

System：Windows 11

CUDA：11.7.64

显卡驱动：551.23 (后面使用共享GPU时会用到)

Python：3.10.13

- pytorch： 2.0.1
- transformers：4.37.2
- streamlit：1.30.0

- bitsandbytes： 0.41.1

- triton： 2.0.0

这里需要注意InternLM官方给出的要求：

Python >= 3.8

- PyTorch >= 1.12.0 (推荐 2.0.0 和更高版本)
- Transformers >= 4.34

### 前言

InternLM 功能强大、搭建简单、生态丰富，类似搭建教程已有许多，部分列举如下：

[CSDN-InternLM-7B模型的简介、安装、使用方法](https://blog.csdn.net/qq_41185868/article/details/131692975#1、通过以下的代码加载 InternLM 7B Chat 模型)

[官方教程-GitHub-InternLM/README_zh-CN.md](https://github.com/InternLM/InternLM/blob/main/README_zh-CN.md)

本文主要介绍：

- 如何在LAPTOP3060等小显存机上量化运行7B-LLM
- 如何跳过量化所需库bitsandbytes与triton只能在Liunx上运行的限制

本人小白，教程可能难免太注重不重要的细节或者存在结构混乱，望各位海涵🙈

### 搭建流程

#### 下载InternLM

1. ##### 创建项目目录如下(命名仅供参考)

   ```
   InternLM_Try
       ├── InternLM_GitHub
       │   └── ...
       └── InternLM2-Chat-7B
       │   └── ...
   ```

2. ##### 访问 [InternLM2 HuggingFace 项目地址](https://huggingface.co/internlm/internlm2-7b/tree/main)

   下载项目文件至目录 `InternLM_Try/InternLM2-Chat-7B`

3. ##### 创建环境，执行命令安装如下依赖库

   这里假设虚拟环境名为 InternLM-env

   ```
   pip install modelscope
   pip install transformers
   pip install streamlit
   pip install sentencepiece
   pip install accelerate
   ```

4. ##### 访问[InternLM2 GitHub 项目地址](https://github.com/InternLM/InternLM)

   下载项目文件至目录 `InternLM_Try/InternLM_GitHub`



此时，通过修改`InternLM_Try/InternLM_GitHub/chat`内 `web_demo.py`内模型的存放地址

```
@st.cache_resource
def load_model():
    model = (AutoModelForCausalLM.from_pretrained('internlm/internlm2-chat-7b',
                                                  trust_remote_code=True).to(
                                                      torch.bfloat16).cuda())
    tokenizer = AutoTokenizer.from_pretrained('internlm/internlm2-chat-7b',
                                              trust_remote_code=True)
    return model, tokenizer
```

调用命令：

```
cd `InternLM_Try/InternLM_GitHub/chat`
activate InternLM-env
streamlit run web_demo.py --server.address 127.0.0.1 --server.port 6006
```

打开 https://127.0.0.1:6006 就可以进行本地网页端对话啦

------

欸？报错了？看来是显存不够了，让我们来进行4-bit量化吧！

#### 4-bit量化

------

首先，以下为官方GitHub指导：

```
tokenizer = AutoTokenizer.from_pretrained("internlm/internlm2-chat-7b", trust_remote_code=True)
# 设置`torch_dtype=torch.float16`来将模型精度指定为torch.float16，否则可能会因为您的硬件原因造成显存不足的问题。
model = AutoModelForCausalLM.from_pretrained("internlm/internlm2-chat-7b", device_map="auto",trust_remote_code=True, torch_dtype=torch.float16)
# (可选) 如果在低资源设备上，可以通过bitsandbytes加载4-bit或8-bit量化的模型，进一步节省GPU显存.
  # 4-bit 量化的 InternLM 7B 大约会消耗 8GB 显存.
  # pip install -U bitsandbytes
  # 8-bit: model = AutoModelForCausalLM.from_pretrained(model_dir, device_map="auto", trust_remote_code=True, load_in_8bit=True)
  # 4-bit: model = AutoModelForCausalLM.from_pretrained(model_dir, device_map="auto", trust_remote_code=True, load_in_4bit=True)
```

不难看出我们需要重点执行`pip install -U bitsandbytes`，并在函数加载时指定`load_in_4bit=True`

如果你是Linux系统，直接运行就成功啦

但......如果你是Windows系统，并且完全没有Linux虚拟机，也不想使用任何云算力平台呢

#### Windows 系统下量化大模型

------

你可能会想：Windows系统怎么使用Linux的Python库呢

但翻阅无数个网页后，我可以 很 负责任地说，还是有办法滴

⭐注：

- 这段内容可能会比较乱，毕竟本人在很多教程的帮助下尝试了很多种办法，此文也是经过了半个月才写，所以有一些链接我不太确定有什么用，但不是完全没用，我还是把它放在末尾了

- 必须确保Python版本为3.10.x，后面下载会用到

- 必须确保pytorch支持CUDA (安装新torch的时候经常忘记用<版本号>+cu117等GPU版本)

  and如何安装适配自己机型的CUDA

  这个如果是小白还是得看几个教程的，具体可以自己百度，很多

- 期间一些依赖库的下载就省略了，可以自己pip (我淡忘了)

  

###### 下载bitsandbytes windows 预编译包

------

⭐根据[使用 12GB 显存体验百川 13B 大型语言模型 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/643307410)

跳转到里面提及的windows预编译包[Release Wheels · jllllll/bitsandbytes-windows-webui (github.com)](https://github.com/jllllll/bitsandbytes-windows-webui/releases/tag/wheels)

在列表中选择0.41.1，跳转进入[jllllll/bitsandbytes at cmake_windows_0.41.1 (github.com)](https://github.com/jllllll/bitsandbytes/tree/cmake_windows_0.41.1)

根据要求下载，最终在你环境的site-packages里即可



###### 确保 bitsandbytes 不报错

------

⭐首先，官方issues，里面很多有用的建议，但都是英文，地址如下：

[Issues · TimDettmers/bitsandbytes (github.com)](https://github.com/TimDettmers/bitsandbytes/issues)



⭐我认为比较有用的几个issue 和 comment：

[Issue-CUDA Setup failed despite GPU being available. Inspect the CUDA SETUP outputs above to fix your environment! · Issue #175 · TimDettmers/bitsandbytes (github.com)](https://github.com/TimDettmers/bitsandbytes/issues/175)

[https://github.com/TimDettmers/bitsandbytes/issues/175#issuecomment-1701085721](https://github.com/oobabooga/text-generation-webui/issues/147#issuecomment-1456040134)

[https://github.com/oobabooga/text-generation-webui/issues/147#issuecomment-1456040134](https://github.com/oobabooga/text-generation-webui/issues/147#issuecomment-1456040134)

[https://github.com/TimDettmers/bitsandbytes/issues/942#issuecomment-1872393853](https://github.com/TimDettmers/bitsandbytes/issues/942#issuecomment-1872393853)



⭐根据[win11下bitsandbytes的用法 – yinfupai](https://blog.yinfupai.com/en/4587.html)

修改`site-packages\bitsandbytes\cuda_setup\main.py`中如下函数返回值为.dll，修改后如下

```
def evaluate_cuda_setup():
    cuda_setup = CUDASetup.get_instance()
    if 'BITSANDBYTES_NOWELCOME' not in os.environ or str(os.environ['BITSANDBYTES_NOWELCOME']) == '0':
        cuda_setup.add_log_entry('')
        cuda_setup.add_log_entry('='*35 + 'BUG REPORT' + '='*35)
        cuda_setup.add_log_entry(('Welcome to bitsandbytes. For bug reports, please run\n\npython -m bitsandbytes\n\n'),
              ('and submit this information together with your error trace to: https://github.com/jllllll/bitsandbytes/issues'))
        cuda_setup.add_log_entry('='*80)
    if not torch.cuda.is_available(): return 'libbitsandbytes_cpu.dll', None, None, None
```



⭐根据[成功解决UnicodeDecodeError: 'utf-8' codec can't decode - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/150371648)

报错`UnicodeDecodeError: 'utf-8' codec can't decode byte 0xd3 in position 238: invalid continuation b`可以通过直接修改文档内的`utf-8`为`ISO-8859-1



⭐需要为 bitsandbytes 下载 libbitsandbytes_cuda117.dll，可以参考以下链接：

[bitsandbytes-cuda117 · PyPI](https://pypi.org/project/bitsandbytes-cuda117/#files)

[TimDettmers/bitsandbytes: Accessible large language models via k-bit quantization for PyTorch. (github.com)](https://github.com/TimDettmers/bitsandbytes)

注：此文件需要被放到`site-packages\bitsandbytes`内



⭐根据[CSDN-大模型GPU安装bitsandbytes报错解决](https://blog.csdn.net/qq_36936730/article/details/132306503?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2-132306503-blog-129930919.235^v43^pc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2-132306503-blog-129930919.235^v43^pc_blog_bottom_relevance_base4&utm_relevant_index=5) 与 [CSDN-大模型训练时，使用bitsandbytes报错的解决方法](https://blog.csdn.net/anycall201/article/details/129930919)

需要在下载bitsandbytes前手动在环境变量LD_LIBRARY_PATH中添加CUDA的lib路径

注：可能还需要添加其他环境变量，但理论上这一个就够了，具体可以自己看源文件`site-packages\bitsandbytes\cuda_setup\main.py`



在可视化界面设置有时会出错，一个不小心还容易误删系统Path变量(别问我怎么知道的)

**WARNING：千万别不小心把系统环境变量删了，如果删了的话千万别关机，关机之后就只能还原了，真的遇到的话可以问我，我被迫还原了一次...**

⭐所以根据[CSDN-使用set命令设置环境变量](https://blog.csdn.net/dlyhlq/article/details/2851509)  [CSDN-windows环境变量 set使用](https://blog.csdn.net/lidengzhi0000/article/details/6108876?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-1-6108876-blog-2851509.235^v43^pc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-1-6108876-blog-2851509.235^v43^pc_blog_bottom_relevance_base4&utm_relevant_index=2) 与 [如何使用 Windows CMD 环境变量 - 知乎](https://zhuanlan.zhihu.com/p/499305351)

可以使用 `set` 在 cmd 内临时设置环境变量



⭐我依稀记得自己改完环境变量还报错，然后好像把如下部分（不确定，可能是其他部分）的报错删了，只要不exit就好代码哈哈哈哈

可以借鉴一下，环境变量设置好之后如果还报错的话不让他退出，不报错是可以跑的

```
    remaining_candidate_env_vars = {
        env_var: value for env_var, value in candidate_env_vars.items()
        if env_var not in {"CONDA_PREFIX", "CUDA_HOME", "CUDA_PATH", "LD_LIBRARY_PATH", "PATH"}
    }
```



⭐根据[Llama2-Chinese项目：1-项目介绍和模型推理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/655645086?utm_id=0)

拷贝 CUDA 的 bin 内的文件至`site-packages\bitsandbytes`     (这也太暴力了)



⭐根据[ModuleNotFoundError: No module named 'charset_normalizer' - CSDN文库](https://wenku.csdn.net/answer/8bf03ee4fdfc42aa9089c5ade10dcccf)

无论报任何 no module 直接 pip 就行，除非linux专属(



⭐还有一些报错比如找不到“transformers” 我通过重启VSC即可解决

遇到无法解决的问题欢迎评论或者私信我

我在官方项目下的issue：[[QA\] Windows11使用bitsandbytes运行InternLM2-chat-7B-4bits量化，大模型精神错乱 · Issue #680 · InternLM/InternLM (github.com)](https://github.com/InternLM/InternLM/issues/680)



###### 下载triton

如果报错No Module 'triton'，证明你离胜利不远了

triton是bitsandbytes运行中不可或缺的一个库，但此库也是仅Linux



⭐根据[CSDN-分析解决【No module named ‘triton‘】的问题_error caught was: no module named 'triton](https://blog.csdn.net/ddrfan/article/details/130127401) 与[CSDN-Windows上安装Triton_triton-2.0.0-cp310-cp310-win_amd64.whl](https://blog.csdn.net/u014288878/article/details/134725508?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2-134725508-blog-130127401.235^v43^pc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2-134725508-blog-130127401.235^v43^pc_blog_bottom_relevance_base4)

我们可以下载triton的.whl文件[triton-2.0.0-cp310-cp310-win_amd64.whl · r4ziel/xformers_prebuilt at main](https://huggingface.co/r4ziel/xformers_pre_built/blob/main/triton-2.0.0-cp310-cp310-win_amd64.whl)

也正因为只有cp310的版本，所以我们需要确保python环境为3.10.x



#### 使用共享GPU

好的，到这里我们已经完成了量化与搭建的所有操作(撒花.gif)

但 LAPTOP3060 只有 6GB 显存，4-bit 量化的 7B LLM 需要至少 8GB 显存

打开资源管理器，我发现有 7.9G 的共享GPU不能进入使用状态

那为什么不将它利用起来呢

于是，根据(主要是后一个)[深度学习GPU专用内存跑满，是否能利用共享GPU加快速度？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/455084239) 与 

[PyTorch现在能不能使用共享GPU内存？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/616766660/answer/3252280836?utm_psn=1698077346941427712)

我们可以通过更新Nvidia 显卡驱动至536.40+并创建张量的方式使用这一块内存

更新显卡驱动只需要打开 GeForce Experience 这个软件直接下载就行

创建张量：

```
activate InternLM-env
python
import torch
a = torch.zeros((1000, 1000, 1000, 2), dtype = torch.int32, device = "cuda")
```

之后就可以发现共享GPU进入可用状态了

注：

- 共享GPU属于CPU加速GPU，会占用CPU，且速度相当于不用CUDA，但因为大部分还是GPU，还是很快的

- 更新显卡驱动会导致电脑配置变怪 打游戏不习惯等问题，请谨慎处理 (我用了几天就习惯了)
- 张量创建后项目能够运行便可，只需要创建一次，再打开项目就不用创建了
- 在你第一次创建并退出项目后，记得使用 `del a` 释放张量，使用 `torch.cuda.empty_cache()` 清理GPU缓存，使用 `import gc` 和`gc.collect()`用垃圾回收机制清除占用 (我不确定用几个，就多用了几个)



⭐其他可以参考的链接：

[Windows 下 bitsandbytes load_in_8bit报错 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/679416233)

[jupyter 下 bitandbytes报错记录 - glowwormss - 博客园 (cnblogs.com)](https://www.cnblogs.com/jax-/p/17812736.html)

[【半精度】Pytorch模型加速和减少显存_model.half()-CSDN博客](https://blog.csdn.net/leviopku/article/details/112472123)

[GitHub - DeXtmL/bitsandbytes-win-prebuilt](https://github.com/DeXtmL/bitsandbytes-win-prebuilt)



⭐一个名为bitsandbytes-windows的库，我记得好像用过，但没成功，不推荐使用：

[Keith-Hon/bitsandbytes-windows: 8-bit CUDA functions for PyTorch in Windows 10 (github.com)](https://github.com/Keith-Hon/bitsandbytes-windows)

## 感想

首先承认我的行为属于多此一举

正确做法是使用虚拟机创建 Linux 环境或者直接使用云算力平台 (InternLM有自己的云算力平台，大大很好说话)

但为了在本机搭建，我一股犟劲完成了如上操作

中途在添加环境变量时不小心误删了 PATH 并且因为怀疑需要重启生效所以关机了

导致不得不将电脑还原为2天前  (只能还原到安装软件的时间节点) (还好2天前因为其他项目安装了MySQL 8.x版本)

心灰意冷下进入梦乡，却在第二天上午奇迹般成功



感谢互联网时代如此多的信息与教程对我的帮助

也感谢 InternLM 社区大佬与各位同学们对我一直以来的帮助

希望 InternLM 越做越好！希望此博客对你有帮助！



想到当晚翻阅 GitHub Issues 时看到的一个评论`thanks man, you saved my day!`，感触很深

They had saved my day!

I wish that one day i can save your day!☺️

## 预告

本人本地搭建 InternLM 初衷是实现一个全栈项目

先放一个占坑地址，此文即为README.md [Scoodtone/screening_helper (github.com)](https://github.com/Scoodtone/screening_helper)

主要由 Node.js后端 React.js前端 与 Python-Flask 构成

前端接受输入后传入后端，后端通过POST传入Flask，大模型回复后原路返回显示在前端网站上

该开源项目的文件将在之后与大家见面，有需要的佬可以浅浅期待一下

(基本没写什么根本不会被需要哈哈哈)

## 附录

### 可能用到的命令

`pip list` 列出该环境的依赖库与版本

`nvcc --version` 看CUDA的版本

### 我的环境库

有点长，见博客此文章隔壁附录

[https://scoodtone.github.io/2024/02/23/appendix](https://scoodtone.github.io/2024/02/23/appendix/)

注：看到这里想翻到最上面有点麻烦，可以往下翻点击页面右下角的蓝色 TOP 返回最顶部喔

