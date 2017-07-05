---
layout: page
title: 如何创建RAMDISK
subtitle: 玩转 Linux
use-site-title: true
---

## 创建 RAMDISK

1. cd ~/ (转到需要创建RAMDISK的目录)

2. sudo mkdir ramdisk （创建挂载 RAMDISK 的目录）

3. sudo chmod 777 ramdisk （更改 RAMDISK 的使用权限）

4. free -h （查看剩余可用内存空间）

5. mount -t tmpfs -o size=1024M my_ramdisk ramdisk （挂载 RAMDISK）

6. df (mount) （查看是否挂载成功）

## 卸载 RAMDISK

sudo unmount ramdisk

<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->



