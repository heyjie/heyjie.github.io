---
title: OracleLinux环境搭建
date: 2019-12-26 16:00:16
categories: 
    - 环境搭建
tags: 
	- 数据库
	- Oracle
	- Linux
---
Oracle Linux以对Oracle软件和硬件支持较好见长,安装Oracle数据库首选系统，最重要的是Oracle数据库跑在上Oracle Linux性能可以提升超过75%  
![](OracleLinux环境搭建/20200522165409.png)

<!-- more -->
## 一、安装操作系统

选择第一个安装系统
![1921_1.png](OracleLinux环境搭建/1921_1.png)

光驱检测选择第二个跳过
![1923_1.png](OracleLinux环境搭建/1923_1.png)

语言选英文，中文会乱码
![1925_1.png](OracleLinux环境搭建/1925_1.png)

选择第一个基本存储设备
![1927_1.png](OracleLinux环境搭建/1927_1.png)

选择第一个“是，忽略数据”
![1929_1.png](OracleLinux环境搭建/1929_1.png)

设置Hostname和网络配置
![1939_1.png](OracleLinux环境搭建/1939_1.png)

选择时区
![1933_1.png](OracleLinux环境搭建/1933_1.png)

设置root密码
![1935_1.png](OracleLinux环境搭建/1935_1.png)

选择最后一个，创建自定义布局
![1937_1.png](OracleLinux环境搭建/1937_1.png)

创建分区swap，大小是内存的两倍
![1941_1.png](OracleLinux环境搭建/1941_1.png)

创建分区boot
![1943_1.png](OracleLinux环境搭建/1943_1.png)

把剩余空间放到最后一个分区
![1945_1.png](OracleLinux环境搭建/1945_1.png)

选择第二个格式化
![1947_1.png](OracleLinux环境搭建/1947_1.png)

选择第二个，将修改写入磁盘
![1949_1.png](OracleLinux环境搭建/1949_1.png)

选择桌面Desktop
![1951_1.png](OracleLinux环境搭建/1951_1.png)

安装成功重启
![1953_1.png](OracleLinux环境搭建/1953_1.png)

重启后进入欢迎页，注意在这里要选择No
![1955_1.png](OracleLinux环境搭建/1955_1.png)

用户可以不创建，看个人情况
![F3GG.png](OracleLinux环境搭建/F3GG.png)

内存大可以开启kdump,我没有开启
![3KDJAM.png](OracleLinux环境搭建/3KDJAM.png)

## 二、配置oracle安装环境

### 2.1配置系统环境

```shell
$ vi /etc/sysconfig/network  # 配置主机名
NETWORKING=yes
HOSTNAME=oracle11g
 
$ vi /etc/hosts  #配置host,ip对应主机名
10.5.5.132 oracle11g

$ service iptables stop  # 关闭防火墙
$ chkconfig iptables off

$ vi /etc/selinux/config  # 关闭selinux
SELINUX=disabled
 
$ mkdir -p /soft/yum  # 创建软件目录
```

### 2.2配置yum源

先拷贝镜像到/soft/目录，然后执行下面的命令

```shell
$ mount -o loop -t iso9660 /soft/oraclelinux6.8.iso /soft/yum  # 挂载镜像
$ cd /etc/yum.repos.d/  # 进入yum源目录
$ mv public-yum-ol6.repo public-yum-ol6.repo.bak  # 备份源软件源
$ vi /etc/yum.repos.d/local.repo
[HighAvailability]
name=HighAvailability
baseurl=file:///soft/yum/HighAvailability
enabled=1
gpgcheck=0

[LoadBalancer]
name=LoadBalancer
baseurl=file:///soft/yum/LoadBalancer
enabled=1
gpgcheck=0

[ResilientStorage]
name=ResilientStorage
baseurl=file:///soft/yum/ResilientStorage
enabled=1 
gpgcheck=0

[Server]
name=Server
baseurl=file:///soft/yum/Server
enabled=1
gpgcheck=0

# 重新加载yum
$ yum clean all
$ yum makecache
$ yum list

$ yum install -y oracle-rdbms-server-11gR2-preinstall  # 安装依赖包

# 创建目录
$ mkdir -p /u01/app/oracle
$ chown -R oracle:oinstall /u01/app/oracle
$ chmod -R 775 /u01/app/oracle
$ mkdir -p /u01/app/oraInventory
$ chown -R oracle:oinstall /u01/app/oraInventory
$ chmod -R 775 /u01/app/oraInventory
```

### 2.3配置oracle用户环境变量

