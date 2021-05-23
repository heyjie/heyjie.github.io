---
title: GIT的基本用法
date: 2021-05-13 10:02:42
categories: 
    - [程序开发,GIT]
tags: 
    - git
---

## 初始化
```bash
$ git config -global user.name <name> #设置提交者名字
$ git config -global user.email <email> #设置提交者邮箱
$ git config -global core.editor <editor> #设置默认文本编辑器
$ git config -global merge.tool <tool> #设置解决合并冲突时差异分析工具
$ git config -list #检查已有的配置信息
```

## 创建新版本库
```bash
$ git clone <url> #克隆远程版本库        
# 如 git clone git@192.168.9.19:myproject.git  
# git clone http://username:password@这里是ip:xx.git

$ git init #初始化本地版本库
# 初始完之后需要clone 远程版本库下载新的数据
```

## 基本操作
常用操作
```
git status #查看本次的修改、新建、删除等信息(new file:新建文件，modified:修改文件，deleted: 删除的文件)
git pull #拉取代码；
git add . #添加所有即将提交的文件，
git add fileNamePath #添加某个文件
git commit -m '提交的日志' #提交到本地，
git push #提交到git服务器；
```
其他操作
```
git commit -a -m '提交的日志' #添加所有即将提交的文件并提交到本地，git commit -a -m 相当于git add . 和 git commi -' '命令的集合，当你使用git commit -a -m ' '命令时，就会执行上述两个操作
git commit -m <file> # 提交指定文件
git commit -amend # 修改最后一次提交
git commit -C HEAD -a -amend #增补提交（不会产生新的提交历史纪录）
git mv <old> <new> #文件重命名
git rm <file> #删除文件
git rm -cached <file> #停止跟踪文件但不删除
```

## 撤销操作
```
git reset -hard HEAD #撤消工作目录中所有未提交文件的修改内容  比如删除也可以撤销
git checkout HEAD <file1> <file2> #撤消指定的未提交文件的修改内容
git checkout HEAD. #撤消所有文件
git revert <commit> #撤消指定的提交
```
删除恢复的例子
``` 
$ git rm eee
     rm 'eee'     
$ git reset --hard    //也可以加上 HEAD 恢复所有删除的文件
      HEAD is now at 9d1df48 aaaaaaaa
$ git checkout HEAD eee  //这样直接恢复单个文件，reset会将所有删除的文件都恢复
$ git checkout HEAD .      //这样是恢复所有文件
$ git revert    //这个不知道昨用
```

## 查看提交历史
```
git log #查看提交历史                            // 这里查看的是所有文件的日志
git log -p <file> #查看指定文件的提交历史        // 如git log -e filename
git blame <file> #以列表方式查看指定文件的提交历史
gitk #查看当前分支历史纪录
gitk <branch> #查看某分支历史纪录
gitk --all #查看所有分支历史纪录
git branch -v #每个分支最后的提交
git status #查看当前状态
git diff #查看变更内容
```

## 暂存

使用暂存的好处是，提交时不会产生类似

