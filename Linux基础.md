



**Linux命令：**（命令+选项+参数）

```
uname：显示系统信息，如内核版本、主机名、处理器类型等。

ls：列出目录内容，可以配合不同参数显示详细信息或隐藏文件

cd：切换工作目录，可以使用绝对路径或相对路径。

pwd：打印当前工作目录的完整路径

clear：清除终端屏幕显示，相当于DOS下的cls命令。

whoami：显示当前登录的用户名称。

reboot：重启操作系统。

shutdown：关闭计算机，可以指定时间延迟关机。

type：显示命令的类型，判断是内置命令还是外部命令。

history：列出用户的命令历史记录。

hostnamectl：显示或更改系统的主机名。
```

**Linux选项：**

-r**(Recursive)**:表递归，对目录及其所有子目录和文件进行操作（一般用于删除）

 -d**(Directory)**:表自身，

 -f**(Force)**:表强制

 -a**(All)**:表全部

-p()：递归，在进行复制或移动操作时，保留源文件或目录的权限属性（一般用于复制）

### Linux目录:

![Linux目录图](imgs\Linux目录图.png)

/bin:经常使用的命令

/sbin:S表示super,为超级管理员的运行程序

/root:管理员的家目录

/home:普通用户的家目录

/boot:存放Linux的核心文件，如镜像文件

/dev:device（设备），存放设备信息的文件

/etc:系统的配置文件

/lib:存放依赖

/media:挂载外设的目录，如光驱，U盘

/mnt:可临时挂载别的文件系统

/opt:第三方程序安装目录

/temp:存放临时文件

**centos中终端提示符的含义是？**

当前用户的用户名+主机名+当前目录+用户权限的符号

**查看命令的帮助有哪些方式？**.

```
Man+命令
```

## 文件管理：

创建文件

```
mkdir 文件路径
```

```
touch 文件名
```

```
rm 文件路径
```

```
#展示路径下所有的文件夹及文件
ls -R 文件路径
```

```shell
#copy
cp -r 文件路径 文件路径
#move
mv 文件路径 文件路径
eg:
36.拷贝目录/tmp/file到/tmp/ａ目录下？
	cp -r /tmp/file /tmp/a/
```

```
#打包
tar [选项] 打包后的文件名 需要打包的文件
tar -c(打包)v(显示进度)f(显示文件) 打包后的文件名 需要打包的文件
```

```
#解打包
tar [选项] 打包后的文件名 -C 指定解打包路径
tar -xvf 打包后的文件名 -C 指定解打包路径
```

```
#查看压缩后的文件
tar -tvf 打包后的文件名
```

-c:打包

-x:解打包

`-t`：列出归档文件的内容

-f:filename,打包后的文件名称

-v:显示打包进度

-u:updata,更新打包文件中的内容

-t:查看打包文件内容

zip压缩工具的使用

```
#如果压缩问价夹得加 -r

zip 压缩的文件名 被压缩的文件

#解压缩

unzip 文件名 -d 指定解压目录
```

## vim编辑器：

​	默认进去的模式就是命令模式，在其他模式下，可以连续按两次esc键即可返回到命令模式

![vim.png](imgs\vim.png)

**命令模式**:

操作：移动光标、复制、粘贴、剪切、撤销、恢复撤销

快捷方式：1.移动光标到首行 ：gg       2.移动光标到末行：G

数字加G/gg：移到指定行

复制：n+yy(复制从当前光标所在行开始到递n行)

剪切：n+dd(剪切从当前光标所在行开始到递n行)

黏贴：P(黏贴到当前光标的下一行)

撤销操作：u

**末行模式下**：

**保存****/****退出**

:w 代表对当前文件内容进行保存，保存完后，并没有退出该文件

:q 代表退出当前，但不保存内容，如果编辑了文件内容，是无法使用:q退出该文件的

:wq 代表对文件内容先保存后退出 == 保存并退出

:q! 代表强制退出，不管有没有编辑文件内容，都直接退出该文件，已经编辑好的文件内容不做保存

**查找****/****搜索**

：/需要查找的内容

**替换**

```shell
#单一行替换

:s/旧词/新词

#全局行替换

:s/旧词/新词/g(global)

#全局单一替换

:%s/旧词/新词

#全局替换

:%s/旧词/新词/g(global)
```

**显示行号:**

```
显示行号

:set nu(nu = number，行号)

取消行号：

:set nonu
```

**可视化模式**

在命令模式中，按ctrl+v 用可视列

​	按小写v 是可视行

​	按大写V是可视块。

在可视模式中，使用方向键上下左右箭头选中需要复制的区域，按y进行复制，按p进行粘贴

**查看文件内容**

```
基本语法：
#cat 文件名称
案例：
#cat a1.txt
功能：正序输出文件内容
```

```
基本语法：
#tac 文件名称
案例：
#tac a1.txt
功能：倒序输入文件的内容
```

```
基本语法：
#head -n n 文件名称
案例：
#head -3 /etc/passwd
#head /etc/passwd 默认显示前十行内容
功能：主要查看一个文件的前n行，如果不指定n，默认显示前10行内容
```

```
基本语法：
#tail -n 文件名称
案例：
#tail /etc/passwd
#tail -2 /etc/passwd
功能：查看一个文件的最后n行，如果不指定n，默认显示后10行内容
```

```
基本语法：
#more 文件名称
```

注意：more命令在加载文件时，不是一点一点加载的，而是打开文件的时候就已经把文件内容加载完到内存中。

所以当打开文件比较大时，可能会出现卡顿情况

![more.png](imgs\more.png)

```
基本语法：
#less 文件名称
```

注意：less命令不是加载整个文件到内存，而是一点一点进行加载，相对于more而言，less读取大文件时，效率更

高一点

![less.png](imgs\less.png)

**文件统计命令**

```shell
基本语法：

#wc [选项] 文件名称

选项说明：

-l：表示lines，行数（以回车/换行符为标准）

-w：表示words，单词数（依照空格来判断单词数量）

-c：表示bytes，字节数
```

