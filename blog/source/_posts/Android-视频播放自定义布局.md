title: Android 视频播放自定义布局
date: 2018-01-19 15:40:57
categories: [Android]
tags: [Coding]
---

在Android开发中，会遇到这样一个问题，需要在App中添加一个视频，但是功能又不需要很复杂，如果我们导入一个很大的视频库的话，会增加我们App的size，所以我们就需要自制一个简易的视频，并且可以自定义它的样式。
接下来就介绍一下怎样快速的自己写一个可自定义样式的视频。

##### VideoView（Android封装的播放器）

我们先看看Android自带的视频播放器，VideoView就是AndroidAPI中的自带视频播放器，其设计有以下几点：
* VideoView继承SurfaceView
* VideoView用的视频播放器是Android自带的MediaPlayer
* 控制VideoView播放器视图的是MediaController，控制一些基本的操作的视图，比如快进，后退，播放占等播放状态


所以如果我们不需要自定义视图，且功能非常的简单，我们就直接可以使用VideoView播放器

**在VideoView中，需要明白的是SurfaceView，MediaController和MediaPlayer各个标注位，SurfaceView是承接MediaPlayer的视图，MediaController是控制MediaPlayer的视图**
所以我们自定义视频播放器的话，也可以按照这个思路来，播放器继承SurfaceView，在写一个控制视图。其实我在想为什么不设计一个小的库，然后我们直接写一个布局传进去，然后就能得到我们想要的播放的样式，这个难点
就是我们传进去的视图组件需要绑定监听啊，所以这点还是我现在无法想到解决办法的，所以我们可以实现一个播放视图，然后自己实现一个控制视图去控制它，所以我们只要把这个思路搞清楚其实就是码代码的事情和对一些逻辑
标志位的处理就行了，还有就是对Android各个自定义系统的适配问题了，因为视频这个问题很多坑。


#### 逻辑结构

这里先整理一下这个视频的整个逻辑
先要知道的是几个逻辑点：
* 连接wifi自动播放
* 连接流量出现播放按钮，不自动播放
* 无网状态：
    * 在开始加载状态突然无网，一直出现加载loading
    * 播放状态无网，播放到加载处，暂停，记录暂停位置，然后出现无网logo
    * 播放状态无网，拖动进度条。直接出现无网logo，暂停，记录暂停位置
* 点击无网状态logo，判断是否连接网络，若有网络，直接加载，否则还显示无网logo
* 加载loading的出现时机与消失时机，在开始时出现。在拖动进度条，若是没有被加载，则出现loading，否则不显示loading，API版本不同的适配
* 前后台切换，对视频状态的保存和恢复，以及不同Android平台的适配，这个坑太多

以上大概就是视频播放的一些必要点，虽然看着这个逻辑不是很多，但是要把这些点串起来，并且去适配各种不同的平台和API还是有很多东西需要考虑的

上面这张图就是一个网络视频播放器的简单流程图。


#### 代码结构

* WMVideoGesture：手势操作类
* IController：WMVideoController绑定Activity生命周期接口
* ControllerChangeView：视图控件显示隐藏接口
* WMMediaInterface：视频播放生命周期接口
* WMVideoController：控制视频的视图，继承WMVideoGesture
* WMVideoView：视频实现类，内部是MediaPlayer，继承SurfaceView，实现WMMediaInterface接口

#### 逻辑细节梳理


