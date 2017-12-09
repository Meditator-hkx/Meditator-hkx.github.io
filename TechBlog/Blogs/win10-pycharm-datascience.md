---
layout: page
title: 如何在Win10 Pycharm下完美使用numpy和theno包
subtitle: 数据科学之路漫漫，我愿陪师妹一起
use-site-title: true
---

## 任务目标

就是在Pycharm IDE下成功导入numpy模块和theno模块，以便后续代码的测试和调整。

## 疑难问题

Windows在安装numpy包和theno包时可能会遇到一些复杂的问题，而且作为数据科学的工具包，如果需要一个就手动找一个来下会比较麻烦，一种一劳永逸的解决方案
是先下载AnaConda平台工具，使用AnaConda来安装数据科学研究需要用到的包，再统一托管起来。然而，AnaConda可能存在一些链接方面的问题：比如它默认的python版本是
3.6，如何替换为3.4版本呢？再比如Pycharm无法直接定位到AnaConda安装的工具包所在的位置，也就无法成功导入了。

## 两种技术路线

针对我们的初始目标，我设计了两种技术路线：

第一，通过pip工具或官网下载的方式直接安装numpy包和theno包，并放到所使用的Python版本的Lib文件夹中（比较直接但略显笨重的方法）；
第二，通过AnaConda下载numpy包和theno包，并在PyCharm中修改解释器路径来定位AnaConda管理的数据科学包（比较折腾但一劳永逸的方法）。

我对两种方法的评价是：身为一个技术人，第一种方案我是永远相信并热爱，因为我知道自己干了什么，以及下一步要干什么。第二种方案是为了将事情变得简单我可以去尝试
做出一番努力并且以后就套用这个框架去解决，然而一旦出现预料之外的问题，事情也许会变得很麻烦（核心组件未必在我的掌控之中）。

## 方案实施

### 直接下载法

本来以为pip安装会很简单，但出乎意料地，坑真的很多很多。

#### 1. 打开 cmd 命令行界面

使用 `python --version` 查看当前python版本，效果如下图所示。

#### 2. 安装 numpy 工具包

使用 `pip install numpy` 进行安装，对于依赖包，选择 yes 执行安装。 

#### 3. 安装 theano 工具包

这一部分会比较复杂，其理由是 pip 除了需要安装 theano 包之外还要安装好 gpu 运算支持。

第一，按照要求下载 CUDA 工具包并一路选择设置。注意，如果 GPU 不是 NVDIA 产品，则无法安装 CUDA 支持。

第二，安装 theano 包，使用 `pip install Theano` 进行安装，注意是 **Theano**。

第三，根据提示使用 git 克隆 gpuarray 项目到本地，按文档安装，于是挑战从这里开始：

gpuarray的安装需要git，cmake，make三个工具（在Linux下都是小case，Windows下可就需要花费我们一点工夫了）。

- git的安装我选择的是这个:[GitSCM](https://git-scm.com/downloads),安装一路按需点击下去就可以了，在此不再详述。
- cmake可以在[这个网页](https://cmake.org/download/)点击进行安装。
- 关于gcc编译器的安装可参考[这篇博客](http://blog.csdn.net/pdcxs007/article/details/8582559)。

简单总结下安装这几个东西后出现的问题：

- 找不到如博客中所说的.make文件，可能是mingw在安装时因网速限制未完全下载。
- 找不到 cmake 命令，可能是因为没有添加环境变量。


### Anaconda托管法

看起来相对容易一些，但实操过程中仍遇到很多问题。

第一，解释器选择问题，默认版本？3.4？

第二，所有的包仍然是下载到 Lib/site-packages下，python.exe没法直接定位到此处。但是在复制出来后，导入没有报错，但运行报错了。




