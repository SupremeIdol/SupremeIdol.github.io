# Centos 基础设置

```sh
# 设置为清华 yum 源
cd /etc/yum.repos.d/
vi CentOS-Base.repo
```

**CentOS-Base.repo**

```yml
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
baseurl=http://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates
baseurl=http://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

**添加RPM-GPG-KEY-EPEL-7**

```sh
curl https://mirrors.tuna.tsinghua.edu.cn/epel/RPM-GPG-KEY-EPEL-7 > /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
```

**设置阿里源**

```sh
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

**生成缓存**

```sh
yum clean all
yum makecache
yum update
```

**防火墙**

```sh
# 启动防火墙：
systemctl start firewalld.service
# 关闭防火墙：
systemctl stop firewalld.service
# 重启防火墙：
systemctl restart firewalld.service
# 查看防火墙状态：
systemctl status firewalld.service
# 开机启动防火墙：
systemctl enable firewalld.service
# 禁止开机防火墙：
systemctl disable firewalld.service
# 修改本机ip
vim /etc/sysconfig/network-scripts/ifcfg-ens33
# 重启网络服务
service network restart
```

**其他**

将用户添加至 root 组

```sh
vim /etc/sudoers
```

```sh
# 找到下面这行内容
root    ALL=(ALL)     ALL
# 添加 sir 用户至 root 组
sir    ALL=(ALL)     ALL
```

添加当前用户至某个组

```sh
#添加 docker 用户组
sudo groupadd docker  
#将登陆用户加入到 docker 用户组中
sudo gpasswd -a $USER docker 
#更新用户组
newgrp docker
#测试 docker 命令是否可以正常使用
docker ps 
```

