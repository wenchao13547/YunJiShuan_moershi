location模块：

​	root /usr/local/nginx/html;  #真实的资源目录路径

​	alias /data/dir01;  #指定真实的资源路径，(url路径就可以是虚拟的)

安装和配置

方式一：yum安装

***\*1、安装epel源\**** 

\# yum -y install epel-release 

***\*2、安装nginx软件包\**** 

\# yum -y install nginx 

***\*3、启动服务\**** 

\# systemctl start nginx  

***\*4、查看版本\**** 

\# nginx -v 

***\*5、测试访问\****

方式二：源码安装

***\*1、环境准备\**** 

卸载其他方式安装的web应用，防止端口冲突 

***\*2、下载nginx源码包\**** 

\# wget http://nginx.org/download/nginx-1.19.7.tar.gz 

***\*3、源码编译安装\**** 

安装依赖包 

\# yum install -y gcc pcre-devel zlib-devel

创建程序运行用户和组 

\# useradd -M -s /sbin/nologin nginx 

解包

\# tar -axf nginx-1.19.7.tar.gz  

进入目录

\# cd ~/nginx-1.19.7 

添加功能模块

\# ./configure --prefix=/usr/local/nginx --user=nginx --group=nginx  

配置说明：  
--prefix=/usr/local/nginx #指定安装目录  

--user=nginx --group=nginx #指定程序运行的用户和组 

编译安装

\# make && make install 

测试编译安装是否成功

\# echo $?

***\*4、nginx使用(前提：关闭防火墙，关闭selinux)\****

进入nginx安装目录 
\#cd /usr/local/nginx 
查看目录下的文件 
\#ls 
启用nginx 
./sbin/nginx 
测试服务启动情况 
\#netstat -tunlp 
查看设备ip 
\#ifconfig 
打开浏览器，输入ip进行访问

***\*5、命令路径优化\****

未优化前，开启nginx
\# /usr/local/nginx/sbin/nginx 
查看nginx相关信息 
\#/usr/local/nginx/sbin/nginx -t 
修改配置文件，生成环境变量 
\# vim /etc/profile.d/nginx.sh 
export PATH="/usr/local/nginx/sbin:$PATH" 
生效配置 
\# source /etc/profile  
再次测试 
\#nginx -t

***\*6、设置开机自启动\****

\# echo "/usr/local/nginx/sbin/nginx" >> /etc/rc.d/rc.local 
\# chmod +x /etc/rc.d/rc.local

也可以使用自己写的安装nginx的脚本

```shell
#!/bin/bash
#上传nginx安装包nginx-1.19.7.tar.gz到/opt/soreware/目录下
cd /opt/software/
wget http://nginx.org/download/nginx-1.19.7.tar.gz 
yum -y install gcc gcc-c++ pcre pcre-devel openssl openssl-devel zlib zlib-devel gd gd-devel
useradd -M -s /sbin/nologin nginx
tar -axf nginx-1.19.7.tar.gz
cd ./nginx-1.19.7
./configure --prefix=/usr/local/nginx --user=nginx --group=nginx --with-http_stub_status_module  --with-http_ssl_module
make && make install
echo $?
cd /usr/local/nginx
ls
netstat -tunlp | grep 80
```

***\*7、服务管理\****

Nginx运行时分为:一个nginx: master process主进程和若干个nginx: worker process工作进程 

```
ps -C nginx -o pid,ppid,command
```

1.启动 
\# nginx 或 nginx -c /usr/local/nginx/conf/nginx.conf 
2.快速关闭 
\# pkill -9 nginx 
或者  
\# nginx -s stop 
\# netstat -tunlp  观察端口是否开启  判断是否关闭掉 
3.重载 
\# nginx -s reload 
4.优雅的关闭 
\# nginx -s quit 或者kill -QUIT nginx主进程号 
5.nginx命令其他常用选项： 
-c </path/to/config> 为 Nginx 指定一个配置文件，来代替缺省的。 
-t 不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。 
-v 显示 nginx 的版本。 
-V 显示 nginx 的版本，编译器版本和配置参数



***\*升级安装nginx\****

***\*1、添加功能模块\****

