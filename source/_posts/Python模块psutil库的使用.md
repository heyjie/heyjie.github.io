---
title: Python模块psutil库的使用
date: 2020-07-17 16:44:47
categories: 
    - 程序开发
    - Python
tags:
    - python
    - psutil
---
psutil (python system and process utilities) 是一个跨平台的第三方库，能够轻松实现获取系统运行的进程和系统利用率（包扩CPU、内存、磁盘、网络等）信息。它主要用于系统监控、分析、限制系统资源和进程的管理。它实现了同等命令行工具提供的功能，如ps、top、lsof、netstat、ifconfig、who、df、kill、free、nice、ionice、iostat、iotop、uptime、pidof、tty、taskset、pmap

<!-- more -->
目前支持的操作系统包括：Linux,Windows,OSX,FreeBSD,OpenBSD,NetBSD,Sun Solaris,AIX

## 一、安装psutil库
```
pip install psutil
```

## 二、功能函数
### 1.1 CPU相关
|  函数   | 描述  |
|  ----  | ----  |
| psutil.cpu_count()  | 单元cpu_count(,[logical]):默认返回逻辑CPU的个数,当设置logical的参数为False时，返回物理CPU的个数 |
| psutil.cpu_percent()  | cpu_percent(,[percpu],[interval])：返回CPU的利用率,percpu为True时显示所有物理核心的利用率,interval不为0时,则阻塞时显示interval执行的时间内的平均利用率 |
| psutil.cpu_times()  | cpu_times(,[percpu])：以命名元组(namedtuple)的形式返回cpu的时间花费,percpu=True表示获取每个CPU的时间花费 |
| psutil.cpu_times_percent()  | cpu_times_percent(,[percpu])：功能和cpu_times大致相同，看字面意思就能知道，该函数返回的是耗时比例 |
| psutil.cpu_stats()  | cpu_stats()以命名元组的形式返回CPU的统计信息，包括上下文切换，中断，软中断和系统调用次数 |
| psutil.cpu_freq()  | cpu_freq([percpu])：返回cpu频率 |

### 1.2 内存相关
|  函数   | 描述  |
|  ----  | ----  |
| virtual_memory()  | 以命名元组的形式返回内存使用情况，包括总内存，可用内存，内存利用率，buffer和cache等。单位为字节 |
| swap_memory()  | 以命名元组的形式返回swap/memory使用情况，包含swap中页的换入和换出 |

### 1.3 磁盘相关
|  函数   | 描述  |
|  ----  | ----  |
| psutil.disk_io_counters() | disk_io_counters([perdisk])：以命名元组的形式返回磁盘io统计信息(汇总的)，包括读、写的次数，读、写的字节数等。当perdisk的值为True，则分别列出单个磁盘的统计信息(字典：key为磁盘名称，value为统计的namedtuple) |
| psutil.disk_partitions() | disk_partitions([all=False])：以命名元组的形式返回所有已挂载的磁盘，包含磁盘名称，挂载点，文件系统类型等信息。当all等于True时，返回包含/proc等特殊文件系统的挂载信息 |
| psutil.disk_usage() | disk_usage(path)：以命名元组的形式返回path所在磁盘的使用情况，包括磁盘的容量、已经使用的磁盘容量、磁盘的空间利用率等 |

### 1.4 网络相关
|  函数   | 描述  |
|  ----  | ----  |
| psutil.net_io_counter([pernic])  | 以命名元组的形式返回当前系统中每块网卡的网络io统计信息，包括收发字节数，收发包的数量、出错的情况和删包情况。当pernic为True时，则列出所有网卡的统计信息 |
| psutil.net_connections([kind])  | 以列表的形式返回每个网络连接的详细信息(namedtuple)。命名元组包含fd, family, type, laddr, raddr, status, pid等信息。kind表示过滤的连接类型，支持的值如下：(默认为inet) |
| psutil.net_if_addrs()  | 以字典的形式返回网卡的配置信息，包括IP地址和mac地址、子网掩码和广播地址 |
| psutil.net_if_stats()  | 返回网卡的详细信息，包括是否启动、通信类型、传输速度与mtu |
| psutil.users()  | 以命名元组的方式返回当前登陆用户的信息，包括用户名，登陆时间，终端，与主机信息 |
| psutil.boot_time()  | 以时间戳的形式返回系统的启动时间 |

