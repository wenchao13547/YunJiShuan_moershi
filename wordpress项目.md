项目:安装wordpress博客网站

项目架构：采用动静结合的方式，nginx负责静态资源，php-ftp负责动态资源，采用mariadb数据库。

# 一、**环境准备**

## 1. **关闭selinux**

\# sed -i '/^SELINUX=/s/enforcing/disabled/g'  /etc/selinux/config

\# setenforce 0

## 2. **主机配置**

| ***\*IP\****  | ***\*组件\****     | ***\*主机名\**** |
| ------------- | ------------------ | ---------------- |
| 192.168.10.50 | Nginx、mariadb10.6 | ng-mysql         |
| 192.168.10.53 | Php8.0             | php              |

## 3. **关闭防火墙**

\# systemctl disable firewalld

\# systemctl stop firewalld

## 4. **配置yum源**

mkdir /etc/yum.repos.d/bak

mv /etc/yum.repos.d/*.repo  /etc/yum.repos.d/bak

curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

curl -o  /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo

yum clean all 

yum makecache

# 一、在192.168.10.50**安装(nginx、mariadb）**

## **1.安装**

在192.168.10.4机器上安装

下载nginx源码包

\# wget https://nginx.org/download/nginx-1.24.0.tar.gz

\# tar -xvf nginx-1.24.0.tar.gz -C /usr/local/

\# cd /usr/local/nginx/nginx-1.24.0

\# yum -y install gcc gcc-c++ \

pcre pcre-devel \

openssl openssl-devel \

zlib zlib-devel gd gd-devel

 

\# useradd -M  -s /sbin/nologin nginx

\# ./configure --prefix=/usr/local/nginx  \

--user=nginx  \

--with-http_stub_status_module  \

--with-http_ssl_module

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/nginx常用模块.png)

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/nginx常用模块2.png)

\# make && make install

也可使用添加模块的方式，但是这里不要使用make install，只用make即可

设置环境变量

\# echo  "export PATH=\$PATH:/usr/local/nginx/sbin"  >> /etc/profile

\# source /etc/profile

\# nginx -v 

## **2.配置nginx**

### **① 修改nginx主配置文件**

\# egrep -v "\s+#|^#|$^" /usr/local/nginx/conf/nginx.conf

worker_processes  1;

events {

  worker_connections  1024;

}

http {

  include    mime.types;

  default_type  application/octet-stream;

  sendfile     on;

  keepalive_timeout  65;

  server {

​    listen    80;

​    server_name  www.wenchao.com;

​    location / {

​      root  /www;

​      index  index.html index.htm index.php;

​    }

​    error_page  500 502 503 504  /50x.html;

​    location = /50x.html {

​      root  html;

​    }

​    location ~ \.php$ {

​      root      /www;

​      fastcgi_pass  192.168.10.53:9000;

​      fastcgi_index  index.php;

​      fastcgi_param SCRIPT_FILENAME  $document_root$fastcgi_script_name;

​      include     fastcgi_params;

​    }

  }

}

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/nginx项目配置.png)

***\*红色部分为主要修改的的内容\****

 

$fastcgi_script_name: 这是一个nginx变量，它包含了当前请求的脚本名称

$document_root: 是nginx配置中定义的根目录，它通常包含了网站的所有文件。

 

### **② 创建对应的目录**

\# mkdir /www

\# chown nginx.nginx /www

### **③ 启动nginx**

\# nginx  -c /usr/local/nginx/conf/nginx.conf

\# yum -y install net-tools 

\# netstat -tunlp |grep 80



## 3. **安装mairadb**

 

### **① 添加源**

***\*# vim /etc/yum.repos.d/mariadb.repo\****

```
# MariaDB 10.6 CentOS repository list - created 2024-09-18 03:57 UTC

# https://mariadb.org/download/

[mariadb]

name = MariaDB

# rpm.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.

# baseurl = [https://rpm.mariadb.org/10.6/centos/$releasever/$basearch](https://rpm.mariadb.org/10.6/centos/)

baseurl = [https://mirrors.aliyun.com/mariadb/yum/10.6/centos/$releasever/$basearch](https://mirrors.aliyun.com/mariadb/yum/10.6/centos/)

module_hotfixes = 1

# gpgkey = https://rpm.mariadb.org/RPM-GPG-KEY-MariaDB

gpgkey = https://mirrors.aliyun.com/mariadb/yum/RPM-GPG-KEY-MariaDB

gpgcheck = 1
```

\# yum clean all 

\# yum makecache

### **② 安装mariadb**

\# yum  -y install MariaDB-server MariaDB-client

 

\# systemctl start mariadb

 

***\*进入mysql\****

\# mysql -uroot  #回车即可

MariaDB [(none)]>ALTER USER 'root'@'localhost' IDENTIFIED BY '123';

MariaDB [(none)]> flush privileges;

MariaDB [(none)]> create database wordpress; #创建wordpress;

***\*创建用户\****

MariaDB [(none)]>CREATE USER "wordpress"@"%" IDENTIFIED BY "123456";

MariaDB [(none)]>GRANT ALL PRIVILEGES ON wordpress.* TO "wordpress"@"%";

MariaDB [(none)]> exit

# 一、**部署php**

在192.168.10.53机器上安装

## 1. **配置php源**

\# yum -y install wget

\# wget https://mirrors.aliyun.com/remi/enterprise/remi-release-7.rpm

\# yum -y install remi-release-7.rpm

\# ls /etc/yum.repos.d/

## 2. **安装php**

\# yum -y install  php80 php80-php php80-php-xml php80-php-xmlrpc php80-php-pecl-mcrypt php80-php-fpm php80-php-pecl-apcu php80-php-mbstring php80-php-gd php80-php-json php80-php-pecl-json-post php80-php-pdo php80-php-mysqlnd php80-php-pecl-mysql php80-php-opcache php80-php-pear php80-php-soap php80-php-intl php80-php-pear php-pear-MDB2-Driver-mysqli.noarch

## 1. **修改配置文件**

\# cd /etc/opt/remi/php80/php-fpm.d/

\# egrep -v "^;|^$" [www.conf](http://www.conf)

![img](file:///imgs/php-ftp项目配置.png)

user = nginx    指定php启动用户

group = nginx    指定php启动组

listen = 0.0.0.0:9000   定义php监听的ip和端口 0.0.0.0表示监控     所有ip

listen.allowed_clients = 192.168.10.4 定义允许访问php的地址这里写nginx的ip,     必须配置允许访问的客户端ip

 

 

创建用户和nginx服务器的用户要一致

\# useradd -M -s /sbin/nologin nginx

## 1. **启动php**

\# systemctl start php80-php-fpm.service

\# systemctl enable php80-php-fpm.service

\# yum -y install net-tools

\# netstat -tunlp |grep 9000

## 1. **创建目录**

 

\# mkdir /www

\# chown nginx.nginx /www/

# 一、**测试php**

 

## **1.在php服务器上编辑php的测试页面**

\# cat > /www/index.php << EOF

<?php

phpinfo()

?>

EOF

## **2.在nginx服务器上编辑nginx的前端页面**

\# echo "hello world" > /www/index.html

## **3.在windows上做本地域名解析**

 

C:\Windows\System32\drivers\etc

192.168.10.50 www.wenchao.com

访问

192.168.10.50 www.wenchao.com/index.html

192.168.10.50 www.wenchao.com/index.php

***\*以上可以看到测试成功。nginx和php可以成功连接\****

# 一、**配置wordpress**

 

## 1. **下载wordpress**

https://cn.wordpress.org/download/releases/

下载完后上传到192.168.10.50的/opt/software目录下

1. **解压并拷贝到nginx的/www和php的/www目录中**

***\*注意nginx和php都要安装wordpress资源\****

\# tar -xvf wordpress-6.6.2-zh_CN.tar.gz -C /www/

\# mv  /www/wordpress/*  /www/

\# rm  -rf  /www/wordpress

\# vim /www/wp-config-sample.php

![img](file:///imgs/wordpress_config.png)

将压缩包上传到php服务器中

scp  wordpress-6.6.2-zh_CN.tar.gz root@192.168.10.53:/opt/sortware

***\*Php服务器配置wordpress\****

\# ls

\# tar -xvf wordpress-6.6.2-zh_CN.tar.gz -C /www/

\# mv  /www/wordpress/*  /www/

\# rm  -rf  /www/wordpress

 

***\*从nginx服务器拷贝文wordpress的配置文件到php服务器对应的/www目录下\****

\# scp  192.168.10.4:/www/wp-config-sample.php  /www

 

***\*重启nginx 和php\****

\# nginx -s reload 

\# systemctl restart php80-php-fpm

## 1. **启动引导安装**

 

http://www.wenchao.com/index.php

![img](file:///imgs/![img](file:///imgs/wordpress1.png)

![img](file:///imgs/![img](file:///imgs/wordpress2.png)

安装后登录即可

***\*致此分布式的lnmp +wordpress部署完成！\****
