**k8s概述**

**♦** **k8s功能介绍**

**● 服务发现和负载均衡**

Kubernetes 可以使用 DNS 名称或自己的 IP 地址来暴露容器。 如果进入容器的流量很大， Kubernetes 可以负载均衡并分

配网络流量，从而使部署稳定。

**● 存储编排**

Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。

**● 自动部署和回滚**

你可以使用 Kubernetes 描述已部署容器的所需状态， 它可以以受控的速率将实际状态更改为期望状态。 例如，你可以自

动化 Kubernetes 来为你的部署创建新容器， 删除现有容器并将它们的所有资源用于新容器。

**k8s组件**

master主节点：kube-api-server、kube-scheduler、kube-controller-manager、etcd

Node节点：kubelet、kube-proxy

**k8s组件**

master主节点：kube-api-server、kube-scheduler、kube-controller-manager、etcd

**● kube-api-server**

API 服务器是 Kubernetes 控制面的组件， 该组件公开了 Kubernetes API。 API 服务器是 Kubernetes 控制面的前端。

Kubernetes API 服务器的主要实现是 kube-apiserver。 kube-apiserver 设计上考虑了水平伸缩，也就是说，它可通过部署多个实例进行伸缩。 你可

以运行 kube-apiserver 的多个实例，并在这些实例之间平衡流量。

**● etcd**

etcd 是兼具一致性和高可用性的键值数据库，可以作为保存 Kubernetes 所有集群数据的后台数据库。您的 Kubernetes 集群的 etcd 数据库通常需

要有个备份计划。

**● kube-scheduler**

控制平面组件，负责监视新创建的、未指定运行节点（node）的 Pods，选择节点让 Pod 在上面运行。调度决策考虑的因素包括单个 Pod 和 Pod 集

合的资源需求、硬件/软件/策略约束、亲和性和反亲和性规范、数据位置、工作负载间的干扰和最后时限。

**● kube-controller-manage**r

运行控制器进程的控制平面组件。从逻辑上讲，每个控制器都是一个单独的进程， 但是为了降低复杂性，它们都被编译到同一个可执行文件，并在一

个进程中运行。

**k8s组件**

master主节点：kube-api-server、kube-scheduler、kube-controller-manager、etcd

**● kube-api-server**

API 服务器是 Kubernetes 控制面的组件， 该组件公开了 Kubernetes API。 API 服务器是 Kubernetes 控制面的前端。

Kubernetes API 服务器的主要实现是 kube-apiserver。 kube-apiserver 设计上考虑了水平伸缩，也就是说，它可通过部署多个实例进行伸缩。 你可

以运行 kube-apiserver 的多个实例，并在这些实例之间平衡流量。

**● etcd**

etcd 是兼具一致性和高可用性的键值数据库，可以作为保存 Kubernetes 所有集群数据的后台数据库。您的 Kubernetes 集群的 etcd 数据库通常需

要有个备份计划。

**● kube-scheduler**

控制平面组件，负责监视新创建的、未指定运行节点（node）的 Pods，选择节点让 Pod 在上面运行。调度决策考虑的因素包括单个 Pod 和 Pod 集

合的资源需求、硬件/软件/策略约束、亲和性和反亲和性规范、数据位置、工作负载间的干扰和最后时限。

**● kube-controller-manage**r

运行控制器进程的控制平面组件。从逻辑上讲，每个控制器都是一个单独的进程， 但是为了降低复杂性，它们都被编译到同一个可执行文件，并在一

个进程中运行。

**♦** **k8s组件**

Node节点：kubelet、kube-proxy

**● kubelet** 

一个在集群中每个节点（node）上运行的代理。 它保证容器（containers）都 运行在 Pod 中。

kubelet 接收一组通过各类机制提供给它的 PodSpecs，确保这些 PodSpecs 中描述的容器处于运行状态且健康。 kubelet 不

会管理不是由 Kubernetes 创建的容器。

**● kube-proxy**

kube-proxy 是集群中每个节点上运行的网络代理， 实现 Kubernetes 服务（Service） 概念的一部分。

kube-proxy 维护节点上的网络规则。这些网络规则允许从集群内部或外部的网络会话与 Pod 进行网络通信。

如果操作系统提供了数据包过滤层并可用的话，kube-proxy 会通过它来实现网络规则。否则， kube-proxy 仅转发流量本身。

**kubeadm部署集群**

**集群安装环境准备**

（1）关闭防火墙，关闭selinux

```
systemctl stop firewalld

systemctl disable firewalld

sed -i 's/enforcing/disabled/' /etc/selinux/config #永久关闭selinux

setenforce 0 #临时关闭selinux
```

各节点主机名解析

```
vim /etc/hosts
```

```
192.168.10.200 k8s-master
192.168.10.201 k8s-node01
192.168.10.202 k8s-node02
```

关闭交换分区(让运行更快)

```
sed -ri 's/.*swap.*/#&/' /etc/fstab #永久关闭

swapoff -a #临时关闭
```

配置时间同步服务

查看模块是否加载（什么都不显示代表未加载）：

