---
title: Python虚拟环境venv的使用
date: 2020-04-15 13:37:12
categories: 
    - 程序开发
    - Python
tags:
    - python
    - venv
---
因为虚拟环境使用较多，所以python3.4版本起，官方直接内置了venv包

## windos环境中使用
创建虚拟环境，第二个venv是虚拟环境名，可以自定义
```bash
python -m venv venv
```

激活虚拟环境
```bash
venv\Scripts\activate
```

退出虚拟环境
```bash
venv\Scripts\deactivate
```

## ubuntu环境中使用
安装venv包
```
sudo apt install python3-venv
```

创建虚拟环境
```
python3 -m venv venv
```

激活虚拟环境
```
source venv/bin/activate
```

退出虚拟环境
```
deactivate
```
