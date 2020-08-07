---
title: Ubuntu18安装Hive2.3.7
date: 2020-07-08 17:26:29
categories: 
    - 程序开发
    - Hive
tags: 
    - hdfs
    - hive
    - ubuntu
---
hive是基于hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表并提供类sql查询功能

<!-- more -->
## 准备工作
* 安装Hive之前必须配置好hadoop环境
* 安装Hive之前必须配置好JDK
* 安装好mysql数据库
* Hive可以只安装在namenode所在的机器上，如果NameNode有多个，则每个都需要安装，可以不在datanode上安装

## 为什么要用Hive
1. 直接使用hadoop所面临的问题:
* 人员学习成本太高
* 项目周期要求太短
* MapReduce实现复杂查询逻辑开发难度太大

2. 为什么要使用Hive
* 操作接口采用类SQL语法，提供快速开发的能力。
* 避免了去写MapReduce，减少开发人员的学习成本。
* 扩展功能很方便。

3. Hive的特点：可扩展，延展性，容错

4. Hive的数据存储
+ Hive中所有的数据都存储在 HDFS 中，没有专门的数据存储格式（可支持Text，SequenceFile，ParquetFile，RCFILE等）
+ 只需要在创建表的时候告诉 Hive 数据中的列分隔符和行分隔符，Hive 就可以解析数据。
+ Hive 中包含以下数据模型：DB、Table，External Table，Partition，Bucket。
  + db：在hdfs中表现为${hive.metastore.warehouse.dir}目录下一个文件夹
  + table：在hdfs中表现所属db目录下一个文件夹
  + external table：与table类似，不过其数据存放位置可以在任意指定路径
  + partition：在hdfs中表现为table目录下的子目录
  + bucket：在hdfs中表现为同一个表目录下根据hash散列之后的多个文件

## hive安装
解压：
```
tar -zxvf apache-hive-2.3.7-bin.tar.gz
```

把解压后的文件移到目录/usr/local/下:
```
mv apache-hive-2.3.7-bin /usr/local/hive
```

配置hive环境变量
```
vim /etc/profile
```

输入下面内容
```
export HIVE_HOME=/usr/local/hive
export HIVE_CONF_DIR=$HIVE_HOME/conf
export PATH=$PATH:$HIVE_HOME/bin
```

授权
```
sudo chown -R hadoop:hadoop /usr/local/hive
```

使配置文件的修改生效
```
source /etc/profile
```

## 配置hive
### 配置hive-site.xml

进入目录
```
cd $HIVE_CONF_DIR
```

拷贝hive-default.xml.template并重命名为hive-site.xml
```
cp hive-default.xml.template  hive-site.xml
```

编辑hive-site.xml
```
vim hive-site.xml
```

使用hadoop新建hdfs目录
因为在hive-site.xml中有这样的配置
```xml
<name>hive.metastore.warehouse.dir</name>
  <value>/user/hive/warehouse</value>
   <name>hive.exec.scratchdir</name>
  <value>/tmp/hive</value>
```

所以要在Hadoop集群新建/user/hive/warehouse目录，执行命令
```bash
# 进入Hadoop主目录
cd $HADOOP_HOME

# 创建目录
hadoop fs -mkdir -p  /user/hive/warehouse

# 新建的目录赋予读写权限
hadoop fs -chmod -R 777 /user/hive/warehouse

# 新建/tmp/hive/目录
hadoop fs -mkdir -p /tmp/hive/

# 目录赋予读写权限
hadoop fs -chmod -R 777 /tmp/hive

# 用以下命令检查目录是否创建成功
hadoop fs -ls /user/hive
hadoop fs -ls /tmp/hive
```

修改hive-site.xml中的临时目录
将hive-site.xml文件中的`${system:java.io.tmpdir}`替换为hive的临时目录，例如我替换为`/usr/local/hive/tmp`，该目录如果不存在则要自己手工创建，并且赋予读写权限。
```
cd $HIVE_HOME
mkdir tmp
chmod -R 777 tmp/
```

编辑hive-site
```
vim hive-site.xml
```

在vim中使用下面的代码替换`${system:java.io.tmpdir}`
```
:%s#${system:java.io.tmpdir}#/usr/local/hive/tmp#g
```

再将配置文件中`${system:user.name}`全部替换为root
```
:%s#${system:user.name}#root#g
```

例如原来：
```xml
 <property>
    <name>hive.downloaded.resources.dir</name><value>${system:java.io.tmpdir}/${hive.session.id}_resources</value>
    <description>Temporary local directory for added resources in the remote file system.</description>
  </property>
```
替换为：
```xml
<property>
    <name>hive.downloaded.resources.dir</name>
    <!--value>${system:java.io.tmpdir}/${hive.session.id}_resources</value-->
    <value>/user/local/apache-hive-2.1.1/tmp/${hive.session.id}_resources</value>
    <description>Temporary local directory for added resources in the remote file system.</description>
  </property>
```

### 修改hive-site.xml数据库相关的配置
`javax.jdo.option.ConnectionDriverName`，将该name对应的value修改为MySQL驱动类路径：
```xml
<property>
<name>javax.jdo.option.ConnectionDriverName</name>
<value>com.mysql.jdbc.Driver</value>
</property>  
```

`javax.jdo.option.ConnectionURL`，将该name对应的value修改为MySQL的地址：
```xml
<name>javax.jdo.option.ConnectionURL</name>
<value>jdbc:mysql://127.0.0.1:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false</value>
```

`javax.jdo.option.ConnectionUserName`，将对应的value修改为MySQL数据库登录名：
```xml
<name>javax.jdo.option.ConnectionUserName</name>
<value>hive</value>
```