场景：添加功能模块 
1）查看当前安装配置信息 
\# nginx -V 
2)版本不升级的情况下，重新配置 
\# cd ~/nginx-1.19.7 
说明：objs目录是存放nginx配置文件的目录 
3）清楚之前编译的文件 
\# make clean 
根据提示信息rm -rf Makefile objs  发现Makefile objs  被删除 
4）添加ssl安全加密功能 
\# ./configure --prefix=/usr/local/nginx --user=nginx --group=nginx \--with-http_ssl_module 
提示： 
./configure: error: SSL modules require the OpenSSL library. 
You can either **do** not enable the modules, or install the OpenSSL library 
into the system, or build the OpenSSL library statically **from** the source 
with nginx by **using** --with-openssl=<path> option. 
说明确实依赖 
5）安装openssl 依赖 
\# yum -y install openssl openssl-devel 
6)重新添加ssl安全加密功能 
\#./configure --prefix=/usr/local/nginx --user=nginx --group=nginx \--with-http_ssl_module 
7）重新编译 
\# make 
或 
\# make && make install    （不建议使用） 
说明：版本升级不能执行make install 只需替换编译好的nginx可执行文件 
8）查看是否生成新的Makefile objs 文件 
\#ls ~/nginx-1.19.7 
9）确保nginx是启动的 
\# netstat -tunlp 
如果没启动，使用命令# nginx进行启动  
10）对原来的配置做备份，方便出了问题回退 
\#mv /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.old 
11）用新生成的配置文件替换原来的配置文件 
\#cp objs/nginx /usr/local/nginx/sbin/nginx 
12） 执行平滑迁移 
\# make upgrade 
13） 检查功能模块是否安装成功 
\# nginx -V

***\*2、版本升级或版本降级\****

1）下载新版本文件

\#wget http://nginx.org/download/nginx-1.20.2.tar.gz 

2）查看文件是否下载成功

\# ls /root

3）清除旧环境

\#curl 127.0.0.1  
保证服务是启动状态才能添加模块或升级 

\#cd /usr/local/nginx/sbin 
切换到运行文件目录下 

\#rm -rf nginx.old 
删除掉之前的备份 

\#mv nginx nginx.old 
将现有的文件做备份

4）重新编译

\# cd /root 
切换回root目录下 

\# tar -axf nginx-1.20.2.tar.gz 
解压缩文件 

\# cd nginx-1.20.2 
切换到nginx-1.20.2目录下 

\#./configure --prefix=/usr/local/nginx --user=nginx --group=nginx \--with-http_ssl_module 
添加功能模块 

\# make 
编译

5）平滑升级

\#cp objs/nginx /usr/local/nginx/sbin 
将相对路径下的nginx 复制到/usr/local/nginx/sbin目录下 

\# make upgrade 
平滑升级

### nginx虚拟主机配置

nginx实现配置两个网站

***\*♦方式一：基于相同ip不同端口号\****

***\*1、创建两个网站的根目录\****

\# mkdir /www01 
\# mkdir /www02 
\# echo "this is yjs" > /www01/index.html 
\# echo "this is test" > /www02/index.html

***\*2、修改主配置文件\****

\# cd /usr/local/nginx/ 
切换到/usr/local/nginx/目录下 

\# vim conf/nginx.conf 编写配置文件 

```
include /usr/local/nginx/conf.d/*.conf； 
```

注意：在主配置文件的中的下加上这个配置，即可匹配/usr/local/nginx/conf.d/下，所有以*.conf结尾

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/nginx_conf1.png)

\# nginx -t  
修改后用该命令去检查语法

***\*3、编辑配置文件\****

\# cd /usr/local/nginx 
切换目录 

\# mkdir conf.d 
在当前路径下创建conf.d目录 

\# cd conf.d 
切换到conf.d目录下 

\# vim yjs.conf 
编辑yjs配置文件 
server { 
 listen 81; #指定不同的监听端口号 
 server_name www.yjs.com; 
 location / { 
 root /www01; 
 index index.html; 
 } 
} 

\# vim test.conf 
编辑test配置文件 
server { 
 listen 82; #指定不同的监听端口号 
 server_name www.test.com; 
 location / { 
 root /www02; 
 index index.html; 
 } 
} 

\# nginx -t  
测试配置文件 

\# nginx -s reload  
重载 

\# netstat -tunlp 
测试

***\*方式二：基于相同端口不同ip\****

***\*1、修改配置文件\****

\# cd /usr/local/nginx/conf.d 
切换目录 

\# vim /usr/local/nginx/conf.d/yjs.conf 
server { 
 listen 192.168.242.50:80; #指定不同ip 
 server_name www.yjs.com; 
 location / { 
 root /www01; 
 index index.html; 
 } 
} 

\# vim /usr/local/nginx/conf.d/test.conf 
server { 
 listen 192.168.242.60:80; #指定不同ip 
 server_name www.test.com; 
 location / { 
 root /www02; 
 index index.html; 
 } 
} 

\# nginx -s reload 
重载

