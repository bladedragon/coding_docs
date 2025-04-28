# Netty笔记

## NIO

IO固有缺点

1. **线程资源受限**：线程是操作系统中非常宝贵的资源，同一时刻有大量的线程处于阻塞状态是非常严重的资源浪费，操作系统耗不起
2. **线程切换效率低下**：单机 CPU 核数固定，线程爆炸之后操作系统频繁进行线程切换，应用性能急剧下降。
3. **IO读写面向流**，数据读写是以字节流为单位。



NIO的改进

1. 一个线程检测多个连接
2. 因为线程数量降低，因此切换效率提高
3. 面向buffer，随意读取里面任何一个字节数据，不需要你自己缓存数据，这一切只需要移动读写指针即可



## 客户端和服务端的启动流程





## byteBuf

![image-20200525175312309](documents/Netty笔记/image-20200525175312309.png)

三部分

+ 已经丢弃的字节
+ 可读字节
+ 可写字节

两指针

+ `readIndex`
+ `writeIndex`
+ `capacity`
+ `maxCapacity`：写数据的时候超过capacity会扩容到`maxCapacity`，再超过就报错

·

### 底层

由于 Netty 使用了堆外内存，而堆外内存是不被 jvm 直接管理的，也就是说申请到的内存无法被垃圾回收器直接回收，所以需要我们手动回收。有点类似于c语言里面，申请到的内存必须手工释放，否则会造成内存泄漏。



> release() 与 retain()

Netty 的 ByteBuf 是通过引用计数的方式管理的，如果一个 ByteBuf 没有地方被引用到，需要回收底层内存。默认情况下，当创建完一个 ByteBuf，它的引用为1，然后每次调用 retain() 方法， 它的引用就加一， release() 方法原理是将引用计数减一，减完之后如果发现引用计数为0，则直接回收 ByteBuf 底层的内存。





### 读写操作



> slice()、duplicate()、copy()



1. `slice() `方法从原始 ByteBuf 中截取一段，这段数据是从 readerIndex 到 writeIndex，同时，返回的新的 ByteBuf 的最大容量 maxCapacity 为原始 ByteBuf 的 readableBytes()
2. `duplicate()` 方法把整个 ByteBuf 都截取出来，包括所有的数据，指针信息
3. ` copy()` 会直接从原始的 ByteBuf 中拷贝所有的信息，包括读写指针以及底层对应的数据，因此，往 copy() 返回的 ByteBuf 中写数据不会影响到原始的 ByteBuf

### 问题

+ 如何选择堆内堆外缓存
+ 是否会出现循环引用的情况



## 通信协议和序列化



<img src="documents/Netty笔记/image-20200525213131641.png" alt="image-20200525213131641" style="zoom:150%;" />





## pipline和channelHandler

> 责任链模式  







## ChannelHandler生命周期













## 服务端启动流程



> b.bind(8888).sync();

bind()

+ init
+ register
+ doBind0





## 高级特性

### 流量整型

java