```
# lsmod | grep overlay

# lsmod | grep br_netfilter
```

手动加载：

```
 modprobe -a overlay

modprobe -a br_netfilter
```

写入配置文件永久加载

```
cat <<EOF > /etc/modules-load.d/k8s.conf

overlay

br_netfilter

EOF
```

修改网桥网络参数：解决于iptables被绕过而导致流量路由不正确的问题

```
cat > /etc/sysctl.d/k8s.conf << EOF

net.ipv4.ip_forward = 1

vm.swappiness = 0

net.bridge.bridge-nf-call-ip6tables = 1

net.bridge.bridge-nf-call-iptables = 1

EOF
```

```
sysctl --system #生效
```

准备yum源（阿里云的centos的base源、epel源、docker源）

```
wget -O /etc/yum.repos.d/ali.repo http://mirrors.aliyun.com/repo/Centos-7.repo

wget -O /etc/yum.repos.d/epel-7.repo https://mirrors.aliyun.com/repo/epel-7.repo

wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
```

（二选一）安装docker引擎和container （安装docker可以提供容器的运行的环境，但是不支持docker去管理容器）

```
yum -y install docker-ce-20.10.16 docker-ce-cli-20.10.16 containerd.io
```

如果安装docker需要配置镜像下载加速器：

```
$ cat > /etc/docker/daemon.json << EOF
{
"registry-mirrors": ["http://hub-mirror.c.163.com"]
}
EOF
$ systemctl restart docker
```

（二选一）也可以单独只按照container： 

```
yum -y install containerd
```

说明：安装docker可以提供容器的运行的环境，但是新版本不支持docker去管理容器

准备k8s组件的yum源

```
cat > /etc/yum.repos.d/kubernetes.repo << EOF

[kubernetes]

name=Kubernetes

baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64

enabled=1

gpgcheck=0

repo_gpgcheck=0

gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg

https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

EOF
```

安装核心软件包

```
# yum install -y kubelet-1.26.3 kubeadm-1.26.3 kubectl-1.26.3 --disableexcludes=kubernetes

# systemctl enable kubelet
```

说明：先不直接启动服务，启动也会停掉，因为/etc/kubernetes/mainfests/没有配置文件，启动会报错

以上所有节点执行

kubelet 的版本不可以超过 API server 的版本

kubeadm：k8集群的一键部署工具，通过把k8的各类核心组件和插件以pod的方式部署来简化安装过程

kubelet：运行在每个节点上的node agent，k8集群通过kubelet真正的去操作每个节点上的容器，由于需要直接操作宿主

机的各类资源，所以没有放在pod里面，还是通过服务的形式装在系统里面。



修改容器运行时配置文件

初始化前需要修改container服务的配置文件：

```
cd /etc/containerd

mv config.toml config.toml.bak

containerd config default > config.toml


vim config.toml 修改如下内容：
#将这两个配置项修改为以下内容

sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.6"

SystemdCgroup = true
```

修改完后重启容器服务：

```
systemctl restart containerd
```

只主节点：初始化集群（只在master节点执行）

```
 kubeadm init --apiserver-advertise-address=192.168.10.200 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.26.3 --service-cidr=10.96.0.0/12 --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=all
```

说明：初始化命令说明

\# kubeadm init \ 

--apiserver-advertise-address=192.168.10.200 \ 

--image-repository registry.aliyuncs.com/google_containers \ 

--kubernetes-version v1.26.3 \ 

--service-cidr=10.96.0.0/12 \ 

--pod-network-cidr=10.244.0.0/16 \ 

--ignore-preflight-errors=all

查看kubeadm初始化需要的镜像，如果初始化拉取镜像很慢可以手动提前拉取。

```
#kubeadm config images list
```

管理配置文件（按输出提示执行）

普通用户：

```
mkdir -p $HOME/.kube

cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

chown $(id -u):$(id -g) $HOME/.kube/config
```

只主节点：如果是管理员只需执行：

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

只主节点：执行完就可以启动集群(加到/etc/profile里实现开机自动启动)

```
[root@master ~]# systemctl status kubelet.service #确认服务已经启动
```

只在主节点，安装网络插件

方式一：calico

下载：kube-flannel.yml文件

```
wget http://manongbiji.oss-cn-beijing.aliyuncs.com/ittailkshow/k8s/download/calico.yaml
```

应用：kube-flannel.yml文件得到运行时容器

```
kubectl apply -f calico.yaml
```

每个从节点执行加入命令（主节点初始化生成）

```
kubeadm join 192.168.100.140:6443 --token 3eiq7z.oitq7szw9gupun4i \ 

--discovery-token-ca-cert-hash sha256:e7ba28538b502af9318e5aaac5d81285099c6928c2f8b290abac0ae093e66733
```

说明：如果无法查看初始化信息，重新生成加入集群命令

```
kubeadm token create --print-join-command
```

再将/etc/kubernetes/admin.conf配置文件发到从节点，让从节点也能执行kubectl 命令