`javax.jdo.option.ConnectionPassword`，将对应的value修改为MySQL数据库的登录密码：
```xml
<name>javax.jdo.option.ConnectionPassword</name>
<value>hive</value>
```

将MySQL驱动包上载到Hive的lib目录下
```
cp /home/heyj/down/mysql-connector-java-5.1.36.jar $HIVE_HOME/lib/
```

新建hive-env.sh文件并进行修改
```bash
cd $HIVE_CONF_DIR

#基于模板创建hive-env.sh
cp hive-env.sh.template hive-env.sh

vim hive-env.sh
```

编辑配置文件并加入以下配置：
```conf
# Set HADOOP_HOME to point to a specific hadoop install directory
HADOOP_HOME=/usr/local/hadoop

# Hive Configuration Directory can be controlled by:
export HIVE_CONF_DIR=/usr/local/hive/conf

# Folder containing extra libraries required for hive compilation/execution can be controlled by:
export HIVE_AUX_JARS_PATH=/usr/local/hive/lib
```

### 创建元数据库
登录mysql数据库
```bash
mysql -u root -p
```

mysql创建hive用户密码
```sql
mysql> CREATE DATABASE hive;
mysql> USE hive;
mysql> CREATE USER 'hive'@'localhost' IDENTIFIED BY 'hive';
mysql> GRANT ALL ON hive.* TO 'hive'@'localhost' IDENTIFIED BY 'hive';
mysql> GRANT ALL ON hive.* TO 'hive'@'%' IDENTIFIED BY 'hive';
mysql> FLUSH PRIVILEGES;
mysql> quit;
```

从Hive 2.1开始，我们需要运行下面的schematool命令作为初始化步骤,MySQL数据库初始化
```bash
# 进入到hive的bin目录
cd $HIVE_HOME/bin

# 对数据库进行初始化
schematool -initSchema -dbType mysql
```
执行成功后，在mysql的hive数据库里已生成metadata数据表：

## 启动Hive

执行hive的sql命令，在sql位置输入sql命令
```
hive -e 'sql'
```

或者启动hive，在命令行输入sql
```bash
hive
```

显示hive中所有的内置函数，测试hive是否可用
```
hive> show functions;
```

不报错就安装成功了

### 启动hiveserver2
启动为前台
```
hiveserver2
```

或者使用下面的命令启动
```
hive --service hiveserver2
```

启动为后台
```
nohup bin/hiveserver2 1>/var/log/hiveserver.log 2>/var/log/hiveserver.err &
```

启动成功后，可以在别的节点上用beeline去连接,有两种方法：

1. `beeline` 回车，进入beeline的命令界面
   输入命令连接hiveserver2
   ```
   beeline> !connect jdbc:hive2://localhost:10000
   ```
   （hadoop01是hiveserver2所启动的那台主机名，端口默认是10000）

2. 或者启动就连接：
   ```
   beeline -u jdbc:hive2://localhost:10000 -n hadoop
   ```

如果hiveserver2无法连接，修改`hive.server2.authentication`
```xml
<name>hive.server2.authentication</name>
    <value>NOSASL</value>
```
在hadoop的core-site.xml中追加
```xml
<property>
  <name>hadoop.proxyuser.root.hosts</name>
  <value>*</value>
 </property>
<property>
  <name>hadoop.proxyuser.root.groups</name>
    <value>*</value>
</property>
<property>
  <name>hadoop.proxyuser.hadoop.hosts</name>
  <value>*</value>
</property>
<property>
  <name>hadoop.proxyuser.hadoop.groups</name>
  <value>*</value>
</property>
```
浏览器打开ip(安装hive的主机ip地址)+端口10002，就能看到hiveserver2服务web页面

## hive常用命令
### 显示命令
> show tables;  
-- 显示表

> show databases;  
-- 显示数据库

> show partitions t_4;  
-- 显示表分区

> show functions;  
-- 显示hive中所有的内置函数

> desc t_name;  
-- 显示表定义

> desc extended t_name;  
-- 显示表定义的详细信息

> desc formatted table_name;  
-- 显示表定义的详细信息，并且用比较规范的格式显示

> show create table table_name;  
-- 显示建表语句 可以查看表创建语句

### 创建表
> create database hive_db  
-- 创建数据库

> use hive_db  
-- 使用数据库

> create table  if not exists table_name (foo int, bar string) row format delimited fields terminated by ',' stored as textfile;  
-- 创建内部表

> create external table if not exists pageview(pageid int,page_url string comment 'The page URL') row format delimited fields terminated by ',' location 'hdfs://mini1:9000/user/hive/warehouse/'  
-- 创建外部表

> create table student_p(Sno int,Sname string,Sex string,Sage int,Sdept string) partition by(part string) row format delimited fields terminated by ',' stored as textfile;  
-- 创建分区表

> create table student(id int,age int,name string) partitioned by(stat_date string) clustered by(id) sorted by (age) into 2 buckets row format delimited fields terminated by ',';  
-- 创建分桶表

### 修改表
> alter table student_p add partition(part='a') partition(part='b');  
-- 增加分区

> alter table table_name rename to new_table_name;  
-- 重命名表

### 删除表
> drop table table_name;  
-- 删除表

> truncate table table_name;  
-- 删除表中的数据

> alter table table_name drop partition (partition_name='分区名')  
-- 按分区删除数据

### 增加数据
> load data local inpath '/home/hadoop/data/data.txt' into table table_name;  
-- 本地模式加载数据到表
