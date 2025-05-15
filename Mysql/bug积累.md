[root@master yum.repos.d]# service mysqld start
Starting MySQL.2025-03-16T13:41:07.495397Z mysqld_safe error: log-error set to '/var/log/mariadb/mariadb.log', however file don't exists. Create writable for user 'mysql'.
 ERROR! The server quit without updating PID file (/usr/local/mysql/data/master.it.cn.pid).
[root@master yum.repos.d]# rm /etc/my.cnf
rm：是否删除普通文件 "/etc/my.cnf"？y
[root@master yum.repos.d]# service mysqld start
Starting MySQL.. SUCCESS!



mysql启动报错，删除原来/etc/my.cnf文件

但是会出现

[root@slave etc]# mysql -p
Enter password: 
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)

service mysqld start
Starting MySQL... ERROR! The server quit without updating PID file (/usr/local/mysql/data/master.it.cn.pid).

解决：

support-files目录下的mysql.server 执行的是 mysqld_safe 而不是 mysqld

使用vim查看mysql.server的内容

找到第281行 可以看出,执行参数start时,运行的是$bindir目录下的/mysqld_safe 程序

由于我是所有操作都是用root 用户执行的, (这是个坑)

而用root用户执行mysqld 跟mysqld_safe 不加--user=root参数 指定用户时会报错的

添加--user=root参数即可

```
echo $echo_n "Starting MySQL"
    if test -x $bindir/mysqld_safe
    then
      # Give extra arguments to mysqld with the my.cnf file. This script
      # may be overwritten at next upgrade.
      $bindir/mysqld_safe --user=root --datadir="$datadir" --pid-file="$mysqld_pid_file_path" $other_args >/dev/null &
      wait_for_pid created "$!" "$mysqld_pid_file_path"; return_value=$?
```

(https://blog.csdn.net/zqin0/article/details/106444580)







2.start slave;
ERROR 1872 (HY000): Slave failed to initialize relay log info structure from the repository

因为之前同步过了，一台主机再次执行

change master to master_host='192.168.10.56',master_port=3306,master_user='slave',master_password='123456',master_auto_position=1;

导致这次再次执行change master to，2次的pos值不一样，

解决：stop slave 停止slave,执行

reset slave;
Query OK, 0 rows affected (0.01 sec)

再次

change master to master_host='192.168.10.56',master_port=3306,master_user='slave',master_password='123456',master_auto_position=1;
Query OK, 0 rows affected, 2 warnings (0.03 sec)

mysql>  start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status\G

即可