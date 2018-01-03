---
layout: post
published: true
title: "github的基本配置"
description: github
---
## SSH key 生成

```
# 检查SSH key是否存在
ls -al ~/.ssh

# 生成新的SSH key
ssh-keygen -t rsa -C "your_email@example.com"

# 添加SSH key到Github
cat ~/.ssh/id_rsa.pub
pbcopy < ~/.ssh/id_rsa.pub~
```

## Git 配置用户名/邮件地址

```
git config --global user.email "your_email@example.com"
git config --global user.name "Your Name"
```


## 参考
- [如何生成SSH key 及访问Github](https://www.jianshu.com/p/21234432c94e)
- [Git配置用户名/邮箱地址访问Github](http://blog.csdn.net/nicolelili1/article/details/52606144)
