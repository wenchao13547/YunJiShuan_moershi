Docker安装：

（1）卸载旧版本

```
yum remove docker \

docker-client \

docker-client-latest \

docker-common \

docker-latest \

docker-latest-logrotate \

docker-logrotate \

docker-engine
```

2）安装docker管理工具，yum-utils获取yum-config-manager

```
# yum install -y yum-utils device-mapper-persistent-data lvm2
```

**docker安装与使用**

**一 、安装docker**

**2、docker安装**

3）获取docker-ce.repo

```
#配置官方docker镜像
# yum-config-manager  --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
#查看 YUM 仓库及其相关信息
# yum repolist
#查看yum源里的docker
# yum list | grep docker-ce
```

4）安装docker-ce

```
# yum install docker-ce
```

如需指定版本安装执行下述命令：

```
# 查看可以安装的版本
# yum list docker-ce --showduplicates | sort -r
```

启动docker服务

```
# systemctl enable docker

# systemctl start docker
```

配置docker镜像加速（替换镜像源为国内镜像源）

**Docker镜像与Docker容器的关系**

● Docker 镜像（就像安装包）：

是静止的容器，它为容器提供恒定不变的基础数据。

● Docker 容器（就像软件）：

是运行的容器，它以镜像为基础数据，其新增数据不会写入镜像。

Docker镜像管理：

查看本地镜像

```
docker images
```

各个选项说明:

REPOSITORY：表示镜像的仓库源，TAG：镜像的标签，同一仓库源可以有多个 TAG，代表这个仓库源的不同个版本，IMAGE ID：镜像ID，CREATED：镜像创建时间，SIZE：镜像大小

在docker仓库中搜索镜像

```
docker search xxx

例：docker search centos:7
```

拉取镜像

```
docker pullh xxx

例：docker pull centos:7
```

\# 采用国内的docker镜像源，加快镜像的拉取速率

```
cat > /etc/docker/daemon.json <<EOF
{
"registry-mirrors": [
"http://hub-mirror.c.163.com"
]
}
EOF

systemctl daemon-reload
systemctl restart docker
```



镜像传输

docker save -o 备份文件名.tar 镜像名            #把指定镜像导出为一个文件

docker load -i 备份文件名.tar                     # 把文件导入为镜像



删除镜像

docker rmi 镜像名 

docker images -q #只列出镜像的编号

清空镜像：

docker images -q |xargs docker rmi

修改镜像源标签，将centos源下版本为7，改为centos源下版本为new

\# docker tag centos:7 centos:new

#### Docker容器管理：

创建容器但不会运行容器

```
# 创建容器，但是不立即运行

docker create [--name 容器别名] [-p 本机端口:容器端口] 镜像名

# 创建容器，并且立即运行

docker run -d [--name 容器别名] [-p 本机端口:容器端口] 镜像名
```

常用选项：

-t 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上

-i 让容器的标准输入保持打开

-d 后台运行

```
举例：

docker run --name mycentos centos:7 #前台运行

docker run -it --name myc1 centos:7 # 前台运行，并分配一个终端

docker run -it -d --name myc1 centos:7 # 后台运行，并分配一个终端
```

**● 查看容器**

```
docker ps #查看运行状态的容器

docker ps -a #查看所有的容器包括停止的容器
```

容器创建后可执行：

```
docker stop 容器名 #关闭容器

docker start 容器名 #启动容器

docker restart 容器名 #重启容器

docker rm 容器名 #删除容器

-f #强制删除 包括运行中的容器
```

**基于容器创建镜像**

```
docker commit 容器名 镜像名

例：docker commit nginx01 mynginx:1.2

再基于修改后的自定义镜像创建容器
docker run -d --name myweb01 mynginx:1.2
```

查看容器日志

```
docker logs 容器id
```

查看容器详细信息

```
docker inspect 容器id

例：docker inspect nginx:1.18
```

**进入运行的容器**

我们通常容器都是使用后台方式运行的，如果需要修改一些配置，就要进入容器中。

方式一：

```
docker exec -it 容器id bashshell

例：# docker exec -it c1 /bin/bash
```

