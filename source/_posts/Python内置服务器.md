---
title: Python内置服务器
date: 2020-04-15 13:33:23
categories: 
    - 程序开发
tags:
	- Python
---
python内置了一个下载服务器。例如你的同事要让你传的文件位于某一个目录下面，那么你可以进入这个目录，然后用python开启一个下载服务器

<!-- more -->
## SimpleHTTPServer搭建web服务器
```
python -m http.server 8080
```
目录下如果有index.html，则会显示该页面。浏览器中输入 http://localhost：8080/ 访问

如果有更多需求，完全可以根据自己需要定制，具体的请参见官方文档SimpleHTTPServer，或者直接看源码。我拷贝一段，方便参考：
```python
import http.server
import socketserver

PORT = 8000
Handler = http.server.SimpleHTTPRequestHandler
httpd = socketserver.TCPServer(("", PORT), Handler)
print("serving at port %s"%PORT)
httpd.serve_forever()
```
## FTP服务器
Python没有内置一个直接可以用的FTP服务器，所以需要第三方组件的支持，我找到的这个组件叫pyftpdlib，首先安装：
```
pip install pyftpdlib
```
安装完后，和HTTP服器类似，执行以下命令就可以启动一个FTP服务器了：
```
python -m pyftpdlib -p 21
```
后面的21端口依然是可选的，不填会随机一个，被占用的端口将跳过。  
在浏览器敲入本机地址：`ftp://localhost:21`这时候，是匿名访问，也就是用户名是anonymous，密码为空，如果想要控制访问权限，你需要自己定制服务器，具体的可以参看pyftpdlib Tutorial，我这里拷贝过来一段作为介绍：
```python
from pyftpdlib.authorizers import DummyAuthorizer
from pyftpdlib.handlers import FTPHandler
from pyftpdlib.servers import FTPServer
import os

def main():
    # 实例化DummyAuthorizer来创建ftp用户
    authorizer = DummyAuthorizer()

    # 参数：用户名，密码，目录，权限
    authorizer.add_user('user', '12345', '.', perm='elradfmwM')
    # 匿名登录
    authorizer.add_anonymous(os.getcwd())

    # 实例化FTP处理程序类
    handler = FTPHandler
    handler.authorizer = authorizer

    # 定义客户端连接时返回的字符串
    handler.banner = "pyftpdlib based ftpd ready."

    # 指定伪装地址和要使用的端口范围
    # 被动连接。 如果您在NAT后面，请退出
    #handler.masquerade_address = '151.25.42.11'
    #handler.passive_ports = range(60000, 65535)

    # 实例化FTP服务器类并侦听0.0.0.0:2121
    address = ('0.0.0.0', 21)
    server = FTPServer(address, handler)

    # 设置连接限制
    #server.max_cons = 256
    #server.max_cons_per_ip = 5

    # 启动ftp服务器
    server.serve_forever()

if __name__ == '__main__':
    main()
```
只看代码应该基本知道该怎么用了，add_user显然是添加用户，21是指定端口，当然也可以随机，还有最大连接数max_cons，每个ip最大连接限制，更多的接口建议直接看docstrings。