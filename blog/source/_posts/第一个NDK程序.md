title: 第一个NDK程序
date: 2017-03-06 14:20:14
categories: [Android]
tags: Coding
---
### 简介
  NDK中文为原生开发工具包，从名字就可以看出‘原生’二字，也就是在Android应用中利用C和C++代码的工具，即将C或C++代码嵌入Android代码中
  可以用自己的源代码构建，也可以用现有的预构建库。以下几种值得使用，除此情况都不提倡使用：
  * 在平台之间移植其应用。
  * 重复使用现有库，或者提供其自己的库供重复使用。
  * 在某些情况下提高性能，特别是像游戏这种计算密集型应用。

### 开发环境的搭建（此项目在windows下中的Android Studio完成）
* 下载[NDK压缩包](https://developer.android.com/ndk/downloads/index.html)
* 解压NDK压缩包，此解压尽量不解压到Android studio和sdk中的目录下，否则在后面的AS中的环境添加会出现问题
* 将NDK根目录添加到Path环境中
* 在cmd中测试** ndk-build ** 出现以下信息表示成功
![ ](/images/第一个NDK程序/1.png)
* 进入AS，进入File->Project Structure->Android Ndk location，将NDK环境添加
![ ](/images/第一个NDK程序/2.png)
到这里环境配置完成
### 开发
* 创建一个android工程
* 在src目录下创建一个NdkJniUtils.java（名字可以自己命名）代码文件，代码如下：
```
package com.example.lenovo.ndkone;

/**
 * Created by lenovo on 3/6/2017.
 */

public class NdkJniUtils {
    static {
        System.loadLibrary("FirstJniName");
    }
    public  native String getCLanguageString();
}   

```
* 编译NdkJniUtils.java生成.h文件
  * Build—> Make Project，生成.classes文件,下图.class 文件在此位置
  ![ ](/images/第一个NDK程序/3.png)
  * 进入Terminal中（在最底部一般会有此选项），进入到上图的/debug 目录下，执行
  ```
  javah -jni com.example.lenovo.ndkone.NdkJniUtils
  ```
  在debug根目录下会生成一个名字超长的.h文件
  ** 若使用网上的另一种 javah -d jni -classpath E:/Android/sdk/platforms/android-22/android.jar;../../build/intermediates/classes/debug example.daosong.com.ndkdemo.NDK 可能会出现找不到.class文件的情况 **
  ![ ](/images/第一个NDK程序/4.png)
  * 在src/main目录下创建一个JNI Folder，将生成的.h文件复制到此文件夹中，然后创建一个.c代码文件，.c代码如下
  ```
#include <string.h>  
#include "com_example_lenovo_ndkone_NdkJniUtils.h" //此处为自己的.h文件名
JNIEXPORT jstring JNICALL Java_com_example_lenovo_ndkone_NdkJniUtils_getCLanguageString
  (JNIEnv *env, jobject obj){
     return (*env)->NewStringUTF(env,"This just a test for Android Studio NDK JNI developer!");
  }
  ```
  * 接着创建一个Android.mk文件，文件内容为
  ```

LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE  := FirstJniName //这个自定义名字与后面说到build.gradle中的ndk定义名对应
LOCAL_SRC_FILES := JniUtils.c //这个为创建的.c代码文件名
include $(BUILD_SHARED_LIBRARY)
  ```
  ![ ](/images/第一个NDK程序/5.png)
  * 在app 中的build.gradle中找到defaultConfig{} ，然后在里面添加
  ```
    ndk {
            moduleName "FirstJniName" // "FirstJniName"与上面的Android.mk中的LOCAL_MODULE 对应
            abiFilters "armeabi", "armeabi-v7a", "x86"
        }
  ```
  * 在gradle.properties 添加** android.useDeprecatedNdk=true **否则会出现文件中有c++文件而无法被编译，建议使用Cmake或者ndk-build
  的错误信息
  完成以上就成功，即能够运行
  ![ ](/images/第一个NDK程序/6.png)