```shell
基本语法：

#du [选项] 统计的文件或文件夹

选项说明：

-s：summaries，只显示汇总的大小，统计文件夹的大小

-h：以较高的可读性显示文件或文件夹的大小（字节换算为mb之类的）
```

**文件处理命令**

```
基本语法：

#find 搜索路径 [选项] 文件名或类型名

选项说明：  

-name:指定要搜索文件的名称，支持*星号通配符

-type:指定搜索的文件类型，f代表普通文件，d代表文件夹
-size: 	指定文件大小 

# find /var/log/ -size +4M			搜索/var/log 大于4M的文件


基本语法：
#grep [选项] 要搜索的关键词 搜索的文件名称

选项说明：
-n ：代表显示包含关键词的行号
功能：在文件中直接找到包含指定关键词的行，并高亮显示出这些信息

案例：
#grep root /etc/passwd
#grep -n root /etc/passwd 显示包含关键字root所在行的行号扩展
```

```
基本语法：

#echo "文本内容"
```

**输出重定向**

·>:标准输出重定向：覆盖输出，会覆盖原先的文件内容，如果重定向的文件不存在，会创建文件

·>>：追加重定向：追加输出，不会覆盖原始文件内容，只会在原始内容末尾继续添加

**软链接**

ln -s 文件 文件

ln -s 目录 目录

特点：1.无论是修改链接文件/文件夹还是源文件/文件夹，另一个文件/文件夹也会变化

2.删除链接文件，源文件不受影响，但删除源文件，链接文件会找不到

2.软连接源文件和链接文件会建立自己的inode号和block块，链接文件会存储源文件的文件名和inode号

3.可以跨分区

**硬链接**

ln 源文件 链接文件

1.无论是修改链接文件/文件夹还是源文件/文件夹，另一个文件/文件夹也会变化

2.源文件和链接文件只要有一个存在，就可以被访问

3.源文件和链接文件供用同一个inode号

4.硬链接不能链接目录

**用户，组，权限管理**

```
#groudadd [选项 选项的值] 用户组的名称、

-g ：代表用户组的组ID编号。自定以组从1000开始，不能重复
```

默认情况下，添加的用户组都会放在一个系统文件中，文件位置 /etc/group

第一列：用户组的组名称

第二列：用户组的组密码，使用一个5x进行占位，安全起见

第三列：用户组的组ID编号，1-999是系统用户组的编号，1000以后的组ID是自定义组的编号

第四列：用户组内的用户信息（如果一个用户的附属组为该组，就会将该用户的名称显示在此位置）

```shell
#groupmod [选项 选项的值] 要修改的组名

选项说明：

-g：gid的缩写，修改用户组的组id

-n：name的缩写，修改用户组的名称
eg:
#groupmod -g 1060 -n yuntest test 将test用户组的编号和名称同时进行修改
```

```
#useradd [选项 选项的值] 用户名称

选项说明：

-g：代表添加用户时指定的用户所属组的主组

-s：代表指定用户可以使用的shell类型，默认为/bin/bash（拥有大部分的权限）还可以是/sbin/nologin，/sbin/nologon

不能用于登录操作系统。/bin/bash ==》给人使用 /sbin/nologin==》给软件使用

-G：代表添加用户时指定用户所属组的附属组，附加组可以指定多个，用，逗号隔开即可

-u：代表添加用户时指定的用户ID编号，ID编号同样也是从1000开始

-c：代表用户的注释信息：lat （龙傲天的账号）

-d：代表用户的家目录，默认为/home/用户名称，可以使用-d进行更改

-n：取消建立以用户名称为名的群组（了解）
```

/etc/passwd文件，每创建一个用户，就会在此文件中追加一行

```
#id 用户名称

功能：查询某个指定的用户信息
```

```
#usermod [选项 选项的值] 用户名称

-s：修改用户可以使用的shell类型，如/bin/bash => /sbin/nologin

-g:修改用户所属的主组的编号

-l：login name 修改用户的名称

扩展：

-L：锁定用户，锁定后用户无法登录系统lock

-U：解锁用户unlocl

-G：修改用户的附属组的编号信息

-d：修改用户的家目录

-c：修改用户的备注信息
```

```
① 方式一：交互式 
#为用户添加修改密码
#passwd 用户名称

功能：为某个用户设置密码（添加或修改）

② 方式二：非交互式 

# echo "123" | passwd --stdin xiaohong
```

```
#切换用户

#su - 用户名
```

```
#userdel [选项] 用户名称

选项说明：

-r: 删除用户的同时，删除掉用户的家目录
```

#### **权限**

**Linux****用户的身份类别**

Linux系统一般将文件权限分为3类：

read（读）

write（写）

execute（执行）

三大用户拥有对文件的读、写、执行权限

user:文件的拥有者

谁创建的文件，谁就是该文件的拥有者

​	1.group:文件所属组内用户

​	2.group所属组内用户

​	3.other其他用户代表这些人既不是文件的拥有者，也不是文件所属组内的用户，这些用户就称为other其他用户

​	4.特殊用户root

在linux操作系统，root拥有最高权限（针对所有文件），所以权限设置对root账户没有效果

在linux系统中，三大类用户有简称形式user（u）、group（g）、other（o）

查看文件权限

![ll.png](imgs\ll.png)

```
基本语法：

#ls -l

简写为

#ll

注意：ll命令是红帽以及centos系统特有的命令，其他操作系统可能不支持
```

**文件类型以及权限解析**

Linux中文件类型一共有7种

-：普通文件

d:目录文件

l：软链接（类似windows的快捷方式）

b：block，块设备文件（硬盘、光驱）

p：管道文件

c：字符设备文件（串口设备、光猫）

s：套接口文件

**文件或文件夹的权限设置（字母、数字）**

三个方面：

第一个：确认要给哪个身份赋予权限u、g、o、ugo（a） 

第二个：添加权限（+）、删除权限（-）、赋予权限（=）

第三个：确认赋予用户什么样的权限，r、w、x

