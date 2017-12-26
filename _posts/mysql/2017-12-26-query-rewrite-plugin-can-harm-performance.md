---
layout: post
published: false
title: "Query Rewrite plugin can harm performance"
description: mysql, rewrite
---
## 原文

本文会讨论Query Rewrite 插件如何影响性能。

MySQL5.7引入了Query Rewrite插件，可以修改到达MySQL的查询。

该插件式基于audit插件API的，其遭受严重的可伸缩性的问题（这似乎是所有基于API的审计插件的情况）。

以下是使用sysbench OLTP RO情况下使用rewrite与不使用rewrite的对比，配置的规则如下：

```
INSERT INTO query_rewrite.rewrite_rules(pattern, replacement) VALUES('SELECT ?', 'SELECT ? + 1');
```

对比结果：

![sysbench OLTP RO](../../images/sysbench_oltp_ro.png)

正如你所见，Query Rewrite插件在线程数在100之后，无法进行扩展。

当我们查阅PMP profile，我们可以看到如下：

```
 170 __lll_lock_wait,__GI___pthread_mutex_lock,native_mutex_lock,my_mutex_lock,inline_mysql_mutex_lock,plugin_unlock_list,mysql_a
udit_release,handle_connection,pfs_spawn_thread,start_thread,clone
 164 __lll_lock_wait,__GI___pthread_mutex_lock,native_mutex_lock,my_mutex_lock,inline_mysql_mutex_lock,plugin_foreach_with_mask,m
ysql_audit_acquire_plugins,mysql_audit_notify,invoke_pre_parse_rewrite_plugins,mysql_parse,dispatch_command,do_command,handle_connec
tion,pfs_spawn_thread,start_thread,clone
77 __lll_lock_wait,__GI___pthread_mutex_lock,native_mutex_lock,my_mutex_lock,inline_mysql_mutex_lock,plugin_lock,acquire_plugin
s,plugin_foreach_with_mask,mysql_audit_acquire_plugins,mysql_audit_notify,invoke_pre_parse_rewrite_plugins,mysql_parse,dispatch_comm
and,do_command,handle_connection,pfs_spawn_thread,start_thread,clone
12 __lll_unlock_wake,__pthread_mutex_unlock_usercnt,__GI___pthread_mutex_unlock,native_mutex_unlock,my_mutex_unlock,inline_mysq
l_mutex_unlock,plugin_unlock_list,mysql_audit_release,handle_connection,pfs_spawn_thread,start_thread,clone
 10 __lll_unlock_wake,__pthread_mutex_unlock_usercnt,__GI___pthread_mutex_unlock,native_mutex_unlock,my_mutex_unlock,inline_mysq
l_mutex_unlock,plugin_lock,acquire_plugins,plugin_foreach_with_mask,mysql_audit_acquire_plugins,mysql_audit_notify,invoke_pre_parse_
rewrite_plugins,mysql_parse,dispatch_command,do_command,handle_connection,pfs_spawn_thread,start_thread,clone
 10 __lll_unlock_wake,__pthread_mutex_unlock_usercnt,__GI___pthread_mutex_unlock,native_mutex_unlock,my_mutex_unlock,inline_mysq
l_mutex_unlock,plugin_foreach_with_mask,mysql_audit_acquire_plugins,mysql_audit_notify,invoke_pre_parse_rewrite_plugins,mysql_parse,
dispatch_command,do_command,handle_connection,pfs_spawn_thread,start_thread,clone
7 __lll_lock_wait,__GI___pthread_mutex_lock,native_mutex_lock,my_mutex_lock,inline_mysql_mutex_lock,Table_cache::lock,open_tab
le,open_and_process_table,open_tables,open_tables_for_query,execute_sqlcom_select,mysql_execute_command,mysql_parse,dispatch_command
,do_command,handle_connection,pfs_spawn_thread,start_thread,clone
 6 __GI___pthread_mutex_lock,native_mutex_lock,my_mutex_lock,inline_mysql_mutex_lock,plugin_unlock_list,mysql_audit_release,han
dle_connection,pfs_spawn_thread,start_thread,clone
 6 __GI___pthread_mutex_lock,native_mutex_lock,my_mutex_lock,inline_mysql_mutex_lock,plugin_foreach_with_mask,mysql_audit_acqui
re_plugins,mysql_audit_notify,invoke_pre_parse_rewrite_plugins,mysql_parse,dispatch_command,do_command,handle_connection,pfs_spawn_t
hread,start_thread,clone
```

很清晰的看到这与audit插件API中获取的mutex有关我提交了一个bug(https://bugs.mysql.com/bug.php?id=81298)。但是令人沮丧的是，虽然InnoDB代码不断改进以实现更好的扩展性，但服务器的其他部分仍然可能受到全局mutex的影响。

该bug已经被官方确认：Audit 插件获取mutex的时候，是基于语句的，这严重影响了扩展性。为了增加性能，目前该类插件获取释放mutex是基于连接的了。

## 参考
- [Query Rewrite plugin can harm performance](https://www.percona.com/blog/2016/05/10/query-rewrite-plugin-can-harm-performance/)
