**redis**

Redis诞生于2009年全称是Remote Dictionary Server,远程词典服务器，是一个基于

内存的键值型NoSQL数据库。

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/sql和nosql对比.png)

**Redis特征**

**高性能**

Redis 是一个使用 C 语言编写的高性能键值数据库，它采用了很多优化措施，例如：使用内存作为数据存储介质、

采用单线程模型、异步 I/O 等技术，从而达到非常高的读写性能。

**支持多种数据结构**

Redis 不像传统关系型数据库那样只支持表格模型，它支持多种数据结构，包括字符串、列表、集合、有序集合

和哈希表等，适用于不同的应用场景。

**支持数据持久化**

Redis 支持数据持久化，可以将内存中的数据异步地保存到硬盘上，保证数据不会在程序重启时丢失。同时，

Redis 还支持 RDB 和 AOF 两种持久化方式，更加灵活可靠。

**支持数据复制**

Redis 支持数据复制，可以将一份数据复制到多台机器上，提高系统的可用性和可靠性。

**支持分布式对象缓存**

Redis 支持分布式对象缓存（Cache），可以缓存常用数据对象，避免频繁地查询数据库，提高系统的读取性能。

**支持事务操作**

Redis 支持事务操作，可以在一次请求中执行多个命令，从而保证在多个命令之间的数据一致性。

安装redis

 https://redis.io/download

下载安装包

 wget http://download.redis.io/releases/redis-7.2.3.tar.gz

**①上传安装包**

```
\# cd /opt/software/
```

**②安装依赖包**

```
\# yum -y install gcc tcl
```

**③解压安装包**

```
\# tar -xvf redis-7.2.3.tar.gz 
```

**⑤编绎并安装**

```
# cd redis-7.2.3/
#make &&  make install
#或者
# make
# make PREFIX=/usr/local/redis install
```

这里多了一个关键字 PREFIX= 这个关键字的作用是编译的时候用于指定程序存放的路径。比如我们现在就是指定了redis必须存放在/usr/local/redis目录。假设不添加该关键字Linux会将可执行文件存放在/usr/local/bin目录，

库文件会存放在/usr/local/lib目录。配置文件会存放在/usr/local/etc目录。其他的资源文件会存放在usr/local/share目录。这里指定号目录也方便后续的卸载，后续直接rm -rf /usr/local/redis 即可删除redis。

**默认安装目录**

```
\# ll /usr/local/bin/
```

该目录以及默认配置到环境变量，因此可以在任意目录下运行这些命令

redis-cli: 是redis提供的命令行客户端

redis-server: 是redis的服务端启动脚本

redis-sentinel: 是redis的哨兵启动脚本

**⑥启动redis**

\# redis-server

注意:**默认是在前台启动，如果ctrl + c 则redis 会自动停止**

**指定配置启动**

备份原来的配置文件

\# cp redis.conf redis.conf.bak

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/redis常用配置.png)

**修改redis配置文件**

\# vim redis.conf

bind 0.0.0.0

protected-mode no

daemonize yes

requirepass 123456

logfile "redis.log"

**启动redis**

\# redis-server /opt/software/redis-7.2.3/redis.conf

\# ps -ef |grep redis

关闭redis

\# redis-cli -a 123456 shutdown

登录redis

\# redis-cli -a 123456



配置开机自启动

\# vim /etc/systemd/system/redis.service

```
[Unit]

Description=redis-server

After=network.target

[Service]

Type=forking

ExecStart=/usr/local/bin/redis-server /opt/software/redis-7.2.3/redis.conf

PrivateTmp=true

[Install]

WantedBy=multi-user.target
```

**数据类型解释**：

String： 它的值就是一个字符串

Hash： 它不是一个普通字符串，而是一个hash表, 这里用字符串表示

List： 它是一个有序的集合

Set： 它是一个无序集合，并且是不能重复的

SortedSet： 它是有序的可排序的集合，并且是不能重复的

**以上五种是redis里面最常见的五种基本的数据类型。**

GEO： 它是一个地理坐标，精度、尾度。

BitMap： 按位进行存储的方式，做数据统计，底层本质就是字符串。

HyperLog： 按位进行存储的方式，做数据统计，底层本质就是字符串。

**以上三种是redis里面特殊类型**。

redis主从复制配置

[root@localhost ~]# mkdir -p /cluster/{redis1,redis2,redis3}

[root@localhost ~]# cd /cluster/

[root@localhost cluster]# ls

[root@localhost cluster]# cp /usr/local/redis-7.2.3/redis.conf /cluster/redis1

