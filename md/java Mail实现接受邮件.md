## 				java Mail实现接受邮件

#### 注意事项：

1. mail.jar必须单独加入工程，idk中并没有包含mail.jar的实现
2. 注意接受邮件时一般使用的是pop3协议。如有特殊协议，请进行更改
3. 使用公用邮箱进行邮件接受，必须加入正确的用户名和密码，否则打开会话（Session）进行连接的时候会失败。

#### 具体代码实现：

``` java
		String protocol = "pop3";//接受邮件使用pop3协议
        boolean isSSL = true;
        String host = "pop.163.com";//邮件服务器
        int port = 995;//端口
        String username = "rose@163.com";//邮件服务器的账户
        String password = "rose";//邮件服务器密码
		/**
        **装载邮件服务的相关属性
        **/
        Properties props = new Properties();
        props.put("mail.pop3.ssl.enable", isSSL);//SSL连接
        props.put("mail.pop3.host", host);//邮件服务器
        props.put("mail.pop3.port", port);//邮件服务器端口
 
        Session session = Session.getDefaultInstance(props);//生成连接回话
 
        Store store = null;
        Folder folder = null;
        try {
            store = session.getStore(protocol);
            store.connect(username, password);//开始连接
 
            folder = store.getFolder("INBOX");
            folder.open(Folder.READ_ONLY);//只读格式打开
 
            int size = folder.getMessageCount();//总信息条数
            Message message = folder.getMessage(size);
 
            String from = message.getFrom()[0].toString();
            String subject = message.getSubject();
            Date date = message.getSentDate();
 
            System.out.println("From: " + from);
            System.out.println("Subject: " + subject);
            System.out.println("Date: " + date);
        } catch (NoSuchProviderException e) {
            e.printStackTrace();
        } catch (MessagingException e) {
            e.printStackTrace();
        } finally {
            try {
                if (folder != null) {
                    folder.close(false);
                }
                if (store != null) {
                    store.close();
                }
            } catch (MessagingException e) {
                e.printStackTrace();
            }
        }
 
        System.out.println("接收完毕！");
    }
```

