---
layout: post
published: true
title: "日志分析常见方法"
description: awk
---
## 集合操作

![awk_collect](../../images/awk_collect.png)

- 交

```
cat f1 f2|sort|uniq -d > r.log
```

- 差

```
cat f1 f2|sort|uniq -d > t.log
cat f1 t.log |sort|uniq -u > r.log
```

- 全集去重

```
cat f1 f2|sort -u > r.log
```

- 全集不去重

```
cat f1 f2|sort > r.log
```


## 参考
- [数据需求统计常用shell命令---AWK分组求和，分组统计次数](http://6226001001.blog.51cto.com/9243584/1659824)