[root@localhost cluster]# cp /usr/local/redis-7.2.3/redis.conf /cluster/redis2

[root@localhost cluster]# cp /usr/local/redis-7.2.3/redis.conf /cluster/redis3

**②修改redis端口及工作目录**

\# sed -i -r -e '$a\port 6380' -e 's#(^dir)(\s.*)#\1 /cluster/redis1#' redis1/redis.conf

\# sed -i -r -e '$a\port 6381' -e 's#(^dir)(\s.*)#\1 /cluster/redis2#' redis2/redis.conf

\# sed -i -r -e '$a\port 6382' -e 's#(^dir)(\s.*)#\1 /cluster/redis3#' redis3/redis.conf

注意：这里原来的6379端口需要手动注释掉，用以下命令检查

\# egrep "^port|^dir" redis1/redis.conf

\# egrep "^port|^dir" redis2/redis.conf

\# egrep "^port|^dir" redis3/redis.conf

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/check_redis_conf_port.png)

**③修改每个实例的声明IP和端口port**

虚拟机中有多个IP，为了避免将来混乱，我们需要在redis.conf文件中指定每一个实例的绑定ip和port信息。

**Redis实例的声明IP和 port**

replica-announce-ip=实例ip

replica-announce-port=实例port

\# sed -i '$a replica-announce-ip 192.168.10.55\nreplica-announce-port 6380 ' redis1/redis.conf

\# sed -i '$a replica-announce-ip 192.168.10.55\nreplica-announce-port 6381 ' redis2/redis.conf

\# sed -i '$a replica-announce-ip 192.168.10.55\nreplica-announce-port 6382 ' redis3/redis.conf

\# tail -2 redis1/redis.conf

\# tail -2 redis2/redis.conf

\# tail -2 redis3/redis.conf

**④配置主从redis服务器**

将redis1目录下启动的redis服务器设为主服务器master，redis2和redis3做为redis1的从服务器slave。

修改配置文件redis.conf

**注意**:redis1先前对其设置了密码认证，所以从服务器要想连接同步主服务器，则需要告诉从服务器主服务器的认证密码，**主服**

**务器不用修改**！

查看密码

\# egrep "requirepass" redis1/redis.conf

**配置从服务器**

修改redis1和redis2配置文件中的以下两行

masterauth <master-password>

replicaof <masterip> <masterport>



\# sed -i -r -e '/^# replicaof/a\replicaof 192.168.10.55 6380' -e '/^# masterauth/a\masterauth 123456' redis2/redis.conf

\# sed -i -r -e '/^# replicaof/a\replicaof 192.168.10.55 6380' -e '/^# masterauth/a\masterauth 123456' redis3/redis.conf

\# egrep "^replicaof|^masterauth" redis2/redis.conf

\# egrep "^replicaof|^masterauth" redis3/redis.conf

**⑤启动redis**

[root@localhost cluster]# redis-server redis1/redis.conf

[root@localhost cluster]# redis-server redis2/redis.conf

[root@localhost cluster]# redis-server redis3/redis.conf

**⑥查看redis的主从信息**

用以下两条命令查看



\# redis-cli -a 123456 -p 6380

127.0.0.1:6380> info replication

**⑦测试**

在主写入数据

\# redis-cli -a 123456 -p 6380

在从服务器查看数据是否同步



**⑧配置从机可写**

将从服务器的 replica-read-only yes 改为 replica-read-only no

\# sed -i '/^replica-read-only/s#yes#no#' redis2/redis.conf

\# egrep "replica-read-only" redis2/redis.conf

**重启redis**

\# ps -ef |grep redis

\# kill -9 40355

\# redis-server redis2/redis.conf

**注意：如果配置从节点可写，其它从节点 和master并不会同步数据**

#### redis哨兵搭建

继续之前的集群

**创建三个哨兵工作目录s1 、s2、s3**

\# mkdir /cluster/{s1,s2,s3}

**准备配置文件sentinel.conf**

\# egrep -v "^#|^$" /opt/software/redis-7.2.3/sentinel.conf > /cluster/s1/sentinel.conf

**修改s1的配置文件**

\# vim /cluster/s3/sentinel.conf

port 36379

daemonize yes

logfile "/cluster/s3/s3.log"

dir "/cluster/s3"

sentinel monitor mymaster 192.168.10.55 6380 2

sentinel announce-port 36379

sentinel announce-ip "192.168.10.55"

sentinel auth-pass mymaster 123456

sentinel down-after-milliseconds mymaster 10000

sentinel failover-timeout mymaster 20000

![Linux目录图](imgs\redis_哨兵配置.png)

