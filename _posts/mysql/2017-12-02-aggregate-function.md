---
layout: post
published: true
title: "MySQL聚合函数(GROUP BY)"
description: mysql, aggregate
---
## AVG([DISTINCT] expr)

```
平均
select c, avg(e) from tb group by c;
```

## BIT_AND(expr)

```
按位与
select c, bit_and(c) from tb group by c;
```

## BIT_OR(expr)

```
按位或
select c, bit_or(c) from tb group by c;
```

## BIT_XOR(expr)

```
按位亦或
select c, bit_xor(c) from tb group by c;
```

## COUNT(expr)

```
求次数
select c, count(c) from tb group by c;
```

注意：
- COUNT(expr)会返回non-NULL的数量，但是COUNT(*)无论是否是NULL就进行计算。
- MyISAM表对COUNT(*)进行了优化，如果不获取任何列且没有WHERE语句。这是因为MyISAM存储引擎存储了准确的行数。

## COUNT(DISTINCT expr[, expr ...])

```
non-NULL的函数
select c, count(distinct c) from student;
```

## GROUP_CONCAT(expr)

```
字符串聚合拼接
GROUP_CONCAT([DISTINCT] expr [,expr ...]
             [ORDER BY {unsigned_integer | col_name | expr}
                 [ASC | DESC] [,col_name ...]]
             [SEPARATOR str_val])
```

## MAX([DISTINCT] expr)

```
最大值
select c, max(d) from tb group by c;
```

## MIN([DISTINCT] expr) 

```
最小值
select c, min(d) from tb group by c;
```

## STD(expr)

```
总体标准偏差
select c, std(c) from tb group by c;
```

## STDDEV(expr)

```
总体标准偏差，与Oracle兼容
select c, stddev(c) from tb group by c;
```

## STDDEV_POP(expr)

```
总体标准偏差
select c, stddev_pop(c) from tb group by c;
```

## STDDEV_SAMP(expr)

```
样本标准差
select c, stddev_samp(c) from tb group by c;
```

## SUM([DISTINCT expr])

```
求和
select c, sum(distinct c) from tb group by c;
```

## VAR_POP(expr)

```
总体标准方差
```

## VAR_SAMP(expr)

```
样本方差
```

## VARIANCE(expr)

```
总体标准方差
```

## 参考
-[mysql中字符串聚合连接](http://blog.csdn.net/tangtong1/article/details/50996905)
