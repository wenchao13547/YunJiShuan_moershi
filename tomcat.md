### 安装部署tomcat

**1. JVM（Java Virtual Machine - Java 虚拟机）**

★ JVM 就是我们耳熟能详的<Java 虚拟机>，它就相当于一台<虚拟机>，它的核心任务就是实现<跨平台性>。

★ JVM 主要功能：

(1) 实现：跨平台性

(2) 解析：Java 编译生成的<.class 文件>（操作系统不能直接识别.class 文件，必须借助 JVM 来解析）

(3) 承载：Java 应用程序，例如：在多实例 tomcat 应用场景中，就是在一台<JVM 虚拟机>中运行了多个<Tomcat 实例>

**2. JRE（Java Runtime Environment - Java 运行时）**

★ JRE 就是<JVM + .class 基础类文件>的集合，从而最终为<Java 应用程序>提供一个<Java 运行时环境>。

★ 当我们只想运行一个<java 应用程序>，而不是做<Java 开发>，则仅仅只需安装 JRE 即可，无需安装 JDK。

**3. JDK（Java Development Kit - Java 开发工具包）**

★ JDK 是一个 Java 开发工具包，它提供了完整的<开发、编译、运行、调试 ... 等>开发环境。

♦ 它包含着：

(1) JRE（即：JVM + .class 基础类文件）

(2) JAVA 编译器(即：将 java 文件编译成为.class 类文件) ... 等等<开发所需的完整组件>

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/jre.png)

了解：Tomcat 与 JAVA 之间的<版本对应关系>https://tomcat.apache.org/whichversion.html

##### 安装jdk

上传jdk压缩包

tar -zxvf /opt/software/jdk-8u311-linux-x64.tar.gz -C /opt/module/

在/etc/profile.d目录下创建my.env.sh，开机会自动加载这个目录下的环境变量

vim /etc/profile.d/my_env.sh

```
#JAVA_HOME 

export JAVA_HOME=/opt/module/jdk1.8.0_311 

export PATH=$PATH:$JAVA_HOME/bin
```

Java -version检查是否安装成功

1. 安装tomcat

上传压缩包解压

tar -xvf apache-tomcat-9.0.102.tar.gz -C /opt/module/

cd 到tomcat下的bin目录下使用sh startup.sh 命令启动tomcat

查看端口

netstat -tunlp | grep java

配置：防火墙，放行8080端口

firewall-cmd --permanent --zone=public --add-port=8080/tcp

firewall-cmd --reload

来到网页访问测试192.168.10.53:8080

yum安装

安装：JRE 7+ 和 Tomcat 7

yum install -y java-1.7.0-openjdk* tomcat* --disablerepo=epel

\## java-1.7.0-openjdk* 

注意：最终安装的是 java-1.8.0 版本

\## --disablerepo=epel 

禁用：EPEL 源，防止版本冲突

java -version

(2) 启停：Tomcat 7

\##

systemctl enable tomcat 

\## 设置：开机自启动

systemctl start tomcat 

\## 当前：立即启动

systemctl status tomcat 

\## 查看：启动状态

systemctl restart tomcat ## 当前：立即重启

systemctl stop tomcat 

当前：立即停止

(3) 配置：防火墙

firewall-cmd --permanent --zone=public --add-port=8080/tcp

firewall-cmd --reload

 (4) 禁用：SELinux 安全上下文机制

```
if ! [[ $(getenforce) == "Disabled" ]]; then

	sed -r -i '/^[ \t]*SELINUX=/c\SELINUX=disabled' /etc/selinux/config

	reboot

fi
```

(6) 设置：针对 Tomcat 的<用户登录环境变量>，方便执行管理命令

\##

cat > /etc/profile.d/tomcat.sh <<EOF

export JAVA_HOME="/usr/lib/jvm/java"

export CATALINA_HOME="/usr/share/tomcat"

export CATALINA_BASE="\$CATALINA_HOME"

EOF

刷新环境变量

source /etc/profile.d/tomcat.sh

访问：Tomcat 首页

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/tomcat端口说明.png)

★ 注意：

♦ 在 YUM 安装中 ，默认：允许<任意客户端 IP 的访问>。

♦ 在二进制安装中，默认：拒绝<任意客户端 IP 的访问>，仅允许<127.0.0.1 的访问>。

(1) 配置允许：访问**<Host Manager 虚拟主机管理>**的<客户端 IP 地址> ◀ 支持使用：正则表达式

configFile="$CATALINA_BASE/webapps/host-manager/META-INF/context.xml"

sed -i -r 's/(^\s*allow=).*/\1"\\d+\\.\\d+\\.\\d+\\.\\d+|0:0:0:0:0:0:0:1" \/>/' $configFile

cat $configFile

(2) 配置允许：访问**<Manager APP 应用发布管理、Server Status 服务器状态查看>**的<客户端 IP 地址> ◀ 支持使用：正则表达式

\##

configFile="$CATALINA_BASE/webapps/manager/META-INF/context.xml"

sed -i -r 's/(^\s*allow=).*/\1"\\d+\\.\\d+\\.\\d+\\.\\d+|0:0:0:0:0:0:0:1" \/>/' $configFile

cat $configFile

(3) 配置允许：访问**<Documentation 和 Configuration 帮助文档>**的<客户端 IP 地址控制> ◀ 支持使用：正则表达式

\##

configFile="$CATALINA_BASE/webapps/docs/META-INF/context.xml"

