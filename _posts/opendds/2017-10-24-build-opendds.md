---
layout: post
published: true
title: "编译opendds"
description: opendds
---
## 1 Linux下编译
- 环境依赖

```
a C++ compiler
GNU Make
Perl
Opentinal Java SDK 1.5 through 1.8 for Java JNI binding support
```

- 获得源码并解压缩

```
tar -zxvf OpenDDS-*.tar.gz
```

- 进入目录

```
cd OpenDDS-*
```

- 编译安装

```
./configure
make -j 32
```

- 运行例子

```
source setenv.sh
cd DevGuideExamples/DCPS/Messager
./run_test.pl
```

## 参考
- [http://opendds.org/quickstart/GettingStartedLinux.html](http://opendds.org/quickstart/GettingStartedLinux.html)
