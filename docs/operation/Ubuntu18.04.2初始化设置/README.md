# Ubuntu 18.04.2 LTS 初始化设置 #

**用户信息**

- 用户名： `sir/root`  
- 密码： `supreme`  

**切换管理员权限**  

	sudo su  

**修改 root 密码**  

	passwd root  

**修改静态IP**  

	vim /etc/netplan/*.yaml

修改后内容  

    network:
    ethernets:
        ens33:
            addresses: [192.168.32.101/24]
            gateway4: 192.168.32.2
            dhcp4: no
    version: 2

**应用修改后的IP**  

	netplan apply

**开启root远程登录权限**  

	vim /etc/ssh/sshd_config

修改步骤  


1. 将“#port 22”修改为“port 22”  
2. 将“#PermitRootLogin prohibit-password”修改为“PermitRootLogin yes”    

**重启SSHD服务**  

	service sshd restart

**修改DNS**  

	vim /etc/systemd/resolved.conf  

修改后内容  

	DNS=114.114.114.114  

**使修改生效**  

	systemctl restart systemd-resolved.service  

**查看修改结果**  

	systemd-resolve --status  

**更换Ubuntu18.04源（如果在安装Ubuntu是配置了阿里云的源，这一步就可以省略了）**

	vim /etc/apt/sources.list  

替换内容为  

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse  
```

**更新源**  

	apt-get update  

**关闭防火墙**  

	ufw disable  

**开启防火墙**

```shell
ufw enable
```