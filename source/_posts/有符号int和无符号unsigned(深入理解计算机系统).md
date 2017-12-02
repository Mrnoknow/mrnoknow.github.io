---
title: 有符号int和无符号unsigned(深入理解计算机系统)
date: 2016-03-25 13:10:55
categories: 计算机系统
tags: 
- other
- 读书笔记
---

在计算机中几乎所有的数据都以二进制存储且是以补码形式存储。

## int与unsigned
``` bash
     int i=-1;
     unsigned u=2147483648;
     printf("x=%u=%d",i,i);
     printf("u=%u=%d",u,u);
```
## 输出
``` bash
     x=4294967295=-1
     u=2147483672=-2147483672
```

