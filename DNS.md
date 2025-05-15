**DNS 概述**

**DNS(域名解析服务)**

DNS: Domain Name System域名系统,应用层协议,是互联网的一项服务。它作为将域名和IP地址相互映射的一个分布式数据库，

能够使人更方便地访问互联网

**基于C/S架构**，服务器端:53/udp, 53/tcp

BIND: Bekerley Internet Name Domain,由 ISC (www.isc.org)）提供的DNS软件实现DNS域名结构

根域

一级域名: Top Level Domain: tldcom, edu, mil, gov, net, org, int,arpa

三类: 组织域、国家域(.cn, .ca, .hk, .tw)、反向域

二级域名:test.com

三级域名: study.test.com

最多可达到127级域名

FQDN全称域名=主机名(名)+域名(姓)域名=子域名+父域名

全域名=主机名+域名+父域名

举例：dns01(主机).test.com.(域名)

DNS原理：

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/DNS原理.png)

**DNS查询类型**

递归查询: 最终结果，负责到底

迭代杳询· 最好结果，不负责到底

**完整的查询请求经过的流程**

client -->hosts文件-->client DNS service Local cache --> DNS server本身数据库 (recursion递归查询) --> DNS server

cache -->iteration(迭代)-->根-->顶级域名DNS-->二级域名DNS...

DNS资源记录：

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/DNS资源记录.png)

NDS服务器的角色：

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/DNS服务器角色.png)

**DNS服务安装**

针对：CentOS7

```
#yum install -y bind bind-utils 

#systemctl enable named

#systemctl start named

#netstat -tunlp | grep named
```

查看配named的配置可以看到

```
#vim /etc/named.conf
```

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/DNS_config.png)

listen-on port 53 { 配置监听本机的ip地址，如果需要为别人配置dns解析，换为any;}

```
listen-on port 53 { any; };
```

allow-query {配置允许使用本DNS的ip地址，可以配为“{192.168.10.0/24 ；}；”允许网段内的ip使用；也可以配置为"any;"即可允许所有ip使用}

```
 allow-query     { any;};
```

开启递归查询，并设置能够使用递归查询的网段

```
recursion yes;
allow-recursion { 192.168.10/24; };
```

配置转发,先尝试解析，如果无法解析，就转发到8.8.8.8dns进行解析，当设置为 `forward only;` 时，DNS服务器将只进行转发查询，而不尝试直接解析查询。这意味着如果本地DNS服务器无法解析某个查询，它将不会尝试自己查找答案，而是将所有查询都转发给上游DNS服务器,

