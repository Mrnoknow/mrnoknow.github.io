---
title: github+hexo搭建博客步骤
date: 2015-12-01 12:05:26
categories: [git,Hexo]
tags: other
---
此篇写一下我搭建博客时的步骤与所犯的一些错误

## 所需原料
1.github
2.node.js

## 基本步骤
---
安装和配置好github与node.js

### 1.打开git bash运行
``` bash
npm install -g hexo
```

### 2.在电脑中创建一个文件夹(eg:D:\git\Hexo),进入到文件夹中运行
``` bash 
npm install
```
本地的博客就搭建完成，运行heox sever检测，出现
``` bash
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```
就表示成功

### 3.运行
``` bash
hexo generate
```
生成静态网页

### 4.部署到github前的准备工作

1.首先在github创建一个与用户名相同的repository
``` bash
格式为:mrnoknow.github.io  mrnoknow 为自己的用户名
```
2.找到Hexo文件夹下的_config.yml文件,找到文件里的
``` bash
deploy:
  type:
```
改为
``` bash
deploy:
  type: git
  repository: git@github.com:Mrnoknow/mrnoknow.github.io.git   (这个为自己repository的ssh连接)
  branch: master
  (特别注意空格问题(eg:type: 这里有一个空格后面再加git))
```

### 5.部署到github,只要依次运行一下命令
``` bash
npm clean
npm generate
hexo deploy
```
当运行hexo deploy 出现错误后，运行下面的命令
``` bash
npm install hexo-server --save
```
然后重新执行，就ok

### 我出现的问题就是版本问题，建议换低版本的git

