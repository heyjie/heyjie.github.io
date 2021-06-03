---
title: 使用CentOS7安装MySql
date: 2020-08-05 09:29:05
categories: 
    - 程序开发
    - Linux
tags: 
    - linux
    - centos
---

## 安装mysql
下载并安装MySQL官方的 Yum Repository
```
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm
```
执行安装命令
```
yum -y install mysql-community-server
```

<!-- more -->

## 使用国内镜像安装

去国内镜像站下载client和server包
> <http://uni.mirrors.163.com/mysql/Downloads/>
```
mysql-community-client-5.7.31-1.el7.x86_64.rpm
mysql-community-server-5.7.31-1.el7.x86_64.rpm
```

上传到centos的`/var/cache/yum/x86_64/7/mysql57-community/packages/`  
再次执行安装
```
yum -y install mysql-community-server
```

## 设置mysql

开启MySQL服务
```
systemctl start  mysqld.service
```

获取临时密码
```
grep "password" /var/log/mysqld.log
```

进入数据库
```
mysql -uroot -p
```

设置密码
```sql
# 修改密码验证规则
mysql> set global validate_password_policy=0;
mysql> set global validate_password_length=1;

# 设置简单密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';

# 开启mysql的远程访问
mysql> grant all privileges on *.* to 'root'@'%' identified by 'password' with grant option;
# 刷新
mysql> flush privileges;
mysql> exit;
```

防火墙开放端口
```
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
```

## 更改mysql为utf-8
修改 mysql 配置文件 my.cnf
```
vi /etc/my.cnf
```
在[client]段增加下面代码：default-character-set=utf8

在[mysql]段增加下面的代码：default-character-set=utf8

在[mysqld]段增加下面的代码：character-set-server=utf8

![](3341.png)

重启mysql服务器
```
service mysqld restart
```

查看字符编码
```
mysql -uroot -p
mysql> status;
```

## 安装python开发模块

```
cd /etc/yum.repos.d/

rpm -ivh http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm

sudo yum install mysql-devel
sudo yum install python3-devel
pip install mysqlclient
```