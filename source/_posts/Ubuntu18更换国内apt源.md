---
title: Ubuntu更换国内apt源
date: 2020-02-16 19:49:11
categories: 
    - 环境搭建
tags: 
	- ubuntu
---
备份原来的源：  
`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`  
更换源:  
`sudo vim /etc/apt/sources.list`  
将里面文件内容全部替换成下面：  

<!-- more -->
```shell
#deb包
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
##测试版源  
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
# 源码  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
##测试版源  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
# Canonical 合作伙伴和附加  
deb http://archive.canonical.com/ubuntu/ xenial partner
deb http://extras.ubuntu.com/ubuntu/ xenial main
```

执行更新：  
`sudo apt-get update`  
复损坏的软件包，尝试卸载出错的包，重新安装正确版本的:  
`sudo apt-get -f install`  
更新软件:  
`sudo apt-get upgrade`  