```shell
$ su - oracle  # 切换到oracle用户
$ vi .bash_profile # 配置环境变量
TMP=/tmp; export TMP
TMPDIR=$TMP; export TMPDIR
ORACLE_BASE=/u01/app/oracle; export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1; export ORACLE_HOME
ORACLE_SID=orcl; export ORACLE_SID
ORACLE_TERM=xterm; export ORACLE_TERM
PATH=/usr/sbin:$PATH; export PATH
PATH=$ORACLE_HOME/bin:$PATH; export PATH
LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib;  export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib; export CLASSPATH
if [ $USER = "oracle" ]; then
   if [ $SHELL = "/bin/ksh" ]; then
       ulimit -p 16384
       ulimit -n 65536
    else
       ulimit -u 16384 -n 65536
   fi
fi

$ source .bash_profile  # 让环境变量立即生效
```

### 2.4进入安装oracle

安装前先重启系统，然后用oracle用户登录

```shell
$ reboot #重启
$ cd /soft/  # 进入刚才上传的文件目录
$ unzip p13390677_112040_Linux-x86-64_1of7.zip  #解压缩
$ unzip p13390677_112040_Linux-x86-64_2of7.zip
$ cd /soft/database
$ ./runInstaller  # 开始安装
```

## 三、安装oracle

取消勾选
![1957_1.png](OracleLinux环境搭建/1957_1.png)

选择跳过
![1959_1.png](OracleLinux环境搭建/1959_1.png)

选择只安装软件
![GW80.png](OracleLinux环境搭建/GW80.png)

选择创建单实例的数据库安装
![JZ93U.png](OracleLinux环境搭建/JZ93U.png)

安装语言使用默认英文
![VOMUK0.png](OracleLinux环境搭建/VOMUK0.png)

选择安装企业版
![NWBN.png](OracleLinux环境搭建/NWBN.png)

Oracle安装路径自动识别到了配置的变量，下一步
![NUQ.png](OracleLinux环境搭建/NUQ.png)

安装文件的路径文件目录，自动识别。下一步
![QXUN.png](OracleLinux环境搭建/QXUN.png)

选择组的所属，下一步
![FUP6557R.png](OracleLinux环境搭建/FUP6557R.png)

点击安装
![LH8B.png](OracleLinux环境搭建/LH8B.png)

等待安装完成
![7LYTP.png](OracleLinux环境搭建/7LYTP.png)

提示需要root用户执行以下的脚本
![6IDDQR.png](OracleLinux环境搭建/6IDDQR.png)

打开命令窗口执行脚本
```sh
su               #切换到root用户
/u01/app/oraInventory/orainstRoot.sh   #执行第一个脚本
/u01/app/oracle/product/11.2.0/db_1/root.sh   #执行第二个脚本
```
执行完去安装窗口点确认

安装完成，点击关闭
![CWVU3.png](OracleLinux环境搭建/CWVU3.png)

## 四、配置和使用

```shell
$ dbca  # 配置数据库
```

![1973_1.png](OracleLinux环境搭建/1973_1.png)

选择创建数据库
![1979_1.png](OracleLinux环境搭建/1979_1.png)

这里选第一个和第二个都可以，第一个是一般事务数据，第二个是定制数据库。我这里选定制数据库
![1977_1.png](OracleLinux环境搭建/1977_1.png)

填写数据库实例名称
![1984_1.png](OracleLinux环境搭建/1984_1.png)

取消勾选然后下一步
![1981_1.png](OracleLinux环境搭建/1981_1.png)

选择统一的密码，然后自己设置密码
![1983_1.png](OracleLinux环境搭建/1983_1.png)

默认需要8位密码，不满8位也可以强制设置
![6OX41.png](OracleLinux环境搭建/6OX41.png)

选择数据库文件类型和位置，我这里默认
![1986_1.png](OracleLinux环境搭建/1986_1.png)

指定数据库快速恢复区，我不需要，取消勾选
![1985_1.png](OracleLinux环境搭建/1985_1.png)

数据库组件，取消勾选
![1982_1.png](OracleLinux环境搭建/1982_1.png)

根据需要配置内存，我的配置如图，取消勾选自动记录
![1978_1.png](OracleLinux环境搭建/1978_1.png)

根据需要设置最大的连接进程数
![SDFD.png](OracleLinux环境搭建/SDFD.png)

配置字符集，因为自己的数据库里用了中文，所以我配置了简体中文，下一步
![1974_1.png](OracleLinux环境搭建/1974_1.png)

设置日志组大小等，下一步
![1975_1.png](OracleLinux环境搭建/1975_1.png)

创建数据库，完成
![1980_1.png](OracleLinux环境搭建/1980_1.png)

确定
![1976_1.png](OracleLinux环境搭建/1976_1.png)

开始安装数据库
![1999_1.png](OracleLinux环境搭建/1999_1.png)

点击退出，安装完成
![1988_1.png](OracleLinux环境搭建/1988_1.png)

