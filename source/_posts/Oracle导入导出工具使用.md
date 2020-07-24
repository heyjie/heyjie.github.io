---
title: Oracle导入导出工具使用
date: 2020-04-15 14:07:07
categories: 
    - 程序开发
    - 数据库
    - Oracle
tags:
    - 数据库
    - Oracle
---
迁移Oracle数据库需要用到导入导出工具exp和imp,可直接跨平台系统导入导出数据,速度较慢，操作简单

<!-- more -->
## 导用户
导出用户MDM_P到当前文件夹（导出文件为testDb.dmp）
`exp MDM_P/8888@192.168.134.9:1521/ora11g file='./testDb.dmp' owner=MDM_P`  
从当前文件夹导入MDM_P用户
`imp MDM_P/8888@192.168.135.10/orcl  file=./testDb.dmp fromuser=MDM_P`  

## 导数据库
将数据库ORCL完全导出到D盘（导出文件为testDb.dmp）其中full=y表示全库导出：  
`exp scott/tiger@192.168.1.1:1521/ORCL file='d:/testDb.dmp' full=y`  
将D:\example.dmp文件中的库导入到mydb下的system用户中  
`imp system/123456@213.234.12.32/mydb file=D:\example.dmp full=y ignore=y`  
如果表已经存在，则会报错，在后面加上igonre=y（忽略创建错误，继续后面操作）。  
## 导表
将数据库的表table1,table2导到D盘（导出文件为testDb.dmp）：  
`exp scott/tiger@192.168.1.1:1521/ORCL file='d:/testDb.dmp' tables=(table1,table2)`

## exp方式导出数据
相关参数项如下：

| 关键字	| 说明 	| 默认 |
| :-----| ----: | :----: |
|USERID                    	用户名/口令
|FULL                        	导出整个文件 	(N)
|BUFFER                   	数据缓冲区的大小
|OWNER                    	导出指定的所有者用户名列表
|FILE                          	输出文件	(EXPDAT.DMP)
|TABLES                   	导出指定的表名列表
|COMPRESS             	是否压缩导出的文件	(Y)
|RECORDLENGTH  	IO 记录的长度
|GRANTS                  	导出权限	(Y)
|INCTYPE                 	增量导出类型
|INDEXES                 	导出索引 	(Y)
|RECORD                  	跟踪增量导出	(Y)
|ROWS                       	导出数据行	(Y)
|PARFILE                  	参数文件名
|CONSTRAINTS       	导出限制 	(Y)
|CONSISTENT          	交叉表一致性
|LOG                          	屏幕输出的日志文件
|STATISTICS             	分析对象(ESTIMATE)
|DIRECT                    	直接路径	(N)
|TRIGGERS               	导出触发器	(Y)
|FEEDBACK             	显示每 x 行 (0) 的进度
|FILESIZE                 	各转储文件的最大尺寸
|QUERY                     	选定导出表子集的子句
|TRANSPORT_TABLESPACE	导出可传输的表空间元数据	(N)
|TABLESPACES	导出指定的表空间列表


## imp方式导入数据
相关参数项如下：