***\*2、临时添加ip地址\****

\# ip a 
添加前网卡的ip地址 

\# ip address add 192.168.242.50/32 dev ens33 
添加ip地址192.168.242.50/32绑定到网卡ens33 

\# ip address add 192.168.242.60/32 dev ens33 
添加ip地址192.168.242.60/32绑定到网卡ens33 

\#ip a 
注意：因为是临时添加，千万不要重启网卡，网卡重启后Ip地址将会消失

***\*方式三：基于不同主机名\****

***\*1、修改配置文件\****

\# cd /usr/local/nginx/conf.d 
切换目录 

\# vim /usr/local/nginx/conf.d/yjs.conf 

```
server { 
 listen 80; #指定不同ip 
 server_name www.yjs.com; 
	 location / { 
 		root /www01; 
		 index index.html; 
	 } 
} 
```

\# vim /usr/local/nginx/conf.d/test.conf 

```
server { 
	 listen 80; #指定不同ip 
 	server_name www.test.com; 
	 location / { 
		 root /www02; 
		 index index.html; 
	 } 
} 
```

 

```
# systemctl restart network 
```

重启网络，去掉临时地址，只剩下主地址 

\#ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000 
  link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 
  inet 127.0.0.1/8 scope host lo 
    valid_lft forever preferred_lft forever 
  inet6 ::1/128 scope host  
    valid_lft forever preferred_lft forever 
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000 
  link/ether 00:50:56:3f:f2:14 brd ff:ff:ff:ff:ff:ff 
  inet 192.168.242.132/24 brd 192.168.242.255 scope global dynamic ens33 
    valid_lft 1798sec preferred_lft 1798sec 
  inet6 fe80::250:56ff:fe3f:f214/64 scope link  
    valid_lft forever preferred_lft forever 

```
# nginx -s reload 
```

重载

***\*2、测试\****

由于域名已经被注册

我们可以修改windows的host配置为件，优先匹配我们的域名地址

想要正确解析，需要修改windows操作系统相关文件，首先进入到C:\Windows\System32\drivers\etc目录下

右键点击hosts文件，选择打开方式，选择记事本打开

在记事本中做如下修改后，保持并关闭文件

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/nginx_host.png)

\# nginx -s reload  
修改后，再重载一次

打开浏览器，进行测试

分别访问www.yjs.com和www.test.com到我们的两个网站

***\*Nginx常用模块介绍\****

nginx正则匹配

***\*1、无任何前缀\****

不加任何规则时，默认是大小写敏感，前缀匹配 

***\*2、 “ = ”精确匹配\**** 

内容要同表达式完全一致才匹配成功 ，终止后续匹配

***\*3、 “^~”普通匹配\**** 

表示普通字符串匹配上以后不再进行正则匹配 

***\*4、 “~”正则匹配\**** 

表示执行正则匹配，区分大小写。 

***\*5、 “~\*”正则匹配\**** 

表示执行正则匹配，忽略大小写。 

***\*6、 “@”前缀\**** 

nginx内部跳转 

***\*一、重定向模块\****

生效模块

http，server,location

***\*重定向基本语法\****

rewrite是实现URL重写的关键指令，根据regex (正则表达式)部分内容,重定向到replacement，结尾是flag标记。 

语法： 

rewrite  [flag]; 

***\*重定向到站内资源\****

rewrite 到内部站点是指 replacement 不带http/https 而是内部的另外一个路径 , 相当于访问隐藏起来的这个 内部路径。 

例： 

server { 

 listen 80; 

 server_name [www.yjs.com](www.yjs.com); 

 rewrite ^/([0-9]+).html$ /my.gif break; 

 \#其他的请求会走到这个location中 

location / { 

 root html; 

 index index.html; 

} 

\# 上面的rewrite 会被路由到这里 

 location /my.gif { 

 root /www/static/; 文件存放路径：/www/static/my.gif 

 } 

}

server 中使用 rewrite ,访问www.yjs.com直接跳转到www.baidu.com

server { 

 listen 80; 

 server_name [www.yjs.com](www.yjs.com); #由于是外部站点带https 的 所以不受flag 影响 break last 都会进行跳转并且变更浏览 器url 

 rewrite ^/(.*)$ https://www.baidu.com break; 

 location / { 

 root html; 

 index index.html; 

 } 

} 

location 中也可以使用 rewrite 意思是只有匹配到 这个location 后再经过 rewrite 的正则通过后，才能跳转。

例如：以数字开通以.html结尾都跳转到baidu.com