```
基本语法：

#chmod [选项] 权限设置 文件或文件夹的名称

选项说明：

-R:递归设置，主要用于文件夹的权限设置
```

eg:给/linux目录设置权限，要求拥有者权限为rwx，组内用户权限为r-x，其他用户为r-x

```
#chmod -R u=rwx,g=rx,o=rx linux chmod -R 755 linux
```

第一个数字：代表文件拥有者权限u

第二个数字：代表文件所属组内用户权限

第三个数字：代表其他用户权限

![权限.png](imgs\权限.png)

```
#给test.txx文件设置权限，要求拥有者权限为rwx，组内用户权限为r-x，其他用户为r-x

#chmod 755 test.txt
```

注意：删除文件必须同时具有写和可执行权限

**文件拥有者，文件的组的设置**

```
#chown [选项] user名 文件名称

选项说明：

-R：代表递归修改，主要针对文件夹
```

```
#chgrp [选项] 新文件所属组名称 文件名称

选项说明：

-R：代表递归修改，主要针对文件夹
```

同时 修改：

```
#chown [选项] 文件拥有者名称:文件所属组名称 文件名称

或

#chown [选项] 文件拥有者名称.文件所属组名称 文件名称

选项说明：

-R ：代表递归修改，主要针对文件夹
```

**特殊权限**

s,无论哪个用户执行时临时拥有文件拥有者的权限

```
#chmod u+s /usr/bin/passwd
或
#chmod 4755 /usr/bin/passwd
```

t,主要用于文件夹，只允许文件的创建者和root用户删除文件（防止误删文件）

```
\# chmod -R o-t 文件夹名称

或

\# chmod -R 0777 文件夹名称
```

## 隐藏权限(对管理员也生效)

***\*基本语法：\****

chattr +a/+i  文件名

 

选项:

a		限制用户只能追加内容，不能删除，剪切文件

i		限制用户只能查看文件内容，不能删除，剪切，追加，修改文件	

```
#chattr +i /tmp/test.txt
```

查看隐藏权限

***\*基本语法：\****

```
lsattr  文件名
```

删除隐藏权限

\# chattr -a  a.txt 

\# chattr -i  b.txt

**ACL****访问控制列表**

```
#查询某个文件的acl

#getfacl 文件或目录名称
```

修改acl列表

```
#setfacl [选项] 文件或目录名称

选项说明：

-m:修改acl策略

-x:去掉某个用户或某个组的权限

-b:删除所有acl策略

-R：递归，主要针对文件夹
```

eg:

```
#针对目录给zhangsan用户设置acl权限

#setfacl -R -m u:zhangsan:rwx linux

#setfacl -R -m g:fq:rwx linux
```

umask决定了，这个用户创建文件和文件夹时的默认权限

例如：用户的umask是002，创建文件时默认权限是666-002=664

​	创建文件夹时，默认的权限是777-002=775

修改umask的值

```
#umask 022 临时修改

#永久修改

#vim ~/.bashrc 

#如何修改？

#在文档末尾添加 umask 022 即可

#保存并退出

#su切换用户生效
```

Linux自有服务

```
#systemctl start|stop|restart|enable|disable 系统服务名称（sshd)
```

\#systemctl [选项]

选项说明：

```
list-units --type service :列出所有启动的服务

list-units --type service --all：列出所有服务
```

**Linux系统运行级别**

0 shutdown 立即关机 shutdown now

1 单用户模式

2 多用户模式 （没有NFS 文件共享）

3 字符模式

4 自定义模式

5 图形模式

6 重启模式

```
#init 级别
#init 0
#init 1
#init 2
#init 3
#init 4
#init 5
#init 6
```

ntp时间同步

\#基本语法：

\#ntpdate NTP服务的IP地址或域名



```
#ntpdate cn.ntp.org.cn

#ntpdate 203.107.6.88
```

启动ntpd服务

```
#systemclt start httpd

开机启动

#systemctl enabe httpd
```

**rpm安装软件**

```
#rpm -qa | grep 要查询的软件名称

选项说明：

-q：query 查询

-a：all，代表所有

案例：

#rpm -qa | grep Ũrefox
```

```
#rpm安装软件

#rpm -ivh 软件包的名称.rpm

选项说明：

i：install 安装

v：显示进度条

h：表示以“#”形式显示进度条
```

升级软件包

```
#rpm -Uvh 升级后的软件包名称.rpm

选项说明：

-U：Update ，更新操作
```

本地yum源的安装：

网络源：

1. 通过命令

curl -o /etc/yum.repos.d/CentOS-Base.repo \

https://mirrors.aliyun.com/repo/Centos-7.repo

下载一个repo文件到/etc/yum.repos.d/目录下直接配置成功

2. 清理缓存即可

\# yum clean all 

 

\#查看二进制文件是由哪个包安装的

```
yum provides 二进制文件
```

\#搜索yum源里的包

```
yum search cc
```



**使用源码安装****nginx**

**1****、安装依赖包**

```
#yum -y install zlib zlib-devel openssl openssl-devel pcre-devel

#yum -y install gcc gcc-c++ autoconf automake libtool make cmake
```

**2****、下载源代码**

```
#wget http://nginx.org/download/nginx-1.22.1.tar.gz
```

**3****、解压****nginx****源代码**

```
#tar -zxvf nginx/1.22.1.tar.gz
```

**4****、编译安装**

```
#ls

nginx-1.22.1

#cd nginx-1.22.1

#./configure

#make

#make install
```

**5****、查看****nginx****版本，判断软件是否安装成功**

```
#cd /usr/local/nginx

#./sbin/nginx -v

nginx version: nginx/1.22.1
```

**进程管理：**

***\*语法格式：\****

```shell
ps [基本选项] [列表选择选项] [输出格式选项] [显示线程选项] [杂项选项]
```

 常用：

查看进程的属性信息：

ps -ef

查看进程使用资源信息

ps -aux



基本选项： 　　　　　

-A ：	所有的进程均显示出来，与 -e 具有同样的效用

