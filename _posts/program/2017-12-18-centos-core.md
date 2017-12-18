---
layout: post
published: true
title: "Centos下core文件相关设置"
description: core
---
## centos下

- 生成方式

```
## 查看是否开启，0表示没有开启，>0表示生成最大的coredump的大小（单位kb），可使用参数unlimited取消core文件大小的的限制
ulimit -c 
ulimit -c unlimited  
ulimit -c 0
ulimit -a
```

- 转储文件命名规范

```
## 设置是否添加pid作为扩展，1:添加 0:不添加
cat /proc/sys/kernel/core_uses_pid

## 设置文件产生时的命名
cat /proc/sys/kernel/core_pattern
##通常这样设置：
/opt/tmp/core/core-%e-%s-%u-%g-%p-%t
```

- 参数介绍

```
%p : insert pid into filename                             # 添加pid
%u : insert current uid into filename                     # 添加当前uid
%g : insert current gid into filename                     # 添加当前gid
%s : insert signal that cased the coredump into the file  # 添加导致产生core的信号
%t : insert UNIX time that the coredump occurred into file# 添加core文件生成时的unix时间
%h : insert hostname where the coredump happened into file# 添加主机名
%e : insert coredumping executablename into file          # 添加命令名
```

- 产生core的测试

```
kill -s SIGSEGV $$
```

- Linux上的信号

发生coredump一般都是在进程收到某个信号的时候，Linux上现在大概有60多个信号，可以使用kill -l命令全部列出来。

针对特定的信号，应用程序可以写对应的信号处理函数。如果不指定，则采取默认的处理方式，默认处理是coredump的信号如下：

```
3) SIGQUIT 4) SIGILL 6) SIGABRT 8) SIGFPE 11) SIGSEGV
7) SIGBUS 31) SIGSYS 5) SIGTRAP 24) SIGXCPU 25) SIGXFSZ
29) SIGIOT
```

## core的使用

编译的程序如果不添加-g，在core中不会有行的信息，如果希望产生更详细的core文件，编译程序时需要指定-g。

```
gdb execute corefile
```

## gdb 基本使用命令

```
# 设置断点
b 行号|函数名
# 删除断点
delete 断点编号
# 禁用断点
disable 断点编号
# 弃用断点
enable 断点编号
# 单步跟踪
next
# 单步跟踪
step
# 打印变量
print 变量名
# 设置变量
set var=value
# 查看变量类型
ptype var
# 顺序执行到结束
count
# 顺序执行直到某一行
util lineno
# 打印堆栈信息
bt
```

## 参考
- [linux下生成core dump文件方法及设置](http://andyniu.iteye.com/blog/1965571)
