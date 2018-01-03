title: Android Adapter重复绘制问题
date: 2016-12-07 13:23:28
categories: [Android]
tags: other
---
#### 概述
今天在实现复用Adapter的时候，我在使用AddView添加组件的时候，发现当界面上下拉动的时候，添加组件会每次增加一个，并且第一部分区域和最后一部分会重复绘制多次
如图：

![](/images/Android Adapter重复绘制问题/1.gif)

然后这是最尴尬的，我不知道它为什么会重复绘制，如果是刷新的话好理解，那为什么第一个和最后一个一开始就重复绘制了如此多的次数
**以下部分来自http://www.cnblogs.com/nailperry/p/4675599.html**
#### ListView.OnMeasure方法源码
```
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    ...

    int childWidth = 0;
    // 默认为0
    int childHeight = 0;
    int childState = 0;
   
    mItemCount = mAdapter == null ? 0 : mAdapter.getCount();
    // ViewMode 处于UNSPECIFIED 状态，且mAdapter.getCount() > 0时绘制首项来探测大小
    if (mItemCount > 0 && (widthMode == MeasureSpec.UNSPECIFIED ||
            heightMode == MeasureSpec.UNSPECIFIED)) {
        // getView(0)
        final View child = obtainView(0, mIsScrap);

        measureScrapChild(child, 0, widthMeasureSpec);

        childWidth = child.getMeasuredWidth();
        // 更新为item0的高度
        childHeight = child.getMeasuredHeight();
        ...
    }
    ...
    /*
     *  ViewMode 处于UNSPECIFIED 状态，当mItemCount > 0时，heightSize为getView(0)的高度+ListView距离顶部和底部的距离+2*getVerticalFadingEdgeLength；
     *  当mItemCount==0时，childHeight=0，则heightSize返回的仅仅是ListView距离顶部和底部的距离+2*getVerticalFadingEdgeLength
     */
    if (heightMode == MeasureSpec.UNSPECIFIED) {
        heightSize = mListPadding.top + mListPadding.bottom + childHeight +
                getVerticalFadingEdgeLength() * 2;
    }
    // ViewMode 处于AT_MOST 状态，绘制多个列表项以确定高度。
    if (heightMode == MeasureSpec.AT_MOST) {
        // 会调用多个getView，这些view将不会被复用
        heightSize = measureHeightOfChildren(widthMeasureSpec, 0, NO_POSITION, heightSize, -1);
    }

    setMeasuredDimension(widthSize , heightSize);
    mWidthMeasureSpec = widthMeasureSpec;        
}
```

