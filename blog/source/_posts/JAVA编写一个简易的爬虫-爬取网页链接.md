title: JAVA编写一个简易的爬虫(爬取网页链接)
date: 2016-04-08 17:41:05
categories: JAVA
tags: Coding
---
这个简易的爬虫需要jsoup包，一个java的第三方包

## jsoup包有什么作用（在这个简易的爬虫程序中）？
在这个简易的程序中，所需要的是jsoup包中的
```
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
这几个类。
主要是解析网页中的标签，直接抽取网页链接
```
## 代码如下

```
import java.io.IOException;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
public class DocOperate {
	UrlOperate urlGet=new UrlOperate();
	public void LinkAcquisition(String url) throws IOException {//链接获取
		urlGet.ArrayAdd(url);
		System.out.println(url);	
		Document doc;
	    try {
			doc = Jsoup.connect(url).timeout(5000).get();//解析网页内容，设置连接时间
			 Elements links = doc.select("a[href]");//获取所有网页链接
			    for (Element link : links) {
			    	
			          String linkHref = link.attr("abs:href");//获取单个链接
			          if(linkHref!=""){
			               //System.out.println(linkHref);
			               urlGet.LinkedAdd(linkHref);}//添加到待爬取链接
			          }
			         LinkRepeatJudgment();//执行链接重复的判断
		} catch (Exception e) {//如果出现异常，就直接执行链接重复判断
			LinkRepeatJudgment();
			e.printStackTrace();
		}
	} 
	public void LinkRepeatJudgment() throws IOException {//链接重复判断
		if(!urlGet.IsEmpty()){//如果待爬取链接不为空
			String url=urlGet.GetUrl();//获取第一个待爬取链接
			urlGet.Remove(url);//移除待爬取链接的第一个
			if(urlGet.CompareUrl(url)){//如果该链接被爬取，继续执行此方法
			//System.out.println(url);
				LinkRepeatJudgment();
			}else{否则执行链接重复判断
			    
		        LinkAcquisition(url);}
		}else{
			System.out.println("game over");
		}
	}
}
```

以上是工具类，主要用于抓取网页链接。
```
import java.util.ArrayList;
import java.util.LinkedList;
public class UrlOperate {
	private LinkedList<String> linked=new LinkedList<String>();//待爬取链接
	
	private ArrayList<String> arry=new ArrayList<String>();//以爬取链接
	public void LinkedAdd(String url){//添加待爬取链接
		linked.add(url);	
	}
	public void Remove(String url){//爬取链接移除
		
		linked.remove(url);
	}
	public String GetUrl(){//得到待爬取第一个链接
		return linked.getFirst();
	}
	public boolean IsEmpty(){//判断待爬取链接是否为空
		return linked.isEmpty();
	}
	
	public LinkedList<String> getHash(){
		return linked;
	} 
	
	public void ArrayAdd(String url){//以爬取链接的添加
		arry.add(url);
	}
	public boolean CompareUrl(String url){//判断将爬取链接是否已存在于以爬取链接中
		return arry.contains(url);
	}
}
```

以上代码为网页链接的存取以及判断是否被爬取，对象类。
```
import java.io.IOException;
public class Spider {
	public static void main(String args[]) throws IOException{
		String line=null;
		String url="http://mrnoknow.github.io/";
		DocOperate operate=new DocOperate();
		operate.LinkAcquisition(url);
	}
}
```

完成。