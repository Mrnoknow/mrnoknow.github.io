---
title: 在VMware11下安装Mac10.10
date: 2015-12-01 11:02:42
categories: [虚拟机,Mac]
tags: other
---
在安装之前你必须确保你有以下3种东西
1.VMware11
2.mac10.10虚拟镜像
3.unlocker 203(必须有这个在VMware中才能找到mac选项)

## 基本操作步骤
1.安装VMware
2.下载unlocker 203
3.运行unlocker 203中的 ```win-install.cmd```（unlock203\win-install.cmd）
4.运行VMware，添加新的虚拟机
5.选择你下载下来的镜像，继续你会发现有一个Apple Mac OS选项，选择OS X 10.10
6.之后一直默认就可以
7.运行后会提示一个错误，找到你Mac虚拟机安装的位置，找到  OS X 10.10.vmx 这个
  文件然后添加 ```smc.version = ”0“```.
8.ok，运行虚拟机，然后安装。
9.在安装时的第二步骤进入到 ```磁盘工具``` 抹掉盘，进行分区

