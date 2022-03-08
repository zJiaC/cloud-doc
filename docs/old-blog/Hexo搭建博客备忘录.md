---
title: Hexo搭建博客备忘录
date: 2016-03-16 20:59:40
tags: [Hexo,备忘录]
categories: [Hexo]
description: 收集和整理如何利用Hexo搭建博客
---
# 下面将简单的整理一下关于：

1. git常用的命令及其如何生成ssh key
2. hexo相关
3. npm相关

<!--more-->

# 一、git常用的命令及其如何生成ssh key

## (1)相关链接
> [如何生成ssh](http://wiki.jikexueyuan.com/project/git-tutorial/remote-repository.html)

> [GIT简单用法指南](http://rogerdudler.github.io/git-guide/index.zh.html)

## (2)个人常用git命令总结
```
 git init
 git checkout -b branch//创建新的分支,branch为所起分支名
 git add .
 git commit -m "提交说明"
 git remote add origin git@... github//仓库地址
 git push -u origin master //所要提交的支干名
 git clone -b branch git@.... //直接克隆分支
 ```

## (3)vscode使用git
* 需要在环境path添加 例如D:\*\Git\cmd

# 二、hexo相关

## (1)相关链接
> [如何搭建hexo](http://www.jianshu.com/p/465830080ea9)
* hexo3: 运行hexo s需要 -- npm install hexo-server --save
* 且需要运行 npm install


# 三、npm相关
* 起因：
	1. 因为国内有墙的原因，所以大部分时候安装某些包的时候并不是那么的容易，
	2. 常见的是利用vpn翻墙出去，修改dns和host，在或者修改npm的源。
	3. 而nrm就是用来切换源的。
* 安装
```
 npm install nrm -g
```
* 使用
```
nrm ls //查看可以使用的源
nrm test //测试源的连接时间
nrm use "源的名字" //切换到指定的源
```
* 使用
gnvm 可以用于管理node版本

# 背景音乐:
<!--<embed src="http://music.163.com/style/swf/widget.swf?sid=27808044&type=2&auto=1&width=320&height=66" width="340" height="86"  allowNetworking="all"></embed>-->
<!--<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=430 height=86 src="http://ksapuw.changba.com/userdata/userwork/29/869052029.mp3"></iframe>-->
<audio autoplay loop src="http://ksapuw.changba.com/userdata/userwork/29/869052029.mp3" controls="controls">
Your browser does not support the audio tag.
</audio>