### 1.5 进程管理
|  函数   | 描述  |
|  ----  | ----  |
| psutil.pids()  | 	以列表的形式返回当前正在运行的进程 |
| psutil.pid_exists(1)  | 判断给点定的pid是否存在 |
| psutil.process_iter()  | 迭代当前正在运行的进程，返回的是每个进程的Process对象 |
| psutil.Process()  | 对进程进行封装，可以使用该类的方法获取进行的详细信息，或者给进程发送信号 |

进程相关信息的方法
* name：获取进程的名称
* cmdline：获取启动进程的命令行参数
* create_time：获取进程的创建时间(时间戳格式)
* num_fds：进程打开的文件个数
* num_threads：进程的子进程个数
* is_running：判断进程是否正在运行
* send_signal：给进程发送信号，类似与os.kill等
* kill：发送SIGKILL信号结束进程
* terminate：发送SIGTEAM信号结束进程

## 三、使用实例
以下代码对系统信息进行监控，包括内存和cpu利用率，网速和监控时间
```python
import psutil
# 获取总内存大小
memoryTotal = round(psutil.virtual_memory().total/(1024.0*1024.0*1024.0), 2)
while True :
    # 设定30秒记录一次
    time.sleep(30)
    # CPU使用率
    cpuUsed = psutil.cpu_percent(1)
    # 内存信息 
    memoryInfo = psutil.virtual_memory()
    # 内存使用大小
    memoryUsedSize = round(memoryInfo.used / (1024.0*1024.0*1024.0),2)
    # 内存使用率
    memoryUsed = round(memoryUsedSize/memoryTotal,2)*100
    # 或者直接查看使用率 psutil.virtual_memory().percent
    # 网络io统计信息
    net = psutil.net_io_counters()
    # 接收字节
    bytesRcvd = (net.bytes_recv / 1024)
    # 发送字节
    bytesSent = (net.bytes_sent / 1024)
    # 过一秒再获取流量信息，算出每秒网速
    time.sleep(1)
    net = psutil.net_io_counters()
    realTimeRcvd = round(((net.bytes_recv / 1024) - bytesRcvd),2)
    realTimeSent = round(((net.bytes_sent / 1024) - bytesSent),2)
    # 获取系统时间
    tim = time.strftime('%H:%M:%S',time.localtime())
    realTimeInfo = {
    'cpu':{'cpuUsed':cpuUsed},
    'memory':{'memoryUsed':memoryUsed},
    'net':{'rcvd':realTimeRcvd,'send':realTimeSent}
    'time':tim
    }
    print(realTimeInfo)
```

输出情况示例
```json
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 6.0}, 'net': {'send': 2.89, 'rcvd': 6.52}, 'time': '17:49:58'}
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 3.8}, 'net': {'send': 1.58, 'rcvd': 0.83}, 'time': '17:50:30'}
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 10.3}, 'net': {'send': 1.71, 'rcvd': 0.12}, 'time': '17:51:02'}
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 5.1}, 'net': {'send': 0.6, 'rcvd': 0.0}, 'time': '17:51:34'}
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 4.4}, 'net': {'send': 1.43, 'rcvd': 4.8}, 'time': '17:52:06'}
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 4.7}, 'net': {'send': 0.92, 'rcvd': 1.02}, 'time': '17:52:38'}
{'memory': {'memoryUsed': 37.0}, 'cpu': {'cpuUsed': 10.4}, 'net': {'send': 0.75, 'rcvd': 0.06}, 'time': '17:53:10'}
```