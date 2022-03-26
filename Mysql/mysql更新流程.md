### 一条更新语句执行流程

1. sql语句依次经过连接器、分析器、优化器、执行器、存储引擎
2. innodb 存储引擎在BufferPool查询这条数据是否存在，不存在则从磁盘加载放入BufferPool
3. 将数据记录到undolog(刷磁盘)，为了事务回滚
4. 对数据进行修改后将其记录到redolog(写入内存redolog buffer)，进入待提交状态
   - 刷磁盘可以通过 innodb_flush_log_at_trx_commit 参数来设置
     - 0 每次事务提交时都只是把 redo log 留在 redo log buffer 中，每秒写入page cache然后刷盘
     - 1 表示立即刷入磁盘
     - 2 表示每次事务提交时都只是把 redo log 写到 page cache，每秒执行一次刷盘
5. mysqlserver将本次修改记录到binlog buffer
   - 刷盘由参数sync_binlog 控制
     - 0 表示每次提交事务写入缓存，由操作系统决定何时刷盘
     - 1表示每次提交事务都会刷入磁盘
     - 表示每次提交事务都写入缓存，累积 N 个事务后进行刷盘
6. 将bin log文件名、更新内容在bin log中的位置记录到redo log，在redo log最后添加commit标记



### 什么是双1原则？

配置redo log（ innodb_flush_log_at_trx_commit） 与 bin log (sync_binlog) 均为1，立即刷盘保证数据安全性，但性能较低

根据需求选择合适的配置，设置为 0 100 会极大提升性能

```mysql
-- 查询日志写入时机配置
SHOW VARIABLES LIKE '%sync_binlog%'
SHOW VARIABLES LIKE '%innodb_flush_log_at_trx_commit%'
```



### redo Log与bin Log区别

| 性质     | redo Log         | bin Log            |
| -------- | ---------------- | ------------------ |
| 实现     | `InnoDB`引擎层   | MySQL  层          |
| 记录方式 | 大小固定，循环写 | 追加的方式记录     |
| 使用场景 | 崩溃恢复         | 主从复制和数据恢复 |







​	