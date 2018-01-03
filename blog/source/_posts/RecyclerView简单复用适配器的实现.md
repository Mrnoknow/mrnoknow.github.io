title: RecyclerView简单复用适配器的实现
date: 2017-07-20 15:17:57
categories: Android 
tags: Coding
---
### 一个简单RecyclerView复用适配器的实现
**其实现方法与复用的ListView基本相似，但在实例化ViewHolder时，我在RecyclerView里使用的是单例模式，而在ListView直接判View是否为null，设置Tag来实现，但都是只创建一个ViewHolder**
* RecyclerView代码
```
public abstract class RecycleryAdapter<T>  extends RecyclerView.Adapter {

    private List<T> list;
    private Context context;
    private int layoutId;
    public RecycleryAdapter(Context context,List<T> list,int layoutId){
        this.context=context;
        this.list=list;
        this.layoutId=layoutId;
    }
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup viewGroup, int i) {
        return RecyclerViewHolder.get(context,viewGroup,layoutId);
    }
    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder viewHolder, int i) {
        fun((RecyclerViewHolder) viewHolder,list.get(i));
        fun((RecyclerViewHolder) viewHolder,list.get(i),i);
    }
    public abstract void fun(RecyclerViewHolder viewHolder, T data);
    public abstract void fun(RecyclerViewHolder viewHolder, T data,int i);
    @Override
    public int getItemCount() {
        return list==null?0:list.size();
    }
}


```
* ViewHolder代码
```
public class RecyclerViewHolder extends RecyclerView.ViewHolder{
    private SparseArray< View> mViews;
    private View Fview;
    private static RecyclerViewHolder recyclerViewHolder;
    private RecyclerViewHolder(View parent){
        super(parent);
        this.mViews=new SparseArray<View>();
        Fview= parent;
    }

    public static RecyclerViewHolder get(Context context, ViewGroup viewGroup,
                                  int layoutId){
        if(recyclerViewHolder==null){
            return new RecyclerViewHolder(LayoutInflater.from(context).inflate(layoutId, viewGroup, false));
        }
        return recyclerViewHolder;
    }
    public <T extends View> T getView(int viewId)  {

        View view = (View) mViews.get(viewId);   //从 集合中找，没有找到从当前所在布局文件中找
        if (view == null)
        {
            view = Fview.findViewById(viewId);
            mViews.put(viewId, view);
        }
        return (T) view;
    }
    public void setTypeface(int id, Typeface typeface){
        TextView textView=getView(id);
        textView.setTypeface(typeface);
    }
    public void setText(int textId,String textView){
        TextView tv=getView(textId);
        tv.setText(textView);
    }
}
```
* MainActivity代码
```
public class MainActivity extends AppCompatActivity {
    private String path;
    private ArrayList<String> paths;
    private ArrayList<String> items;
    private ArrayList<Typeface> res;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.i("ssssssssssss","1111111111111");
        inti();

    }
    public void  inti(){
        items=new ArrayList<String>();
        paths=new ArrayList<String>();
        path="/system/fonts/";
        final File f = new File(path);
        File[] files = f.listFiles();// 列出所有文件
        if(files != null){
            int count = files.length;// 文件个数
            for (int i = 0; i < count; i++) {
                File file = files[i];
                items.add(file.getName());
                paths.add(file.getPath());
            }
        }
        res=new ArrayList<Typeface>();
        for (int i=0;i<paths.size();i++){
            res.add(Typeface.createFromFile(paths.get(i)));
        }
        RecyclerView recyclerView=(RecyclerView)findViewById(R.id.recyclerView);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));
        recyclerView.setAdapter(new RecycleryAdapter<Typeface>(this,res,R.layout.item) {

            @Override
            public void fun(RecyclerViewHolder viewHolder, Typeface data) {

            }

            @Override
            public void fun(RecyclerViewHolder viewHolder, Typeface data, int i) {
                viewHolder.setTypeface(R.id.textView,data);
                viewHolder.setTypeface(R.id.textView1,data);
                viewHolder.setText(R.id.textView1,items.get(i));
                viewHolder.setText(R.id.textView,"北京上海40$50°ABCabc");
            }

        });

    }
}

```
**其实基本上和ListView中的实现是差不多，区别就是ViewHolder的实现，单例模式，需要理解的就是ViewHolder中的代码**