> (Merge branch 'prd_v1.5' of http://...*/bb_ios/bbch**** into prd_v1.5)

的日志，且安全可靠，会尽量避免将错误提交到服务器上面。

git stash暂存（存储在本地，并将项目本次操作还原）
git stash pop 使用上一次暂存，并将这个暂存删除，使用该命令后，如果有冲突，终端会显示，如果有冲突需要先解决冲突（这就避免了冲突提交服务器，将冲突留在本地，然后解决）
git stash list 查看所有的暂存
git stash clear 清空所有的暂存
git stash drop [-q|--quiet] [<stash>] 删除某一个暂存，在中括号里面放置需要删除的暂存ID
git stash apply 使用某个暂存，但是不会删除这个暂存

### 暂存不小心清空，结果里面有需要的代码，也是有找回方法的
git fsck --lost-found 命令找出刚才删除的分支里面的提交对象。
然后使用 git show 命令查看是否正确，如果正确使用git merge命令找回

举个栗子🌰：
git fsck --lost-found
终端显示
```
Checking object directories: 100% (256/256), done.
Checking objects: 100% (109977/109977), done.
dangling commit bb01f8dfaa14ea7960d294304c61c4b401eaf2c6
dangling commit 0203281d5dee10835022ff6cfdcda5050a372762
```
然后查看那个版本
git show bb01f8dfaa14ea7960d294304c61c4b401eaf2c6
结果查看图1

记录中会描述日期和摘要，日期是你git stash的日期，摘要会记录你是在哪一条commit 上进行git stash操作的，找到后将执行 git merge bb01f8dfaa14ea7960d294304c61c4b401eaf2c6

## 分支与标签
```
git branch #显示所有本地分支
git checkout <branch/tagname> #切换到指定分支或标签
git branch <new-branch> #创建新分支
git branch -d <branch> #删除本地分支
git tag #列出所有本地标签
git tag <tagname> #基于最新提交创建标签
git tag -d <tagname> #删除标签
```
创建分支并推送到远程例子
```bash
# 创建并切换到分支branchName
git checkout -b branchName

#推送本地的branchName(冒号前面的)分支到远程origin的branchName(冒号后面的)分支(没有会自动创建)
git push origin branchName:branchName

#在这样的命令下，如果你本地有代码，会自动切换到新的分支上，所以不必担心，你修改很多之后，从新创建分支会出现正常切换分支因为有未提交的代码而创建不成功的情况.
```
发现问题  
如果命令行提示git branch --set-upstream dev origin/branchName则需在终端输入git push -u origin branchName

删除分支
```bash
# 删除本地分支
git branch -d [branchname]
# 删除远程分支
git push origin --delete [branchname]
```

## 合并与衍合
```
$ git merge <branch> #合并指定分支到当前分支
$ git rebase <branch> #衍合指定分支到当前分支
```

合并例子
```bash
#场景如下：
#例如现在有分支master和分支A,要将分支A的代码合并到master
#第一步：切换到分支master，在终端输入命令
git checkout master 

#第二步：合并分支
git merge --no-ff -m 'version:3.0.5_buildL19011720' 

#第三步：如果有冲突解决冲突，解决冲突后add . -> git commit -m '提交原因'
#第四步：
git push
```

## 远程操作
```
git remote -v #查看远程版本库信息
git remote show <remote> #查看指定远程版本库信息
git remote add <remote> <url> #添加远程版本库
git fetch <remote> #从远程库获取代码
git pull <remote> <branch> #下载代码及快速合并
git push <remote> <branch> #上传代码及快速合并
git push <remote> : <branch>/<tagname> #删除远程分支或标签
git push -tags #上传所有标签
```

## 忽略文件
生成git忽略文件.gitignore文件，使用终端进入到你要生成.gitignore的目录，使用命令touch .gitignore,就会生成。

下面是.gitignore的常用规则：
```
/mtk/ 过滤整个文件夹
*.zip过滤所有.zip文件
/mtk/do.c 过滤某个具体文件
这样文件或者文件夹就被过滤了，当然本地库中还有，只是push的时候不会上传。
.gitignore还可以指定要将哪些文件添加到版本管理中：
!*.zip
!/mtk/one.txt
```
如果你将文件加入到了.gitignore文件里面，但是没有说生效，为什么呢？因为之前你已经把这个文件push到过远程仓库了，请使用以下命令
```
git rm --cached `git ls-files -i --exclude-from=.gitignore`
```
经过我几次实验之后发现，上述命令在之前没有创建.gitignore文件但是已经向服务器push过代码的时候好使，但是在已经使用过这个命令后，由于其他分支并没有添加忽略文件，合并分支后，忽略文件失效，那么上述命令可能失效，那么先把本地缓存删除（改变成未track状态），然后再提交，方案如下
```
git rm -r --cached .
git add .
git commit -m '日志'
```
要记得，因为改变了一些东西，所以要从新pod install 然后提交代码，但是要使用git status查看状态，比如已经忽略了Podfile.lock，那么查看时没有Podfile.lock那就是好了。

## 重定向项目地址
第一种方法
```bash
#第一步：cd到项目目录下
#第二步 ：
git remote set-url origin 新项目地址
#例如新项目地址是 www.jianshu.com，示例如下
git remote set-url origin www.jianshu.com
```
第二种方法
```bash
#第一步：cd到项目目录下
#第二步：执行命令
cd .git
#第三步：执行命令
open config
#第四步：将新项目地址替换到配置里reote "origin"下的url的后面
[remote "origin"]
utl = 新项目地址
```

## 常见错误
> Failed to connect to github.com port 443: Operation timed out

如果您使用代理进行网络连接，则必须将代理添加到git config文件中。
您需要通过在终端或命令行中运行此命令来配置Git以使用代理设置。
```
git config --global http.proxy http://proxyuser:proxypwd@proxy.server.com:8080
```
其中proxyuser和proxypwd是您的凭据，proxy.server.com:8080是您连接到的服务器名称和端口。

> Unsupported proxy syntax in '127.0.0.1:8118'
首先查看git的配置列表
```
git config --list
```
可以看到有两个代理配置，http.proxy和https.proxy，就是这两个配置的原因

删除这两项
```
git config --global --unset http.proxy
git config --global --unset https.proxy
```