server { 

 listen 80; 

 server_name [www.yjs.com](www.yjs.com); 

 \#rewrite ^/([0-9]+).html$ /my.gif last; 

 location /my.gif { 

 rewrite ([0-9]+).html$ https://www.baidu.com last; 

 }

 

 location / { 

 root html; 

 index index.html index.htm; 

 } 

}

说明： 

regex: 正则表达式语句进行规则匹配 

replacement: 将正则匹配的内容替换成replacement 

flag: last | break | redirect | permanent 

last : 本条规则匹配完成后，继续向下匹配新的location URI规则 

break: 本条规则匹配完成即终止，不再匹配后面的任何规则 

redirect : 回302临时重定向，浏览器地址会显示跳转后的URL地址 (防爬虫) 

permanent : 返回301永久重定向，浏览器地址栏会显示跳转后的URL地址

例如：

```
server { 

 listen 80; 

 server_name [www.yjs.com](www.yjs.com); 

 #只有当后缀是 数字.html 的时候才会转发到 https://www.baidu.com 

 rewrite ^/([0-9]+).html$ https://www.baidu.com break; 

 #其他的请求会走到这个location中 

 location / { 

 root html; 

 index index.html; 

 } 

} 
```

***\*If 中使用rewrite\****

```
server { 

 listen 80; 

 server_name [www.yjs.com](www.yjs.com); 

 location / { 

 root /usr/local/nginx/test; 

 if ( !-e $request_filename ) { 

 rewrite ^/.* http://www.baidu.com redirect; 

 } 

} 

} 
```

说明：结合if指令来对nginx请求进行判断，若访问http://www.yjs.com的资源存在root目录，则返回，若当前请求的资 

源文件不存在，则进行重定向跳转

#### 压缩模块

***\*gzip_comp_level\****

语法: gzip_comp_level 1..9 gzip压缩比，1 压缩比最小处理速度最快，9 压缩比最大但处理最慢（传输快但比较消耗cpu）。 

默认值: gzip_comp_level 1 

作用域: http, server, location

 

***\*2、gzip_min_length\****

语法: gzip_min_length length 

默认值: gzip_min_length 0 

作用域: http, server, location 

设置允许压缩的页面最小字节数，页面字节数从header头中的Content-Length中进行获取。 

默认值是0，不管页面多大都压缩。 

建议设置成大于1k的字节数，小于1k可能会越压越大。即: gzip_min_length 1024



***\*3、gzip_types\****

语法: gzip_types mime-type [mime-type …] 

默认值: gzip_types text/html 

作用域: http, server, location 

匹配MIME类型进行压缩，（无论是否指定）”text/html”类型总是会被压缩的

#### 代理概念

正向代理 

正向代理是一个位于客户端和目标服务器之间的代理服务器（中间服务器）。为了从目标服务器取得内容，客户端向代理服务器 

发送一个请求，并且指定目标服务器，之后代理向目标服务器转发请求，将获得的内容返回给客户端。正向代理的情况下，客户端必 

须要进行一些特殊的设置才能使用。 

正向代理需要主动设置代理服务器ip或者域名进行访问，由设置的服务器ip或者域名去访问内容并返回

 反向代理 

反向代理是指以代理服务器来接收客户端的请求，然后将请求转发给内部网络上的服务器，将从服务器上得到的结果返回给客户 

端，此时代理服务器对外表现为一个反向代理服务器。 

正向代理需要配置代理服务器，而反向代理不需要做任何设置。反向代理是代理服务器，为服务器收发请求，使真实服务器对客 户端不可见

 反向代理用途 

##### 负载平衡：

​	反向代理服务器可以充当驻留在我们后端服务器前面的交通警察，并以提高速度和容量利用率的方式在一组服务器之间分 

配客户端请求，同时确保没有任何服务器过载。如果服务器未启动，则负载平衡器会将流量重定向到其余的在线服务器。 

##### Web 加速：

 	Nginx 反向代理用于压缩出站和入站数据，以及缓存常见请求的内容，这两者都加快了客户端和服务器之间的流量流动。 

安全性和匿名性：我们可以拦截前往我们后端服务器的客户端的请求，通过这样做，反向代理服务器可以保护他们的身份并作为对安 

全攻击的额外防御。 

要将请求传递到 HTTP 代理服务器，使用 proxy_pass 指令。 

例如： 

location /some/path/ { 

proxy_pass http://www.example.com/; 

} 

要将请求传递到非 HTTP 代理服务器，请使用适当的 **_pass 指令： 

fastcgi_pass：将请求传递给 fastCGI 服务器。 

uwsgi_pass：将请求传递给 uwsgi 服务器。 

