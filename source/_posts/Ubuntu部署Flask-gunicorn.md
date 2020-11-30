---
title: Ubuntu部署Flask+gunicorn
date: 2020-04-15 13:56:15
categories: 
    - [程序开发,Python]
    - [程序开发,Linux,Ubuntu]
tags: 
    - python
    - flask
    - gunicorn
    - ubuntu
---
Gunicorn是一个unix上被广泛使用的高性能的Python WSGI UNIX HTTP Server。
和大多数的web框架兼容，并具有实现简单，轻量级，高性能等特点。
![gunicorn结构图](Ubuntu部署Flask-gunicorn/12145841_UgME.jpg)

<!-- more -->
## 安装gunicorn
安装pip版本
```bash
pip3 install gunicorn
```
或者安装ubuntu版本
```
sudo apt-get install gunicorn
```

## flask示例
```python
from flask import Flask

app = Flask(__name__)

@app.route('/demo', methods=['GET'])
def demo():
    return "gunicorn and flask demo."
```
gunicorn运行flask app
```bash
gunicorn -w 4 -b 127.0.0.1:5001 manage:app
```
命令参数详解
```bash
  --version             # 显示程序的版本号并退出
  -h, --help            # 显示此帮助消息并退出
  -c FILE, --config=FILE
                        # Gunicorn配置文件的路径。 [None]
  --debug               # 在服务器中打开调试。 [False]
  --spew                # 安装一个跟踪函数，该函数将抛出服务器执行的每一行。 [False]
  --access-logfile=FILE
                        # 要写入的访问日志文件。 [None]
  --access-logformat=STRING
                        # 访问日志格式。 [%(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s"]
  --error-logfile=FILE, --log-file=FILE
                        # 要写入的错误日志文件。 [-]
  --log-level=LEVEL     # 错误日志输出的等级。 [info]
  --logger-class=STRING
                        # 要用在gunicorn中记录事件的日志记录器。 [simple]
  -n STRING, --name=STRING
                        # 与setproctitle一起用于进程命名的基础。 [None]
  --preload             # 在worker进程被复制（派生）之前载入应用的代码。通过预加载应用，可以节省内存资源和提高服务启动时间。当然，如果你将应用加载进worker进程这个动作延后，那么重启worker将会容易很多。
  --reload              # 更改代码的时候重启workers， 只建议在开发过程中开启。文档推荐下载inotify这个包来作为重载引擎。
  --reload_engine       # 选择重载的引擎，支持的有三种，分别是auto，poll，inotify（需要单独安装） 
  -D, --daemon          # 以守护进程形式来运行Gunicorn进程。其实就是将这个服务放到后台去运行。 [False]
  -p FILE, --pid=FILE   # PID文件使用的文件名。 [None]
  -u USER, --user=USER  # 将工作进程切换为以该用户身份运行。[1000]
  -g GROUP, --group=GROUP
                        # 切换工作进程以该组身份运行。 [1000]
  -m INT, --umask=INT   # Gunicorn编写的文件上文件模式的位掩码。 [0]
  -b ADDRESS, --bind=ADDRESS
                        # 要绑定的套接字。 [127.0.0.1:8000]
  --backlog=INT         # 最大挂起连接数。 [2048]
  -w INT, --workers=INT
                        # 处理请求的工作进程数。 [1]
  -k STRING, --worker-class=STRING
                        # 使用的工作类型。 [sync]
  --worker-connections=INT
                        # 并发客户端的最大数量。 [1000]
  --max-requests=INT    # 工作进程在重新启动之前将处理的最大请求数。 [0]
  -t INT, --timeout=INT
                        # 超过这个时间的工作进程被杀死并重新启动。 [30]
  --keep-alive=INT      # 等待“保持活动”连接上的请求的秒数。 [2]
```
## 配置supervisor
在production环境，起停和状态的监控最好用supervisior之类的监控工具，然后在gunicorn的前端放置一个http proxy server, 譬如nginx。

用apt安装supervisor
```
sudo apt-get install supervisor
```
生成默认配置文件
```bash
echo_supervisord_config > /etc/supervisor/supervisord.conf
```
加入监控程序的配置
```conf
[program:gunicorn_demo]
directory=/var/www/gunicorn_demo/
command=gunicorn --preload -c gunicorn_demo.py gunicorn_demo:app ; 程序启动命令
autostart=true       ; 在supervisord启动的时候也自动启动
startsecs=10         ; 启动10秒后没有异常退出，就表示进程正常启动了，默认为1秒
autorestart=true     ; 程序退出后自动重启,可选值：[unexpected,true,false]，默认为unexpected，表示进程意外杀死后才重启
startretries=3       ; 启动失败自动重试次数，默认是3
user=root          ; 用哪个用户启动进程，默认是root
priority=999         ; 进程启动优先级，默认999，值小的优先启动
redirect_stderr=true ; 把stderr重定向到stdout，默认false
stdout_logfile_maxbytes=100MB  ; stdout 日志文件大小，默认50MB
stdout_logfile_backups = 20   ; stdout 日志文件备份数，默认是10
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile=/var/log/supervisor_logs/axf.out
stopasgroup=false     ;默认为false,进程被杀死时，是否向这个进程组发送stop信号，包括子进程
killasgroup=false     ;默认为false，向进程组发送kill信号，包括子进程
```

supervisor常用命令
```bash
sudo supervisorctl status        //查看所有进程的状态
sudo supervisorctl stop es       //停止es
sudo supervisorctl start es      //启动es
sudo supervisorctl restart es    //重启es
sudo supervisorctl update        //配置文件修改后使用该命令加载新的配置
sudo supervisorctl reload        //重新启动配置中的所有程序
```
把es换成all可以管理配置中的所有进程。直接输入supervisorctl进入supervisorctl的shell交互界面，此时上面的命令不带supervisorctl可直接使用。  
如果启动问题,先去看看log log的位置 stdout_logfile=/var/log/supervisor_logs/axf.out

