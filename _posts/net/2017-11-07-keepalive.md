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

## 带来的问题
存活（keepalive）并不是TCP规范的一部分，在HostRequirements RFC罗列有不适用它的三个理由：
- 在短暂的故障期间，它们可能引起一个良好连接被释放 
- 它们消费了不必要的宽带 
- 在以数据包击飞的互联网上它们耗费金钱。

## 参数含义
- tcp_keepalive_time

一个连接需要TCP开始发送keepalive探测数据包之前的空闲时间，单位秒。

- tcp_keepalive_probes

发送TCP keepalive探测数据包的最大数量，默认是9。如果发送9个keepalive探测包仍旧对端没有响应，则关闭该连接。

- tcp_keepalive_intvl

发送两个TCP keepalive探测包的时间间隔，默认75秒

## 查看

```
cat /proc/sys/net/ipv4/tcp_keepalive_time
cat /proc/sys/net/ipv4/tcp_keepalive_probes
cat /proc/sys/net/ipv4/tcp_keepalive_intvl
```

## 修改

全局设置可以修改/etc/sysctl.conf:

```
net.ipv4.tcp_keepalive_intvl = 20
net.ipv4.tcp_keepalive_probes = 3
net.ipv4.tcp_keepalive_time = 60
```

立即生效方式：

```
/sbin/sysctl -p
/sbin/sysctl -w net.ipv4.tcp_keepalive_intvl=20
/sbin/sysctl -w net.ipv4.tcp_keepalive_probes=3
/sbin/sysctl -w net.ipv4.tcp_keepalive_time=60
```

## 编程

```
int keepAlive = 1;//开启keepalive属性
int keepIdle= 60;//如果连接在60s内没有任何数据来往，则进行探测
int keepInterval = 5;//探测时发包的时间间隔为5秒
int keepCount=3;//探测尝试的次数。如果第一次探测包就收到响应，则后2次不再发

setsockopt(rs, SOL_SOCKET, SO_KEEPALIVE, (void *)&keepAlive, sizeof(keepAlive));
setsockopt(rs, SOL_TCP, TCP_KEEPIDLE, (void *)&keepIdle, sizeof(keepIdle));
setsockopt(rs, SOL_TCP, TCP_KEEPCNT, (void *)&keepInterval, sizeof(keepInterval));
setsockopt(rs, SOL_TCP, TCP_KEEPCNT, (void *)&keepCount, sizeof(keepCount));
```
在程序表现为，当tcp检测到对端socket不再可用时（不能发出探测包，或是没有收到ACK响应包），select会返回socket可读，并且在recv时返回-1，同时设置上errno为ETIMEDOUT。

## 抓包

```
10:12:08.437357 IP 10.4.230.14.search-agent > 10.5.233.200.ethercat: Flags [.], ack 3318227115, win 114, options [nop,nop,TS val 710872749 ecr 3499508470], length 0
10:12:08.437538 IP 10.5.233.200.ethercat > 10.4.230.14.search-agent: Flags [.], ack 3979202483, win 115, options [nop,nop,TS val 3499528470 ecr 710852748], length 0
10:12:10.723416 IP 10.4.230.14.search-agent > 10.5.233.200.ethercat: Flags [F.], seq 3979202483, ack 3318227115, win 114, options [nop,nop,TS val 710875035 ecr 3499528470], length 0
10:12:10.763097 IP 10.5.233.200.ethercat > 10.4.230.14.search-agent: Flags [.], ack 3979202484, win 115, options [nop,nop,TS val 3499530796 ecr 710875035], length 0
10:12:12.379118 IP 10.5.233.200.ethercat > 10.4.230.14.search-agent: Flags [F.], seq 3318227115, ack 3979202484, win 115, options [nop,nop,TS val 3499532412 ecr 710875035], length 0
10:12:12.379134 IP 10.4.230.14.search-agent > 10.5.233.200.ethercat: Flags [.], ack 3318227116, win 114, options [nop,nop,TS val 710876690 ecr 3499532412], length 0
```

## 场景
通常keepalive根据需求配置到服务端。

- 客户端主机依旧活跃（UP），且服务器可达

服务器知道对方依然活跃，服务器的TCP为其复位存活定时器，如果在这两个小时到期之前，连接上发生应用程序的通信，则定时器重新复位存货定时器。

- 客户端已经崩溃，货已经关闭（down），或者正在重启过程中

在这两种情况下，其TCP都不会进行相应。服务器没有收到对其发出探测的响应，并在75秒后超时。服务器将总共发送10个这样的探测包，每个探测间隔75秒。如果没有收到一个响应，它就认为客户端主机已经关闭或是终止连接。

- 客户端曾经崩溃，单已经重启

该情况下，服务器将会收到对其存活探测的相应，但该相应是一个复位，从而引起服务器对连接的终止。

- 客户端主机活跃运行，但从服务器不可到达

该情况与2类似，因为TCP无法区别它们两个，其所表明的仅是未收到对其探测的回复。

- 客户端正常关闭

客户端TCP会在连接上发送一个FIN包，收到这个FIN后，服务器TCP向服务器进程报告一个文件结束，以允许服务器检测这种状态。


## 参考
- [Linux下TCP的Keepalive相关参数学习](http://www.linuxidc.com/Linux/2015-03/115321.htm)
- [TCP Keepalive HOWTO](http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/index.html)
- [linux下TCP keepalive 属性设置](http://blog.csdn.net/sunxiaopengsun/article/details/56842748)
- [linux下使用TCP存活(keepalive)定时器](http://blog.csdn.net/guomsh/article/details/8484222)
