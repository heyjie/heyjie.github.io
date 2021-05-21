---
title: 使用Ubuntu19.10搭建一个编程工作站如何配置
date: 2020-04-16 11:31:30
categories: 
    - 程序开发
    - Linux
    - Ubuntu
tags: 
    - ubuntu
---
ubuntu在个人工作站与服务器领域都表现的非常稳定优秀，工程师使用ubuntu做为个人开发电脑完全没有问题，功能齐全性能稳定，因为用户众多解决问题也比较容易。
![](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_250,h_250/https://assets.ubuntu.com/v1/28e0ebb7-Focal-Fossa-gradient-outline.svg)

<!-- more -->
## 下载
从官网下载[https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_15-02-34.png)

## 制作u盘启动盘
点击链接
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_18-23-24.png)
选择第二步，下载工具
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_18-18-57.png)
点击下载
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_18-26-33.png)
双击打开刚才下载的工具，选择镜像后点开始制作
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_18-28-51.png)

## 安装系统
重启电脑按f12选择u盘启动，根据自己主板可能其他按键

![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_18-40-58.png)

![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_19-03-29.png)

![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_19-04-53.png)
如果要装双系统可以选择其他选项
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_19-44-55.png)

![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_20-22-18.png)

![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_20-24-03.png)

等待安装完成
![](Ubuntu19-10编程工作站配置/Snipaste_2020-04-16_20-25-02.png)

## 设置镜像源
打开软件和更新
![](Ubuntu19-10编程工作站配置/SDFSILU.png)

选择下载服务器为阿里云
![](Ubuntu19-10编程工作站配置/1H0H2JYE.jpg)

等待完成
![](Ubuntu19-10编程工作站配置/CPZIQ42Z.jpg)

## 更新系统
打开软件更新器，更新系统
![](Ubuntu19-10编程工作站配置/JNRY.png)

![](Ubuntu19-10编程工作站配置/V1VX.jpg)

执行系统更新
![](Ubuntu19-10编程工作站配置/5Z6GB07.jpg)

重启
![](Ubuntu19-10编程工作站配置/WIXTV.png)

## 使用终端
打开终端
![](Ubuntu19-10编程工作站配置/CYLAFKLZN.png)

安装常用软件vim,git,php
![](Ubuntu19-10编程工作站配置/7BW3.jpg)

## 设置桌面
在软件里面找到tweaks工具，用来设置ubuntu桌面
![](Ubuntu19-10编程工作站配置/6FSHYRTH.png)

进入安装页面选择安装
![](Ubuntu19-10编程工作站配置/4MA62D3.png)

打开刚才下载的工具，设置不显示桌面图标
![](Ubuntu19-10编程工作站配置/XIYPY.jpg)

设置顶栏显示时间和电池百分比
![](Ubuntu19-10编程工作站配置/TM6EUL.jpg)

在桌面右击打开显示设置
![](Ubuntu19-10编程工作站配置/YQRSDWXP.png)

选择一个桌面背景
![](Ubuntu19-10编程工作站配置/NHRN.jpg)

单击设置为桌面背景
![](Ubuntu19-10编程工作站配置/HGVT8I6.jpg)

设置dock栏显示在底部和自动隐藏
![](Ubuntu19-10编程工作站配置/81QJFRT.jpg)

## 设置主题
访问 https://www.pling.com/p/1136805/ ,安装主题工具
![](Ubuntu19-10编程工作站配置/LQM8NHXT.jpg)

选择打开
![](Ubuntu19-10编程工作站配置/2AF998.png)

等待完成出现安装
![](Ubuntu19-10编程工作站配置/2SSSR.png)

安装完成
![](Ubuntu19-10编程工作站配置/JF0DWRD.png)

访问https://vinceliuice.github.io/ ，选择一个主题
![](Ubuntu19-10编程工作站配置/CFN85F.jpg)

我选择mac主题
![](Ubuntu19-10编程工作站配置/2RVBQ.png)

用终端下载安装图标
![](Ubuntu19-10编程工作站配置/KNBVFR.jpg)

使用tweaks工具设置主题和图标
![](Ubuntu19-10编程工作站配置/MDPR3K8.jpg)

## 安装vscode
安装vscode,选择下载deb
![](Ubuntu19-10编程工作站配置/HPNQU.png)

安装
![](Ubuntu19-10编程工作站配置/3BBUC.png)

打开vscode商店，安装sync同步工具，点击`Download Public Gist`输入`86120a7addb2aa40b20514be7d130991`开始同步
![](Ubuntu19-10编程工作站配置/RVWBLY.png)

## 安装字体
下载字体
![](Ubuntu19-10编程工作站配置/W1JWH.png)

打开安装
![](Ubuntu19-10编程工作站配置/HZPBUX.png)