
<span style="font-size:14px;">[root@iZuzc9f4ma6h2iZ ~]# service mysqld restart<strong>
</strong>MySQL server PID file could not be found!                  [FAILED]
Starting MySQL.Logging to '/var/log/mysql/error.log'.
The server quit without updating PID file (/data/mysql/iZuz[FAILED]2iZ.pid).</span>

第一直觉告诉我google “ MySQL server PID file could not be found!  [FAILED]”，可基本没什么用。
后开有人说看看报错信息，于是：

<span style="font-size:14px;">[root@iZuzc9f4ma6h2iZ ~]# tail -n 50 /data/mysql/iZuzc9f4ma6h2iZ.err
InnoDB: mmap(137363456 bytes) failed; errno 12
2017-12-28 17:09:44 25065 [ERROR] InnoDB: Cannot allocate memory for the buffer pool
2017-12-28 17:09:44 25065 [ERROR] Plugin 'InnoDB' init function returned error.
2017-12-28 17:09:44 25065 [ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
2017-12-28 17:09:44 25065 [ERROR] Unknown/unsupported storage engine: InnoDB
2017-12-28 17:09:44 25065 [ERROR] Aborting</span>

这里需要解释一下，我直接贴上来了

Out of memory error: InnoDB: Fatal error: cannot allocate memory for the buffer pool
InnoDB can't start without enough memory [ERROR] Plugin 'InnoDB' init function returned error.[ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.[ERROR] Unknown/unsupported storage engine: InnoDB[ERROR] Aborting
mysqld is shutting down, which in this context, really means failing to restart! [Note] /usr/sbin/mysqld: Shutdown complete
ok，明白了吗？
于是继续，来看看内存占用情况：

<span style="font-size:18px;">[root@iZuzc9f4ma6h2iZ ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:            992         435         413           1         142         403
Swap:             0           0           0</span>
显然，结果很磕馋。内存仅413M，而且没有 Swap 分区。

那么问题来了，mysql 启动到底需要多少内存？

官网是这什么回答的：



How MySQL Uses Memory？


MySQL allocates buffers and caches to improve performance of database operations. The default configuration is designed to permit a MySQL server to start on a virtual machine that has approximately512MB of RAM. You can improve MySQL performance by increasing the values of certain cache and buffer-related system variables. You can also modify the default configuration to run MySQL on systems with limited memory.

于是，那么有什么办法可以小内存启动 mysql 吗？

答案是有，

vim /etc/my.cnf

添加：

performance_schema_max_table_instances=200
table_definition_cache=200
table_open_cache=128

ok，再次运行 service mysqld restart ，没什么问题。

原文：https://blog.csdn.net/QQ648472886/article/details/78936040 