**拷贝s1的sentinel.conf文件到s2、s3目录下**

[root@localhost cluster]# cp s1/sentinel.conf s2/

[root@localhost cluster]# cp s1/sentinel.conf s3/

修改s2、s3的端口和ip 其它不变

[root@localhost cluster]# sed -i 's/16379/26379/g' s2/sentinel.conf

[root@localhost cluster]# sed -i 's/s1/s2/g' s2/sentinel.conf

[root@localhost cluster]# cat s2/sentinel.conf

[root@localhost cluster]# sed -i 's/16379/36379/g' s3/sentinel.conf

[root@localhost cluster]# sed -i 's/s1/s3/g' s3/sentinel.conf

[root@localhost cluster]# cat s3/sentinel.conf



**修改master的配置**

\# sed -r -i '/^# masterauth/a\masterauth 123456' /cluster/redis1/redis.conf

**不配置会出现以下问题**

当配置了哨兵，将集群也都启动完成。master节点宕机之后，该master相应的slave节点会自动切换为master节点，但是当

master节点服务恢复后，却无法变成现有master的slave节点。

**配置步骤**

**④启动三台节点哨兵**

先看一下我们目前的主节点是redis1 6380端口

启动集群

[root@mysql cluster]#  redis-server redis1/redis.conf

[root@mysql cluster]#  redis-server redis2/redis.conf

[root@mysql cluster]#  redis-server redis3/redis.conf

启动各哨兵

[root@localhost cluster]# redis-sentinel s1/sentinel.conf

[root@localhost cluster]# redis-sentinel s2/sentinel.conf

[root@localhost cluster]# redis-sentinel s3/sentinel.conf

**查看日志**

[root@localhost cluster]# tail -f s1/s1.log

[root@localhost cluster]# tail -f s2/s2.log

[root@localhost cluster]# tail -f s3/s3.log

**配置步骤**

**⑤测试**

杀死redis主服务器master

查看日志

[root@localhost cluster]# tail -f s1/s1.log

**查看主服务器**

\# redis-cli -a 123 -p 6382

**再将6380重新启动查看主服务器和从服务器信息**

[root@localhost cluster]# redis-server redis1/redis.conf

[root@localhost cluster]# redis-cli -a 123456 -p 6382

127.0.0.1:6380> info replication

**redis分片概述**

Redis 分片集群是一种将 Redis 数据库分散到多个节点上的方式，以提供更高的性能和可伸缩性。在分片集

群中，数据被分为多个片段，每个片段存储在不同的节点上，这些节点可以是物理服务器或虚拟服务器。

Redis 分片集群的主要目的是将数据分布在多个节点上，以便可以通过并行处理来提高读写吞吐量。每个节点

负责处理一部分数据，并且在需要时可以进行扩展以适应更多的负载。此外，分片集群还提供了故障容错和

高可用性的功能，即使其中一个节点发生故障，其他节点仍然可以继续工作。

**主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决:**

海量数据存储问题

高并发写的问题

**使用分片集群可以解决上述问题，分片集群特征:**

集群中有多个master，每个master保存不同数据

每个master都可以有多个slave节点

master之间通过ping监测彼此健康状态

客户端请求可以访问集群任意节点，最终都会被转发到正确节点



**①准备实例及配置**

[root@localhost ~]# cd /cluster/

[root@localhost cluster]# mkdir 6380 6381 6382 7380 7381 7382

**配置步骤**

```
port 6380 

cluster-enabled yes 

cluster-config-file /cluster/6380/nodes.conf

cluster-node-timeout 5000 

dir /cluster/6380 

bind 0.0.0.0 

daemonize yes 

replica-announce-ip 192.168.10.55 

protected-mode no 

databases 1 

logfile "/cluster/6380/redis.log" 
```

端口

开启集群

 集群配置文件位置(集群启动自动生成)

集群节点连接超时间

数据存储目录

允许连接redis集群的ip

允许后台运行

复本声明ip

保护模式

redis库个数

日志文件位置

**拷贝配置文件**

\# echo "6381 6382 7380 7381 7382" | xargs -t -n 1 cp 6380/redis.conf

**修改端口6个节点，各自修改成自己的节点**

\# cd /cluster

\# echo -e "6381\n6382\n7380\n7381\n7382" |xargs -t -n 1 -I {} sed -i 's/6380/{}/g' {}/redis.conf

**查看端口是否已修改**

\# echo -e "6381\n6382\n7380\n7381\n7382" |xargs -t -n 1 -I {} cat {}/redis.conf