-f ：	输出全格式(默认包含：UID、PID、PPID、C、STIME、TTY、TIME、CMD 字段）

-a ： 	显示现行终端机下的所有进程，包括其他用户的进程

-u ：	以用户为主的进程状态 

x  ：	通常与 a 这个参数一起使用，可列出较完整信息

L  ：	列出所有字段名称

-o | o | 	--format <format> ：指定输出字段

| ***\*关键字\**** | ***\*描述\****                                               |
| ---------------- | ------------------------------------------------------------ |
| USER             | 进程的运行用户账户                                           |
| TTY              | 进程的终端（切换终端的快捷键ctrl+Alt+F1~F6，用tty命令查看当前终端号）TTY为“?”时,表示不依赖任何终端运行 |
| SID              | 会话ID号                                                     |
| PGID             | 进程组ID号                                                   |
| PID              | 进程ID号                                                     |
| PPID             | 父进程ID号                                                   |
| %CPU             | CPU的占用率                                                  |
| %MEM             | 内存占用率                                                   |
| VSZ              | 允许进程访问的虚拟内存大小，单位KB , 包含了：swap空间占用    |
| RSS              | 进程在内存的实际占用大小单位KB ,包含了：可共享内存，不包含：swap空间占用 |
| STAT, S          | 进程的状态                                                   |
| TIME             | 进程运行的时长                                               |
| COMMAND, CMD     | 进程运行的命令                                               |
| WCHAN            | 显示进程正在等待的内核事件名称，正在运行中的进程将显示一个[ - 破折号 ] |
| PRI              | 进程优先级的当前有效值，数值越大，优先级越高，动态变化范围是： 0 ~ 139 |
| NI               | 进程优先级的用户调整值，数值越大，优先级越低，手动调整范围是：-20 ~ 19 |

```
<	高优先级（对其他用户不好）

N	低优先级（对其他用户很好）

L	已将页面锁定到内存中（用于实时和自定义IO）

s	是会话领导者

l	是多线程的（使用CLONE_THREAD，就像NPTL pthreads一样）

+   在前台进程组中
```

```
#动态查看进程信息
# top
```

***\*参数:\****

-d 	指定每两次屏幕信息刷新之间的时间间隔。

-p 通过指定监控进程ID来仅仅监控某个进程的状态

-S	指定累计模式，每个进程的CPU时间为该进程及关闭的子进程所累加的时间

-s	使top命令在安全模式中运行。这将去除交互命令所带来的潜在危险

-i 	使top不显示任何闲置或者僵尸进程

-c	显示整个命令行(包括命令参数),而不只是显示命令名

-b	批量模式执行，用来将输出重定向到指定文件，一般配合-n 指定输出几次统计信息

-n  表示更新n次后终止更新显示,选项后面根一个整数。

-u	显示指定用户

-U 	显示指定UID的进程

***\*top命令所展示字段信息的含义：\****

| 字段    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| PID     | 进程id                                                       |
| USER    | 进程所有者的有效用户名                                       |
| PR      | 优先级                                                       |
| NI      | 优先级调整值(即nice值),值越小优先级越高                      |
| VIRT    | 进程使用的虚拟内存总量，单位kbtye,VIRT=SWAP+RES              |
| RES     | 进程使用的未被换出的物理内存大小，单位Kbtye，RES=CODE+DATA   |
| SHR     | 共享内存，单位Kbtye                                          |
| S       | 睡眠状态，各种进程(状态标识符）D=不可中断的睡眠状态R=运行S=睡眠T=跟踪/停止Z=僵尸进程 |
| %CPU    | 上次更新到现在的CPU占用百分比                                |
| %MEM    | 进程使用的物理内存百分比                                     |
| TIME+   | 进程使用的CPU时间总计，单位1/100秒                           |
| COMMAND | 命令名/命令行                                                |

***\*top命令前五行输出重点信息详解：\****

 

**1）*****\*load average平均负载率\****

![img](C:\Users\文超\OneDrive\桌面\云计算\imgs\cpu负载率.png) 

 

过去1、5、15分钟的cpu负载，与CPU核心数相关，1核 平均负载小于1正常，2核 平均负载小于2 正常，3核小于3正常。以此类推，值越大表示CPU负载越重，一般最好不要超过CPU负载的80%。

 

***\*2) 任务（进程\****）

![img](C:\Users\文超\OneDrive\桌面\云计算\imgs\进程信息.png) 

 

显示：任务总数184个，1个正在运行的进程，183个睡眠进程，0个停止进程，0个僵尸进程。

 

***\*3) CPU状态\****

![img](C:\Users\文超\OneDrive\桌面\云计算\imgs\cpu信息.png) 

 

3.1 	 us	用户空间占用CPU百分比

6.6 	 sy	内核空间占用CPU百分比

0.0 	 ni	用户进程空间内改变过优先级的进程占用CPU百分比

89.0 id	空闲CPU百分比

0.0  wa	等待输入输出的CPU时间百分比

0.0  hi	硬件CPU中断占用百分比

0.5  si	软中断占用百分比

0.0  st	虚拟机占用百分比	

**4)** ***\*物理内存状态\****

![img](C:\Users\文超\OneDrive\桌面\云计算\imgs\物理硬件信息.png) 

 

5989132 total			物理内存总量=free+used+buff/cache

4943092 free			空闲内存总量

563872 	 used			使用的物理内存总量

482168 	 buff/cache		用于内核缓存的内存量

 

 

 

***\*5) SWAP交互分区状态\****

![img](file:///C:\Users\文超\AppData\Local\Temp\ksohtml32296\wps5.jpg) 

 

2097148 total			交换分区总量

2097148 free			空闲交换分区总量

0 used					使用的交换分区总量

5159496 avail Mem		可用的物理内存（包含：MemFree和可回收的已用内存)



***\*top 运行中界面中，按钮\*******\*功能\*******\*（区分大小写）：\****

 