sed -i -r 's/(^\s*allow=).*/\1"\\d+\\.\\d+\\.\\d+\\.\\d+|0:0:0:0:0:0:0:1" \/>/' $configFile

cat $configFile

(4) 配置允许：访问**<Examples 示例文件>**的<客户端 IP 地址> ◀ 支持使用：正则表达式

\##

configFile="$CATALINA_BASE/webapps/examples/META-INF/context.xml"

sed -i -r 's/(^\s*allow=).*/\1"\\d+\\.\\d+\\.\\d+\\.\\d+|0:0:0:0:0:0:0:1" \/>/' $configFile

cat $configFile

在修改tomcat根目录/conf/tomcat-users.xml，创建用户tomcat密码为s3cret

♦ manager-status 角色：可访问【Server Status 服务器状态查看页面】

♦ **manager-gui** 角色：可访问【Manager APP 应用发布管理页面】和【Server Status 服务器状态查看页面】

♦ manager-script 角色：仅可访问【文本界面】和【Server Status 服务器状态查看页面】

♦ manager-jmx 角色：仅可访问【JMX 代理】和【Server Status 服务器状态查看页面】

❁ 注意：该【manager-gui】不能和【manager-script】或【manager-jmx】在一起使用

♦ **admin-gui** 角色：可访问【Host Manager 虚拟主机管理页面】

♦ admin-script 角色：仅可访问【文本界面】

```
 <role rolename="admin-gui"/>
  <user username="tomcat" password="s3cret" roles="admin-gui"/>
  <role rolename="manager-gui"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```

systemctl restart tomcat            ──── 针对：YUM 安装

sh /opt/module/apache-tomcat-9.0.102/bin/startup.sh          ——启动针对：源码安装

sh /opt/module/apache-tomcat-9.0.102/bin/shutdown.sh            ——停止针对：源码安装



项目：

基于不同主机主机名部署两个网站

1. 部署需要开放的资源

mkdir -p /www.wczy.test.cn/webapps/ROOT 

cat > /www.wczy.test.cn/webapps/ROOT/index.html <<EOF 

<html> 

<head> 

<meta charset="UTF-8"> 

<title>我的首页</title> 

</head> 

<body> 

这是 武昌职院 网站首页 

</body> 

</html> 

EOF 



mkdir -p /www.slxy.test.cn/webapps/ROOT 

cat > /www.slxy.test.cn/webapps/ROOT/index.html <<EOF 

<html> 

<head> 

<meta charset="UTF-8"> 

<title>我的首页</title> 

</head> 

<body> 

这是 水利学院 网站首页 

</body> 

</html> 

EOF

1. 让tomcat加载资源

   vim /opt/tomcat/conf/server.xml

   ![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/基于主机名host配置.png)

<Host name="***\*www.wczy.test.cn\****" appBase="***\*/www.wczy.test.cn/webapps\****" unpackWARs="true" autoDeploy="true"> 

<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" 

prefix="localhost_access_log" suffix=".txt" 

pattern="%h %l %u %t "%r" %s %b" /> 

</Host> 



<Host name="***\*www.slxy.test.cn\****" appBase="***\*/www.slxy.test.cn/webapps\****" unpackWARs="true" autoDeploy="true"> 

<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" 

prefix="localhost_access_log" suffix=".txt" 

pattern="%h %l %u %t "%r" %s %b" /> 

</Host>

配置两个主机名+域名区，指定项目根目录

重启tomcat

sh /opt/module/apache-tomcat-9.0.102/bin/startup.sh 



搭建dns服务器用于解析域名

部署：一台 DNS 独立服务器(192.168.168.11)

yum install -y bind bind-utils

sed -i -r 's/(^\s*listen-on\s+port\s+53\s+\{).*(\}\s*;$)/\1any;\2/' /etc/named.conf

sed -i -r 's/(^\s*allow-query\s+\{).*(\}\s*;$)/\1any;\2/' /etc/named.conf

sed -i -r 's/(^\s*dnssec-validation\s+).*/\1no;/' /etc/named.conf

systemctl enable named

systemctl restart named

firewall-cmd --permanent --zone=public --add-port=53/udp

firewall-cmd --reload

```
if ! [[ $(getenforce) == "Disabled" ]]; then

sed -r -i '/^[ \t]*SELINUX=/c\SELINUX=disabled' /etc/selinux/config

reboot

fi
```

```
cat > /etc/test.cn.zone.conf <<EOF

zone "test.cn" IN { 

// 标明：域名空间 = test.cn

type master; 

// 设置：区域类型 = master 主

file "test.cn.zone"; 

// 指定：区域数据文件 = test.cn.zone 文件(这是相对路径)

allow-update { none; }; 

// 禁止：DNS 动态更新

};

EOF
```

echo "include \"/etc/test.cn.zone.conf\";" >> /etc/named.conf

vi /var/named/test.cn.zone

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/基于主机名练习.png)

$TTL 3H
$ORIGIN test.cn.
@ IN SOA dns01.test.cn. admin.test.cn. (
    1 ; 序列号
    3H ; 刷新时间
    1H ; 重试时间
    1W ; 过期时间
    3H ) ; 最小TTL
@ NS dns01.test.cn.
dns01 A 192.168.10.50
server01 A 192.168.10.53
www.wczy CNAME server01.test.cn.
www.slxy CNAME server01.test.cn.

systemctl restart named

重新找一台客户端，修改dns服务器为我们搭建的服务器

访问http://www.wczy.test.cn:8080

访问http://www.slxy.test.cn:8080
