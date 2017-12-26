---
layout: post
published: true
title: "索引的操作"
description: index, mysql
---
## 索引的作用

一句话：空间换时间

## 创建索引

在执行create table语句时可以创建索引，也可以单独使用create index或是alter table来为表增加索引

- ALTER TABLE

ALTER TABLE用来创建普通索引、UNIQUE索引或是PRIMARY KEY索引。

```
ALTER TABLE table_name ADD INDEX index_name (column_list);
ALTER TABLE table_name ADD UNIQUE index_name (column_list);
ALTER TABLE table_name ADD PRIMARY KEY index_name (column_list);
```

另外，ALTER TABLE允许在单个语句中更改多个表，因此可以同时创建多个索引。

- CREATE INDEX

CREATE INDEX可对表增加普通索引或是UNIQUE索引。

```
CREATE INDEX index_name ON table_name (column_list);
CREATE UNIQUE INDEX index_name ON table_name (column_list);
```

- 删除索引

可以利用ALTER TABLE或DROP INDEX语句来删除索引。类似于CREATE INDEX 语句，DROP INDEX可以在ALTER TABLE内部作为一条语句处理，语法如下：

```
DROP INDEX index_name ON table_name;
ALTER TABLE table_name DROP INDEX index_name;
ALTER TABLE table_name DROP PRIMARY KEY;
```

第3条语句只在PRIMARY KEY索引时使用，因为一个表只可能有一个PRIMARY KEY索引，因此不需要指定索引名。如果没有创建PRIMARY KEY索引，单表具有一个或多个UNIQUE索引，则MySQL将删除第一个UNIQUE索引。

如果从表中删除了某列，则索引会受到影响。对于多列组合的索引，如果删除其中的某列，则该列也会从索引中删除。如果删除组成索引的所有列，则整个索引将被删除。

- 查看索引

```
SHOW INDEX FROM tblname;
SHOW KEYS FROM tblname;
```

显示结果中，Cardinality表示索引中为抑制的数据的估计值。通过运行ANALYZE TABLE或myisamchk -a可以更新。技术根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL使用该索引的机会就越大。

## 参考
- [MySQL查看、创建和删除索引的办法](http://www.jb51.net/article/73372.htm)
