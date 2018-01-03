---
title: git——pull不上远程库
date: 2016-03-13 19:38:11
categories: git
tags: other
---
有的时候使用git的时候会出现pull上去的情况，让我们了解一下这里面的一些原因

## 出现的情况
![图  1](/images/git——pull不上远程/1.png)

## 为何会出现
``` bash
出现上图一的情况是因为版本库的不同，需要将2个本地与远程库统一。
```

## 解决方案
``` bash
git pull origin master
```
出现下图
![图  ](/images/git——pull不上远程/2.png)

ok,成功了


