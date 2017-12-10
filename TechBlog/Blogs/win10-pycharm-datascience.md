---
layout: page
title: Win10 PyCharm numpy theano
subtitle: 数据科学之路漫漫，我愿陪师妹一起
use-site-title: true
---

## 任务目标

就是在Pycharm IDE下成功导入numpy模块和theno模块，以便后续代码的测试和调整。

## 疑难问题

Windows在安装numpy包和theno包时可能会遇到一些复杂的问题，而且作为数据科学的工具包，如果需要一个就手动找一个来下会比较麻烦，一种一劳永逸的解决方案
是先下载AnaConda平台工具，使用AnaConda来安装数据科学研究需要用到的包，再统一托管起来。然而，AnaConda可能存在一些链接方面的问题：比如它默认的python版本是3.6，如何替换为3.4版本呢？再比如Pycharm无法直接定位到AnaConda安装的工具包所在的位置，也就无法成功导入了。

## 两种技术路线

针对我们的初始目标，我设计了两种技术路线：

第一，通过pip工具或官网下载的方式直接安装numpy包和theno包，并放到所使用的Python版本的Lib文件夹中（比较直接但略显笨重的方法）；
第二，通过AnaConda下载numpy包和theno包，并在PyCharm中修改解释器路径来定位AnaConda管理的数据科学包（比较折腾但一劳永逸的方法）。

我对两种方法的评价是：身为一个技术人，第一种方案我是永远相信并热爱，因为我知道自己干了什么，以及下一步要干什么。第二种方案是为了将事情变得简单我可以去尝试
做出一番努力并且以后就套用这个框架去解决，然而一旦出现预料之外的问题，事情也许会变得很麻烦（立下了奇怪的FLAG）。

## 方案实施

### 直接下载法

本来以为pip安装会很简单，但出乎意料地，坑真的很多很多。

#### 1. 打开 cmd 命令行界面

使用 `python --version` 查看当前python版本。

#### 2. 安装 numpy 工具包

使用 `pip install numpy` 进行安装，对于依赖包，选择 yes 执行安装。 

#### 3. 安装 theano 工具包

这一部分会比较复杂，其理由是 pip 除了需要安装 theano 包之外还要安装好 gpu 运算支持。

第一，按照要求下载 CUDA 工具包并一路选择设置。注意，如果 GPU 不是 NVDIA 产品，则无法安装 CUDA 支持。

第二，安装 theano 包，使用 `pip install theano` 进行安装。

第三，根据提示使用 git 克隆 gpuarray 项目到本地，按文档安装，于是挑战从这里开始：

gpuarray的安装需要git，cmake，make三个工具（在Linux下都是小case，Windows下可就需要花费我们一点工夫了）。

- git的安装我选择的是这个:[GitSCM](https://git-scm.com/downloads),安装一路按需点击下去就可以了，在此不再详述。
- cmake可以在[这个网页](https://cmake.org/download/)点击进行安装。
- 关于gcc编译器的安装可参考[这篇博客](http://blog.csdn.net/pdcxs007/article/details/8582559)。

简单总结下安装这几个东西后出现的问题：

- 找不到如博客中所说的 .make 文件（来添加到环境变量中），可能是mingw在安装时因网速限制未完全下载。
- cmake 通过但是 make 会失败，报告的问题是缺少 Microsoft Visual C++ 10.0 的支持。

基本上第一个问题是比较好解决的，重新安装一遍 mingw 应该就可以了；而第二个问题则需要付出较大的代价，仅仅是为了一个 gpuarray 的包就
在整个 Windows 系统上加了许多软件及驱动支持，其实是非常可悲的一件事。做肯定是可以继续的，但是事到如今，Windows 上大概也不会有人想去真的重复
这样的方案吧。

本技术路线到此终结。


### Anaconda托管法

首先，按照 [anaconda 官方安装文档](https://conda.io/docs/user-guide/install/index.html) 选择 windows 版本的 anaconda 进行安装。

安装完毕后，根据 [Windows theano 官方安装文档](http://deeplearning.net/software/theano/install_windows.html) 一步步操作，进行安装。

接下来至关重要的步骤是





