---
title: 如何使用Docker部署Flask应用
date: 2020-07-08 09:58:09
categories: 
    - [程序开发,Python]
tags: 
    - python
    - flask
    - docker
---
Docker部署Flask应用

<!-- more -->
## 创建应用
创建一个文件夹testflask
```
mkdir testflask
```
创建一个Flask应用 testflask/app.py，这是项目的启动文件
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```
## 使用Gunicorn+Gevent运行Flask应用
Flask应用是一个符合WSGI规范的Python应用，不能独立运行（类似app.run的方式仅适合开发模式），需要依赖其他的组件提供服务器功能。在这里，我们选择Gunicorn+Gevent的组合。
在根目录下新建Gunicorn配置文件 testflask/gunicorn.conf.py
```
workers = 5    # 定义同时开启的处理请求的进程数量，根据网站流量适当调整
worker_class = "gevent"   # 采用gevent库，支持异步处理请求，提高吞吐量
bind = "0.0.0.0:8888"    # 监听IP放宽，以便于Docker之间、Docker和宿主机之间的通信
```
在本地测试是否能正确运行
```
pip install flask gunicorn gevent
gunicorn start:app -c gunicorn.conf.py
```

## Docker运行Flask应用
首先我们需要为该应用创建一个testflask/requirements.txt文件，以便容器里面python环境的安装
```
gunicorn
gevent
flask
```

在安装 python 应用依赖的三方包时，可以直接用如下命令执行
```
pip install -r requirements.txt
```

然后我们还要创建一个 testflask/Dockerfile 文件，以便Docker镜像的构建
```
FROM python:3.6
WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

COPY . .

CMD ["gunicorn", "app:app", "-c", "./gunicorn.conf.py"]
```

其中，第一行是python版本，第二行 WORKDIR 后面写的是要部署到服务器上的路径，最后一行里面的 app 是我们上面写的 python 启动文件名，第二个app 是启动文件里面要启动的应用名（变量名）

完成这两个文件的创建之后，执行如下命令，就可以开始构建Docker镜像
```
sudo docker build -t 'testflask' .
```
这个过程需要一点时间，因为它有几百兆。 构建完成之后，通过如下命令查看镜像列表，可以发现 testflask 显示在其中
```
sudo docker images
```

临时运行docker镜像
```
sudo docker run -it --rm -p 8888:8888 testflask
```
可以看到Docker镜像成功地运行起来了，并处于阻塞状态。这时，我们打开浏览器，输入127.0.0.1:8888，可以看到返回的网页中展示内容Hello World!

生产环境运行(以daemon方式运行)
```
sudo docker run -d -p 8888:8888 --name test-flask-1 testflask
```
