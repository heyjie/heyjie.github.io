---
title: Hexo博客搭建
date: 2020-04-15 14:19:06
tags:
---
## 新建仓库
github 新建一个仓库，仓库名必须为 <user-name>.github.io 格式，其中 <user-name> 是你 github 的昵称

## 全局安装hexo
npm install hexo-cli -g

## 初始化项目
hexo init，就会自动构建一个 hexo 项目，紧接着执行 hexo s，浏览器访问 localhost:4000 就可以看到效果啦

## 部署到github
在项目根目录下找到 _congif.yml，找到 deploy 字段并填写完整
```
deploy:
  type: git
  repo: <你的仓库地址> # https://github.com/YouName/YouName.github.io
  branch: master
```

<!-- more -->
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