```
scp /etc/kubernetes/admin.conf 192.168.10.201:/etc/kubernetes/admin.conf
scp /etc/kubernetes/admin.conf 192.168.10.202:/etc/kubernetes/admin.conf
```

再在从节点执行

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

```
vim /etc/profile

在结尾添加，环境变量永久有效

export KUBECONFIG=/etc/kubernetes/admin.conf
```

从节点安装网络插件

下载：

```
wget http://manongbiji.oss-cn-beijing.aliyuncs.com/ittailkshow/k8s/download/calico.yaml
```

应用：

```
kubectl apply -f calico.yaml
```

**主节点查看集群状态，验证部署成功**

```
kubectl get nodes #查看所有节点信息

kubectl cluster-info #查看集群信息

kubectl get nodes #查看所有节点信息

kubectl get nodes -o wide #查看节点详情

kubectl describe node master #描述指定节点详情

kubectl get pods -n kube-system 或者

kubectl get pods -A #查看系统名称空间运行的pod（可以看到了所有集群组件的运行情况）

kubectl get pod --all-namespaces -o wide #查看详细信息
```

确保下次启动正常容器引擎自启，kubelet自启，设置export KUBECONFIG=/etc/kubernetes/admin.conf环境变量

```
systemctl enable containerd

systemctl enable kubelet

vi /etc/profile
```

在最后一行添加

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

刷新环境变量

```
source /etc/profile
```

替代ctr的小工具（可以像docker一样操控容器）

```
wget https://github.com/containerd/nerdctl/releases/download/v1.7.1/nerdctl-1.7.1-linux-amd64.tar.gz
```

使用：

```
nerdctl images
```

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/crictl报错.png)

critl报错解决（每台节点上运行）：

```
[root@master1 ~]# crictl config runtime-endpoint unix:///run/containerd/containerd.sock
[root@master1 ~]# crictl config image-endpoint unix:///run/containerd/containerd.sock
```

资源对象：

pod控制器controller用于控制pod

### **控制器主要分为:**

无状态，持续型服务：

**ReplicationController**：RC用来管理正常运行相同类型Pod数量，控制pod副本数量，当pod不健康，运行出错或者无法提供服务时，RC也会杀死不健康的pod，重新创建新的，来保证相同类型的pod数量来提供服务

**ReplicaSet** ：升级版ReplicationController

**Deployment**：1.定义创建一组Pod的状态，Pod数量，镜像版本等，可以创建删除更新Pod2.可以通过修改配置文件来更新Pod，

3.如果更新出现问题可以回滚到之前版本用于部署无状态应用，例如简单运行一个静态web程序



有状态，持续性服务：

**StatefulSets** ：部署有状态的pod应用，像数据库，Pod 重新调度后还是能访问到相同的持久化数据，基于 PVC 来实现

适合部署，每个节点各有一个pod的服务：

**DaemonSet**： 部署的pod在每个节点一个，适合部署运行在所有集群节点(包括master)的pod，像资源监控pod, 比如使filebeat,node_exporter



部署任务型服务：

**Jobs** ：适合部署执行一次性任务的pod，任务结束就释放，例如备份

**Cronjob**： 周期性：适合部署周期性执行任务的pod，任务结束就释放，例如定期备份

说明：

无状态应用的特点:

**●** 所有pod无差别

**●** 所有pod中容器运行同一个image

**●** 所有pod可以运行在集群中任意node上

**●** 所有pod无启动顺序先后之分

**●** 随意pod数量扩容或缩容

### 服务型资源：

**HPA管理**：g(Pod横向自动扩容,简称HPA)，根据一定策略弹性伸缩容器数量（**算法介绍**从最基本的角度来看，Pod 水平自动扩缩控制器根据当前指标和期望指标来计算扩缩比例。**期望副本数 = ceil[当前副本数 \* (当前指标 / 期望指标)]**）

**访问Service**:

**1、ClusterIP**

默认，分配一个集群内部可以访问的虚拟IP，分为**普通Service：**为Kubernetes的Service分配一个集群内部可访问的固定虚拟

IP(Cluster IP), 实现集群内的访问；**Headless Service：**该服务不会分配Cluster IP, 也不通过kube-proxy做反向代理和负

载均衡。而是通过DNS提供稳定的网络ID来访问，DNS会将headlessservice的后端直接

解析为podIP列表。

**2、NodePort**

在每个Node上分配一个端口作为外部访问入口，端口映射到pod服务端口，

**3、LoadBalance**r

基于NodePort,但是NodePort只能但节点访问，不能做到负载均衡，通过增加一个外部设备，将流量负载均衡到每个Node的端口上，实现负载均衡。并且工作在特定的Cloud Provider上，例如Google Cloud，AWS，OpenStack

**4、ExternalName**

创建一个域名，提供访问。表示把集群外部的服务引入到集群内部中来，即实现了集群内部和集群外部的服务进行通信。

说明：集群内的Pod会继承Node上的DNS解析规则。所以只要Node可以访问的服务，Pod中也可以访问到, 这就

实现了集群内服务访问集群外服务。
