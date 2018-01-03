title: Andorid 手势识别(GestureDetector)
date: 2017-03-07 14:32:20
categories: [Android]
tags: [Coding]
---
** 先贴代码 **
### 
```
package com.example.lenovo.touchscreen;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {
    private GestureDetector mGestureDetector = new GestureDetector(new MyGesture());
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        return mGestureDetector.onTouchEvent(event);

    }
    private TextView textView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mGestureDetector.setIsLongpressEnabled(true);
        
    }
    class MyGesture extends GestureDetector.SimpleOnGestureListener{
        @Override
        public boolean onDown(MotionEvent ev) {
           Log.d("onDown", ev.toString());
            Toast.makeText(MainActivity.this, "onDown", Toast.LENGTH_SHORT)
                    .show();
            return true;
        }

        /**
        滚动
        e1按上屏幕的起始位置，e2为离开屏幕的结束位置
        distanceX是X轴起点与终点的水平距离
        distanceY是Y轴起点与终点的水平距离
        */
        @Override
        public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
            Log.d("onScroll", e1.toString());
            Toast.makeText(MainActivity.this, "onScroll", Toast.LENGTH_SHORT)
                    .show();
            if(distanceX-distanceY<=10||distanceX-distanceY>=-10) System.exit(0);
            return true;
        }
        /**
        长按
        */
        @Override
        public void onLongPress(MotionEvent ev) {
            Log.d("onLongPress", ev.toString());
            Toast.makeText(MainActivity.this, "onLongPress", Toast.LENGTH_SHORT)
                    .show();
        }
        /**
        抬起
        */
        @Override
        public boolean onSingleTapUp(MotionEvent ev) {
            Log.d("onSingleTapUp", ev.toString());
            Toast.makeText(MainActivity.this, "onSingleTapUp", Toast.LENGTH_SHORT)
                    .show();
            return true;
        }
        /**
        按住，比长按要短
        */
        @Override
        public void onShowPress(MotionEvent ev) {
            Log.d("onShowPress ", ev.toString());
            Toast.makeText(MainActivity.this, "onShowPress", Toast.LENGTH_SHORT)
                    .show();
        }
        /**
        抛
        e1按上屏幕的起始位置，e2为当前屏幕的位置
        velocityX每秒X轴上移动的像素
        velocityY每秒Y轴上移动的像素
        这2个数据为速率
        */
        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            Log.d("onFling e1 ",e1.toString());
            Log.d("onFling e2 ",e2.toString());
            Toast.makeText(MainActivity.this, "onFling", Toast.LENGTH_SHORT)
                    .show();
            return true;
        }
        /**
        双击，连续
        */
        @Override
        public boolean onDoubleTap(MotionEvent e) {
            Log.d("onDoubleTap",e.toString());
            Toast.makeText(MainActivity.this, "onDoubleTap", Toast.LENGTH_SHORT)
                    .show();
          //  System.exit(0);
            return super.onDoubleTap(e);
        }
        /**
        抬起确认，手离开屏幕
        */
        @Override
        public boolean onSingleTapConfirmed(MotionEvent e) {
            Log.d("onSingleTapConfirmed",e.toString());
            Toast.makeText(MainActivity.this, "onSingleTapConfirmed", Toast.LENGTH_SHORT)
                    .show();
            return super.onSingleTapConfirmed(e);
        }
    }
}

```
* 此段代码是监控整个屏幕，但是上面的TextView不在此范围，因为TextView覆盖了父View，而监控是的父View，
所以点击TextView不会有任何反应
![ ](/images/Andorid 手势识别GestureDetector/1.png)
* 当你触摸屏幕后，会将MotionEvent 传入重写的onTouchEvent函数中，然后通过GestureDetector.onTouchEvent()
将触摸事件的状态传入GestureDetector的各个重写的状态
* 在代码中记得添加mGestureDetector.setIsLongpressEnabled(true)这行代码，否则会有事件无法被捕获
* 无论什么事件都会触发Ondown()函数
* 各个触摸事件
  * 单击 ** onDown——>onShowPress——>onSingleTapUp——>onSingleTapConfirmed **
  * 双击 ** onDown——>onShowPress——>onSingleTapUp——>OnDoubleTap——>onDown——>onShowPress **
  * 长按 ** onDown——>onShowPress——>onLongPress **
  * 抛(onFling) ** onDown——>onScroll——>onScroll——> ……… ——>onFling **
** 下一节使用手势打开程序 **