| 关键字	| 说明 	| 默认 |
| :-----| ----: | :----: |
| USERID | 用户名/口令 |
| FULL | 导入整个文件 | (N) |
|BUFFER	数据缓冲区大小
|FROMUSER	所有人用户名列表
|FILE	输入文件	  (EXPDAT.DMP)
|TOUSER	用户名列表
|SHOW 	只列出文件内容	(N)
|TABLES	表名列表
|IGNORE	忽略创建错误	(N) 
|RECORDLENGTH 	IO记录的长度
|GRANTS	导入权限	(Y)
|INCTYPE	增量导入类型
|INDEXES	导入索引 	 (Y)
|COMMIT 	提交数组插入	 (N)
|ROWS	导入数据行	 (Y) 
|PARFILE	参数文件名
|LOG	屏幕输出的日志文件
|CONSTRAINTS 	导入限制	 (Y)
|DESTROY	覆盖表空间数据文件	 (N)
|INDEXFILE	将表/索引信息写入指定的文件
|SKIP_UNUSABLE_INDEXES	跳过不可用索引的维护	  (N)
|FEEDBACK	每 x 行显示进度
|TOID_NOVALIDATE 	跳过指定类型 ID 的验证
|FILESIZE 	每个转储文件的最大大小
|STATISTICS	始终导入预计算的统计信息
|RESUMABLE	在遇到有关空间的错误时挂起
|RESUMABLE_NAME	用来标识可恢复语句的文本字符串
|RESUMABLE_TIMEOUT	RESUMABLE 的等待时间
|COMPILE	编译过程, 程序包和函数	 (Y)
|STREAMS_CONFIGURATION	导入 Streams 的一般元数据	 (Y)
|STREAMS_INSTANITATION 	导入 Streams 的实例化元数据	 (N)
|TRANSPORT_TABLESPACE	导入可传输的表空间元数据
|TABLESPACES 	将要传输到数据库的表空间
|DATAFILES	将要传输到数据库的数据文件
|TTS_OWNERS	拥有可传输表空间集中数据的用户

## 交互命令方式导入导出
### 导出工具 exp

1. 它是操作系统下一个可执行的文件 存放目录/ORACLE_HOME/bin

   exp导出工具将数据库中数据备份压缩成一个二进制系统文件.可以在不同OS间迁移
   
   它有三种模式：
       a.  用户模式： 导出用户所有对象以及对象中的数据；
       b.  表模式： 导出用户所有表或者指定的表；
       c.  整个数据库： 导出数据库中所有对象。

2. 导出工具exp交互式命令行方式的使用的例子
```bash
$exp test/test123@appdb
Enter array fetch buffer size: 4096 > 回车
Export file: expdat.dmp > m.dmp  生成导出的文件名 
(1)E(ntire database), (2)U(sers), or (3)T(ables): (2)U > 3
Export table data (yes/no): yes > 回车
Compress extents (yes/no): yes > 回车
Export done in ZHS16GBK character set and ZHS16GBK NCHAR character set
About to export specified tables via Conventional Path ...
Table(T) or Partition(T:P) to be exported: (RETURN to quit) > cmamenu  要导出的表名
. . exporting table                        CMAMENU       4336 rows exported
Table(T) or Partition(T:P) to be exported: (RETURN to quit) >要导出的表名n
Table(T) or Partition(T:P) to be exported: (RETURN to quit) > 回车
Export terminated successfully without warnings. 
```
3. 导出工具exp非交互式命令行方式的例子
```bash
$exp scott/tiger tables=(emp,dept) file=/directory/scott.dmp grants=y 

# 说明:把scott用户里两个表emp,dept导出到文件/directory/scott.dmp

$exp scott/tiger tables=emp query=\"where job=\'salesman\' and sal\<1600\" file=/directory/scott2.dmp 

# 说明:在exp里面加上导出emp的查询条件job='salesman' and sal<1600 (但我个人很少这样用,还是把满足条件的记录生成临时表后,再exp会方便一些)

$exp parfile=username.par file=/directory1/username_1.dmp,/directory1/username_2.dmp filesize=2000M log=/directory2/username_exp.log
```
参数文件username.par内容
```
userid=username/userpassword
buffer=8192000
compress=n
grants=y
```
说明:username.par为导出工具exp用的参数文件,里面具体参数可以根据需要去修改

     filesize指定生成的二进制备份文件的最大字节数
     
     (可用来解决某些OS下2G物理文件的限制及加快压缩速度和方便刻历史数据光盘等)

### 导入工具 imp

1. 它是操作系统下一个可执行的文件 存放目录/ORACLE_HOME/bin

