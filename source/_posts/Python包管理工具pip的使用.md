---
title: Python包管理工具pip的使用
date: 2019-12-28 09:50:08
updated: 2020-1-15 15:32:00
categories: 
    - 程序开发
tags:
	- Python
	- pip
---
## 简介

pip是Python包管理工具，该工具提供了对Python包的查找、下载、安装、卸载的功能。

## 常用命令

查看版本和路径

``` python
pip --version
```

获取帮助

``` python
pip --help
```

升级pip版本

``` python
pip install -U pip
```

查看安装包信息

``` python
pip show SomePackage
pip show -f SomePackage  # 查看安装包详细信息
```

列出已安装的包

``` python
pip list
pip list -o  # 查看可升级的包
```

在文件中生成包依赖

``` python
pip freeze > requirements.txt  # 生成txt文件
pip install -r requirements.txt  # 安装txt依赖
```

安装包

``` python
pip install SomePackage              # 最新版本
pip install SomePackage==1.0.4       # 指定版本
pip install 'SomePackage>=1.0.4'     # 最小版本
pip install SomePackage -i http://pypi.douban.com/simple --trusted-host pypi.douban.com  # 用国内镜像安装，可以解决安装慢的问题
```

包管理

``` python
pip install --upgrade SomePackage  # 升级包
pip uninstall SomePackage  # 卸载包
pip search SomePackage  # 搜索包
```

安装whl包

``` python
pip install wheel  # 先安装wheel库
pip install XXX.whl  # 再安装whl文件
'''
whl下载网址https://www.lfd.uci.edu/~gohlke/pythonlibs/
'''
```

在命令前加`python -m`来指定python

``` python
python -m pip install XXX
python2 -m pip install XXX  # 使用python2
python3 -m pip install XXX  # 使用python3
```
