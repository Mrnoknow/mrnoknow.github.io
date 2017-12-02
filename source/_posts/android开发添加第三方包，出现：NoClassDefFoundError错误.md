---
title: android开发添加第三方包，出现：NoClassDefFoundError错误
date: 2015-12-03 17:43:52
tags: other
categories: [Android]
---
在上篇中，我们提到以为我的源生包中没有Jsoup的类，所以需要添加Jsoup包，
但是出现：NoClassDefFoundError错误

## 为什么会发生这种情况了？
``` bash
是因为你导入的包和你的源生包不是一个版本的，比如：我用的1.6.0，而导入的是1.6.2
所以就出现了以上的情况。
```

## 解决(如下图)
![一步一歩来](/images/android开发添加第三方包，出现：NoClassDefFoundError错误/1.png)
![创建lib文件夹](/images/android开发添加第三方包，出现：NoClassDefFoundError错误/2.png)
![将需要添加的jar添加到lib中](/images/android开发添加第三方包，出现：NoClassDefFoundError错误/3.png)
![如图设置为Use a Source...](/images/android开发添加第三方包，出现：NoClassDefFoundError错误/4.png)
![最后别忘Buid Path将jar添加](/images/android开发添加第三方包，出现：NoClassDefFoundError错误/5.png)


