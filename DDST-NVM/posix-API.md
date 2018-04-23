---
layout: page
title: 分布式持久性内存文件系统专题
subtitle: POSIX 兼容接口注意事项
use-site-title: true
---

该文件系统的任务指标之一是兼容 POSIX 接口，具体来说，是以下接口(可能不全，有待添加)：

## 文件操作

1. 文件打开/创建

[`int open(const char *filepath, int flags)`](http://man7.org/linux/man-pages/man2/open.2.html)

[`int open(const char *filepath, int flags, mode_t mode)`](http://man7.org/linux/man-pages/man2/open.2.html)

2. 文件关闭

[`int close(int fd)`](http://man7.org/linux/man-pages/man2/close.2.html)

3. 文件控制

[`int fcntl(int fd, int cmd, ... /* arg */ )`](http://man7.org/linux/man-pages/man2/fcntl.2.html)

4. 文件读写

[` ssize_t read(int fd, void *buf, size_t count)`](http://man7.org/linux/man-pages/man2/read.2.html)

[`ssize_t write(int fd, const void *buf, size_t count)`](http://man7.org/linux/man-pages/man2/write.2.html)

5. 文件定位

[`off_t lseek(int fd, off_t offset, int whence)`](http://man7.org/linux/man-pages/man2/lseek.2.html)

6. 文件同步

[`void sync(void)`](http://man7.org/linux/man-pages/man2/sync.2.html)

[`int syncfs(int fd)`](http://man7.org/linux/man-pages/man2/sync.2.html)

[`int fsync(int fd)`](http://man7.org/linux/man-pages/man2/fsync.2.html)

[`int fdatasync(int fd)`](http://man7.org/linux/man-pages/man2/fsync.2.html)

## 目录操作

1. 目录打开

[DIR *opendir(const char *name)](http://man7.org/linux/man-pages/man3/opendir.3.html)
[DIR *fdopendir(int fd)](http://man7.org/linux/man-pages/man3/opendir.3.html)

2. 目录关闭

[`int closedir(DIR *dirp)`](http://man7.org/linux/man-pages/man3/closedir.3.html)

3. 目录读

[`struct dirent *readdir(DIR *dirp)`](http://man7.org/linux/man-pages/man3/readdir.3.html)

4. 目录定位

[`long telldir(DIR *dirp)`](http://man7.org/linux/man-pages/man3/telldir.3.html)

[`void seekdir(DIR *dirp, long loc)`](http://man7.org/linux/man-pages/man3/seekdir.3.html)

5. 目录删除/添加

[`int rmdir(const char *pathname)`](http://man7.org/linux/man-pages/man2/rmdir.2.html)

[`int mkdir(const char *pathname, mode_t mode)`](http://man7.org/linux/man-pages/man2/mkdir.2.html)

6. 当前路径获取/修改

[char *getcwd(char *buf, size_t size)](http://man7.org/linux/man-pages/man2/getcwd.2.html)

[char *getwd(char *buf)](http://man7.org/linux/man-pages/man2/getcwd.2.html)

[char *get_current_dir_name(void)](http://man7.org/linux/man-pages/man2/getcwd.2.html)

[`int chdir(const char *path)`](http://man7.org/linux/man-pages/man2/chdir.2.html)

[`int fchdir(int fd)`](http://man7.org/linux/man-pages/man2/chdir.2.html)


## 参考链接

1. [Linux系统文件I/O编程(一)---open()等基本函数](https://blog.csdn.net/mybelief321/article/details/8989755)
2. [POSIX文件及目录管理](https://blog.csdn.net/sunrise_2013/article/details/8060889)
3. [C open vs fopen](https://stackoverflow.com/questions/1658476/c-fopen-vs-open)
4. [What is the functional difference between msync() and fsync() in linux](https://www.quora.com/What-is-the-functional-difference-between-msync-and-fsync-in-linux)
