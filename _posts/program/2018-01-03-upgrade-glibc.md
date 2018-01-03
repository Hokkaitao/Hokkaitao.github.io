---
layout: post
published: true
title: "Centos6.5升级glibc过程"
description: glibc
---
## glibc版本查看

```
strings /lib64/libc.so.6 |grep GLIBC_
```

## glibc安装

```
# 下载
wget http://ftp.gnu.org/gnu/glibc/glibc-2.14.tar.gz

# 解压
tar -xzvf glibc-2.14.tar.gz

# 编译安装
mkdir build
cd build
mkdir /opt/glibc-2.14
../configure --prefix=/opt/glibc-2.14
make && make install
```

## 软链

```
mv /lib64/libc.so.6 /lib64/libc.so.6.bk
ln -s /opt/glibc-2.14/lib/libc-2.14.so /lib64/libc.so.6
```

## 参考
-[分享Centos6.5升级glibc过程](http://cnodejs.org/topic/56dc21f1502596633dc2c3dc)