imp导入工具将EXP形成的二进制系统文件导入到数据库中.

   它有三种模式：
       a.  用户模式： 导出用户所有对象以及对象中的数据；
       b.  表模式： 导出用户所有表或者指定的表；
       c.  整个数据库： 导出数据库中所有对象。
       
   只有拥有IMP_FULL_DATABASE和DBA权限的用户才能做整个数据库导入
   
   imp步骤：
   (1) create table  (2) insert data  (3) create index (4) create triggers,constraints

2.导入工具imp交互式命令行方式的例子
```bash
$ imp
Import: Release 8.1.6.0.0 - Production on 星期五 12月 7 17:01:08 2001
(c) Copyright 1999 Oracle Corporation.  All rights reserved.
用户名:  test
口令:****
连接到: Oracle8i Enterprise Edition Release 8.1.6.0.0 - 64bit Production
With the Partitioning option
JServer Release 8.1.6.0.0 - Production
导入文件: expdat.dmp> /tmp/m.dmp
输入插入缓冲区大小（最小为 8192 ) 30720>
经由常规路径导出由EXPORT:V08.01.06创建的文件
警告: 此对象由 TEST 导出, 而不是当前用户
已经完成ZHS16GBK字符集和ZHS16GBK NCHAR 字符集中的导入
只列出导入文件的内容(yes/no)：no>
由于对象已存在, 忽略创建错误(yes/no)：no> yes
导入权限(yes/no)：yes>
导入表数据(yes/no)：yes>
导入整个导出文件(yes/no)：no> yes
. 正在将TEST的对象导入到 SCOTT
. . 正在导入表                       "CMAMENU"       4336行被导入
成功终止导入，但出现警告。 
```

3.导入工具imp非交互式命令行方式的例子
```bash
$ imp system/manager fromuser=jones tables=(accts) 
$ imp system/manager fromuser=scott tables=(emp,dept) 
$ imp system/manager fromuser=scott touser=joe tables=emp 
$ imp scott/tiger file = expdat.dmp full=y 
$ imp scott/tiger file = /mnt1/t1.dmp show=n buffer=2048000 ignore=n commit=y grants=y full=y log=/oracle_backup/log/imp_scott.log
$ imp system/manager parfile=params.dat 
params.dat 内容 
file=dba.dmp show=n ignore=n grants=y fromuser=scott tables=(dept,emp) 
```
4.导入工具imp可能出现的问题

(1) 数据库对象已经存在
一般情况, 导入数据前应该彻底删除目标数据下的表, 序列, 函数/过程,触发器等;  
数据库对象已经存在, 按缺省的imp参数, 则会导入失败
如果用了参数ignore=y, 会把exp文件内的数据内容导入
如果表有唯一关键字的约束条件, 不合条件将不被导入
如果表没有唯一关键字的约束条件, 将引起记录重复

(2) 数据库对象有主外键约束
      不符合主外键约束时, 数据会导入失败 
      解决办法: 先导入主表, 再导入依存表
disable目标导入对象的主外键约束, 导入数据后, 再enable它们
(3)  权限不够
如果要把A用户的数据导入B用户下, A用户需要有imp_full_database权限

(4)  导入大表( 大于80M ) 时, 存储分配失败
      默认的EXP时, compress = Y, 也就是把所有的数据压缩在一个数据块上.
      导入时, 如果不存在连续一个大数据块, 则会导入失败.
      导出80M以上的大表时, 记得compress= N, 则不会引起这种错误.

(5) imp和exp使用的字符集不同
      如果字符集不同, 导入会失败, 可以改变unix环境变量或者NT注册表里NLS_LANG相关信息.
      导入完成后再改回来.

(6) imp和exp版本不能往上兼容
imp可以成功导入低版本exp生成的文件, 不能导入高版本exp生成的文件
根据情况我们可以用
$ imp username/password@connect_string
说明: connect_string 是在/ORACLE_HOME/network/admin/tnsnames.ora
   定义的本地或者远端数据库的名称
注意事项:
UNIX: /etc/hosts           要定义本地或者远端数据库服务器的主机名
win98:    windows\hosts             和IP地址的对应关系  

win2000:  winnt\system32\drivers\etc\hosts