方式二(不常用）：

```
docker attach 容器id

例：# docker attach c1
```

区别：

​	docker exec #进入当前容器后开启一个新的终端，可以在里面操作。（常用）

​	docker attach # 进入容器正在执行的终端

**复制文件**

(1）复制容器文件到主机

docker cp 容器id:容器内路径 主机目的路径

例：docker cp centos7-2:/tmp/1.txt ./

(2）复制主机文件到容器

docker cp [选项] 源路径 容器名:目标路径

例：docker cp 2.txt centos7-2:/tmp/ #把宿主机的文件复制到容器



**网络类型**

查看默认网络

docker network ls #默认有三种网络类型

host 模式：使用--net=host 指定，能与宿主机在同一个网络中通信，但是没有独立IP地址。

none 模式：Docker 容器拥有自己的Network Namespace ，但是，并不为Docker 容器进行任何网络配置。

bridge 模式是docker 的默认网络模式，不用--net 参数，就是bridge模式。相当于VMware 中的NAT模式

**网络管理**

\#创建自定义网络

 #创建网络，默认是bridge模式

```
docker network create --subnet=192.168.1.0/24 mynet

docker network ls #查看网络
```

\#创建容器使用自定的网络

```
docker run -d --net=mynet --name nginx06 nginx
```

\#删除网络

```
docker network rm mynet

docker network --help #查看和网络相关的操作
```

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/docker架构图.png)

仓库管理

（1）拉取管理仓库镜像

```
docker pull registry
```

（2）运行容器

```
docker run -d \ 

-v /home/docker/registry:/var/lib/registry \ 

-p 5000:5000 \ 

--restart=always \ 

--name registry \

registry
```

使用卷挂载，便于容器镜像持久保存

验证仓库可用性

[root@localhost docker]# curl http://192.168.100.140:5000/v2/_catalog

可以看到上传到仓库的镜像

{"repositories":[]}

上传镜像到私有库

```
docker tag nginx 192.168.100.140:5000/nginx #镜像重新打标记

docker push 192.168.100.140:5000/nginx
```

推送需要https协议，需要修改配置文件

```
vim /etc/docker/daemon.json
{
"registry-mirrors": ["http://hub-mirror.c.163.com"],
"insecure-registries": ["192.168.100.140:5000"]
}
```

重启服务再次推送

拉取镜像

```
docker pull 192.168.100.140:5000/nginx
```

**基于用户密码验证**

创建将保存凭据的文件夹

```
mkdir -p /data/auth
```

安装htpasswd工具。

```
yum -y install httpd-tools
```

创建验证用户admin

```
# htpasswd -Bbn admin 123 > /data/auth/passwd
# cat /date/auth/passwd
```

将auth目录挂载到容器中

```
docker run -d -p 5000:5000 --restart=always \ 
--name reg -v /data/auth:/etc/registry/auth \ 
-v /data/docker.registry/var/lib/registry:/var/lib/registry \ 
-e "REGISTRY_AUTH=htpasswd" \ 
-e "REGISTRY_AUTH_HTPASSWD_REALM=Registr Realm" \ 
-e "REGISTRY_AUTH_HTPASSWD_PATH=/etc/registry/auth/passwd" \
registry:latest
```

5)测试访问

[root@localhost ~]# curl http://192.168.163.10:5000/v2/_catalog # 需要验证才能访问

{"errors":[{"code":"UNAUTHORIZED" , "message":"authentication required" , "detail":[{"Type":"registry" , "Class":"" , "Name":"ca

talog" , "Action":"*"}]}]}

登录

```
docker login 192.168.100.140:5000

Username (): admin

Password:
```

7)上传镜像并查看

```
docker tag nginx 192.168.100.140:5000/nginx #镜像重新打标记
docker push 192.168.100.140:5000/nginx
# curl http://192.168.100.140:5000/v2/_catalog
```

可以看到{"repositories":["nginx"]}

**Docker-Compose安装**

Docker-Compose 是 Docker 的独立产品，因此需要安装 Docker 之后再单独安装 Docker-Compose。

1.下载cocker-compose

```
\# curl -SL https://github.com/docker/compose/releases/download/v2.7.0/docker-compose-linux-x86_64 -o

/usr/local/bin/docker-compose
```

2.给docker-compose执行权限

```
\# chmod +x /usr/local/bin/docker-compose
```

3.使用docker-compose

```
\# docker compose version
```

4.卸载docker-compose

御载时删除即可：

```
\# rm -rf /usr/local/bin/docker-compose
```

**Docker-Compose常用命令**

docker-compose -h 

查看帮助

docker-compose up 

启动所有docker-compose服务

docker-compose up -d 

启动所有docker-compose服务并后台运行

docker-compose down 

停止并删除容器、网络、卷、镜像

docker-compose exec 容器id /bin/bash

1.进入容器实例内部 docker-compose exec 容器id /bin/bash

docker-compose exec 服务名/bin/bash

2.这里的服务名是，docker-compose.yml中指定的服务名称

docker-compose ps 

显示当前docker-compose编排过的运行的所有容器

docker-compose top 

显示当前docker-compose编排过的容器进程

docker-compose logs 

查看容器输出日志

docker-compose config 

检查配置

docker-compose config -q 

检查配置，有问题才输出

docker-compose restart 

重启服务

docker-compose start 

启动服务

docker-compose stop 

停止服务

### 企业级私有docker仓库安装

**Harbor镜像仓库简介**

l **Harbor的优势**

（1）基于角色控制：有管理员与普通用户，可赋权普通用户，比如只能上传和下载，可根据项目来进行操作和管理。

（2）基于镜像的复制策略：镜像可以在多个Harbor实例之间进行复制(同步)。

（3）支持LDAP/AD：Harbor 可以集成企业内部有的AD/LDAP (类似数据库的一-张表)，用于对已经存在的用户认证和管理。

（4）镜像删除和垃圾回收：镜像可以被删除，也可以回收镜像占用的空间。

（5）图形化用户界面：用户可以通过浏览器来浏览，搜索镜像仓库以及对项目进行管理。

（6）审计管理：所有针对镜像仓库的操作都可以被记录追溯，用于审计管理。

（7）支持RESTful API：RESTful API提供给管理员对于Harbor 更多的操控，使得与其它管理软件集成变得更容易。

（8）Harbor和docker registry的关系：Harbor实质上是对docker registry做了封装，扩展了自己的业务模板

1.**安装docker与docker-compose**

\# yum -y install docker-ce

下载docker-compose：

最新发行的版本地址：https://github.com/docker/compose/releases

```
wget https://github.com/docker/compose/releases/download/v2.20.3/docker-compose-linux-x86_64

\#mv docker-compose-linux-x86_64 /usr/local/bin/docker-compose

\# chmod +x /usr/local/bin/docker-compose

\# docker-compose --version
```

**二、Harbor镜像仓库安装部署**

```
wget https://github.com/goharbor/harbor/releases/download/v2.8.4/harbor-offline-installer-v2.8.4.tgz

tar -xvf harbor-offline-installer-v2.8.4.tgz -C /opt/software/

cd harbor/

cp harbor.yml.tmpl harbor.yml
```

#修改主机名，注释掉https段 初始密码设置

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/harbor配置文件.png)

安装：

使用docker-compose时，当前目录下必须有一个docker-compose.yml无论是创建，查看，删除容器都是通过这个容器进行管理

```
./install.sh

# docker-compose down ##关闭 或者stop
注意：docker-compose关闭容器是将容器全部删除

# docker-compose up -d ##启动 或者start
```

登录：访问harbor地址，默认是 用户名是：admin,密码是：Harbor12345

说明：创建仓库，存储容量: -1 表示不限制使用的大小

我们使用http访问harbor,客户端需要配置

```
# cat /etc/docker/daemon.json
/etc/docker/daemon.json
{
"insecure-registries": ["192.168.100.40"]
}
systemctl daemon-reload
systemctl restart docker
```

推送镜像

修改镜像名称（**harbor的服务器地址/项目名/镜像名:版本号**）

\# docker tag 0b1c06f9547d 192.168.10.50/myrepo/mytest:v3.0.0

登录仓库

\# docker login -u admin -p Harbor12345 192.168.100.40

\# docker logout 192.168.100.40 //退出仓库

推送

\# docker push 192.168.100.40/myrepo/mytest:v3.0.0

拉取镜像

\# docker pull 192.168.100.40/myrepo/mytest:v3.0.0 //拉取镜像