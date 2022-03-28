# RedHat 常用命令 #

**1. 查询注册模块软件**  

    yum list all | grep subscription  

**2. 卸载注册模块**  

    yum remove subscription-manager   

**3. 立即重启**  

    shutdown -r now  

**4. 解压到指定路径**  

    tar -zxvf /source/kernel.tgz -C /Document  

**5. 打包压缩文件**  

    tar -cvf /home/www/images.tar /home/www/images ← 仅打包，不压缩  
    tar -zcvf /home/www/images.tar.gz /home/www/images ← 打包后，以gzip压缩  

**6. 创建目录**  

    mkdir -p[递归创建]  

**7. 删除非空目录**  

    rmdir（只能删除空目录） // rmdir 目标目录  
    rm (默认删除文件) -r[删除目录] -f[不希望询问确认]  // rm -rf 目标文件  

**8. 复制文件**  

    cp -r[复制目录] -p[复制时保持文件属性]  // cp -rp 目标文件 目标目录

**9. 移动文件**  

    mv（剪切文件）  // mv 目标文件 目标目录 （将目标目录属性改为文件名时，可对文件进行改名）  

**10. 创建文件**  

    touch  //touch 文件名  

**11. 查看文件内容**  

    cat -n[显示行号]  // cat -n 目标文件  

**12. 反向显示文件内容**  

    tac(适用于短文件)  

**13. 分页浏览文件**   

    mroe(可用 空格 或 F 翻页，回车 换行， q 退出)  // more 目标文件
    less（在 more 命令的基础上还可用 上下箭头 换行， pgup、pgdown 翻页， / 加搜索内容查找， n 跳转下一个匹配项， N 跳转至上一个匹配项）
    // less 目标文件

**14. 查看文件头**  

    head(默认前10行) -n[指定行数]  // head -n 5 目标文件  

**15. 查看文件尾**  

    tail（默认10行） -n[显示指定行数]  // tail -n 5 目标文件  

**16. 创建连接文件**  

    ln(默认创建硬链接，实时更新) -s[创建软连接]  // ln -s 目标目录/[文件名]  

**17. 更改文件权限**  

	chmod -R[递归更改]  // chmod -R 777 目标文件  

**18. 更改文件所有者**  

    chown -R[递归更改]  // chown 目标用户 目标文件  

**19. 文件搜索**  

    find -name[按文件名搜索，通配符 * ？]  -iname[按文件名搜索忽略大小写]
    -size[按文件大小搜索， + 大于， - 小于，缺省表示等于]  -type[根据类型查找， d 为目录， f 为文件]
    -user[根据所属用户查找]  -group[根据所属组查找]
    -cmin[根据更改时间查找，+2两分钟前，-2两分钟内] -mtime[根据修改时间，+2两天前，-2两天内]
    -a[查询条件 and 连接符]  -r[查询条件 or 连接符]
    // find -name *关键字*
    // find -size +1M (大于1M的文件)
    // find -iname *关键字*  

**20. 快速文件查找**  

    locate（从Linux文件资料库查找，系统定期自动更新） -i[忽略大小写]  

**21. 搜索命令所在路径及别名**  

    which // which 命令名  

**22. 搜索文件中指定内容**  

    grep(^a 以a开头) -i[忽略大小写] -v[排除指定字符]
    // grep ^aa a.txt &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ls |grep -v ^G  

**23. 查看命令帮助**  

    man // man ls
    info // info ls  

**24. 查看用户登录信息**  

    who  

**25. 压缩文件**  

    gzip -d[解压文件]  // gzip -d 目标.gz文件  

**26. 目录打包压缩**  

    tar -c[打包]  -v[显示详细信息]  -f[指定文件名]  -z[打包同时压缩]  -x[解包]
    // tar -zcvf 打包压缩后文件名 目标文件名
    // tar -zxvf 目标文件名  

**27. 压缩 zip 文件**  

    zip -r[压缩目录]    // zip 压缩后文件名 目标文件名

**28. 解压 zip 文件**  

    unzip 目标文件名  

**29. 压缩 biz 文件**  

    biz（默认只能压缩文件） -k[压缩后保留文件]    // biz 目标文件
    // 打包目录并压缩  tar -cjf 打包压缩后文件名 目标文件名  

**30. 解压 bz2 文件**  

    bunzip2 -k[解压后保留原文件] // bunzip2 -k 目标文件名
    // 解压 tar.bz2 文件  tar -xjf 目标文件名  

