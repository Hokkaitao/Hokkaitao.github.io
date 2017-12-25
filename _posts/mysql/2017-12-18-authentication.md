---
layout: post
published: true
title: "mysql身份认证插件"
description: mysql, authentication
---
## 身份认证插件

当client连接到MySQL server时，server会使用client发送过来的user和host从mysql.user表中查找到对应的行。之后server对client进行身份认证，server会调用身份认证插件认证user，并且返回状态来决定是否认证成功。如果server不能找到认证插件，会返回错误并且拒绝当前连接。

权限认证插件


## 参考
- [Pluggable Authentication](https://dev.mysql.com/doc/refman/5.7/en/pluggable-authentication.html)
