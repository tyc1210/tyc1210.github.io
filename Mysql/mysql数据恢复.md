

##  数据恢复
利用mysqldump定时备份与恢复数据库
```shell
# 备份指令(可备份多个数据库或某个库部分表)
mysqldump -h ip地址 -P 端口号 -u 用户名 -p 数据库名称  > /var/lib/mysql/2021122414.sql
# 恢复指令
# mysql若部署在容器中 在容器外执行此语句 可能会由于版本原因报错
# 通过docker inspect 容器id 查看文件挂载路径
mysql -h 47.107.53.118 -P 33307 -u root -p  test < /var/lib/mysql/2021122414.sql
```
假如我们通过脚本每小时执行一次数据库备份，在14:40进行了误操作删除了表数据，此时我们通过定时备份将数据库恢复到14：00，那14：00到14：40之间的数据怎么办呢？此时我们就需要用到借助mysql的binlog(主从复制也需要binlog文件)来恢复这段时间的数据。前提是我们得开启了binlog日志，mysql8默认开启，查看指令(show variables like 'log_bin')。  binlog文件默认存储在data目录下文件名 例如：binlog.000004，binlog部分指令如下：

```mysql

-- 查看binlogs文件
show binary logs;

-- 查看当前正在写入的binlog文件 
show master status

-- 查看指定binlog文件的内容
show binlog events in 'binlog.000004'

-- 查看指定binlog文件详细信息
mysqlbinlog binlog文件名

-- 通过position或者时间从binlog文件导出指定sql
mysqlbinlog binlog文件名  --start-position=开始位置 --stop-position=结束位置 > /var/lib/mysql/logback.sql

mysqlbinlog binlog文件名  --start-datetime=开始时间 --stop-datetime=结束时间 > /var/lib/mysql/logback.sql

-- 运行导出的sql语句
mysql -h ip的地址 -P 端口号 -u 用户名 -p  数据库名 < /var/lib/mysql/logback.sql
```