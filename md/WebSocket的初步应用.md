## WebSocket实现由服务器端向客户端推送消息（B/S架构）

前提：

1. WebSocket是在H5下实现生成的，因此只有支持H5协议的浏览器才能够支持WebSocket.可利用代码：
   
   ``` javascript
   if(window.WebSocket)
   ```
   
2. 目前WebSocket协议仍在修订中，因此各个版本的支持变化非常之大。本页面代码的实现是在jdk7和tomcat7.0.57版本之下，如需转移到其他的版本之中需注意兼容性问题。
   
3. 具体服务器端代码实现
   
   ``` java
   package com.jiangbo212.websocket;
   
   import java.io.IOException;
   import java.nio.ByteBuffer;
   import java.nio.CharBuffer;
   import java.util.ArrayList;
   import java.util.List;
   
   import javax.servlet.http.HttpServletRequest;
   
   import org.apache.catalina.websocket.MessageInbound;
   import org.apache.catalina.websocket.StreamInbound;
   import org.apache.catalina.websocket.WebSocketServlet;
   import org.apache.catalina.websocket.WsOutbound;
   
   @SuppressWarnings("deprecation")
   public class DemoWebSocket extends WebSocketServlet{//注意继承关系
   	
   	/**
   	 * webSocket初步使用
   	 */
   	private static final long serialVersionUID = 1L;
   	private static List<MyMessageInbound> mmiList = new ArrayList<MyMessageInbound>();
   
   	@Override
   	protected StreamInbound createWebSocketInbound(String arg0,
   			HttpServletRequest arg1) {
   		// TODO Auto-generated method stub
   		return new MyMessageInbound();
   	}
   	
   	private class MyMessageInbound extends MessageInbound {
           WsOutbound myoutbound;
   
           @Override
           public void onOpen(WsOutbound outbound) {//客户端初次连接的时候执行
               try {
                   System.out.println("Open Client.");
                   this.myoutbound = outbound;
                   mmiList.add(this);
                   outbound.writeTextMessage(CharBuffer.wrap("Hello!"));
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
   
           @Override
           public void onClose(int status) {//客户端结束的时候执行
               System.out.println("Close Client.");
               mmiList.remove(this);
           }
   
           @Override
           public void onTextMessage(CharBuffer cb) throws IOException {//接受客户端传过来的文本信息
               System.out.println("Accept Message : " + cb);
               for (MyMessageInbound mmib : mmiList) {
                   CharBuffer buffer = CharBuffer.wrap(cb);
                   mmib.myoutbound.writeTextMessage(buffer);//向客户端返回数据
                   mmib.myoutbound.flush();
               }
           }
   
           @Override
           public void onBinaryMessage(ByteBuffer bb) throws IOException {//接受客户端传过来的二进制信息
           }
       }
   
   }
   
   ```
   
4. 具体客户端代码实现
   
   ``` javascript
   var ws = new WebSocket("ws://localhost:8080/webSocket/wsServlet");//初始化WebSocket
   ws.onopen = function(){//初次连接
   };
   ws.onmessage = function(message){//接受服务器端返回的信息
   document.getElementById("chatlog").textContent += message.data + "\n";
   };
   function postToServer(){
   ws.send(document.getElementById("msg").value);//发送文本信息到服务器端
   document.getElementById("msg").value = "";
   }
   function closeConnect(){
   ws.close();
   }
   ```