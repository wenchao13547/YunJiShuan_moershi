***\*1、php介绍\****

***\*● php概念\**** 

PHP是一种开源服务器端脚本语言， PHP通常用于服务器端Web开发，包括构建Web应用程序、Web服务、CMS、博客、论坛等等。许 

多流行的Web框架，都是用PHP编写的。PHP也可以用于命令行脚本、系统管理和其他一些用途。 

***\*● php的安装环境配置\**** 

yum install php php-fpm php-mysqlnd php-gd php-mbstring 

安装以上 PHP 组件： 

php：PHP 语言核心库 

php-fpm：PHP FastCGI 进程管理器，用于管理 PHP 进程池 

php-mysqlnd：PHP MySQL 数据库驱动程序 

php-gd：PHP 图形处理库，用于处理图像 

php-mbstring：PHP 多字节字符串扩展，提供了对多字节字符集的支持

配置步骤说明：

1)安装环境,首先查看有没有安装epel源，没有的话使用 # yum -y install epel-release命令进行安装

nginx：

安装环境
*# yum install php php-fpm php-mysqlnd php-gd php-mbstring* 

配置fpm管理工具 
*# vim /etc/php-fpm.d/www.conf* 
搜索/user，找到 user = apache，修改为： 
user = nginx 
搜索/group，找到 group = apache，修改为： 
group = nginx 
搜索/listen.owner，找到 group = apache，修改为： 
listen.owner = nginx 
listen.group = nginx

listen.allowed_clients = 192.168.10.4 定义允许访问php的地址这里写nginx的ip,     必须配置允许访问的客户端ip



![Linux目录图](imgs\php_fpm.png)

创建用户和nginx服务器的用户要一致

\# useradd -M -s /sbin/nologin nginx

 **启动php**

\# systemctl start php80-php-fpm.service

\# systemctl enable php80-php-fpm.service

\# yum -y install net-tools

\# netstat -tunlp |grep 9000

**创建目录** 

\# mkdir /www

\# chown nginx.nginx /www/

测试

\# cat > /www/index.php << EOF

<?php

phpinfo()

?>

EOF

等待nginx配好即可