#### measureHeightOfChildren方法的源码
```
final int measureHeightOfChildren(int widthMeasureSpec, int startPosition, int endPosition,
            final int maxHeight, int disallowPartialChildPosition) {

        final ListAdapter adapter = mAdapter;
        if (adapter == null) {
            return mListPadding.top + mListPadding.bottom;
        }
        int returnedHeight = mListPadding.top + mListPadding.bottom;
        ...
        View child;
        // onMeasure传递过来的endPosition==NO_POSITION，也就是-1，则令endPosition取数据源的adapter.getCount() - 1
        endPosition = (endPosition == NO_POSITION) ? adapter.getCount() - 1 : endPosition;
        final AbsListView.RecycleBin recycleBin = mRecycler;
        final boolean recyle = recycleOnMeasure();
        final boolean[] isScrap = mIsScrap;
        /*
         * 从起始位置0（onMeasure中传入的是0）开始，循环地创建ItemView，并累加ItemView的高度，当高度和超过onMeasure传递过来的maxHeight（其实是测量规格中的size）时，跳出循环
         */
        for (i = startPosition; i <= endPosition; ++i) {
            child = obtainView(i, isScrap);

            measureScrapChild(child, i, widthMeasureSpec);

            if (i > 0) {
                // 计算高度的时候还需将ItemView间的分隔距离考虑进来
                returnedHeight += dividerHeight;
            }

            // Recycle the view before we possibly return from the method
            if (recyle && recycleBin.shouldRecycleViewType(
                    ((LayoutParams) child.getLayoutParams()).viewType)) {
                /*
                 *  注意，这里addScrapView方法传入的第二个参数也就是position为-1，
                 *  由于Layout过程中调用getScrapView方法时传入的position>=0，
                 *  故position为-1的ScrapView都不会被回收，读懂这句话需要了解RecycleBin类，这里暂不深究。
                 */
                recycleBin.addScrapView(child, -1);
            }

            returnedHeight += child.getMeasuredHeight();
            // 循环提前终止条件
            if (returnedHeight >= maxHeight) {
                // 加上第i个的高度后returnedHeight已经超过了maxHeight，故高度探测应结束，说明最多只能容纳到第i个item
                return (disallowPartialChildPosition >= 0) // Disallowing is enabled (> -1)
                            && (i > disallowPartialChildPosition) // We've past the min pos
                            && (prevHeightWithoutPartialChild > 0) // We have a prev height
                            && (returnedHeight != maxHeight) // 第i个Item不能显示完全，即超出容器
                        ? prevHeightWithoutPartialChild
                        : maxHeight;
            }

            if ((disallowPartialChildPosition >= 0) && (i >= disallowPartialChildPosition)) {
                prevHeightWithoutPartialChild = returnedHeight;
            }
        }
        return returnedHeight;
    }
```
*  A parent view may call measure() more than once on its children. For example, the parent may measure each child once with unspecified dimensions to find out how big they want to be, then call measure() on them again with actual numbers if the sum of all the children's unconstrained sizes is too big or too small.
* 一个父视图可能多次调用子视图的measure方法，意思就是说，当ListView的OnMeasure方法计算完Item的大小，ListView的父视图将要查看此结果，然后进行判定这个结果是否能被接受，若不能，则会再次调用measureHeightOfChildren方法，从而导致getView方法的重复调用，而造成第一行和最后一行被多次绘制
**但是，那个上下拉后，隐藏的Item重现是再次被绘制还不是不是太清楚，有几张图，然后看了网上的ListView源码解释，还是很模糊，在此先贴图**
![](/images/Android Adapter重复绘制问题/2.png)

* 上图意思很容易看懂，但是我还是无法理解重复绘制的问题，我在代码中添加的组件怎么会在重回屏幕时增加
![](/images/Android Adapter重复绘制问题/3.jpg)
* 上图的意思就是，每次内存中只保存显示的几个Item，之后拖动后，被隐藏的Item（View）会保存到Recycler中，当再次回到原来位置时，会从Recycler中获取，感觉好像解释不了重新被绘制的问题
**总结上面2张图，就是如果你ListView中一个屏幕中有N个Item（View），你的内存中也只会产生N+1个Item（View），无论你ListView中有多少行数据，然后每当一个Item（View）移除屏幕，放在RecycleBin中的Item（View）就会去填充，然后移除的Item（View）进入RecycleBin中，如此循环往复，这里应该表达清楚的是每个Item中的信息是改变的，只是用的View那个对象，和View中的信息无关，所以尽管如此还是不能解释我代码中添加的元素越来越多问题**
**以上基本类似的意思，但是在以上讲解中，并没有提到getView被复用的点，相反而是为了解决此问题和OOM问题所做的解释**


### 2017-7-26更
  **对于上面的问题，我在前几天突然豁然开朗，就是ListView中的item放进RecycleBin后，在下拉后会复用RecycleBin中的item，所以在item加一个view后，在复用中，此view还是会存在于此item中，我们每次下拉或者刷新，都只是更改item中的信息，或者图片，并没有添加过view或者其他一些组件，所以当你有item的添加组件操作，会在下面的刷新中，复用此item中的view越来越多，因为我们是在getView（）方法中进行信息或者图片加载，而每次下拉总是会调用getView(),所以添加向item添加view这一操作，证实了itme复用的实现。**

### 2017-8-1更
   * 当加载第一页ListView时，会加载比第一页item多2个的item