## 配置nginx
为什么有了gunicorn为什么还需要nginx？因为nginx具备优秀的静态内容处理能力，然后将动态内容转发给gunicorn服务器，这样可以达到很好的客户端响应。

安装nginx
```
sudo apt-get install nginx
```
mynginx.conf是需要自己创建的一个nginx配置文件，此文件放在项目文件夹中，配置内容如下
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

刚建立的配置文件使用符号链接到Nginx配置文件文件夹中
```bash
sudo ln -s /var/www/iclinicAI/mynginx.conf /etc/nginx/conf.d/
sudo /etc/init.d/nginx restart # 重启nginx
```
## gunicorn配置
```bash
# gunicorn.conf.py
# 监听地址和端口。
bind = "127.0.0.1:5001"

# worker进程的数量。建议值2-4 x $(NUM_CORES)， 缺省为1。
workers = 2

# worker进程的工作方式。 有 sync, eventlet, gevent, tornado, gthread, 缺省值sync。
# 这里工作模式协程
worker_class = 'gevent'

# 工作进程中线程的数量。建议值2-4 x $(NUM_CORES)， 缺省值1。
# 此配置只适用于gthread 进程工作方式， 因为gevent这种使用的是协程工作方式。
# threads = 2

# worker重启之前处理的最大requests数， 缺省值为0表示自动重启disabled。主要是防止内存泄露。
max_requests = 50000

# 抖动参数，防止worker全部同时重启。
max_requests_jitter = 2

timeout = 70

graceful_timeout = 30

limit_request_line = 8190

limit_request_fields = 200

limit_request_fields_size = 8190

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

## gunicorn 配置详解

gunicorn 配置项可以通过 gunicorn 的启动命令行中设定，也可以通过配置文件指定。强烈建议使用一个配置文件。

配置项如下：

### server socket

*   bind  
    监听地址和端口。
    
*   backlog  
    服务器中在 pending 状态的最大连接数，即 client 处于 waiting 的数目。超过这个数目， client 连接会得到一个 error。  
    建议值 64-2048。
    

### worker 进程

*   workers  
    worker 进程的数量。建议值 2-4 x $(NUM_CORES)， 缺省为 1。
    
*   worker_class  
    worker 进程的工作方式。 有 sync, eventlet, gevent, tornado, gthread, 缺省值 sync。
    
*   threads  
    工作进程中线程的数量。建议值 2-4 x $(NUM_CORES)， 缺省值 1。  
    此配置只适用于 gthread 进程工作方式， 因为 gevent 这种使用的是协程工作方式。
    
*   worker_connections  
    客户端最大同时连接数。只适用于 eventlet， gevent 工作方式。
    
*   max_requests  
    worker 重启之前处理的最大 requests 数， 缺省值为 0 表示自动重启 disabled。主要是防止内存泄露。
    
*   max_requests_jitter  
    抖动参数，防止 worker 全部同时重启。
    
*   timeout  
    通常设为 30。
    
*   graceful_timeout  
    接收到 restart 信号后，worker 可以在 graceful_timeout 时间内，继续处理完当前 requests。
    
*   keepalive  
    server 端保持连接时间。
    

### security

*   limit_request_line  
    http request line 最大字节数。值范围 0-8190， 0 表示无限制。
    
*   limit_request_field  
    http request 中 header 字段数的最大值。缺省为 100，最大 32768。
    
*   limit_request_field_size  
    http request header 字段最大字节数。0 表示无限制。
    

### 调试

*   reload  
    当代码有修改时，自动重启 workers。适用于开发环境。
    
*   reload_extra_files  
    扩展 reload 配置，增加 templates，configurations 等文件修改监控。
    
*   spew  
    跟踪程序执行的每一行。
    
*   check_config  
    检查配置。
    

### server 机制

*   sendfile  
    系统底层拷贝数据方式，提供 performance。
    
*   chdir  
    在 app 加载之前，进入到此目录。
    
*   daemon  
    应用是否以 daemon 方式运行。
    
*   raw_env  
    key=value, 传递环境参数。
    
*   pidfile  
    pid 存储文件路径。
    
*   worker_tmp_dir  
    临时工作目录。
    
*   user  
    指定 worker 进程的运行用户名。
    
*   group  
    指定 worker 进程运行用户所在组。
    
*   umask  
    gunicorn 创建文件的缺省权限。
    
*   pythonpath  
    附加到 python path 的目录列表。
    

### 日志

*   accesslog  
    访问日志文件路径。
    
*   access_log_format  
    日志格式。 例如 %(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s" 。
    
*   errorlog  
    错误日志路径。
    
*   loglever  
    日志级别。debug, info, warning, error, critical.
    
*   capture_output  
    重定向 stdout/stderr 到 error log file。
    
*   logger_class  
    日志实现类。缺省 gunicorn.glogging.Logger 。
    
*   logconfig  
    日志配置文件。同 python 标准日志模块 logging 的配置。
    

### 进程名

*   proc_name  
    设置进程名 (setproctitle)，在 ps，top 等命令中会看到. 缺省值为 default_proc_name 配置。

### server 钩子

*   on_starting
*   on_reload
*   when_ready
*   pre_fork
*   post_fork
*   post_worker_init
*   worker_init
*   worker_abort
*   pre_exec
*   pre_request
*   post_request
*   child_exit
*   worker-exit
*   nworkers_changed
*   on_exit
