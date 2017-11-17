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

## 查找文件中的内容

- 递归从多个文件中进行搜索

```
grep "search content" filename1 filename2 filename3 
grep "search content" *.sql
```

- 显示搜索文本在文件中的行数

```
grep -n "v\$temp_space_header"
```

- 忽略大小写

```
grep -i "V\$TEMP_SPACE_HEADER"
```

- 从文件内容查找不匹配指定字符串的行

```
ps -ef|grep proxy|grep -v grep 
```

- 搜索匹配的行数

```
grep -c "v\$temp_space_header"
```

- 递归搜索

```
grep -r "v\$temp_space_header"
```

- 获取包含搜索内容的文件名

```
grep -H -r "v\$temp_space_header"
```

- 获得和整个搜索字符匹配的内容

```
grep -w "ORB" filename
```

- 搜索多个关键字

```
egrep -w -R 'word1|word2' ~/klbtmp
``` 

## 参考
- [数据需求统计常用shell命令---AWK分组求和，分组统计次数](http://6226001001.blog.51cto.com/9243584/1659824)
