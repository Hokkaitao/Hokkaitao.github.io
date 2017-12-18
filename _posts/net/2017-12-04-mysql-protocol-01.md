---
layout: post
published: true
title: "mysql protocol 解析"
description: mysql protocol, analyse
---
## 连接建立

- 抓包

```
tcpdump -i eth1 "host 10.32.89.22 and tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"
./sniffer eth1 3306 "host 10.32.89.22"
```

- 抓包结果

```
## net握手
18:07:57.860168 IP aa.com.47974 > bb.mysql: Flags [S], seq 1788258507, win 14600, options [mss 1460,sackOK,TS val 1086162288 ecr 0,nop,wscale 7], length 0
18:07:57.860197 IP bb.mysql > aa.com.47974: Flags [S.], seq 3290868786, ack 1788258508, win 14480, options [mss 1460,sackOK,TS val 2640222171 ecr 1086162288,nop,wscale 7], length 0
18:07:57.861395 IP aa.com.47974 > bb.mysql: Flags [.], ack 1, win 115, options [nop,nop,TS val 1086162289 ecr 2640222171], length 0
## app握手
18:07:57.861516 IP bb.mysql > aa.com.47974: Flags [P.], seq 1:86, ack 1, win 114, options [nop,nop,TS val 2640222173 ecr 1086162289], length 85
18:07:57.862735 IP aa.com.47974 > bb.mysql: Flags [.], ack 86, win 115, options [nop,nop,TS val 1086162290 ecr 2640222173], length 0

18:07:57.863211 IP aa.com.47974 > bb.mysql: Flags [P.], seq 1:85, ack 86, win 115, options [nop,nop,TS val 1086162291 ecr 2640222173], length 84
18:07:57.863229 IP bb.mysql > aa.com.47974: Flags [.], ack 85, win 114, options [nop,nop,TS val 2640222174 ecr 1086162291], length 0

18:07:57.863269 IP bb.mysql > aa.com.47974: Flags [P.], seq 86:97, ack 85, win 114, options [nop,nop,TS val 2640222174 ecr 1086162291], length 11
18:07:57.864454 IP aa.com.47974 > bb.mysql: Flags [P.], seq 85:122, ack 97, win 115, options [nop,nop,TS val 1086162292 ecr 2640222174], length 37

## select 
18:07:57.864563 IP bb.mysql > aa.com.47974: Flags [P.], seq 97:222, ack 122, win 114, options [nop,nop,TS val 2640222176 ecr 1086162292], length 125
18:07:57.905204 IP aa.com.47974 > bb.mysql: Flags [.], ack 222, win 115, options [nop,nop,TS val 1086162333 ecr 2640222176], length 0
```

```
port:47974 => port:3306, len = 0
FIN:0 SYN:2 RST:0 PUSH:0 ACK:0 URG:0
syn:1788258507

port:3306 => port:47974, len = 0
FIN:0 SYN:2 RST:0 PUSH:0 ACK:16 URG:0
syn:3290868786
ack:1788258508

port:3306 => port:47974, len = 85
FIN:0 SYN:0 RST:0 PUSH:8 ACK:16 URG:0
ack:1788258508

port:47974 => port:3306, len = 84
FIN:0 SYN:0 RST:0 PUSH:8 ACK:16 URG:0
ack:3290868872

port:3306 => port:47974, len = 11
FIN:0 SYN:0 RST:0 PUSH:8 ACK:16 URG:0
ack:1788258592

port:47974 => port:3306, len = 37
FIN:0 SYN:0 RST:0 PUSH:8 ACK:16 URG:0
ack:3290868883

port:3306 => port:47974, len = 125
FIN:0 SYN:0 RST:0 PUSH:8 ACK:16 URG:0
ack:1788258629
```



