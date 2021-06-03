---
title: 在Ubuntu16.04使用命令行方式安Matlab
date: 2020-04-15 14:02:43
categories: 
    - [程序开发,Matlab]
    - [程序开发,Linux]
tags: 
    - ubuntu
    - matlab
---
Linux服务器命令行模式安装Matlab

<!-- more -->
## 安装jdk
```bash
java -version                #查看当前java版本，未出现版本信息代表未安装
sudo apt-get install default-jdk  #使用apt-get安装JDK
java -version                         #再次查询Java版本，查看是否安装成功
```

## 安装matlab
创建挂载文件夹，挂载镜像
```bash
sudo mkdir  /data/matlab
sudo mount -o loop R2018b_glnxa64_dvd1.iso /data/matlab
```

创建安装目录
```bash
sudo mkdir -p /data/MATLAB/etc
sudo mkdir -p /data/MATLAB/R2018b
```

拷贝配置文件
```bash
sudo cp /data/matlab/installer_input.txt /data/MATLAB/etc
sudo cp /data/matlab/activate.ini /data/MATLAB/etc
sudo cp license_standalone.lic /data/MATLAB/etc
```

修改文件的读写属性
```bash
chmod +w /data/MATLAB/etc/installer_input.txt
chmod +w /data/MATLAB/etc/activate.ini
```

编辑activate.ini文件，按如下内容设置
```bash
isSilent=true #开启silent模式
activateCommand=activateOffline #设置激活方式, 离线激活 无需联网
licenseFile=/usr/local/MATLAB/etc/license_standalone.lic #license文件位置
```

```bash
sudo /data/matla/install -mode silent -agreeToLicense yes -fileInstallationKey 09806-07443-53955-64350-21751-41297 -destinationFolder /data/MATLAB/R2018b -activationPropertiesFile /data/MATLAB/etc/activate.ini -licensePath /data/MATLAB/etc/license_standalone.lic -outputFile /tmp/mathwork_install.log
```

分别指定：开启无人值守安装，同意协议，序列号，安装目录，激活文件，license文件

激活文件内容：
```bash
isSilent=true #开启silent模式
activateCommand=activateOffline #设置激活方式, 离线激活 无需联网
licenseFile=/usr/local/MATLAB/etc/license_standalone.lic #license文件位置
```

此时不要CTRL+C中止，而是另开一个命令行窗口,将挂载的dvd1.iso推出，然后在原目录下挂载dvd2.iso即可
```bash
sudo umount /media/cdrom
sudo mount -o loop R2018b_glnxa64_dvd2.iso /data/matlab
```

出现`End - Successful`就安装成功

## 破解matlab
替换so文件
```bash
cp ./Crack/R2018b/bin/glnxa64/matlab_startup_plugins/lmgrimpl/* /data/MATLAB/R2018b/bin/glnxa64/matlab_startup_plugins/lmgrimpl
```
破解
```
sudo /data/MATLAB/R2018b/bin/activate_matlab.sh -isSilent -propertiesFile /data/MATLAB/etc/activate.ini
```

## 配置环境变量

```bash
vim ~/.bashrc
export MATLAB_HOME=/home/zh/MATLAB/2018b
export PATH=${MATLAB_HOME}/bin:$PATH
```
保存退出后激活
```
source ~/.bashrc
```