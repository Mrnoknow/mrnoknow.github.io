title: git命令(因为经常忘记，所以记录下)
date: 2016-04-14 17:03:21
categories: git
tags: other
---

1.git init
2.git remote add origin -ssh
3.git add .
4.git commit -m "First"
5.git push origin master

**在创建密钥时，一定要进入<code>cd /</code>中，然后一直回车就行了，有时会出各种幺蛾子**
* 密钥创建： ssh-keygen -t rsa -C "your_email@example.com"
* 密钥生成在 ~/.ssh 中，有2个id_rsa与id_rsa.pub，将id_rsa.pub中的key值复制道github中ssh key值就行了
* 查看远程仓库：$ git remote -v
* 添加远程仓库：$ git remote add [name] [url]
* 删除远程仓库：$ git remote rm [name]
* 拉取远程仓库：$ git pull [remoteName] [localBranchName]
* 推送远程仓库：$ git push [remoteName] [localBranchName]
* 查看本地分支：$ git branch
* 查看远程分支：$ git branch -r
* 创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
* 切换分支：$ git checkout [name]



<code>remoteName</code> 就是git remote add [name] [url]中的<code>name</code>
origin	git@github.com:Mrnoknow/mrnoknow.github.io.git (fetch)
origin	git@github.com:Mrnoknow/mrnoknow.github.io.git (push)
origin 就是对应的remoteName，也就是remote add [name] 中的name，
localBranchName 就是远程仓库的分支名字