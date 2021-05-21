---
title: Python虚拟环境pipenv的常用命令
date: 2020-04-15 13:19:54
categories: 
    - 程序开发
    - Python
tags:
    - python
    - pipenv
---
Pipenv可以用于简化Python项目中依赖项的管理。只用了一年， Pipenv 就变成了管理软件包依赖关系的 Python 官方推荐资源。 它汇集了Pip，Pipfile和Virtualenv的功能，是一个强大的命令行工具。

<!-- more -->
## 安装和升级pipenv
```bash
pip install  pipenv  # 安装pipenv
pip install  --upgrade pipenv   # 升级安装pipenv
```

## 使用pipenv
### 初始化
```bash
pipenv install  # 初始化项目，将目录更改为python项目文件夹，并启动pipenv。这将创建 pipfile和 pipfile.lock，这两个文件描述了项目依赖环境的配置情况，当有人git-clone了项目，只需安装了pipenv就可以配置出同样的环境。

pipenv install --dev  # 初始化项目，安装所有依赖项，包括开发包

pipenv install --dev --pypi-mirror https://mirrors.aliyun.com/pypi/simple  # 使用国内镜像来初始化

pipenv --three  # 会使用当前系统的Python3创建环境
pipenv --two  # 使用python2创建
pipenv --python 3.6  # 指定某一Python版本创建环境
```
### 查看信息
```bash
pipenv --where  # 显示目录信息
pipenv --venv  # 显示虚拟环境信息
pipenv --py  # 显示Python解释器信息
```
### 使用虚拟环境
```bash
pipenv shell  # 激活虚拟环境
exit  # 退出虚拟环境
pipenv – – rm  # 删除虚拟环境
```
### 其他命令
```bash
pipenv run  # 不用shell,直接在虚拟环境中执行命令
pipenv run python 文件名.py  # 使用pipenv环境运行
pipenv run pip 命令  # 运行pip

alias prp="pipenv run python"  # 给命令设置别名

pipenv install requests  # 安装相关模块并加入到Pipfile

pipenv install django==1.11  # 安装固定版本模块并加入到Pipfile

pipenv install --dev nose2  # 在开发环境安装模块nose2

pipenv graph  # 显示依赖图

pipenv check  # 检查安全漏洞

pipenv uninstall requests  # 卸载包并从Pipfile中移除

pipenv uninstall --all  # 卸载全部包

pipenv uninstall --dev  # 卸载全部开发包

pipenv update --outdated  # 查看所有需要更新的包

pipenv update  # 更新所有包

pipenv update <包名>  # 更新指定的包

pipenv lock -r  # 将Pipfile和Pipfile.lock文件里面的包导出为requirements.txt文件

pipenv lock -r --dev  # 导出开发模块到requirements.txt

pipenv install -r path/to/requirements.txt  # 
如果项目中有requirements.txt文件，pipenv会在安装的时候自动导入。如果需要导入其他位置的requirements.txt，可以用这个命令
```