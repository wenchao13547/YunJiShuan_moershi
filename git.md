## 本地操作：

初始化仓库

```
mkdir workdir
cd workdir/
#将workdir初始化为仓库
git init 或 git init 指定目录
```

```
将全部文件提交到暂存区
#git add .
```

```
提交到版本区
git commit -m "版本说明"
```

```
可以查看<版本提交历史>，我们可以看到每次<commit提交>的<快照指针哈希值>，从而方便我们调用，例如版本回退。
git log
```

```
Git本地库查看当前状态
git status
```

```
Git本地库：回滚（git reset，后退方式回退、git revert，前进型回退）
git reset --hard 版本哈希值
git revert --hard 版本哈希值
```

```
Git本地库撤销修改
git checkout -- 文件名
```

分支管理

```
查看分支
git branch

创建分支
git branch 分支名

切换分支
git checkout 切换到的分支名

删除分支
git branch -d 被删除的分支
```

分子commit提交后合并分支

```
git merge 被合并的分支
```

多个分支对相同文件做出不同修改并提交，合并分支到主分支时就会出现冲突。

我们需要手动处理冲突。

**gitlab远程库**

**1、下载安装**

以下是官方源安装命令，经常会连接不上

```
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

手动创建：清华源

```
cat > /etc/yum.repos.d/gitlab-ce.repo << EOF

[gitlab-ce]

name=Gitlab CE Repository

baseurl=https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el\$releasever/

gpgcheck=0

enabled=1

EOF
```

**下载安装**

```
yum install -y gitlab-ce.x86_64
```

也可以直接下载软件包，离线安装

修改配置：

```
vi /etc/gitlab/gitlab.rb
```

external_url 'http://192.168.168.106'           #指定本机地址

重新配置初始化服务：

```
gitlab-ctl reconfigure
```

**登录gitlab**

管理员账号：root

查看初始密码：cat /etc/gitlab/initial_root_password

回显显示初始登录密码如下：

Password: ek5WmqJmvGWpQFSoYF6O7UKVZj9RT8UiiRgT3T4Slkw=

登录后修改密码

**创建git远程库**

这里有多种选项：Private(私有)、Internal(内部)、Public(公有)：

\# Private(私有)：项目访问权必须明确授予每个用户。如果此项目是组的一部分，则将向组成员授予访问权限。

\# Internal(内部)：除<外部用户>之外，任何<登录用户>都可以访问该项目

\# Public(公有)：无需任何身份验证即可访问该项目

**gitlab权限管理**

1、GitLab权限管理构成

**● 第一类：用户访问等级**（Access level）

☛ Reguler（一般使用者）

只能<读/写>访问：自己所属的<group组资源>和<project项目库资源>。

☛ Admin（管理者）

可以<读/写>访问：所有的<Group组资源>和<Project项目库资源>，并可操作<管理空间>。

☛ External users（外部使用者）

只能<读/写>访问：<公共库>和<个别被授权的内部库与私有库>。

不能创建<project项目库资源>

**● 第二类：组权限（Group）**

☛ <User用户>可以創建<Group组>；<Group组>中可以包含<Project项目 和 Member用户成员>。

☛ <Group组>具备三个<配置要素>：

◆ <Group组>包含哪些<Member用户成员>

◆ <Group组>包含哪些<Project项目>

◆ <Group组>自身 及其 包含的<Project项目>，他们的<Visibility Level 可见度等级>

**gitlab权限管理**

1、GitLab权限管理构成

**● 第三类：可见度等级（Visibility Level）**

☛ Visibility Level可见度等级有三种：

◆ public 公共：对<任何人>均可见，无需<用户账户>。

◆ internal内部：对<验证用户>或<明确授权用户>可见。

◆ private 私有: 对<Project项目 或 Group组>的<Member成员>可见。

**● 第四类：成员权限（Member Permissions）**

☛ <成员权限>用来设定：<User用户>对<Project项目>的具体<权限细则>。

◆ 例如：User 是否能建立 branch、能否推送 merge request。

☛ <成员权限>有五种<角色权限集>：

◆ Guest 来宾

◆ Reporter 记者

◆ Developer 开发者

◆ Maintainer 维护管理者

◆ Owner 所有者