---
title: Ubuntu部署Flask+gunicorn
date: 2020-04-15 13:56:15
categories: 
    - 环境搭建
tags: 
    - python
    - flask
	- ubuntu
---
## 安装gunicorn
```bash
pip3 install gunicorn
```
## 配置`gunicorn.conf.py`
```bash
# 普通模式启动

# gunicorn -w 4 -b 127.0.0.1:5001 manage:app

# 后台模式启动

# gunicorn -w 4 -D -b 127.0.0.1:5001 manage:app

# 配置文件模式启动

# gunicorn --preload -c gunicorn.conf.py manage:app

bind = "127.0.0.1:5001"

workers = 2

threads = 2



max_requests = 50000

max_requests_jitter = 2

timeout = 70

graceful_timeout = 30

limit_request_line = 8190

limit_request_fields = 200

limit_request_fields_size = 8190



# 工作模式协程

worker_class = 'gevent'

# 设置最大并发量

worker_connections = 2000

# 设置gunicorn访问日志格式，错误日志无法设置

access_log_format = '%(t)s %(p)s %(h)s "%(r)s" %(s)s %(L)s %(b)s %(f)s" "%(a)s"'

# 设置进程文件目录

pidfile = '/var/www/IASDataFlask/run/gunicorn.pid'

# 设置访问日志和错误信息日志路径

accesslog = '/var/www/IASDataFlask/logs/gunicorn_acess.log'

errorlog = '/var/www/IASDataFlask/logs/gunicorn_error.log'

# 设置日志记录水平

loglevel = 'warning'


```
## 配置nginx
```nginx
server {    
    listen 9001;
    server_name 203.176.71.157 localhost; # 这是HOST机器的外部域名，用地址也行    
    access_log /var/www/iclinicAI/logs/access.log;
    error_log /var/www/iclinicAI/logs/error.log;
    client_max_body_size 100M;
    location / {
        proxy_pass http://127.0.0.1:5001; # 这里是指向 gunicorn host 的服务地址
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_read_timeout 60;
        }  
}
```