```shell
$ netca  # 配置监听及本地网络服务
```

![1998_1.png](OracleLinux环境搭建/1998_1.png)

![1990_1.png](OracleLinux环境搭建/1990_1.png)

![1987_1.png](OracleLinux环境搭建/1987_1.png)

![1993_1.png](OracleLinux环境搭建/1993_1.png)

![1994_1.png](OracleLinux环境搭建/1994_1.png)

![2000_1.png](OracleLinux环境搭建/2000_1.png)

![2001_1.png](OracleLinux环境搭建/2001_1.png)
监听设置存储在\data\oracle\product\10.2.0\db_1\NETWORK\ADMIN\listener.ora

接下来配置远程服务：
![1997_1.png](OracleLinux环境搭建/1997_1.png)

![1989_1.png](OracleLinux环境搭建/1989_1.png)

![1995_1.png](OracleLinux环境搭建/1995_1.png)

![1996_1.png](OracleLinux环境搭建/1996_1.png)

![1992_1.png](OracleLinux环境搭建/1992_1.png)

![1991_1.png](OracleLinux环境搭建/1991_1.png)

注意：若测试不成功有可能是用户登录出错，点击 change login，用户名输入：system，密码输入前面创建数据库时设置的密码
![2004_1.png](OracleLinux环境搭建/2004_1.png)

![2002_1.png](OracleLinux环境搭建/2002_1.png)

![2003_1.png](OracleLinux环境搭建/2003_1.png)

## 五、启动数据库

```shell
$ ln -s /u01/app/oracle/product/11.2.0/db_1/bin/sqlplus /usr/bin  # 把sqlplus in到/usr/bin目录，方便使用

# 切换到oracle /bin 目录，
$ cd $ORACLE_HOME/bin
# 启动监听
$ lsnrctl start
# 查看监听状态
$ lsnrctl status
# lsnrctl stop命令可以关闭监听

# 启动数据库
$ sqlplus /nolog
SQL> connect /as sysdba
SQL> startup
# shutdown immediate 命令来关闭数据
```

如果要关闭数据库可以用oracle的shutdown命令用来关闭当前实例，有4个可选参数：normal、transactional、immediate和abort。不带参数时默认是normal。  
这几个参数的差异体现在以下几个维度：  
是否允许新的连接；  
当前已连接的回话是否自动断开；  
当前未提交的事务是等待用户提交完成还是自动回滚；  
关闭时是否需要做检查点；  
启动时是否需要进行实例恢复。  

shutdown normal：不允许新的连接、等待会话结束、等待事务结束、做一个检查点并关闭数据文件。启动时不需要实例恢复。  
shutdown transactional：不允许新的连接、不等待会话结束、等待事务结束、做一个检查点并关闭数据文件。启动时不需要实例恢复。  
shutdown immediate：不允许新的连接、不等待会话结束、不等待事务结束、做一个检查点并关闭数据文件。没有结束的事务是自动rollback的。启动时不需要实例恢复。  
shutdown abort：不允许新的连接、不等待会话结束、不等待事务结束、不做检查点且没有关闭数据文件。启动时自动进行实例恢复。  

## 六、创建表空间和用户

```shell
$ sqlplus / as sysdba

# 查询临时表空间文件的绝对路径。如果需要的话，可以通过查询来写定绝对路径。一般用${ORACLE_HOME}就可以了
SQL> select name from v$tempfile;

# 创建临时表空间‘OACESHI_TEMP’
SQL> create temporary tablespace OACESHI_TEMP tempfile
'${ORACLE_HOME}/oradata/OACESHI_TEMP.bdf' size 100m reuse autoextend on next 20m
maxsize unlimited;

# 查询用户表空间文件的绝对路径
SQL> select name from v$datafile;

# 创建表空间‘OACESHIDB’
SQL> create tablespace OACESHIDB datafile '${ORACLE_HOME}/oradata/OACESHIDB.bdf' size 100M
reuse autoextend on next 40M maxsize unlimited default storage(initial 128k next 128k
minextents 2 maxextents unlimited);

# 创建用户‘OACESHI’和密码‘OACESHI’，指定上边创建的临时表空间和表空间
SQL> create user OACESHI identified by OACESHI default tablespace OACESHIDB temporary tablespace OACESHI_TEMP;

# 赋予权限
SQL> grant connect,resource to OACESHI;
SQL> grant create any view to OACESHI;

# 或者快速创建dba用户
SQL> create user TEST identified by TEST;
SQL> grant connect,resource to TEST;
```

## 七、问题解决

注意：  
操作监听和数据库都需要切换到oracle用户下  
操作数据库还需要通过sqlplus，以管理员身份登录到oracle  

plsql连接时无监听程序
<https://www.linuxidc.com/Linux/2017-10/147393.htm>