```
1 		显示所有CPU占用情况

s  	改变画面更新频率

l  	关闭或开启第一行 top 信息的表示

t  	关闭或开启第二行 Tasks 和第三行 Cpus 信息的表示

m  	关闭或开启第四行 Mem 和 第五行 Swap 信息的表示

N  	以 PID 的大小的顺序排列表示进程列表

P  	以 CPU 占用率大小的顺序排列进程列表

M  	以内存占用率大小的顺序排列进程列表

b    打开/关闭加亮效果	

h  	显示帮助

n  	设置在进程列表所显示进程的数量

q  	退出 top
```

netstat 和 ss 命令

```
# netstat -r    # 显示：本机路由表
# netstat -tunp  # 显示：网络连接，即：socket网络套接字
# netstat -tunlp  # 显示：侦听端口，即：socket网络套接字
```



```
# ss -tunp 		显示：网络连接，即：socket网络套接字
# ss -H -tunp	显示：网络连接，即：socket网络套接字，不显示标题行 注意：更新之		后iproute软件包，才支持 -H 选项
# ss -tunlp		显示：侦听端口，即：socket网络套接字
# ss -H -tunlp	显示：侦听端口，即：socket网络套接字，不显示标题行 注意：更新之		后iproute软件包，才支持 -H 选项
```

t:tcp连接

u:utp连接

n:表示不将服务名解析为端口

p:表进程

**lsof命令列出进程打开的文件**

1. 列出：所有活动的进程所打开的文件

\# lsof



2. 列出：以user01用户身份运行的进程所打开的文件

\# lsof -u user01



3. 列出：指定PID进程号的进程所打开的文件……以进程号8533为例

\# lsof -p 8533



4. 列出：执行指定命令的进程所打开的文件

\# lsof -c vi



5. 列出：指定port端口的进程信息 

\# lsof -i:22

 

***\*输出字段解释：\****
COMMAND：	进程的名称

PID：			进程标识符

PPID：			父进程标识符（需要指定-R参数）

USER：			进程所有者

FD：			文件描述符，应用程序通过文件描述符识别该文件

TYPE : 			与文件关联结点的类型；

DEVICE : 		设备号

SIZE/OFF : 		文件大小/偏移量，以字节为单位

NODE : 			文件节点

NAME : 		文件挂载点和文件所在的系统

查看内容使用情况

```
free -h
```

查看剩余磁盘空间

```
df -h
```

### **进程的基本管理**

***\*控制进程前台、后台运行\****

ctrl + z  	将正在执行的任务暂停放入后台

jobs -l  		查看后台任务

fg 任务编号  将任务调回前台

bg 任务编号  在后台继续运行任务



#### **将进程放入后台运行**

使用& 符将进程放入后台运行

```
# ping 127.0.0.1 > /tmp/ping.txt  &

# jobs -l
```

***\*注意：\*******\*以上打开进程后不管是放入后台还是放在前台，关闭终端进程关闭\****

#### **将进程放入后台运行并脱离终端**

 

***\*语法：\****

nohup 命令 &

 

功能：nohup故名思议就是忽略SIGHUP信号，确保：运行中的后台作业，一律不接收当前会话的leader领导进程发来的SIGHUP信号。一般搭配&一起使用，方可不占用控制终端的标准(错误)输出，而是直接输出到nohup.out文件中。&表示以后台进程组来运行一个进程组。

```
\# nohup ping 127.0.0.1 > /tmp/ping.txt &

\# jobs -l
```

将ping命令在后台执行，并且不随着bash的关闭而关闭

#### **SIGNAL信号种类**

 

| ***\*信号\**** | ***\*值\**** | ***\*描述\****                                               |
| -------------- | ------------ | ------------------------------------------------------------ |
| SIGKILL        | 9， KILL     | 强制立即终止进程                                             |
| SIGTERM        | 15, TERM     | 正常关闭进程                                                 |
| SIGHUP         | 1， HUP      | 终端挂起信号，通常用于通知进程重新加载配置文件或重新启动     |
| SIGSTOP        | 19，STOP     | 停止信号，暂停进程的执行                                     |
| SIGCONT        | 18,  CONT    | 继续信号，恢复被暂停的进程的执行                             |
| SIGINT         | 2,  INT      | 终端中断信号，通常由Ctrl+C键触发，用于中断正在运行的进程     |
| SIGQUIT        | 3,  QUIT     | 不同于SIGINT，这个是会产生core dump文件的。 进程终止并且产生core文件 |
| SIGUSR2        | 12,  USR2    | 用户自定义信号 进程终止                                      |
| SIGUSR1        | 10,  USR1    | 用户自定义信号 进程终止                                      |
| SIGWINCH       | 28,  WIHCH   | 当控制终端窗口大小变化时，SIGWINCH信号发送到进程             |

 ***\*基本语法：\****

***\*kill  选项  进程ID\****

 

***\*常用选项\****

```
-l		列出信号名称

-n 		SIG 是信号编号

-s		SIG 是信号名称
```

 

***\*信号编号含义\****

```
9 	杀死进程，强制结束进程

15 	正常结束进程，是kill命令的默认信息
```

杀死所有同名的进程

***\*基本语法：\**** 

```
#killall [信号编号] 进程名称
```

### **查看进程的优先级**

 

```
PR 				优先级 ，数值越小优先级越高

NI 				优先级，数值越小优先级越高。可以认为更改。

NICE = 绅士 	NICE值越高 代表这个人越绅士

NI值范围：		-20~19
```

#### 1）**使用renice命令调整进程的优先级**

将pid为1220的进程的nice值修改为-10

```
#renice -10 1220
```

#### **磁盘管理**

查看磁盘使用情况

```
# lsblk
```

查看各挂载点使用情况

```
# df -h
```

#### mbr分区方式（MBR <2TB fdisk 4个主分区或者3个主分区+1个扩展分区（N个逻辑分区））

添加磁盘-->磁盘的分区方式(总共有4个分区号，可分为：4个主分区或3个主分区和1个扩展分区，主分区用于存操作系统，扩展分区用于划分逻辑分区存储使用)-->添加文件系统（如：ext4文件系统）-->挂载使用

