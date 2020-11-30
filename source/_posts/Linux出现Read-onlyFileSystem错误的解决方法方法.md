---
title: Linux出现Read-onlyFileSystem错误的解决方法方法
date: 2020-09-01 14:10:57
categories: 
    - 程序开发
    - Linux
tags: 
    - linux
---
造成这个问题的原因大多数是因为非正常关机后导致文件系统受损引起的，在系统重启之后，
受损分区就会被Linux自动挂载为只读。解决的方法是通过fsck来修复文件系统，然后重启即可。

<!-- more -->

## 解决方法
查看系统分区
```
df -lh
```

修复文件系统
```
fsck -y /dev/sda2
```

## 临时解决
报错read-only file system的原因是你所在的分区只有读权限，没有写权限。
mount为挂载分区命令，重新挂载分区并增加读写权限即为 -rw。
比如提示data目录只读，可通过如下命令，将目标目录临时变更为可读写模式
```
mount -o remount rw /data
```