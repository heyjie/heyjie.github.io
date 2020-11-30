---
title: Ubuntu18下安装MySql
date: 2020-02-16 20:00:11
categories: 
    - [程序开发,Linux,Ubuntu]
    - [程序开发,数据库,MySql]
tags: 
    - mySql
    - ubuntu
---

查看有没有安装MySQL
```
dpkg -l | grep mysql
```

安装MySQL，注意安装前先更新一下软件源以获得最新版本
```
sudo apt-get update  #更新软件源
sudo apt-get install mysql-server  #安装mysql
```

上述命令会安装以下包：  
apparmor  
mysql-client-5.7  
mysql-common  
mysql-server  
mysql-server-5.7  
mysql-server-core-5.7  
因此无需再安装mysql-client等。安装过程会提示设置mysql root用户的密码，设置完成后等待自动安装即可。默认安装完成就启动了mysql

<!-- more -->

安装MySQL依赖库
```
sudo apt-get install libmysqlclient-dev
```

启动和关闭mysql服务器：
```
service mysql start
service mysql stop
```

确认是否启动成功，mysql节点处于LISTEN状态表示启动成功：
```
netstat -tap | grep mysql
```  

进入mysql shell界面：
```
mysql -u root -p
```
-u 表示选择登陆的用户名， -p 表示登陆的用户密码。  
然后通过 show databases; 就可以查看当前的所有数据库。  

初始化数据库：
```
mysql_secure_installation
```

``` sh
secure enough. Would you like to setup VALIDATE PASSWORD plugin?    # 要安装验证密码插件吗?
Press y|Y for Yes, any other key for No: N    # 这里我选择N
New password:   # 输入要为root管理员设置的数据库密码
Re-enter new password:   # 再次输入密码
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y     # 删除匿名账户
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : N    # 禁止root管理员从远程登录，这里我没有禁止
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y   # 删除test数据库并取消对它的访问权限
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y   # 刷新授权表，让初始化后的设定立即生效
```

检查mysql服务状态：
```
systemctl status mysql
```

现在配置mysql允许远程访问，首先编辑 /etc/mysql/mysql.conf.d/mysqld.cnf 配置文件：
```
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
注释掉bind-address          = 127.0.0.1  
保存退出，然后进入mysql数据库，执行授权命令：  

```sh
mysql -u root -p
```

```bash
mysql> grant all on *.* to root@'%' identified by '你的密码' with grant option;

mysql> flush privileges;    # 刷新权限

mysql> exit
```
然后执行exit命令退出mysql服务，再执行如下命令重启mysql：
```
systemctl restart mysql
```

附加说明：
```
sudo /etc/init.d/mysql start 这是启动mysql
sudo /etc/init.d/mysql restart  这是重启
sudo /etc/init.d/mysql stop 这是停止
```
一些问题：  
https://blog.csdn.net/guoguicheng1314/article/details/80526111
https://blog.csdn.net/vin_1991216/article/details/82632710