注意，先forward first; 再forwarders {8.8.8.8;       # 如Google DNS223.5.5.5;     # 如阿里DNS }; 顺序不能错

```
forward first;  # 或 only
forwarders {
        8.8.8.8;       # 如Google DNS
        223.5.5.5;     # 如阿里DNS
    };
```



```
修改完配置文件，检测配置文件是否正确

#named-checkconf

重新加载配置文件

#rndc reload或#systemctl restart named 

查看本机ip地址是否绑定

#netstat -tunlp|grep named
```



```
修改DNS服务

#vim /etc/sysconfig/network-scripts/ifcfg-ens33

修改DNS1为DNS服务器ip地址

重启网卡

#ifdownens33;ifupens33

查看DNS缓存服务器

#cat/etc/resolv.conf
```

测试

ping www.baidu.com -c 4

**①DNS正向解析配置**

正向解析将域名解析成ip

编辑主配置文件，添加域名

\#vim /etc/named.rfc1912.zones

```
zone"test.com"IN{		//定义一个名为test.com的区域

​	type master;		//声明这是一个主区域，这是一个主DNS服务器。

​	file "zone.test.com"; //指定区域数据文件=zone.test.com文件(这是相对路径)

​	allow-update{ none; }; //禁止：DNS动态更新

};
```

创建并编辑区域数据文件`zone.test.com`，指定test.com.的权威服务器

\#cp -a /var/named/named.localhost /var/named/zone.test.com

\#vim /var/named/zone.test.com

```
$TTL	1D

@	IN  SOA  dns01.test.com.  123456789.qq.com.(	//指：对于test.com这个域名，其起始授权记录（SOA）由																dns01.test.com担任，管理员邮箱为123456789.qq.com（注意这里的表														示方式略有不同），并且括号内给出了相关的更新和刷新策略。

​			0	;

​			1D	;

​			1H	;

​			1W 	;

​			3H)	;

@	NS	dns01.test.com.;	//指定了该区域的权威名字服务器

dns01	A	192.168.10.8;	//将域名dns01.test.com解析为IP地址192.168.10.8
```

；号后面是注释

@表示当前域名：test.com

\##重启服务、检测效果

\#named-checkconf /etc/named.conf 

\#systemctl restart named

\#named-check zonetest.com /var/named/zone.test.com 	#（检测区域配置文件语法格式，是否正确）

测试域名能否解析正确

nslookup dns01.test.com 或 ping dns01.test.com -c 4



#### DNS做负载均衡：

编辑主配置文件，添加域名

\#vim /etc/named.rfc1912.zones

```
zone"test.com"IN{		//定义一个名为test.com的区域

​	type master;		//声明这是一个主区域，这是一个主DNS服务器。

​	file "zone.test.com"; //指定区域数据文件=zone.test.com文件(这是相对路径)

​	allow-update{ none; }; //禁止：DNS动态更新

};
```

编辑域名的数据文件，配置访问主机名为websrv的完整域名为www.test.com.对应的ip地址为192.168.10.9，192.168.10.10均可提供服务，实现对域名的负载均衡，并且实现指定www的别名为websrv,实现用户输入域名为websrv.test.com.也可访问,并添加泛域名解析

\#vim /var/named/zone.test.com

```
$TTL	1D

@	IN  SOA  dns01.test.com.  123456789.qq.com.(	

​		0	；

​		1D	；

​		1H	;

​		1W 	;

​		3H)	;

@	NS	dns01.test.com.;	指定：权威主机的FQDN完全限定域名（域名全名）

dns01	A	192.168.10.8	;	指定将dns01解析为192.168.10.8，指定主DNS服务器的位置
www		CNAME		websrv	;	做别名记别名叫websrv
websrv 	A	192.168.10.9	;	指定websrv可被指定为192.168.10.9
websrv 	A	192.168.10.10	;	指定websrv可被指定为192.168.10.10
*       A   192.168.10.9   	; 实现泛域名访问，即使www写成wwwww.test.com也可访问192.168.10.9
@       A   192.168.10.10	;	实现本域名访问，即使不写www,使用test.com.也可访问192.168.10.10
mailsrv1  A     192.168.10.8;	mailsrv1对应的A记录，邮件服务器
mailsrv2  A		192.168.10.10;	mailsrv2对应的A记录，邮件服务器
```

 \##重启服务、检测效果

```
#named-checkconf /etc/named.conf  检查name.conf配置文件是否配置错误

#named-checkzone test.com /var/named/zone.test.com#（检测区域配置文件语法格式，是否正确）

#systemctl restart named	重启DNS服务
```



测试：

准备两台服务器192.168.10.9、192.168.10.10并安装httpd服务，以便看效果,两台机器指定DNS服务器为192.168.10.8 

```
#hostnamectlset-hostnameclient01

#hostnamectlset-hostnameclient02

[root@client01~]#yum-yinstallhttpd

[root@client01~]#echo"webserver192.168.10.9">/var/www/html/index.html 

[root@client01~]#systemctlstarthttpd

[root@client02~]#yum-yinstallhttpd

[root@client02~]#echo"webserver192.168.10.10">/var/www/html/index.html 

[root@client02~]#systemctlstarthttpd
```

**开一台机器测试**

```
#cat/etc/resolv.conf 

#ipa|grepens33

#for((i=1;i<=6;i++));do curl www.test.com; done
```



**②配置：DNS反向区域PTR记录**

反向区域：将IP地址解析为名字

区域名称：网络地址反写.in-addr.arpa.

**建：独立的反向区域控制文件**

\#vim/etc/named.rfc1912.zones

zone"10.168.192.in-addr.arpa"IN{//标明：区域名称=10.168.192.in-addr.arpa

type master;	//设置：区域类型=master主

file "zone.10.168.192";	//指定：区域数据文件=zone.10.168.192文件(这是相对路径)

allow-update{none;};//禁止：DNS动态更新

**创建：独立的反向区域数据文件**

\#vim/var/named/zone.10.168.192

$TTL1D

@INSOAdns01.test.com.123456789.qq.com.(

​			0

​			1D

​			1H

​			1W 

​			3H

@				NS	dns01.test.com.

9				PTR 	web.test.com.

10.10.168.192.in-addr.arpa.PTRmail.test.com.;  	反向全称写法，上面是缩写

重启服务、检测效果

\#named-checkconf/etc/named.conf 	//（检测配置文件语法格式，是否正确）

\#systemctlrestartnamed

\#named-checkzone10.168.192.in-addr.arpa/var/named/zone.10.168.192#（检测区域配置文件语法格式，是否正确）

\#dig-tptr9.10.168.192.in-addr.arpa		//查看能否将ip解析为域名

\#dig-x192.168.10.10				//查看能否将ip解析为域名

**DNS主从复制**

**主从复制条件**

1.应该为一台独立的名称服务器

2.主服务器的区域解析库文件中必须有一条NS记录指向从服务器

3.从服务器只需要定义区域，而无须提供解析库文件;解析库文件应该放置于/var/named/slaves/目录中

4.主服务器得允许从服务器作区域传送

5.主从服务器时间应该同步，可通过ntp进行

6.bind程序的版本应该保持一致;否则，应该从高，主低

主从复制的意义：

​	实现DNSMaster主/Slave从冗余部署，实现DNS高可用性

​	在Master主无法响应的时候，客户端可以自动通过Slave从获得名称解析服务。

**环境准备**

两台主机，一台为主DNS服务器，一台为从NDS服务器

关闭防火墙，selinux,修改主机名,做时间同步服务(略)

\#hostnamectlset-hostnamemaster

\#hostnamectlset-hostnameslave

**①配置：Master主**（192.168.10.8）

**②配置：Slave从**（192.168.10.9）

**从服务器也安装DNS**

主服务器和从服务器进行时间同步

#ntpdate ntp.aliyu.com

添加一个时间同步

Crontab -e

*/1 * * * *  /usr/sbin/ntpdate ntp.aliyu.com.



[root@slave~]#yum -y install bind bind-utils

[root@slave~]#vim/etc/named.conf

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/named_conf.png)

在下面配置文件中添加以下内容

\#vim /etc/named.rfc1912.zones 

```
zone"test.com"IN{

​	type slave;

​	masters { 192.168.10.8; };

​	file "slaves/zone.test.com.slave";

};
```

\#ls /var/named/slaves/

\#systemctl start named 

\#ll /var/named/slaves/

**客户端测试**

\#dig www.test.com@192.168.10.9

注意：这样当主配置/etc/named.rfc1912.zones 发生变化，从服务器不会同步

**因**

①主服务器修改配置文件以后，版本号需要变更，因为同步有两种机制一种是推一种是拉，主服务器要么推数据到从服务器，但是

版本号未变，因此会认为没有变化，一种是从机向主拉数据，但是有时间上面配置的刷新时间是1D也就是一天，时间没到(版本号

也需要变化)

②主服务器需要指定从服务器的NS记录和A记录，告诉网络中还有一台从服务器，需要进行数据同步

master~]#vim/var/named/zone.test.com

* ```
  $TTL	1D
  
  @	IN  SOA  dns01.test.com.  123456789.qq.com.(	
  
  		1			//;serial,版本号需要有变化
  	
  		1D			//;refresh
  	
  		1H			//;retry
  	
  		1W 			//;expire
  	
  		3H)			//;minimum
  
  @	NS	dns01.test.com.;	指定：权威主机的FQDN完全限定域名
  @	NS	dns02.test.com.;	指定从服务器的全域名
  
  dns01	A	192.168.10.8	指定将dns01解析为192.168.10.8，指定主DNS服务器的位置
  dns02	A	192.168.10.9	指定从服务器的位置
  www		CNAME		websrv		做别名记别名叫websrv
  websrv 	A	192.168.10.9	指定websrv可被指定为192.168.10.9
  websrv 	A	192.168.10.10	指定websrv可被指定为192.168.10.10
  
  *       A   192.168.10.9    实现泛域名访问，即使www写成wwwww.test.com也可访问192.168.10.9
  @       A   192.168.10.10	实现本域名访问，即使不写www,使用test.com.也可访问192.168.10.10
  mailsrv1  A     192.168.10.8;mailsrv1对应的A记录，邮件服务器
  mailsrv2  A		192.168.10.10;mailsrv2对应的A记录，邮件服务器
  ```

  master~]#rndcreload

  slave~]#ll /var/named/slaves/

​	**客户端测试**

​	\#digwww.test.com@192.168.10.9

**现在可以看到可以备用DNS服务器可以正常同步主服务器的解析数据了**

#### 安全注意：

对于从服务器而言，它同步的配置文件是加密的方式保存的！这是一种安全机制！但实际上仅仅是把同步文件加密了就安全了

吗？并不是!对于从服务器而言，主服务器并没有限制谁能同步自己的数据，因此任何服务器只要配置了DNS都可以去同步主

服务器的数据。这样很明显是不安全的

默认任何都可以从主服务器同步区域数据库，这有很大安全风险

加固方式(注意主服务器都需要加固)：

master~]#vim/etc/named.conf

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/DNS加固.png)

allow-transfer  {192.168.10.9;};只允许192.168.10.9机器同步数据

master~]#rndc reload

或者

[slave~]#vim /etc/named.conf

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/DNS从加固.png)

[slave~]allow-transfer 	{none;};从不允许任何机器同步数

#rndc reload

一般：主服务器允许从服务器同步，从服务器不允许任何人同步

**再次测试**（发现无法同步主和从服务器上的DNS信息）

\#dig-taxfrtest.com@192.168.10.8 

\#dig-taxfrtest.com@192.168.10.9