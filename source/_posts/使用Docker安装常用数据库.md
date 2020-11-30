---
title: 使用Docker安装常用数据库
date: 2020-09-07 16:51:05
categories: 
    - 程序开发
    - Docker
tags:
    - docker
---
## 使用Docker搭建Mysql服务

拉取官方镜像,不写后面的版本号则会自动拉取最新版
```bash
docker pull mysql:5.7   # 拉取 mysql 5.7
```

<!-- more -->

检查是否拉取成功
```bash
sudo docker images
```

直接启动容器
```bash
sudo docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```
* –name：容器名，此处命名为mysql
* -e：配置信息，此处配置mysql的root用户的登陆密码
* -p：端口映射，此处映射 主机3306端口 到 容器的3306端口

如果要建立目录映射
```bash
suso docker run -p 3306:3306 --name mysql \
-v /usr/local/docker/mysql/conf:/etc/mysql \
-v /usr/local/docker/mysql/logs:/var/log/mysql \
-v /usr/local/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:5.7
```

检查容器是否正确运行
```
docker container ls
```

如果无法访问到MySQL，需要允许通过防火墙
```bash
# 开放端口：
systemctl status firewalld
firewall-cmd  --zone=public --add-port=3306/tcp -permanent
firewall-cmd  --reload
# 关闭防火墙：
sudo systemctl stop firewalld
```

进入docker本地客户端设置远程访问账号
```bash
sudo docker exec -it mysql bash
mysql -uroot -p123456
mysql> use mysql;
mysql> grant all privileges on *.* to root@'%' identified by "password";
mysql> flush privileges;
mysql> select host,user,password from user;
```

现在可以通过以下信息连接到mysql了
```
host: 127.0.0.1
port: 3306
user: root
password: 123456
```

## 使用Docker搭建Sql Server服务

从 Microsoft 容器注册表中拉取 SQL Server 2019 Linux 容器映像
```
sudo docker pull mcr.microsoft.com/mssql/server:2019-CU5-ubuntu-18.04
```

使用命令提示符使用以下命令运行容器映像
```
sudo docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 \
   -d mcr.microsoft.com/mssql/server:2019-CU5-ubuntu-18.04
```
* -e "ACCEPT_EULA=Y"    
  将 ACCEPT_EULA 变量设置为任意值，以确认接受最终用户许可协议。 SQL Server 映像的必需设置。

* -e "SA_PASSWORD=<YourStrong@Passw0rd\>"    
  指定至少包含 8 个字符且符合 SQL Server 密码要求的强密码。 SQL Server 映像的必需设置。

* -p 1433:1433    
  将主机环境中的 TCP 端口（第一个值）映射到容器中的 TCP 端口（第二个值）。 在此示例中，SQL Server 侦听容器中的 TCP 1433，并对主机上的端口 1433 公开。

* --name sql1    
  为容器指定一个自定义名称，而不是使用随机生成的名称。 如果运行多个容器，则无法重复使用相同的名称。

* mcr.microsoft.com/mssql/server:2019-CU5-ubuntu-18.04    
  SQL Server 2019 Ubuntu Linux 容器映像。

使用 docker exec 运行sqlcmd，以使用 Transact-SQL 更改密码。 在下面的示例中，将旧密码 <YourStrong!Passw0rd> 和新密码 <YourNewStrong!Passw0rd> 替换为你自己的密码值。
```
sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U SA -P "<YourStrong@Passw0rd>" \
   -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
```

参考：https://docs.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash

## 使用Docker搭建Oracle11g服务

下载的过程较长，镜像6.8G
```
docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```

创建容器
```
docker run -d -p 1521:1521 --name oracle11g registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```

启动容器
```
docker start oracle11g
```

进入镜像进行配置
```
docker exec -it oracle11g bash
```

切换root用户，密码：helowin
```
su root
```

编辑profile文件配置ORACLE环境变量
```
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
 
export ORACLE_SID=helowin
 
export PATH=$ORACLE_HOME/bin:$PATH
```

创建软连接
```
ln -s $ORACLE_HOME/bin/sqlplus /usr/bin
```

登录sqlplus并修改sys、system用户密码
```
su - oracle
sqlplus /nolog
conn /as sysdba

# 打开数据库
alter database mount;
alter database open;

# 修改密码
alter user system identified by system;
alter user sys identified by sys;

exit
```

修改 tnsnames.ora的 service_name=helowinXDB
```
docker_oracle11 =
 (DESCRIPTION =
   (ADDRESS_LIST =
     (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.211.135)(PORT =1521))
   )
   (CONNECT_DATA =
     (SERVICE_NAME = helowinXDB)
   )
)
```

启用监听
```
lsnrctl status
```