---
layout: post
published: true
title: "pt-query-digest用法"
description: mysql, pt-query-digest
---
## 基本用法

```
Usage: pt-query-digest [OPTIONS] [FILES] [DSN]
```

pt-query-digest 可以用于分析slow log、general log和binary log，除此之外也可以分析<SHOW PROCESSLIST>中的内容以及tcpdump中抓取的MySQL协议数据。默认情况，数据结果会根据fingerprint聚合，并以查询时间倒序的顺序输出（最慢的先输出）。如果没有给定<FILES>，工具会从<STDIN>中读取。可选的<DSN>可以使用<--since>和<--until>来指定。

## 分析slow.log

```
pt-query-digest mysql.slow
```

## 分析processlist

```
pt-query-digest --processlist h=host1
```

## 分析MySQL协议

```
tcpdump -s 65535 -x -nn -q -tttt -i any -c 1000 port 3306 > mysql.tcp.txt
pt-query-digest --type tcpdump mysql.tcp.txt
```

## 保存数据

```
pt-query-digest --review h=host2 --no-report slow.log
```

## 输出格式
输出格式分为三部分：总体统计结果、查询分组统计结果、每一种查询的详细统计结果。

- 总体统计结果

```
# 59.5s user time, 90ms system time, 51.81M rss, 228.12M vsz
# Current date: Sun Aug  3 16:09:31 2017
# Hostname: mac..test.com
# Files: ./slowquery.log
# Overall: 224.01k total, 570 unique, 0.01 QPS, 0.09x concurrency ________
# Time range: 2013-10-09 17:55:04 to 2014-08-03 15:16:38
# Attribute          total     min     max     avg     95%  stddev  median
# ============     ======= ======= ======= ======= ======= ======= =======
# Exec time        2188385s      2s   7229s     10s     13s     67s      5s
# Lock time        100721s       0    365s   450ms      2s      5s   119us
# Rows sent         26.98G       0   3.30M 126.27k 328.61k 371.66k    4.96
# Rows examine     394.85G       0   3.32G   1.80M   3.03M  20.83M 562.03k
# Query size        78.65M       6   5.54k  368.18  685.39  241.61  271.23

```
vsz: 进程的虚拟内存大小
rss: 驻留集大小，可以理解为内存中页的数量
stddev: 标准偏差
Exec time: 执行时间
Lock time: 锁占用时间
Rows examine: 执行器需要检查的行数
Query size: 返回给客户端的结果集大小

- 查询的汇总信息（Profile）

```
# Profile
# Rank Query ID           Response time     Calls R/Call    V/M   Item
# ==== ================== ================= ===== ========= ===== ========
#    1 0xA6FE3D6982868655 351140.1266 16.0%   622  564.5340 99... CREATE TABLE dw_user_cache `dw_user_cache`
#    2 0x281C83DE62A11A8B 310896.7675 14.2% 40841    7.6124  6.51 SELECT dw_borrow_tender dw_borrow dw_user dw_credit dw_credit_rank
#    3 0x26BA6BEAE6C74350 279545.6534 12.8%  1305  214.2112 25... SELECT dw_account_log
#    4 0x54032826ADCE00D8 265383.3007 12.1% 27125    9.7837  0.75 SELECT dw_borrow dw_user
#    5 0xC6D55EBA716FD06A 155096.6704  7.1% 22411    6.9206  0.76 SELECT dw_borrow_tender dw_borrow_collection
```

Response: 总响应时间
time: 该查询在本次分析中总的时间占比
calls: 执行次数，即本次分析总共有多少条这种类型的查询语句
R/Call: 平均每次执行的响应时间
V/M: 响应时间的差异平均对比率
Item: 查询对象

- 每一类查询的详细统计结果

```
# Query 1: 0.00 QPS, 0.03x concurrency, ID 0xA6FE3D6982868655 at byte 3667736
# This item is included in the report because it matches --limit.
# Scores: V/M = 996.42
# Time range: 2013-10-28 21:15:52 to 2014-03-25 17:00:36
# Attribute    pct   total     min     max     avg     95%  stddev  median
# ============ === ======= ======= ======= ======= ======= ======= =======
# Count          0     622
# Exec time     16 351140s      2s   2518s    565s   2437s    750s    113s
# Lock time      0    105s       0     25s   169ms       0      1s       0
# Rows sent      0       0       0       0       0       0       0       0
# Rows examine   0       0       0       0       0       0       0       0
# Query size     0  54.67k      90      90      90      90       0      90
# String:
# Databases    test2
# Hosts
# Users        rx1919 (369/59%), k1818 (246/39%)... 1 more
# Query_time distribution
#   1us
#  10us
# 100us
#   1ms
#  10ms
# 100ms
#    1s  ###
#  10s+  ################################################################
# Tables
#    SHOW TABLE STATUS FROM `test2` LIKE 'dw_user_cache'G
#    SHOW CREATE TABLE `test2`.`dw_user_cache`G
CREATE TABLE IF NOT EXISTS `dw_user_cache` (
                         `user_id` int(11) NOT NULL DEFAULT '0')G

```

## 用法示例
- 直接分析慢查询文件

```
pt-query-digest slow.log > slow_report.log
``` 

- 分析12小时内的查询

```
pt-query-digest --since=12h slow.log > slow_report.log
```

- 分析指定时间范围内的查询

```
pt-query-digest --since '2014-04-17 09:30:00' --until '2014-04-17 10:00:00' > slow_report.log
```

- 分析含有select语句的慢查询

```
pt-query-digest --filter '$event->{fingerprint} =~ m/^select/i' slow.log > slow_report.log
```

- 针对某个用户的慢查询

```
pt-query-digest --filter '($event->{user}||"")=~ m/^root/i' slow.log > slow_report.log
```

- 查询所有的全表扫描或full join的慢查询

```
pt-query-digest --filter '(($event->{Full_scan}||"")eq "yes")||(($event->{Full_join}||"") eq "yes")' slow.log > slow_report.log
```

- 把查询保存到query_review表

```
pt-query-digest --user=root --password=abc123 --review h=localhost, D=test, t=query_review --create-review-table slow.log
```

- 吧查询保存到query_history表

```
pt-query-digest --user=root --password=abc123 --review h=localhost, D=test, t=query_history --create-review-table slow.log
```

- 通过tcpdump抓取MySQL的tcp协议数据，然后再分析

```
tcpdump -s 65535 -x -nn -q -tttt -i any -c 1000 port 3306 > mysql.tcp.txt
pt-query-digest --type tcpdump mysql.tcp.txt> slow_report.log
```

- 分析binlog

```
mysqlbinlog mysql-bin.000093 > mysql-bin000093.sql
pt-query-digest --type=binlog mysql-bin000093.sql > slow_report.sql
```

- 分析general log

```
pt-query-digest --type=genlog localhost.log > slow_report.log
```

## 其他
- 服务器摘要

```
pt-summary
```

- 服务器磁盘监测

```
pt-diskstats
```

- MySQL服务状态摘要

```
pt-mysql-summary --user=root --password=123456
```


## 参考
- [分析pt-query-digest输出结果](http://f.dataguru.cn/thread-338724-1-1.html)
- [MySQL慢查询之pt-query-digest分析慢查询日志](http://www.jb51.net/article/107698.htm)
