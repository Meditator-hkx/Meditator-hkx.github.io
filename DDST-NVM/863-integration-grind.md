---
layout: page
title: 情系 863
subtitle: 863 系统集成注意事项
use-site-title: true
---

## 关于持久内存系统

目前看来除了无法负载张博文的应用，一般情况下运行良好。


## 关于复旦卷模式代码

测试算是通过了，但目前遇到的问题比较严峻：就是无法安装 pmvolume.ko 模块。

究其原因，应该是 .ko 模块的编译内核与安装（操作）内核版本不同，导致 format 报错。

"Are you compiling the module against the correct Kernel Version headers ?
I faced this issue and then I installed headers of correct kernel version."

一个网友的回复如上所示。

完整的测试指南命令如下：

```


```
