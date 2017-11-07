---
layout: post
published: true
title: "Keepalive"
description: keepalive, tcp
---
## 基本原理
TCP的Keepalive可以理解成keep tcp alive，用来检测TCP socket的连接是否正常或是已经断开。

Keepalive原理很简单，当建立一个TCP连接时，发送端会创建一些计时器，其中一些计时器就是处理keepalive相关问题的。当keepalive计时器计数到0时，发送端就会向对端发送一些不含数据的keepalive数据包并开启ACK标志。如果得到keepalive探测包的回复，就可以认为当前的TCP连接正常。事实上，TCP允许处理数据流而非数据包，所以对于用户程序来说零字节的数据包没有危害。

开启Keepalive会对防火墙或路由器产生额外的流量。

Keepalive主要有两个任务：

- 检测死掉的对端连接

Keepalive可以用于在对端死掉并发送通知之前检测到对端的连接状态。内核错误或是强制终止对端的应用程序进程都可能造成这种情况发生。还有一种情况使用keepalive来检测对端是否死掉是对端依然存活但是连接到对端之前的网络已经断开。

- 网络断开的情况下组织对端的TCP连接断开

## 参数含义
- tcp_keepalive_time

一个连接需要TCP开始发送keepalive探测数据包之前的空闲时间，单位秒。

- tcp_keepalive_probes

发送TCP keepalive探测数据包的最大数量，默认是9。如果发送9个keepalive探测包仍旧对端没有响应，则关闭该连接。

- tcp_keepalive_intvl

发送两个TCP keepalive探测包的时间间隔，默认75秒

## 查看与配置

```
cat /proc/sys/net/ipv4/tcp_keepalive_time
cat /proc/sys/net/ipv4/tcp_keepalive_probes
cat /proc/sys/net/ipv4/tcp_keepalive_intvl
```

## 参考
- [Linux下TCP的Keepalive相关参数学习](http://www.linuxidc.com/Linux/2015-03/115321.htm)
- [TCP Keepalive HOWTO](http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/index.html)