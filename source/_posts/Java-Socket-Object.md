title: Java Socket中的ObjectOutputStream和ObjectInputStream
date: 2015-11-17 17:16:14
categories:
- Java
- Socket
tags: Java
---

Java Socket中的ObjectOutputStream和ObjectInputStream

在写Socket通讯程序时出现的bug     
获取socket对象的InputStream和OutputStream封装为    
ObjectInputStream和ObjectOutputStream,启动Server和Client发送序列化的对象     
会发现Client和Server连上了,但是一直没有传输数据     
后来发现问题    
如果Server端首先建立的是      
ObjectInputStream = new ObjectInputStream(socket.getInputStream()); 
那么Client绝对不能先建立 ObjectInputStream  后建立ObjectOutputStream
否则程序会一直阻塞...
列出所有情况,只限封装为Object流的情况    
其他的三种情况都是可以的     
(Server : 1st Output 2nd Input  Client :1st Output 2nd Input)  <<----允许
(Server : 1st Output 2nd Input  Client :1st Input 2nd Output)  <<----允许
(Server : 1st Input 2nd Output  Client :1st Output 2nd Input)  <<----允许
(Server : 1st Input 2nd Output  Client :1st Input 2nd Output)  <<----只有这种情况不允许

备注:     
使用不带缓冲区的流,直接写就好了,带缓冲区的,先刷一刷flush