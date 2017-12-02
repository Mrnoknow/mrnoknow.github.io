title: 模板Adapter
date: 2016-12-05 17:38:05
categories: [Android]
tags: Coding
---

#### 概述
当前一个项目会有很多的Adapter与它所对应的Model，所以这样会产生很多的Adapter类与Model，这样代码量就会很大，并且还代码重复多，这是一个烦人的过程，所以需要一个万能的模板Adapter，一个Adapter，所有的ListView都能用

#### ViewHolder模板
*对此我直接上源码*
```
package com.example.againadapter;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.drawable.Drawable;

import android.util.SparseArray;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

public class CommonViewHolder {
  private SparseArray< View> mViews;
  private View Fview;
  /*
   * 构造函数，设置Tag，
   */
  private CommonViewHolder(Context context, ViewGroup parent, int layoutId,  
            int position){
    this.mViews=new SparseArray<View>();
    Fview=LayoutInflater.from(context).inflate(layoutId, parent,false);
    Fview.setTag(this); 
  }
  
  /*
   * 得到Tag
   */
  public static CommonViewHolder get(Context context, View convertView,  
            ViewGroup parent, int layoutId, int position){
    
    if(convertView==null){
    
      return new CommonViewHolder(context,parent,layoutId,position);
    }
    return (CommonViewHolder)convertView.getTag();
   }
  
  /*
   * 最重要获取布局文件中的元素
   */
   @SuppressWarnings("unchecked")
  public <T extends View> T getView(int viewId)  {  
      
          View view = (View) mViews.get(viewId);   //从 集合中找，没有找到从当前所在布局文件中找
          if (view == null)  
          {
            
              view = Fview.findViewById(viewId);  
              mViews.put(viewId, view);  
          }  
          return (T) view;  
   }
   
   
   public View getView(){
     return Fview;
   }
   /*
    * 以下全是设置元素信息的函数
    */
   public void setText(int textId,String textView){
     TextView tv=getView(textId);
     tv.setText(textView);
   }
   public void setImageResource(int imageViewId,int imageView){
     ImageView im=getView(imageViewId);
     im.setImageResource(imageView);
   }
   public void setImageDrawable(int imageViewId,Drawable drawable){
     ImageView im=getView(imageViewId);
     im.setImageDrawable(drawable);
   }
   public void setImageBitmap(int imageViewId,Bitmap bitmap){
     ImageView im=getView(imageViewId);
     im.setImageBitmap(bitmap);
   }
}
```
以上代码是不是简短，但是很精辟
* 此代码主要在于构造函数，和getView（int viewId）函数，你可以加上Log查看它 的执行过程
#### Adapter模板
```
package com.example.againadapter;

import java.util.List;



import android.content.Context;

import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;


public abstract class ReplyAdapter<T> extends BaseAdapter{
  private List<T> list;
  private Context context;
  private int layoutId;
  public ReplyAdapter(Context context,List<T> list,int layoutId){
    this.context=context;
    this.list=list;
    this.layoutId=layoutId;
  }
  @Override
  public int getCount() {
    // TODO Auto-generated method stub
    return list.size();
  }

  @Override
  public Object getItem(int poistion) {
    // TODO Auto-generated method stub
    return poistion;
  }

  @Override
  public long getItemId(int poistion) {
    // TODO Auto-generated method stub
    return poistion;
  }

  @Override
  public View getView(int poistion, View view, ViewGroup viewGroup) {
    // TODO Auto-generated method stub
    /*ViewHolder viewHolder=null;
    view = LayoutInflater.from(context).inflate(
                R.layout.replay_text, null);
    viewHolder=new ViewHolder();
    viewHolder.setText((TextView)view.findViewById(R.id.textView1));
    view.setTag(viewHolder);
    viewHolder.getText().setText(list.get(poistion));*/
    
  /*  CommonViewHolder viewHolder=CommonViewHolder.get(context, view, viewGroup, R.layout.replay_text, poistion);
    TextView tv=viewHolder.getView(R.id.textView1);
    tv.setText(list.get(poistion)); 
    */
    CommonViewHolder viewHolder=CommonViewHolder.get(context, view, viewGroup, layoutId, poistion);
    fun(viewHolder,list.get(poistion));
    return viewHolder.getView();
  }
  abstract void fun(CommonViewHolder viewHolder,T fun);//直接重写此函数，设置你需要设置的元素信息

}
```

#### MainActivity
```
package com.example.againadapter;


import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import android.app.Activity;
import android.os.Bundle;


import android.util.Log;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;




public class MainActivity extends Activity {
  private List<Integer> mDatas=new ArrayList<Integer>(Arrays.asList(R.drawable.ic_launcher,R.drawable.fun));
  private ListView listView;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        listView=(ListView)findViewById(R.id.listView1);
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener(){

      @Override
      public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
          long arg3) {
        // TODO Auto-generated method stub
        Log.i("ssssssssss", "fun");
        
      }
          
        });
        /*
         *  直接设置Adapter,重写fun函数，设置元素信息
         */
        listView.setAdapter(new ReplyAdapter<Integer>(this,mDatas,R.layout.replay_text){

      @Override
      void fun(CommonViewHolder viewHolder, Integer fun) {
        // TODO Auto-generated method stub
        viewHolder.setText(R.id.textView1, "ssssss");
        viewHolder.setImageResource(R.id.imageView1, fun);
      }
          
        });
    }
}
```
**以上是看完张鸿洋博客，依葫芦画瓢写的一个例子，博文地址http://blog.csdn.net/lmj623565791/article/details/38902805/**