**31. 查看所有已安装软件**  

    rpm -qa  

**32. 卸载已安装软件**  

    rpm -e --nodeps 软件包名  

**33. rpm 软件包安装**  

    rpm -ivh 软件包名

**34.  配置全局环境变量（ Java 为例）**  

    export JAVA_HOME=/usr/java/jdk1.7.0_04
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin  

**35. 更新环境变量配置信息**  

    source /etc/profile  

**36. 跳转上次所在目录**  

    cd -  

**37. 查看端口是否开启**  

    lsof // lsof -i :端口号  17001 12600 20220 20250 20002

**38. weblogic 启动与关闭**  

    nohup ./startWebLogic.sh &  // user_project/xxx 路径下
    nohup ./startManagedWebLogic.sh  gisserver1 & // user_project/domains/xxx/bin 路径下
    ./stopManagedWebLogic.sh gisserver1// user_project/domains/xxx/bin 路径下
    ./stopWebLogic.sh // user_project/domains/xxx/bin 路径下  

**39. 查看 17001 和 12600 的 pid 并杀死该进程**  

    lsof -i:17001    //找到 PID 列
    kill -9    // kill -9 进程的 PID 的值  

**40. 查看当前用户下所有进程**  

	ps -u weblogic  

**41.查看 RedHat 版本号**

	more /etc/redhat-release  

**42. 查看系统内存**

	free -h

**43. 查看 Redhat yum**  

	rpm -qa |grep yum

**44. 卸载 Redhat yum **

	rpm -aq|grep yum|xargs rpm -e --nodeps  

**45. 完全切换当前用户**  

	su - 用户名（该方式可切换至登录用户的环境变量）  

**46. 新建用户**  

	adduser 用户名  

**47. 修改用户密码**  

	passwd 用户名  

**48. 赋予用户 sudo 权限**  

	chmod -v u+w /etc/sudoers
	vim /etc/sudoers
	chmod -v u-w /etc/sudoers  

**49. 更换为国内yum源**  

1. 删除 `/etc/yum.repos.d` 目录下所有文件  
2. 下载 `http://mirrors.aliyun.com/repo/Centos-7.repo`  
3. 将下载好的 `Centos-7.repo` 文件放至 `/etc/yum.repos.d` 目录  
4. `yum clean`  
5. `yum makecache`  
6. `yum update`  

**50. 在线安装 wget**  

	yum -y install wget  

**51. 删除旧内核**  

    rpm -qa | grep kernel（查看内核程序）  
    yum erase 内核名 （删除多余的内核）  
    shutdown -r now

**52.防火墙操作**

```
启动防火墙： systemctl start firewalld.service
关闭防火墙： systemctl stop firewalld.service
重启防火墙： systemctl restart firewalld.service
查看防火墙状态： systemctl status firewalld.service
开机启动防火墙： systemctl enable firewalld.service
禁止开机防火墙： systemctl disable firewalld.service
```



## Q&A

**Tip_1. useradd 命令异常：Creating mailbox file: 文件已存在? **

	rm -rf /var/spool/mail/用户名  

**Tip_2. 【Oracle】Linux7安装11g Error in invoking target 'agent nmhs' of makefile**  

	    cd $ORACLE_HOME/sysman/lib
	    cp ins_emagent.mk ins_emagent.mk.bak
	    vim ins_emagent.mk
	    进入 vim 编辑器后  命令模式输入/NMECTL 进行查找，快速定位要修改的行
	    在后面追加参数-lnnz11 (第一个是字母l   后面两个是数字1)  

**Tip_3. oracle 启动图形界面：**
  确保当前为 root 用户登录  

	export DISPLAY=localhost:1
	xhost +  

  通过 `su - oracle` 命令切换为 oracle 用户：  

	export DISPLAY=localhost:1`
	xhost +
  输出：`access control disabled, clients can connect from any host` 表示设置成功  


**Tip_4.  oracle 忽略环境检查安装**  

	./runInstall -ignorePrereq  

**Tip_5. 启动 oracle**

	     su - oracle（切换至 oracle 用户）
	     lsnrctl start（启动 oracle 监听）
	     lsnrctl status（查看 oracle 监听状态）
	     sqlplus（运行 sqlplus）
	     SYS AS SYSDBA（输入用户名，管理员登录）
	     earthviewdky（输入用户密码，管理员密码）
	     startup（启动 oracle 实例）  




