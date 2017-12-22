---
layout: post
published: true
title: "Mac基于Docker的proxy开发测试过程"
description: proxy, docker
---
## 安装Docker

下载安装Docker for Mac

```
https://download.docker.com/mac/stable/Docker.dmg
``` 

如果之前安装过virtualbox，需要升级virtualbox或卸载virtualbox。卸载virtualbox流程：进入Applications目录，右键点击virtualbox，选择move to trash，另外需要重启系统。

## 创建Docker

- 获取基础的centos image

```
bash>docker pull centos:6.6
```

- 辅助文件：sankuai.repo、my.cnf、mysql_init.sh、mysql_init.sh

XX.repo

```
[sankuai]
name=XX Repo
baseurl=http://YY
enabled=1
gpgcheck=0
metadata_expire=120
priority=1
```

Percona56.repo

```
[Percona56]
name=Percona56 Repo
baseurl=http://mirrors.XX
enabled=1
gpgcheck=0
priority=1
```

my.cnf

```
[mysql]
port=3306
 
[client]
port=3306
socket=/opt/tmp/mysql.sock
 
[mysqld]
#dir
basedir=/usr
datadir=/opt/local/mysql/var/
tmpdir=/opt/tmp
log-error=mysql.err
slow_query_log_file=/opt/tmp/mysql.slow
general_log_file=/opt/tmp/mysql.genlog
socket=/opt/tmp/mysql.sock
pid-file=/opt/local/mysql/var/mysql.pid
 
#innodb
innodb_file_format=Barracuda
innodb_data_home_dir=/opt/local/mysql/var/
innodb_log_group_home_dir=/opt/local/mysql/var/
innodb_data_file_path=ibdata1:100M:autoextend
innodb_buffer_pool_size=1G
innodb_buffer_pool_instances=4
 
 
#replication
skip-slave-start
relay-log=relay
slave_load_tmpdir=/opt/tmp
 
#binlog
log-bin=mysql-bin
binlog_cache_size=1M
max_binlog_cache_size=2G
max_binlog_size=1024M
binlog-format=ROW
#sync_binlog=1
log-slave-updates
expire_logs_days=7
 
#server
default-storage-engine=INNODB
innodb_stats_on_metadata=OFF
 
#because bug https://bugs.launchpad.net/percona-server/+bug/1199213,default is 1
innodb_adaptive_hash_index_partitions=1
 
#suppress warning
log_warnings_suppress=1592
 
#on have performance problem.
performance_schema=OFF
#gtid
gtid_mode=ON
enforce-gtid-consistency=ON
  
#userstat
userstat=ON
```

mysql_init.sh

```
#!/bin/bash
set -ue
mkdir -p /opt/local/mysql/var
mkdir -p /opt/tmp
chown -R mysql:mysql /opt/local/mysql/var
chown -R mysql:mysql /opt/tmp
mysql_install_db --datadir=/opt/local/mysql/var --user=mysql 
```

Dockerfile

```
FROM centos:6.6
ADD sankuai.repo /etc/yum.repos.d/
ADD Percona56.repo /etc/yum.repos.d/
RUN yum clean all
RUN yum install -y openssh vim libevent-devel libevent-headers libevent-doc openssl-devel
RUN yum install -y Percona-Server-server-56 Percona-Server-devel-56.x86_64 Percona-Server-client-56.x86_64 Percona-Server-shared-56
RUN yum install -y libevent openssl glib2 glib2-devel jemalloc jemalloc-devel lua lua-devel gcc make gdb bison flex
ADD my.cnf /etc/my.cnf
ADD mysql_init.sh /tmp/mysql_init.sh
RUN sh /tmp/mysql_init.sh
```

- build image创建命令

```
bash>docker build -t proxy-dev:0.2 .
```

- 查看docker image

```
bash>docker images
```

- 挂载外部目录运行（假设proxy代码在/Users/guest/code目录中），将目录挂载内部的系统中，便于在内部编译安装proxy

```
bash> docker run -v ~/workstation:/data -it -d --name proxy proxy-dev:0.2 /bin/bash
```

- 运行方式

```
bash>docker exec -it proxy /bin/bash
```

## 基于Docker的proxy编译安装流程

```
bash> cd /data/proxy/dbproxy
bash> sh bootstrap.sh
bash> make
bash> make install
bash> mkdir -p /usr/local/mysql-proxy/conf
bash> cp script/source.cnf.samples /usr/local/mysql-proxy/conf/source.cnf
bash> cd /usr/local/mysql-proxy
bash> vi /usr/local/mysql-proxy/conf/source.cnf (调整文件)
bash> bin/mysql-proxyd source start
```

## 注意事项

- 启动mysqld

```
/etc/init.d/mysql start
grant all on *.* to 'guest'@'localhost' identified by 'guest';
```

- 查看正在运行的containerid

```
docker ps
```

- Docker内部不允许使用gdb，会报错“ptrace: Operation not permitted.”错误

可以使用下面的方法解决：

```
1 Run the container with --privileged (insecure)
2 Run the container with --security-opt seccomp:unconfined (this turns off seccomp altogether)
3 Create your own seccomp profile to allow the proper syscalls for gdb to function
(https://github.com/docker/docker/blob/master/docs/security/seccomp.md)
```