scgi_pass：将请求传递给 SCGI 服务器。 

memcached_pass：将请求传递给 memcached 服务器。

配置示例：

实现访问网站但是真正的服务器在192.168.242.128

```
  server {
    listen    80;
    server_name localhost;
    *#charset koi8-r;*

​    *#access_log  logs/host.access.log  main;*

​    location / {
​    *#   root  html;*
​    *#   index  index.html index.htm;*
​     proxy_pass http://192.168.242.128;

​    }
```

nginx基于代理实现网站的动静分离

Nginx的动静分离，原理是将动态请求和静态请求分开，不能单纯的理解成只把动态页面和静态页面物理分离。严格意义上， 可以理解成使用nginx处理静态页面，tomcat或PHP处理动态页面

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/nginx_动静分离.png)

准备两台虚拟机，一台安装php实现动态内容

另一装nginx作为代理，同时静态内容有nginx自己提供或另一个nginx服务器提供

后端服务器准备动态页面

yum -y install php* 

注意如果提示需要添加--skip-broken，则命令为yum install -y php* --skip-broken

cd /var/www/html/

vim index.php

<?php 

phpinfo(); 

?> 

systemctl start httpd

另一台虚拟机安装源码安装nginx提供静态内容

*cd /usr/local/nginx/conf*

配置nginx.conf，实现访问以html结尾的用nginx服务器响应，动态内容用php服务器提供

![Linux目录图](imgs\qqqqq.png)

nginx -s reload

访问192.168.10.50/index.html为静态

访问192.168.10.50/index.php为动态

#### 负载均衡：

## ***\*负载均衡集群作用\****

● 提高系统性能 

负载均衡技术将负载(请求或任务)分发到多个资源上，使得系统能够处理更多的并发请求，从而提高整体的处理能力和性能。 

● 实现高可用性 

负载均衡可以将负载分发到多个资源上，当其中一个资源发生故障或不可用时，负载均衡可以自动将请:求转发到其他可用的资源。 

● 提高系统可伸缩性 

随着业务的增长，负载均衡技术可以动态地增加或减少资源的数量，根据实际负载情况进行扩展或收缩。 

● 优化资源利用 

负载均衡技术可以根据资源的性能、可用性和负载情况，合理地分配请求或任务。



四层的负载均衡就是基于IP端口的负载均衡, 用ip port接收请求,再转发到对应的机器。

实现四层负载均衡的软件有: 

●F5:硬件负载均衡器，功能很好,但是成本很高。 

●Ivs: 重量级的四层负载软件 

●nginx:轻量级的四负载软件，带缓存功能，正则表达式较灵活 

●haproxy: 模拟四层转发,较灵活



七层的负载均衡，就是基于虚拟的URL或主机IP的负载均衡，根据虚拟的ur|或IP，主机名接收请求，再转向相应的处理服务器。

实现七层负载均衡的软件有: 

●haproxy: 全面支持七层代理，会话保持，标记，路径转移; 

![Linux目录图](imgs\1负载均衡.png)

## ***\*负载均衡算法\****

★ Nginx 负载均衡算法-1：round-robin(轮询) ☚ 这是：默认值 

★ Nginx 负载均衡算法-2：weight 加权轮询 

● 指定轮询的频率，weight和访问率成正比，用于后端服务器性能不均匀的情况 

### ***\*权重weight\****

 http { 

 upstream ipHashLoadBalanceServer { 

 ip_hash; 

 server [www.address1.com](www.address1.com) weight=3; 

 server [www.address2.com](www.address2.com); // default weight=1 

 } 

 server { 

 listen 80; 

 location / { 

 proxy_pass http://loadBalanceServer; 

 } 

 } 

} 

服务1将会轮3次服务2才会轮一次



★ Nginx 负载均衡算法-3：ip_hash 

● 针对<客户端 IPv4 地址>，按照<C类网络>提取<相应的网络IP地址>，然后计算<该C类网络地址>的<哈希值>，从而基于<该哈希值>进 

行<负载均衡>。 

● 注意：这会造成具备<相同网络IP地址>的<客户端>，会被<负载均衡>到<相同的后端服务器>。 

```
upstream wenchao{ 

ip_hash; 

	server srv1.example.com; 

	server srv2.example.com; 

​	 server srv3.example.com; 

} 
```

★ Nginx 负载均衡算法-4：least_conn(最小连接数) 

●<下一个请求>将被分配给<活动连接最少的服务器>

```
upstream wenchao { 

​	 least_conn; 

	server srv1.example.com; 

	server srv2.example.com; 

	server srv3.example.com; 

} 
```

