---
layout: page
title: 分布式持久性内存文件系统专题
subtitle: POSIX 兼容接口注意事项
use-site-title: true
---

该文件系统的任务指标之一是兼容 POSIX 接口，具体来说，是以下接口：

1. 打开文件

[`int open(const char *filepath, int flags, mode_t mode)`](http://man7.org/linux/man-pages/man2/open.2.html)
