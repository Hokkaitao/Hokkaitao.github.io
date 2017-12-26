---
layout: post
published: true
title: "InnoDB gap locks"
description: gap, innodb
---
## 原文

InnoDB最重要的特征之一就是row level locking。该特性在写负载很大的情况下增加了并发度，但是需要额外措施防止幻读，并且基于语句复制的一致性。因此row level lock需要gap locks。

- 什么是幻读

幻读通常发生在事务中，两个一样的语句获得不同的值，这还犹豫一些事务修改了表的行。例如：

```
transaction1>start transaction;
transaction1>select * from t where i>20 for update;
+------+
| i |
+------+
| 21 |
| 25 |
| 30 |
+------+

transaction2>start transaction;
transaction2>insert into t values (26);
transaction2>commit;

transaction1>select * from t where i>20 for update;
+------+
| i |
+------+
| 21 |
| 25 |
| 26 |
| 30 |
+------+
```

如果只是select操作时，是不会发生幻读的。只有执行UPDATE/DELETE/SELECT FOR UPDATE的时候才有可能发生。InnoDB对于只读SELECT提供了Repeatable Read，但其行为就像对所有写查询使用READ COMMITTED一样，尽管选择了十五个例界别（只考虑两个常见的隔离级别REPEATABLE READ/READ COMMIT）。

- 什么事gap lock

gap lock只一个存在在索引记录之间的锁。由于gap lock的存在，执行形同查询两次，会得到一致的结果，而忽略其他session在表上的修改。这会保证一致性读，且保证复制时保证主从一致性。如果执行SELECT * FROM id>1000 FOR UPDATE两次，你希望获得两次一样的执行结果。为了保证这样，InnoDB会将WHERE条件符合的索引记录加上排它锁，并且在期间加上共享gap lock。

这个锁不只会影响SELECT ... FOR UPDATE。以下是DELETE语句：

```
transaction1>select * from t;
+------+
| age |
+------+
| 21 |
| 25 |
| 30 |
+------+

transaction1>start transaction;
transaction1>delete from t where age=25;

transaction2>start transaction;
transaction2>insert into t values (21);//error
transaction2>insert into t values (23);//error
transaction2>insert into t values (26);//error
transaction2>insert into t values (29);//error
transaction2>insert into t values (30);//ok
```

在第一个session执行DELETE语句之后，不止受影响的索引记录被锁定了，在该记录的前面和后面也使用共享gap lock锁住，防止在其他session中进行插入操作。

- 如果解决gap locl?

可以使用命令：SHOW ENGINE INNODB STATUS命令来查看gap lock。

如果在事务中存在很多gap lock，会影响并发和性能。你可以使用两种方法来关闭：

```
1 修改隔离级别到Read Committed。
2 innodb_locks_unsafe_for_binlog=1 除外键约束检查或重复键检查外，进制使用cap lock
```

上述两种方法不同点在于第二个方法修改的是全局变量，会影响所有的session并且需要重启server生效。两种方法都会引起幻读，因此为了防止复制的问题，应该将binlog 的格式修改为row。


## 参考
- [InnoDB gap locks](https://www.percona.com/blog/2012/03/27/innodbs-gap-locks/)