查看当前设备分区情况

```
# fdisk -l
```

1.添加硬盘

2.新建分区 fdisk /dev/sdb

3.更新分区表(刷新分区表)

4.格式化分区——>文件系统 mkfs.ext4 /dev/sdb1

5.挂载使用——>mount【开机自动挂载|autofs自动挂载】

#### GPT >2TB gdisk(parted) 128个主分区

打印指定设备分区表

```
# parted /dev/sdb print
```

创建GPT的分区表类型

```
# parted /dev/sdb mklabel gpt
```

创建分区

```
\# parted  /dev/sdb mkpart gpt 0% 5G

\# parted  -s  /dev/sdb mkpart gpt 5G 7G

\# parted  -s /dev/sdb mkpart gpt 7G  100%

#打印分区信息

\# parted  /dev/sdb print
```

挂载使用——>mount【开机自动挂载|autofs自动挂载】



逻辑卷：lvm

卷组：vg

物理卷：pv

***\*真实的物理设备\*******\*==>\*******\*逻辑上(命令创建)\*******\*==>\*******\*物理卷（pv）\*******\*==>\*******\*卷组（vg）\*******\*==>\*******\*逻辑卷（lv）\*******\*==>\*******\*逻辑卷格式化\*******\*==>\*******\*挂载使用\**** 

1.分区

```
\# parted /dev/sdb print

\# umount  /dev/sdb1

\# parted  /dev/sdb rm 1

\# parted -s /dev/sdb mkpart gpt 0% 3G

\# parted -s /dev/sdb mkpart gpt 3G 6G

\# parted -s /dev/sdb mkpart gpt 6G 100%

\# parted /dev/sdb print 
```

2.创建物理卷

```
\# pvcreate  [选项]  devices

\# pvcreate /dev/sdb{1..2}  或 # pvcreate /dev/sdb1 /dev/sdb2

\# pvs或 #pvscan 
```

3.第三步：创建卷组

```
\# vgcreate  [选项]  vg_name  pv_name

\# vgcreate  vg01 /dev/sdb{1..2}  或 # vgcreate  vg01 /dev/sdb1  /dev/sdb2

\# vgs 或 # vgscan
```

***\*第四步：创建逻辑卷\****

```
\# lvcreate  [选项]  vg_name
```

-L  指定逻辑卷大小

-n	指定逻辑卷名称

***\*注意\****：逻辑卷的空间是从卷组中分配的，因此逻辑卷的大小不能超过卷组的大小

```
\# lvcreate -L 4G -n lv01 vg01

\# lvs 或  # lvscan
```

***\*第五步：格式化逻辑卷\****

```
\# mkfs.xfs /dev/vg01/lv01
```

***\*第六步：挂载使用\****

```
\# mkdir /lvm

\# mount /dev/vg01/lv01  /lvm

\# lsblk 或 # df -h 
```

开机挂载（永久挂载）

```
\# echo "/dev/vg01/lv01  /lvm  xfs  default 0 0" >> /etc/fstab

\# tail -5 /etc/fstab 
```

扩容逻辑卷：

***\*第一步：查看lvm目录属于哪个卷组\****

```
\# df -h

\# lvs
```

***\*第二步：查看卷组的剩余空间\****

```
\# vgs
```

查看卷组空闲空间不足，需要先对卷组扩容，再进行逻辑卷扩容

需要准备物理卷，将物理卷做成物理卷，再将新的物理卷扩容到卷组！

***\*第三步：创建物理卷\****

```
\# parted /dev/sdb print

\# pvcreate /dev/sdb3

\# pvs
```

***\*第四步：扩容卷组\****

```
\# vgextend [选项] vg_name  pv_name

\# vgextend vg01 /dev/sdb3

\# vgs
```

***\*第五步：扩容逻辑卷\****

```
\# lvextend  [选项]  /path/lv_name
```

***\*常用选项：\****

-L   指定扩容大小

```
\# lvextend -L +4G /dev/vg01/lv01

\# lvs
```

***\*第六步：扩容文件系统\**** 

***\*ext4的文件系统扩容\****

```
用法： resize2fs  lv_name

# resize2fs /dev/vg02/lv01
```

***\*xfs的文件系统扩容\****

```
用法：# xfs_growfs lv_name

# xfs_growfs /dev/vg01/lv01
```

查看是否扩容成功

```
\# df  -h 
```

对逻辑卷做快照

#### **创建快照**

用法： lvcreate [选项]  lvm_name

-L		指定快照大小

-n		指定快照名称

-s		表示创建快照

 

```
\# lvcreate -s -n lv02_snap -L 1G  /dev/vg01/lv01
# lvs
```

#### **模拟lvm数据丢失**

```
\# ls

\# rm -rf /lvm/passwd

\# ls -l
```

#### **快照恢复数据**

用法：

```
\# lvconvert  [选项]  snap_name(逻辑卷的名称)
# lvconvert --mergesnapshot /dev/vg01/lv01_snap
# lvs
```

提示：恢复快照需要将原来的逻辑卷卸载，快照是一次性的，用完就会删除

#### **快照实现自动扩容****(扩展)**

 

***\*修改逻辑卷配置文件参数\****

```
\# vim /etc/lvm/lvm.conf 

snapshot_autoextend_threshold = 80 

snapshot_autoextend_percent = 20 
```

 

注意：当快照使用到80%时，自动扩容20%；当snapshot_autoextend_threshold = 100表示关闭自动扩容 

防火墙：

***\*启动防火墙\****

\# systemctl  start  firewalld 

 

***\*停止防火墙\****

\# systemctl  stop  firewalld 

 

***\*重启防火墙\****

\# systemctl  restart  firewalld 

 

***\*查看防火墙状态\****

\# systemctl  status  firewalld 

 

***\*设置开机启动\****

\# systemctl  enable  firewalld 

 

***\*设置开机不启动\****

\# systemctl  disable  firewalld 

 

***\*重载防火墙\****

\# systemctl  reload  firewalld

