---
title: Ubuntu18安装Spark
date: 2020-07-21 11:17:21
tags:
---
Spark运行架构包括集群资源管理器（Cluster Manager）、运行作业任务的工作节点（Worker Node）、每个应用的任务控制节点（Driver）和每个工作节点上负责具体任务的执行进程（Executor）。其中，集群资源管理器可以是Spark自带的资源管理器，也可以是YARN或Mesos等资源管理框架。
![](Ubuntu18安装Spark/Spark运行架构.jpg)

## Scala的安装
因为Spark本身是以Scala开发的，所以必须先安装Scala.
2.4.x的spark  ->选择2.12.x的scala
## 