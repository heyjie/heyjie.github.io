---
title: Hexo博客搭建
date: 2020-04-15 14:19:06
categories: 
    - 其他
    - Hexo
tags: 
    - hexo
---
## 新建仓库
github 新建一个仓库，仓库名必须为 <user-name>.github.io 格式，其中 <user-name> 是你 github 的昵称

## 全局安装hexo
npm install hexo-cli -g

## 初始化项目
hexo init，就会自动构建一个 hexo 项目，紧接着执行 hexo s，浏览器访问 localhost:4000 就可以看到效果啦

<!-- more -->
## 部署到github
在项目根目录下找到 _congif.yml，找到 deploy 字段并填写完整
```
deploy:
  type: git
  repo: <你的仓库地址> # https://github.com/YouName/YouName.github.io
  branch: master
```

我们需要额外的一个工具来帮助我们推到仓库上，那就是 hexo-deployer-git
```
npm install hexo-deployer-git --save
```

执行下面两个命令，就可以把项目自动部署到 github
```
hexo clean
hexo deploy
```

浏览器访问：https://YouName.github.io/ 即可看到效果。

## 创建新文章
```
// [layout] 为布局，可选项为 `post`、`page`、`draft`，这将决定文章所在文件路径。
// <title> 为文章标题
hexo new [layout] <title>
```

## 常用Hexo命令
+ 初始化目录：`hexo init [folder]`
+ 新建文章：`hexo new [layout] <title>`或`hexo n [layout] <title>`
  + 新建草稿：`hexo new draft <title>`
+ 将草稿发布为正式文章：`hexo publish <title>`
+ 生成静态文件：`hexo generate`或`hexo g`
  + 监听文件变化：`hexo g --watch`或`hexo g -w`
+ 部署：`hexo deploy`或`hexo d`
  + 先生成后部署：`hexo d -g`
+ 启动本地服务器（服务器会监听文件变化并自动更新）：`hexo server`或`hexo s`
  + 启动调试：`hexo s --debug`
  + 预览草稿：`hexo s --draft`
+ 清除缓存：`hexo clean`

## 重新部署
在更换电脑后，要把博客文件夹移动到新电脑,执行两个命令
安装hexo
```
npm install hexo-cli -g
```
安装模块，不要用hexo init初始化，部分文件已经拷贝生成，如果不慎使用，则站点配置文件_config.yml会被初始化为默认值
```
npm install --registry=https://registry.npm.taobao.org
```