##### 防火墙zone的概念

| ***\*区域名\**** | ***\*默认规则（策略）\****                                   |
| ---------------- | ------------------------------------------------------------ |
| trusted          | 允许所有数据包                                               |
| home             | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh、mdns、ipp-client、amba-client与dhcpv6-client服务相关，则允许流量 |
| internal         | 等同于home区域                                               |
| work             | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh、ipp-client、dhcpv6-client服务相关，则允许流量 |
| public（默认）   | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh、dhcpv6-client服务相关，则允许流量 |
| external         | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh服务相关，则允许流量 |
| dmz              | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh服务相关，则允许流量 |
| block            | 拒绝流入的流量，除非与流出的流量相关                         |
| drop             | 拒绝流入的流量，除非与流出的流量相关                         |

***\*对应配置文件位置\****

\# ll /lib/firewalld/zones/

防火墙命令和策略：

***\*查看防火墙状态\****

\# systemctl status firewalld 或者 # firewall-cmd --state

 

***\*查看\*******\*当前活动zone\*******\*信息\****

\# firewall-cmd --get-active-zones

 

***\*查看默认的zone\****

\# firewall-cmd --get-default-zone

 

***\*设置默认的zone\****

\# firewall-cmd --set-default-zone=zone_name

 

***\*查看指定zone的详细信息\****

\# firewall-cmd --info-zone=zone_name

 

***\*查看当前zone可用的服务\****

\# firewall-cmd --list-services

 

***\*查看所有的zones\****

\# firewall-cmd --list-all-zones

***\*查看当前所有zone名称\****

\# firewall-cmd --get-zones

 

***\*查看现有的规则\****

\# firewall-cmd --list-all

 

***\*重新加载防火墙规则\****

\# firewall-cmd --reload

 

### **② 基本于服务配置firewalld策略**

 

#### **基于服务添加策略**

***\*基本语法：\****

临时修改(重载失效)

\# firewalld-cmd --add-service=service_name 

 

永久修改(重载生效)

\# firewalld-cmd --add-service=service_name --permanent

\# firewalld-cmd --reload

***\*案例：\****

安装httpd服务，并配置防火墙规则，httpd是一个web服务，安装后可以通过访问虚拟机的ip地址加上访问端口80，即可访问到一个apache服务的界面

 

***\*安装\****

\# yum -y install httpd

 

***\*启动\****

\# systemctl start httpd

 

***\*查看状态\**** 

\# systemctl status httpd

 

***\*查看端口\****

\# ss -taunlp |grep 80

***\*测试访问\****（不通，因为防火墙开放80端口）

http://192.168.10.128/

***\*配置firwalld规则\****

\# firewall-cmd --list-all

\# firewall-cmd --add-service=http 



***\*备注\****：服务必须在/usr/lib/firewalld/services目录中

\# ll /usr/lib/firewalld/services|grep http 

查看服务中是否有了httpd服务

\# firewall-cmd --list-all

永久开启http服务

\# firewall-cmd  --add-service=http --permanent

现在访问即可访问到

http://192.168.10.128/

#### **基于端口添加策略**

 

***\*基本语法：\****

临时修改(重载失效)

\# firewall-cmd --add-port=port/protocol

 

永久修改(重载生效)

\# firewall-cmd --add-port=port/protocol --permanent

\# firewall-cmd --reload

***\*案例：\****

临时修改

\# firewall-cmd --add-port=80/tcp 

 

永久修改

\# firewall-cmd --add-port=80/tcp --permanent

\# firewall-cmd --reload

 

 

#### **基于端口删除策略**

 

***\*基本语法：\****

临时修改(重载失效)

\# firewall-cmd --remove-port=port/protocol

 

永久修改(重载生效)

\# firewall-cmd --remove-port=port/protocol --permanent

\# firewall-cmd --reload

案例：

临时修改

\# firewall-cmd --remove-port=80/tcp 

 

永久修改

\# firewall-cmd --remove-port=80/tcp --permanent

\# firewall-cmd --reload

#### **基于规则添加策略**

 

***\*基本语法：\****

临时修改(重载失效)

\# firewall-cmd --add-rich-rule='rule "规则明细"  action ' 

 

永久修改(重载生效)

\# firewall-cmd --add-rich-rule='rule "规则明细"  action ' --permanent

 

 

action		对添加的策略实现的动作

***\*动作\****

Reject		拒绝，对进来的流量直接拒绝

Accept		接收，对进徕的流量接收 		

Drop		丢弃，对进来的流量包直接丢弃

 

***\*reject与drop区别\****

DROP动作只是简单的直接丢弃数据，并不反馈任何回应。需要Client等待超时，Client容易发现自己被防火墙所阻挡

 

REJECT动作则会更为礼貌的返回一个拒绝(终止)数据包，明确的拒绝对方的连接动作。连接马上断开，Client会认为访问的主机不存在。

 

 

***\*使用Firewalld和rich rules常见的网络访问控制场景：\****

 

***\*源和目标地址过滤：\****

允许特定网段地址的访问：

\# firewall-cmd --add-rich-rule='rule family=ipv4 source address=192.168.10.0/24 accept' 

 

允许特定ip地址访问：

\# firewall-cmd --add-rich-rule='rule family=ipv4 source address=192.168.10.10 accept'

 

 

***\*服务\*******\*过滤：\****

允许特定的服务的访问：

\# firewall-cmd --add-rich-rule='rule family=ipv4 service name=http accept'

 

***\*协议过滤：\****

允许特定协议的访问：
\# firewall-cmd --add-rich-rule='rule family=ipv4 protocol value=icmp accept'

 

 

***\*源和目标端口过滤：\****

允许从特定端口访问特定目标端口：

\# firewall-cmd --add-rich-rule='rule family=ipv4 service name=ssh source port=1024-65535 destination port=22 accept'

 

 

***\*高级条件过滤：\****

限制特定IP地址的访问速率：

\# firewall-cmd --add-rich-rule='rule family=ipv4 source address=192.168.0.10 limit value=3/m burst=5 accept'

 

