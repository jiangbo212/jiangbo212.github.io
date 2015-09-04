# 		java通过url抓取页面数据

1. 使用架包
   
   org.htmlparser，主要用来提取获取的html页面中的有用信息
   
   org.apache.http，主要用来模拟发送get或post请求，抓取指定url页面
   
2. 具体实现
   
   模拟发送get请求，抓取指定URL页面。主要代码：
   
   ``` java
   HttpClient http=new DefaultHttpClient();
   HttpGet hg=new HttpGet("http://www.sohu.com/");
   HttpResponse hr=http.execute(hg);
   ```
   
   将抓取的页面转化成字符串形式，通过org.parser抓取指定标签中的数据。
   
   将get返回的数据转化成字符串形式：
   
   ``` java
   HttpEntity he=hr.getEntity();
   if(he!=null){
   	String charset=EntityUtils.getContentCharSet(he);
   	InputStream is=he.getContent();
   	BufferedReader br=new BufferedReader(new InputStreamReader(is,"GBK"));
   	String line=null;
   	while((line=br.readLine())!=null){
   		List<LinkTag> link=Attrbuite.getText(line, LinkTag.class);
   		for(LinkTag l:link){
   			System.out.println(l.getStringText());
   		}
   	}
   }
   ```
   
    抓取指定标签中的数据，示例代码为抓起超链接的对象。
   
   ``` java
   public static <T extends TagNode> List<T> getText(String html,final Class<?> tagType,final String attrName,final String attrValue){
   	try{
   		Parser p=new Parser();
   		p.setInputHTML(html);
   		NodeList list=p.parse(new NodeFilter(){
   
   			public boolean accept(Node node) {
   				if(node.getClass()==tagType){
   					T t=(T)node;
   					if(attrName==null){
   						return true;
   					}
   					String attrValue=t.getAttribute("luanma:"+attrName);
   					System.out.println(attrValue);
   					if(attrValue!=null&&attrValue.equals(attrValue)){
   						return true;
   					}
   				}
   				return false;
   			}
   			
   		}
   		
   		);
   	List<T> tags=new ArrayList<T>();
   	for(int i=0;i<list.size();i++){
   		T tt=(T)list.elementAt(i);
   		tags.add(tt);
   	}
   	return tags;
   	}catch(Exception e){
   		e.printStackTrace();
   	}return null;
   }
   public static <T extends TagNode> List<T> getText(String html,final Class<?> tagType){
   return getText( html,  tagType,null,null);
   }
   
   ```

