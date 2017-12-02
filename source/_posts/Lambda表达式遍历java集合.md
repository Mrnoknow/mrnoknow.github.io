title: Lambda表达式遍历java集合
date: 2016-04-09 22:38:57
categories: JAVA
tags: 读书笔记
---
*以下为java 8才能实现*

## Iterable接口实现
Iterable（不是Iterator，这里需要说:**Iterable接口是Collection接口的父接口**）
新增了一个forEach（Consumer action）默认方法，Collection直接调用改方法而该方法需要函数式
接口(是不是函数式编程，一脸懵逼~可能不是)。
需要说的是:**Consumer的accept（T t）方法（是该接口的唯一方法），因为Consumer事函数式接口。**
### 代码如下
``` 
noknow.forEach(obj->System.out.println("Your name:"+obj));
```

## Iterator（迭代器）接口实现
Iterator新增forEachRemaining（Consumer action）方法
此方法与Iterable接口不同的是集合必须获取Iterator迭代器
### 代码如下
```
Iterator mr=noknow.iterator();
mr.forEachRemaining(obj->System.out.println("Your name:"+obj));
```

**课外扩展：Iterator为快速失败(fail-fast)机制，就是如果有多线程对同一个集合进行操作，如果有
一个线程进行了修改操作，程序就会发生异常，而不会使用修改的值，以此避免共享资源潜在的危险。**