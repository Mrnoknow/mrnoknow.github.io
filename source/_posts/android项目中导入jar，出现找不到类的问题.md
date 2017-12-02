---
title: android项目中导入jar，出现找不到类的问题
date: 2015-12-02 15:22:56
categories: [Android]
tags: other
---
因为我在写android时，我想用 Jsoup 来解析html，但是我发现找不到我的要用的包，
所以我就导入了一个。

谨记，此种方法导入的第3方包与版本要与自己的一样，如若不同:[请点击](http://mrnoknow.github.io/2015/12/03/android开发添加第三方包，出现：NoClassDefFoundError错误/)

## 问题所在
Buid Path------configure Buid Path------Add External JARs
然后找到路径添加
``` bash
我添加后发现并没有什么卵用，因为我发现我导入的包并没有被编译。
```
## 解决
编译我的所要添加的包，具体步骤如下：
```
进入CMD，进入到对应文件夹，用javac进行编译，因为每个java文件都是相关联的，所以必须一次性全部编译。
```
``` bash
javac *.java (编译当前文件夹下的所有java文件，不包括子文件)
javac examples/*.java(编译子文件夹examples所有的java文件)
和起来就是
javac *.java examples/*.java helper/*.java(注意空格问题)
```
最后将编译的class文件放入jar包中(或者直接放进android自己的包中)，导入完成。
![android自带jar](/images/jar1.png)
![android.jar](/images/jar2.png)
![将编译的文件夹导入](/images/jar3.png)

