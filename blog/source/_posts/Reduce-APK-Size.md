title: Reduce APK Size
date: 2017-12-14 16:14:41
categories: Android
tags: 读书笔记
---

在开发Android工程中，要尽量减少APK的大小，提高性能

### 理解Apk的结构
  * <code>META-INF/</code>:包含<code>CERT.SF</code>和<code>CERT.RSA</code>签名文件，还包括<code>MANIFEST.MF</code>的文件清单

  * <code>assets/</code>:直接打包进APK的资源，不会直接在<code>R.java</code>生成资源ID，可使用[AssetManager](https://developer.android.google.cn/reference/android/content/res/AssetManager.html)来对它进行引用使用

  * <code>res/</code>:包含不编译成<code>resources.arsc</code>的资源

  * <code>lib/</code>:包含不同处理器的编译代码，包含各个平台类型，比如<code>armeabi, armeabi-v7a, arm64-v8a, x86, x86_64, and mips.</code>

  **APK还包含以下几个部分，除了<code>AndroidManifest.xml</code>是必须的，其他都是不强制的**

  * <code>resources.arsc</code>:包含所有配置的XML内容来自<code>res/values/</code>文件夹，通过工具获取XML内容，然后编译二进制文件，然后归档内容,内容包括字符串和样式，但是不包括内容的路径，例如布局文件和图片

  * <code>classes.dex</code>:包含通过**Dalvik/ART**虚拟机编译java源码生成的DEX文件

  * <code>AndroidManifest.xml</code>:包含核心的Android清单文件，此文件包含应用的名称，权限，版本，引用的库。此文件是二进制文件

### 减少资源文件的数量和大

*APK的大小会影响APP的性能，如加载速度，内存的使用，以及电力的消耗。一个很常见的减少APK大小的方法就是减少资源文件的数量和大小，比如，你可以移除不在使用的资源，使用可以扩展的[Drawable](https://developer.android.google.cn/reference/android/graphics/drawable/Drawable.html)对象来代替图像文件，所以接下来就是讨论怎样减少资源文件的数量和大小*

##### 删除不使用的资源文件
  * [lint](https://developer.android.google.cn/studio/write/lint.html)工具是AS中自带的静态代码分析器，检测<code>res/</code>文件下没有被引用的资源，当检测发现了没有被引用的资源后会输出像以下的警告。

  ```
  res/layout/preferences.xml: Warning: The resource R.layout.preferences appears
    to be unused [UnusedResources]
  ```

  **但是Lint工具无法扫描检测<code>assets/</code>文件夹，因为此文件夹下的是通过反射引用的资源或者是链接的库文件，所以它是不可以的移除的资源，它只会存在一个提醒**
  <code>Libraries</code>代码库中也可能存在不被使用的情况,<code>Gradle</code>可以自动的移除这些无用的资源当你在<code>build.gradle</code>文件中配置[shrinkResources](https://developer.android.google.cn/studio/build/shrink-code.html)。

  ```
  android {
    // Other settings

    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
  }
  ```

  使用[shrinkResources](https://developer.android.google.cn/studio/build/shrink-code.html)，就必须使用代码压缩，在构建的过程中，[ProGuard](https://developer.android.google.cn/studio/build/shrink-code.html)会首先移除未使用的代码但不会移除未使用的资源，之后<code>Gradle</code>会移除未使用的资源，（关于ProGuard和其他方法的来压缩代码和资源可学习此文章[Shrink Your Code and Resources](https://developer.android.google.cn/studio/build/shrink-code.html)）

##### 减少资源库
  * 在开发中我们肯定会使用外部库来提高我们应用的可用性，所以[库](https://developer.android.google.cn/topic/libraries/support-library/index.html)中会包含很多对象和方法是不需要，所以如果许可证认可，可以去动态的编辑库文件，增加删除库文件，来定制自己的APP内容，[ProGuard](https://developer.android.google.cn/studio/build/shrink-code.html)可以移除不使用的库中代码，但是它不能够移除库中的内部依赖项

##### 支持特定的屏幕密度
  * 若只有一小部分的用户使用，则需要特殊处理，如果你APP中不包含这些特定的[屏幕密度](https://developer.android.google.cn/about/dashboards/index.html#Screens)，android会自动缩放现有的资源。如果你需要自动缩放的资源，你可以通过绘制图像的变种放置<code>drawable-nodpi/</code>来节省空间，我们建议至少存在一个<code>xxhdpi</code>的图片变种

##### 使用<code>Drawable</code>对象
  * 一些图片不需要静态的图片资源而是在运行的时候动态的绘制图片，[Drawable](https://developer.android.google.cn/reference/android/graphics/drawable/Drawable.html)对象（<code>shape</code> in XML）在APK中占用很少的资源，并且[Drawable](https://developer.android.google.cn/reference/android/graphics/drawable/Drawable.html)符合**md**的设计原则

##### 复用资源
  * 比如可以对图像的变化包含一个单独的资源，在图片的旋转，切片等，建议使用同一组资源，然后在使用的时候定制它们。Android提供工具来改变资源的颜色，在5.0以上，可以是使用<code>android:tint</code> 和<code>tintMode</code>，而低版本可以使用[ColorFilter](https://developer.android.google.cn/reference/android/graphics/ColorFilter.html)类来实现。还可以通过旋转图片来达到资源的复用，比如下面的代码就是通过旋转180度，来达到『拇指向上』到『拇指向下』的转变

  ```
  <?xml version="1.0" encoding="utf-8"?>
  <rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/ic_thumb_up"
    android:pivotX="50%"
    android:pivotY="50%"
    android:fromDegrees="180" />

  ```

##### 代码渲染
  * 通过代码渲染来试图来节省图片的size，通过代码渲染就可以节省存储图片的空间。

##### 压缩PNG文件
  * <code>aapt</code>在构建过程中可以优化和无损压缩放置在<code>res/drawable/</code>的图片资源，例如，<code>aapt</code>可以将不需要超过256色的真彩色PNG转换为具有调色板的8位PNG，这样可以相同质量的图片占更小的内存。但是<code>aapt</code>有以下几点局限：
    * 不能够压缩在<code>asset/</code>文件下的PNG文件
    * <code>aapt</code>优化图片文件需要256或者更少色
    * <code>aapt</code>可能会填充已经压缩的PNG文件，为了防止个问题，我们应该在<code>Gradle</code>配置中使用<code>cruncherEnabled</code>标志来防止此问题

     ```
    aaptOptions {
    cruncherEnabled = false
    }
  ```

##### 压缩PNG和JPEG文
  * 可以使用[pngcrush](http://pmt.sourceforge.net/pngcrush/), [pngquant](https://pngquant.org/), or [zopflipng](https://github.com/google/zopfli)在无损的情况下减少PNG质量，[pngcrush](http://pmt.sourceforge.net/pngcrush/)是非常有效的，它遍历PNG过滤器和 zlib (Deflate)参数,使用每个组合的过滤器和参数来压缩图片，然后选择最好的压缩输出，而JPEG可以使用[packJPG](http://www.elektronik.htw-aalen.de/packjpg/)和[guetzli](https://github.com/google/guetzli)

##### 使用<code>WebP</code>文件格式
  * 可以使用<code>WebP</code>格式来代替PNG和JPEG格式，当targeting超过3.2(API 13)，<code>WebP</code>可以提供有损压缩(JPEG)以及透明度(PNG)并且压缩效果要比PNG和JPEG效果好,所以可以使用<code>WebP</code>来代替BMP, JPG, PNG 和静态GIF文件在AS中，更多信息关于<code>WebP</code>可以看 [Create WebP Images Using Android Studio](https://developer.android.google.cn/studio/write/convert-webp.html)

  **Google Play 只接受启动图标为PNG格式的APK**

##### 使用矢量图标
  * 可以使用矢量图形创建独立于分辨率的图标和其他可伸缩的媒体。使用这些图标可以大大的减少APK的体积，矢量图标在Android中代表[VectorDrawable](https://developer.android.google.cn/reference/android/graphics/drawable/VectorDrawable.html)对象，一个[VectorDrawable](https://developer.android.google.cn/reference/android/graphics/drawable/VectorDrawable.html)对象只需使用100个字节大小就可以生成清晰的图像，但是系统在渲染[VectorDrawable](https://developer.android.google.cn/reference/android/graphics/drawable/VectorDrawable.html)对象时需要大量的时间，所以如果是一张大图，则在显示过程中会花费大量的时间，所以只推荐小图的时候使用矢量图标。更多关于使用[VectorDrawable](https://developer.android.google.cn/reference/android/graphics/drawable/VectorDrawable.html)对象的信息可以看 [Working with Drawables](https://developer.android.google.cn/training/material/drawables.html)

##### 使用矢量动画图像
  * 千万不要使用[AnimationDrawable](https://developer.android.google.cn/reference/android/graphics/drawable/AnimationDrawable.html)对象来创建帧动画，因为它会需要每一帧的单独的图片文件，所以必定会增加你的APK大小。所以应该使用[AnimatedVectorDrawableCompat](https://developer.android.google.cn/reference/android/support/graphics/drawable/AnimatedVectorDrawableCompat.html)来创建[animated vector drawables](https://developer.android.google.cn/training/material/animations.html#AnimVector)

### 减少本地和Java代码

##### 减少不必要的生成代
  * 要了解自动生成的代码空间大小，例如，许多协议缓冲工具生成了大量的方法和类，可以将应用程序的大小增加一倍甚至更多。

##### 避免枚举
  * 一个枚举在应用程序class.dex文件中将会增加1-1.4kb，这些添加会很快的积累到复杂的系统和共享库中，如果可以考虑使用<code> @IntDef</code>注释和[ProGuar](https://developer.android.google.cn/studio/build/shrink-code.html)处理枚举代码转换为interger值，这样可以保证枚举类型的安全效益。

##### 减少二进制文件的大小
  * 如果你的APP中有存在native代码和AndroidNDK代码，也可以通过优化你的代码来减少APP的size，所以可以通过下面2种方式来进行优化。
    * 移除debug符号：当你在开发的时候使用debug符号是有意义的，Android NDK 提供<code>arm-eabi-strip</code>工具，用来去除native库中不必要的debug符号，在这之后在打release包。
    * 避免复制native库：<code>.so</code>文件在APK存储中是不能够被压缩的，在[<application> ](https://developer.android.google.cn/guide/topics/manifest/application-element.html)元素中设置<code>android:extractNativeLibs</code>标记为false可以避免[PackManager](https://developer.android.google.cn/reference/android/content/pm/PackageManager.html)在应用安装时复制<code>.so</code>文件到文件系统并且还可以使应用程序增量更新更小

### 维护多个精益的APK
  * 你的APK可能有用户下载但是却没有使用的内容，像国家和语言信息，使用户能够有一个最小的下载，所以可以将APP分割成几个APK，比如不同的屏幕和GPU支持，当用户下载时，可以根据用户的型号，进行定制下载，下载用户需要的资源，而不需要的不下载，比如<code>hdpi</code>的设备，只需要<code>hdpi</code>资源，而不需要<code>xxxxhdpi</code>资源，所以就是不需要的资源，更多的信息可以查看[Configure APK Splits](https://developer.android.google.cn/studio/build/configure-apk-splits.html)和[Maintaining Multiple APKs](https://developer.android.google.cn/training/multiple-apks/index.html)

##### 参考文档
* https://developer.android.google.cn/topic/performance/reduce-apk-size.html#multiple-apks
