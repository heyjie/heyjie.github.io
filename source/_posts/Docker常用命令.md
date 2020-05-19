---
title: Docker常用命令
date: 2020-05-09 20:09:22
tags:
---

```bash
sudo service docker start  # 启动docker
sudo service docker stop  # 停止docker
sudo service docker restart  # 重启docker
sudo systemctl enable docker  # 开机启动docker
# 列出镜像
docker image ls
# 拉取镜像
docker image pull library/hello-world
# 删除镜像
docker image rm 镜像id/镜像ID
# 创建容器
docker run [选项参数] 镜像名 [命令]
# 停止一个已经在运行的容器
docker container stop 容器名或容器id
# 启动一个已经停止的容器
docker container start 容器名或容器id
# kill掉一个已经在运行的容器
docker container kill 容器名或容器id
# 删除容器
docker container rm 容器名或容器id
```
启动容器
```bash
docker start <容器id或name>
```
使容器自启动
```bash
docker update --restart=always <容器id或name>
```
--restart具体参数值详细信息：
    no -  容器退出时，不重启容器；
    on-failure - 只有在非0状态退出时才从新启动容器；
    always - 无论退出状态是如何，都重启容器；


