---
layout: post
published: true
title: "pt工具的安装"
description: mysql, packet
---
## 检查和安装与Perl相关的模块

PT工具是使用Perl语言编写和执行的，所以需要系统中有perl环境，依赖包检查命令为：

```
>rpm -qa perl-DBI perl-DBD-MySQL perl-Time-HiRes perl-IO-Socket-SSL
perl-IO-Socket-SSL-1.31-3.el6_8.2.noarch
perl-DBI-1.622-1.el6.rfx.x86_64
perl-Time-HiRes-1.9721-141.el6_7.1.x86_64
perl-DBD-MySQL-4.013-3.el6.x86_64
```

如果有依赖包，可以使用下面命令进行安装：

```
yum install perl-DBI
yum install perl-DBD-MySQL
yum install perl-Time-HiRes
yum install perl-IO-Socket-SSL
```

注意，需要安装Term::ReadKey包，否则会报perl(Term::ReadKey) is needed by percona-toolkit-2.2.14-1.noarch

```
wget http://pkgs.repoforge.org/perl-TermReadKey/perl-TermReadKey-2.30-1.el3.rf.x86_64.rpm
rpm -ivh perl-TermReadKey-2.30-1.el3.rf.x86_64.rpm

wget https://www.percona.com/downloads/percona-toolkit/2.2.14/RPM/percona-toolkit-2.2.14-1.noarch.rpm
rpm -ivh percona-toolkit-2.2.14-1.noarch.rpm

## http://pkgs.repoforge.org/perl-TermReadKey/（key
## https://www.percona.com/downloads/percona-toolkit/ （tool）
```

安装成功后可以通过上面命令确认：

```
pt-query-digest --help
pt-table-checksum --help
```

## pt工具的整体介绍

<table>
<tr>
<td>
<p>工具类别</p>
</td>
<td>
<p>工具命令</p>
</td>
<td>
<p>工具作用</p>
</td>
<td>
<p>备注</p>
</td>
</tr>
<tr>
<td rowspan="5">
<p>开发类</p>
</td>
<td>
<p><span lang="EN-US">pt-duplicate-key-checker</span></p>
</td>
<td>
<p>列出并删除重复的索引和外键</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-online-schema-change</span></p>
</td>
<td>
<p>在线修改表结构</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-query-advisor</span></p>
</td>
<td>
<p>分析查询语句，并给出建议，有<span lang="EN-US">bug</span></p>
</td>
<td>
<p>已废弃</p>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-show-grants</span></p>
</td>
<td>
<p>规范化和打印权限</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-upgrade</span></p>
</td>
<td>
<p>在多个服务器上执行查询，并比较不同</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td rowspan="4">
<p>性能类</p>
</td>
<td>
<p><span lang="EN-US">pt-index-usage</span></p>
</td>
<td>
<p>分析日志中索引使用情况，并出报告</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-pmp</span></p>
</td>
<td>
<p>为查询结果跟踪，并汇总跟踪结果</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-visual-explain</span></p>
</td>
<td>
<p>格式化执行计划</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-table-usage</span></p>
</td>
<td>
<p>分析日志中查询并分析表使用情况</p>
</td>
<td>
<p><span lang="EN-US">pt 2.2新增命令</span></p>
</td>
</tr>
<tr>
<td rowspan="3">
<p>配置类</p>
</td>
<td>
<p><span lang="EN-US">pt-config-diff</span></p>
</td>
<td>
<p>比较配置文件和参数</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-mysql-summary</span></p>
</td>
<td>
<p>对<span lang="EN-US">mysql配置和<span lang="EN-US">status进行汇总</span></span></p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-variable-advisor</span></p>
</td>
<td>
<p>分析参数，并提出建议</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td rowspan="5">
<p>监控类</p>
</td>
<td>
<p><span lang="EN-US">pt-deadlock-logger</span></p>
</td>
<td>
<p>提取和记录<span lang="EN-US">mysql死锁信息</span></p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-fk-error-logger</span></p>
</td>
<td>
<p>提取和记录外键信息</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-mext</span></p>
</td>
<td>
<p>并行查看<span lang="EN-US">status样本信息</span></p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-query-digest</span></p>
</td>
<td>
<p>分析查询日志，并产生报告</p>
</td>
<td>
<p>常用命令</p>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-trend</span></p>
</td>
<td>
<p>按照时间段读取<span lang="EN-US">slow日志信息</span></p>
</td>
<td>
<p>已废弃</p>
</td>
</tr>
<tr>
<td rowspan="6">
<p>复制类</p>
</td>
<td>
<p><span lang="EN-US">pt-heartbeat</span></p>
</td>
<td>
<p>监控<span lang="EN-US">mysql复制延迟</span></p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-slave-delay</span></p>
</td>
<td>
<p>设定从落后主的时间</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-slave-find</span></p>
</td>
<td>
<p>查找和打印所有<span lang="EN-US">mysql复制层级关系</span></p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-slave-restart</span></p>
</td>
<td>
<p>监控<span lang="EN-US">salve错误，并尝试重启<span lang="EN-US">salve</span></span></p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-table-checksum</span></p>
</td>
<td>
<p>校验主从复制一致性</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-table-sync</span></p>
</td>
<td>
<p>高效同步表数据</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td rowspan="6">
<p>系统类</p>
</td>
<td>
<p><span lang="EN-US">pt-diskstats</span></p>
</td>
<td>
<p>查看系统磁盘状态</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-fifo-split</span></p>
</td>
<td>
<p>模拟切割文件并输出</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-summary</span></p>
</td>
<td>
<p>收集和显示系统概况</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-stalk</span></p>
</td>
<td>
<p>出现问题时，收集诊断数据</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-sift</span></p>
</td>
<td>
<p>浏览由<span lang="EN-US">pt-stalk创建的文件</span></p>
</td>
<td>
<p><span lang="EN-US">pt 2.2新增命令</span></p>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-ioprofile</span></p>
</td>
<td>
<p>查询进程<span lang="EN-US">IO并打印一个<span lang="EN-US">IO活动表</span></span></p>
</td>
<td>
<p><span lang="EN-US">pt 2.2新增命令</span></p>
</td>
</tr>
<tr>
<td rowspan="5">
<p>实用类</p>
</td>
<td>
<p><span lang="EN-US">pt-archiver</span></p>
</td>
<td>
<p>将表数据归档到另一个表或文件中</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-find</span></p>
</td>
<td>
<p>查找表并执行命令</p>
</td>
<td>
<div>&nbsp;</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-kill</span></p>
</td>
<td>
<p><span lang="EN-US">Kill掉符合条件的<span lang="EN-US">sql</span></span></p>
</td>
<td>
<div>常用命令</div>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-align</span></p>
</td>
<td>
<p>对齐其他工具的输出</p>
</td>
<td>
<p><span lang="EN-US">pt 2.2新增命令</span></p>
</td>
</tr>
<tr>
<td>
<p><span lang="EN-US">pt-fingerprint</span></p>
</td>
<td>
<p>将查询转成密文</p>
</td>
<td>
<p><span lang="EN-US">pt 2.2新增命令</span></p>
</td>
</tr>
</table>




## 参考
- [percona-toolkit工具包的安装和使用](https://www.cnblogs.com/zping/p/5678652.html)

