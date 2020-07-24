---
title: 'SqlServer获取客户端信息'
date: 2020-04-15 13:43:56
categories: 
    - 程序开发
    - 数据库
tags:
    - 数据库
---
用Sql获取操作的数据库名,计算机名,用户名,网卡物理地址,IP地址,程序名
![](SqlServer获取客户端信息/20200522154113.png)

<!-- more -->
## 创建存储过程

```sql
create proc p_getlinkinfo

@dbname sysname=null,--要查询的数据库名,默认查询所有数据库的连接信息

@includeip bit=0     --是否显示IP地址,因为查询IP地址比较费时,所以增加此控制

as

declare @dbid int

set @dbid=db_id(@dbname)


create table #tb(id int identity(1,1),dbname sysname,hostname 
nchar(128),loginname nchar(128),net_address nchar(12),net_ip 
nvarchar(15),prog_name nchar(128))

insert into #tb(hostname,dbname,net_address,loginname,prog_name)

select distinct hostname,db_name(dbid),net_address,loginame,program_name 
from master..sysprocesses

where hostname<>'' and (@dbid is null or dbid=@dbid)


if @includeip=0 goto lb_show  --如果不显示IP地址,就直接显示


declare @sql varchar(500),@hostname nchar(128),@id int

create table #ip(hostname nchar(128),a varchar(200))

declare tb cursor local for select distinct hostname from #tb

open tb

fetch next from tb into @hostname

while @@fetch_status=0

begin

set @sql='ping '+@hostname+' -a -n 1 -l 1'

insert #ip(a) exec master..xp_cmdshell @sql

update #ip set hostname=@hostname where hostname is null

fetch next from tb into @hostname

end


update #tb set net_ip=left(a,patindex('%:%',a)-1)

from #tb a inner join (

select hostname,a=substring(a,patindex('Ping statistics for 
%:%',a)+20,20) from #ip

where a like 'Ping statistics for %:%') b on a.hostname=b.hostname


lb_show:

select id,数据库名=dbname,客户机名=hostname,用户名=loginname
,网卡物理地址=net_address,IP地址=net_ip,应用程序名称=prog_name from #tb

GO


--显示所有本机的连接信息：
exec p_getlinkinfo

--显示所有本机的连接信息,包含ip地址：
exec p_getlinkinfo @includeip=1

--显示连接指定数据库的信息：
exec p_getlinkinfo @dbname=数据库名,@includeip=1
```

## 遇到的问题
错误描述：SQL Server阻止了对组件‘xp_cmdshell’的过程‘sys.xp_cmdshell’的访问。因为此组件已作为此服务嚣安全配置的一部分而被关闭。系统管理员可以通过使用sp_configure启用‘xp_cmdshell’。有关启用‘xp_cmdshell’的详细信息，请参阅sQL帮助文件。  

解决方案
```sql
sp_configure 'show advanced options',1
reconfigure
go
sp_configure 'xp_cmdshell',1
reconfigure
go
```



