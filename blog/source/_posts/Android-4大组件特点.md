title: Android 4大组件特点
date: 2017-08-02 12:37:43
categories: Android
tags: 读书笔记
---

#### Activity
* Acitvity简单的理解为View
* Activity之间使用Intent进行通信
* 在AndroidManifest文件进行配置申明

#### BoradcastReceiver 
* 简单的理解为一个监听，对各个广播的监听
* 两种注册方式：
  * 静态：在AndroidManifest文件中注册，当设备启动并不关闭，这个BoradcastReceiver会一直存在，直到设备关闭。
  * 动态：在代码中注册，当注册的进程被干掉，这个BoradcastReceiver也被干掉
* 在onReceive()中不能进行耗时的操作，进行耗时操作时会导致ANR，如果需要耗时操作时，必须启动一个Service服务，不能够使用线程，因为BoradcastReceiver生命周期很短，当线程还没执行完操作，就会被干掉。

#### Service
* 可以在后台进行一些耗时操作的服务
* 两种启动方式：
  * startService()，一旦启动，必须在不使用的时候关闭，因为这个时候与启动它的组件无关，它可以在后台无限的运行，必须调用stopSelf()或者stopService()方法关闭它。
  * bindService(),调用者与Service进行了绑定，所以会随着调用者的死亡而死亡，不求同年同月生，只求同年同月死的意思
* 在AndroidManifest文件进行配置申明

#### ContentProvider
* 内容提供者，内容的共享，通过ContentProvider可以指定Android中可共享的数据，这些数据可以存储在任何合理的方式，其他应用可以通过此组件获取共享数据。
* 需要内容共享才需要使用此组件。
* 在AndroidManifest文件进行配置申明