表示源地址192.168.0.10 允许每分钟三次封包，每次封包的数量包不能超过5个！

***\*网络地址转换和端口转发\****：

启用源地址转换：

\# firewall-cmd --add-rich-rule='rule family=ipv4 source address=192.168.0.10 masquerade'

 

启用源地址192.168.0.10伪装

 

 

#### **基于规则删除策略**

 

***\*基本语法：\****

临时修改(重载失效)

\# firewall-cmd --remove-rich-rule='rule "规则明细"  action ' 

 

永久修改(重载生效)

\# firewall-cmd --remove-rich-rule='rule "规则明细"  action ' --permanent

\# firewall-cmd --reload

永久删除

\# firewall-cmd \

--remove-rich-rule='rule family=ipv4 source address="192.168.10.0/24" port port="80" protocol="tcp" accept' --permanent

 

\# firewall-cmd --reload

计划任务：

### **周期性计划任务crontab**

 

***\*基本语法：\****

\#crontab [选项]

-l：list，显示当前已经设置的计划任务

-e：使用vim编辑器编辑计划任务文件

 

 

#### **①** **计划任务的编辑**

 

计划任务的规则语法格式，以行为单位，一行就是一个计划：分 时 日 月 周 要执行的命令（必须使用命令的完整路径，可以使用which查看）

 

***\*取值范围\****

分：0-59

时：0-23

日：1-31

月：1-12

周：0-7，0和7表示星期天

 

***\*四个符号\**** ：

*：表示取值范围中的每一个数字

-：做连续区间表达式的，表示1-7；可以写为1-7

/：表示每多少执行一次，如：每30分钟一次，*/30

,：表示多个取值，例如每个月的1号5号15号执行，1，5，15

#### **计划任务案例**

 

***\*案例1\****：每月1、10、22日的4：45 重启network服务

 

***\*定制格式\****

分 时 日 月 周 /usr/bin/systemctl restart network

45 4 1,10,12 * * /usr/bin/systemctl restart network

 

 

***\*案例2\****：每周六、周日的1：10分 重启network服务

 

***\*定制格式\****

分 时 日 月 周 /usr/bin/systemctl restart networktl restart network

10 1 * * 6,7 /usr/bin/systemctl restart network

 

 

***\*案例3\****：每天18：00到23：00之间每隔30分钟重启network服务

 

***\*定制格式\****

分 时 日 月 周 /usr/bin/systemctl restart networktl restart network

*/30 18-23 * * * /usr/bin/systemctl restart networktl restart network

***\*案例4\****：每隔两天的上午8点到11点的第三分钟和第十五分钟执行一次重启network服务

 

***\*定制格式\****

分 时 日 月 周 /usr/bin/systemctl restart networktl restart network

3,15 8-11 */2 * * /usr/bin/systemctl restart networktl restart network

 

 

***\*案例\*******\*5:  每月每周每天每小时每分钟，创建一个内容为1的文件名为readme.txt的文件\**** 

 

\#crontab -e

\* * * * * /usr/bin/echo 1 >> /root/readme.txt

 

***\*注 意：\**** 计划任务设置完成后，确保计划任务服务是运行状态

\#systemctl status crond

\#systemctl start crond

 

 

#### **③ 计划任务权限**

 

crontab 通过 /etc/cron.allow（白名单） 和 /etc/cron.deny(黑名单) 文件来限制用户是否可以使用 crontab

***\*规则如下：\****

l 如果文件/etc/cron.allow存在，则只允许在cron.allow提及的用户名使用。

l 如果/etc/cron.allow不存在，则检查/etc/cron.deny，然后允许cron.deny未提及的每个用户名在使用。

l 空的/etc/cron.deny表示每个用户都可以使用crontab。

l 如果两者都不存在，则只允许超级用户在使用。

l 白名单优先级优于黑名单，如果一个用户同时存在两个名单文件，则会被默认允许创建计划任务

 

 

 

 

 

 

 

 

#### **④ 计划任务的保存文件**

 

计划任务的文件具体保存在/var/spool/cron/用户名称

 

案例：

\# crontab -e

*/5 * * * *  /usr/bin/touch  /tmp/linux.txt

 

\# su - u1

$ crontab -e

*/5 * * * *  /usr/bin/touch  /tmp/u1.txt

$ exit

 

\# ll /var/spool/cron/

***\*注意\****： 如果删除计划任务文件，等于删除了计划任务，也可以通过创建文件来创建计划任务

 

 

 

 

#### **⑤ 计划任务日志文件**

 

在实际应用中，一般会通过计划任务日志，去查看任务的运行情况

 

\# tail -f /var/log/cron

### **一次性计划任务at**

 

在linux系统下，有两个命令可以实现计划任务：crontab与at（第三方安装）

crontab：每天定时执行计划任务

at：一次性定时执行任务

 

 

#### **①** **安装at** 

 

图形化自带at，最小化需安装

 

\#yum install at -y

 

 

 

#### **②** **启动atd服务**

 

\# systemctl start atd

\# systemctl enable atd

 

 

#### **③ 案例**

 

***\*案例1\****：三天后下午5点执行/bin/ls

 

\# at 5pm+3 days

at>/bin/ls >> /root/readme.txt

at>ctrl+d

 

***\*注意\****：按键盘上的ctrl + d	键保存退出

 

 

 

 

***\*案例2\****：明天下午18点，输出时间到指定的文件中

\# at 18:00 tomorrow

at>date > /root/readme.txt

at>ctrl+d (退出)

 

***\*查看还没有执行的计划任务\****

\# atq 或 # at  -l 

***\*查看计划任务的详细信息\****

\# at -c 2

***\*删除指定的计划任务\****

\#atrm  任务id 删除任务

 

\# atrm 2 

\# atrm 3

 

 

***\*计划任务权限\****

 

at通过 /etc/at.allow（白名单） 和 /etc/at.deny(黑名单) 文件来限制用户是否可以使用 at

 

规则与crontab一致！