摩尔狮**配置步骤**

**②启动redis节点**

\# printf "%s\n" 6380 6381 6382 7380 7382 7381 |xargs -t -n 1 -I{} redis-server {}/redis.conf

\# ps -ef |grep redis

**③创建集群**

\# redis-cli --cluster help

Redis5.0以后集群管理集成到了redis-cli中，格式如下

**用法： redis-cli --cluster create --cluster-replicas n ip:port ip:port ...**

redis-cli --cluster create --cluster-replicas 1 \

192.168.10.55:6380 192.168.10.55:6381 192.168.10.55:6382 \

192.168.10.55:7380 192.168.10.55:7381 192.168.10.55:7382

**④查看集群状态**

\# redis-cli -p 7380 cluster nodes

Redis 会把每一个master节点映射到0~16383共16384个插槽(hash slot)上，查看集群信息时就能看到。

**⑤连接集群**

\# redis-cli -c -p 7380

**注意:** 用redis-cli 进入redis分片集群时一定要加上 -c 表示进入集群模式



**集群申缩**

**①添加一个节点到集群**

redis-cli --cluster 提供了很多操作集群的命令，可以通过下面方式查看：

\# redis-cli --cluster help

**向集群中添加一个新的master节点，并向其中存储 num=10**

需求：

1.启动一个新的redis实例，端口为6383

2.添加6383到之前的集群，并作为一个master节点

3.给6383节点分配插槽，使得num 这个key可以存储到6383实例

摩尔狮**集群申缩**

**②创建6383实例**

[root@localhost cluster]# mkdir 6383

[root@localhost cluster]# cp 6380/redis.conf 6383/

[root@localhost cluster]# sed -i 's/6380/6383/g' 6383/redis.conf

[root@localhost cluster]# cat 6383/redis.conf

**启动实例**

[root@localhost cluster]# redis-server 6383/redis.conf

[root@localhost cluster]# ps -ef |grep redis

**③添加6383节点到集群**

\# redis-cli --cluster add-node 192.168.10.200:6383 192.168.10.200:6382

**④查看新的master节点redis是否加入集群**

\# redis-cli -p 7380 cluster nodes

**⑤分配插槽**

给redis分配插槽，并确保 添加num=10 能被分配到6383节点上

[root@localhost cluster]# redis-cli -p 7380 cluster nodes

[root@localhost cluster]# redis-cli -c -p 7380

\# redis-cli --cluster help

**用法：redis-cli --cluster reshard 重新分片的主机:端口**

\# redis-cli --cluster reshard 192.168.10.200:6380

#查看集群节点

\# redis-cli -p 6382 cluster nodes

#登录查看

\# redis-cli -c -p 6382

**删除节点**

\# redis-cli -c -p 6380

\# redis-cli --cluster help

**语法：redis-cli --cluster del-node ip:port REDIS_ID**

\# redis-cli --cluster del-node 192.168.10.200:6383 3acfd7ccc86b475abb702f05ba0c828c94e31bba

**注意**：如果节点redis不是一个空节点，不能直接删除节点，需要先将被删除节点的插槽移动到其它节点，再进行删除

**将6383上的3000个插槽全部分配到6380这个节点上**

\# redis-cli --cluster reshard 192.168.10.200:6383

\# redis-cli -c -p 6380 cluster nodes

**删除节点**

\# redis-cli --cluster del-node 192.168.10.200:6383 3acfd7ccc86b475abb702f05ba0c828c94e31bba

\# redis-cli -c -p 6380 cluster nodes



**故障转移**

时时查看集群状态

[root@localhost cluster]# watch -n 1 redis-cli -c -p 7380 cluster nodes

模拟6380宕机测试是否能故障转移，

[root@localhost cluster]# redis-cli -c -p 6380 shutdown



重启6380，自动成为从节点

\# redis-server 6380/redis.conf

时时查看集群状态6380重启后是slave节点

**手动故障转移**

数据迁移

利用cluster failover 命令可以手动让集群中的某个master宕机，切换到执行cluster failover命令的这个slave节点，实现无感

知的数据迁移。流程如下图：

**动的failover支持三种不同模式**：

1.缺省：默认的流程如上图

2.force:省略offset的一致性效验

3.takeover:直接执行第5步，忽略数据一致性，忽略master状态和其它master意见

在6380这个slave节点上执行手动故障转移，重新夺回master地位

步骤如下：

①利用redis-cli连接6380这个节点

②执行clusterfailover命令

\#redis-cli-c-p6380

127.0.0.1:6380>CLUSTER FAILOVER

**6380又变成了master节点**

