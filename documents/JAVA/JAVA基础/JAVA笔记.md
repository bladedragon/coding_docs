

配合[JAVAGUIDE](https://snailclimb.gitee.io/javaguide)  和[interview](https://qinxuewu.github.io/docs/#/2019/深入理解JVM)看



### [Jdk1.7 与 jdk1.8的区别](https://www.cnblogs.com/aspirant/p/8617201.html)

[Java 8 新特性最佳指南](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247484744&idx=1&sn=9db31dca13d327678845054af75efb74&chksm=cea24a83f9d5c3956f4feb9956b068624ab2fdd6c4a75fe52d5df5dca356a016577301399548&token=1082669959&lang=zh_CN&scene=21#wechat_redirect)

[其他版本](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jdk-new-features/new-features-from-jdk8-to-jdk14.md#java9)

![img](https://upload-images.jianshu.io/upload_images/32047-8fd2bc912471b91f.png)



## 变量

一、一个整数字面值是long类型，否则就是int类型。 建议使用**大写的L**



**二、静态变量和实例变量区别？**

静态变量存在方法区，属于类所有，实例变量存储在堆中，引用存在当前线程栈



#### 三、java 创建对象的几种方式

1. 采用new
2. 通过反射gi
3. 采用clone(实现clonable接口然后重写clone方法)
4. 通过序列化机制(实现序列化接口然后流式输出)



#### 四、字符串常量池



> **`System.arraycopy 对于数组是深拷贝，对于数组内的对象是浅拷贝。因为操作的传入参数是数组，那么回归本意，效果是深复制。`**
>
> [由 System.arraycopy 引发的巩固：对象引用 与 对象 的区别](https://www.cnblogs.com/linguanh/p/7650925.html)

**设计思想**

- 为字符串开辟一个字符串常量池，类似于缓存区
- 创建字符串常量时，首先坚持字符串常量池是否存在该字符串
- 存在该字符串，返回引用实例，不存在，实例化该字符串并放入池中

实现基础

+ 字符串是不变的，不用担心数据冲突进行共享
+ 实例创建的全局字符串常量池中有一个表，为池中唯一的字符串对象维护一个引用，因此不会被垃圾回收

![](http://cdn.zblade.top/qiniu_img/1353903-20180906112833927-1544152281.png)

常量池存放在方法去，和堆区都属于线程共享的

创建对象过程：`String str4 = new String(“abc”) `

1. 在常量池中查找是否有“abc”对象

2. - 有则返回对应的引用实例
   - 没有则创建对应的实例对象

3. 在堆中 new 一个 String("abc") 对象

4. 将对象地址赋值给str4,创建一个引用



例：

```java
String str1 = new String("A"+"B") ;// 会创建多少个对象? 
String str2 = new String("ABC") + "ABC" ;// 会创建多少个对象?

/** str1：
字符串常量池："A","B","AB" : 3个
堆：new String("AB") ：1个
引用： str1 ：1个
总共 ： 5个
*/

/**str2 ：
字符串常量池："ABC" : 1个
堆：new String("ABC") ：1个
引用： str2 ：1个
总共 ： 3个
*/
```



> String.intern()

intern()方法会首先从常量池中查找是否存在该常量值,如果常量池中不存在则现在常量池中创建,如果已经存在则直接返回.


五、各类型字节数

![](http://cdn.zblade.top/qiniu_img/985411-20191027220414112-1940633335.png)



六、String,StringBuffer和StringBuilder区别

+ String是字符串常量,final修饰;
+ StringBuffer字符串变量(线程安全);
+ StringBuilder 字符串变量(线程不安全).

StringBuffer是对对象本身操作,而不是产生新的对象,因此在有大量拼接的情况下,我们建议使用StringBuffer.

StringBuffer是线程安全的可变字符串,其内部实现是可变数组.

StringBuilder是jdk 1.5新增的,其功能和StringBuffer类似,但是非线程安全.因此,在没有多线程问题的前提下,使用StringBuilder会取得更好的性能.



#### 七、什么是编译器常量?使用它有什么风险?

公共静态不可变（public static final ）变量也就是我们所说的编译期常量，这里的 public 可选的。实际上这些变量在编译时会被替换掉，因为编译器知道这些变量的值，并且知道这些变量在运行时不能改变。

这种方式存在的一个问题是你使用了一个内部的或第三方库中的公有编译时常量，但是这个值后面被其他人改变了，但是你的客户端仍然在使用老的值，甚至你已经部署了一个新的jar。为了避免这种情况，当你在更新依赖 JAR 文件时，确保重新编译你的程序。



八、byte[] 转String 可以使用String的构造器，但是注意使用正确编码



#### 九、匿名内部类传入的参数为什么需要final

传入参数是定义在方法内部的，因此属于方法的局部变量，意味着这个变量无法共享，匿名内部类无法直接访问这个变量，只能通过值传递的方式传递到匿名内部类中，如果不加final，内部类修改该变量就会导致内外环境变量不同步。

不用final的话可以将变量移到方法的外部，上升成局部变量

## 基本特性



**面向对象三特性**

> 封装,继承,多态

**多态的好处**

+ 可替换性
+ 可扩充性：增加新的子类不影响已经存在的类结构
+ 接口性：多态是超累通过方法签名，向子类提供一个公共接口
+ 灵活性
+ 简化性



**如何实现多态**

1. 接口实现
2. 继承父类重写方法

抽象类意义

+ 为其他子类提供一个公共的类型
+ 封装子类中重复定义的内容
+ 定义抽象方法,子类虽然有不同的实现,但是定义是一致的



#### 一、抽象类和接口的区别

[抽象类和接口区别](https://www.cnblogs.com/cocoxu1992/p/10647676.html)

+ 一个类只能继承一个类，但是可以实现多个接口
+ 接口类只能做方法申明，抽象类可以做方法申明也可以做方法实现
+ 接口类定义的变量是公共的静态常量，抽象类中的变量是普通变量
+ 
+ 抽象类的抽象方法必须全部被子类实现，如果没有实现，子类只能是抽象类；同样实现一个接口时不实现全部方法，该类只能是抽象类
+ 抽象方法只能申明，不能实现，接口是设计的结果，抽象类是重构的结果
+ ==抽象类中可以没有抽象方法==，抽象方法要被实现，不能是静态的也不能是私有的
+ 接口可继承接口，类只能单根继承
+ ==接口中的变量会被隐式地指定为public static final变量==



语法层级区别

1. 抽象类提供给成员方法的实现细节，接口中只存publi abstract方法
2. 抽象类中的成员变量可以是各种类型的，接口中的成员变量只能是public static final
3. 接口中不能有静态代码块，抽象类中可以有
4. 一个类只能继承一个抽象类，但是可以实现多个接口



设计层次区别

1. 抽象列是对类整体抽象，接口事对局部的行为进行抽象
2. 抽象类是模板式设计，接口是行为规范

![img](https://img-blog.csdnimg.cn/2019040312445614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI5NDk4NA==,size_16,color_FFFFFF,t_70)

**二、short类型在进行运算时会自动提升为int类型**

三、final,finalize和finally的不同之处

+ final 是一个修饰符，可以修饰变量、方法和类。如果 final 修饰变量，意味着该变量的值在初始化后不能被改变。
+ finalize 方法是在对象被回收之前调用的方法，给对象自己最后一个复活的机会，但是什么时候调用 finalize 没有保证。
+ finally 是一个关键字，与 try 和 catch 一起用于异常的处理。finally 块一定会被执行，无论在 try 块中是否有发生异常。

**final的用法**

1.被final修饰的类不可以被继承
2.被final修饰的方法不可以被重写
3.被final修饰的变量不可以被改变.如果修饰引用,那么表示引用不可变,引用指向的内容可变.
4.被final修饰的方法,JVM会尝试将其**内联**,以提高运行效率
5.被final修饰的常量,在**编译阶段**会存入**常量池**中.

编译器对final域要遵守的两个重排序规则：

1.在构造函数内对一个final域的写入,与随后把这个被构造对象的引用赋值给一个引用变量,这两个操作之间不能重排序.
2.初次读一个包含final域的对象的引用,与随后初次读这个final域,这两个操作之间不能重排序.





四、如何正确的退出多层嵌套循环.

1. 使用标号label和break;
2. 通过在外层循环中添加标识符



#### 五、深拷贝和浅拷贝的区别是什么?

浅拷贝：被复制对象的所有变量都含有与原来的对象相同的值，而所有的对其他对象的引用仍然指向原来的对象。换言之，**浅拷贝仅仅复制所考虑的对象，而不复制它所引用的对象**。

深拷贝：被复制对象的所有变量都含有与原来的对象相同的值，而那些引用其他对象的变量将指向被复制过的新对象，而不再是原有的那些被引用的对象。换言之，**深拷贝把要复制的对象所引用的对象都复制了一遍**。



`值传递和引用传递和拷贝没有关系`



六、static都有哪些用法?

**静态变量和静态方法**：也就是被static所修饰的变量/方法都属于类的静态资源,类实例所共享.

**初始化操作，静态块：**

```java
public calss PreCache{
 static{
 //执行相关操作

    }
}
```

**static也多用于修饰内部类**

**静态导包**：import static是在JDK 1.5之后引入的新特性,可以用来指定导入某个类中的静态资源,并且不需要使用类名.资源名,可以直接使用资源名



#### 七、进程、线程相关

**进程,线程,协程之间的区别**

+ 进程是==程序运行和资源分配==的基本单位,一个程序至少有一个进程,一个进程至少有一个线程。进程在执行过程中拥有==独立的内存单元==,而多个线程共享内存资源,==减少切换次数,从而效率更高.==
+ 线程是进程的一个实体,是==cpu调度和分派的基本单位==,是比程序更小的能独立运行的基本单位.同一进程中的多个线程之间可以并发执行.
+ 协程，是一种比线程更加轻量级的存在，协程不==是被操作系统内核所管理==，而==完全是由程序所控制==（也就是在用户态执行）
+ **协程在子程序内部是可中断的，然后转而执行别的子程序，在适当的时候再返回来接着执行**。由程序自身控制，没有线程切换，执行效率高。
+ 因为只有一个线程，也不存在同时写变量冲突，**因此在协程中控制共享资源不加锁**

##### 协程

程序调用协程与调用函数不一样的是，协程可以通过暂停或者阻塞的方式将协程的执行挂起，而其它协程可以继续执行。**这里的挂起只是在程序中（用户态）的挂起**，同时将**代码执行权转让给其它协程使用**，待获取执行权的协程执行完成之后，将从挂起点唤醒挂起的协程。 协程的挂起和唤醒是通过一个调度器来完成的。

**相比线程，协程少了由于同步资源竞争带来的CPU上下文切换，I/O密集型的应用比较适合使用，特别是在网络请求中，有较多的时间在等待后端响应，协程可以保证线程不会阻塞在等待网络响应中，充分利用了多核多线程的能力**。而对于CPU密集型的应用，由于在多数情况下CPU都比较繁忙，协程的优势就不是特别明显了。



**java为什么坚持用多线程不用协程?**

1. 一个tomcat上的woker线程池的最大线程数一般会配置为50～500之间（目前springboot的默认值给的200）,实际内存增幅对整体性能影响不大
2. 使用netty，NIO+worker thread可以大致等于一套协程
3. 通过线程池可以很好创建销毁线程开销
4. 线程的切换实际上只会发生在那些“活跃”的线程上。java web中大量存在的是IO请求挂起的线程，不会参与OS的线程切换



**守护线程和非守护线程区别**

+ 程序运行完毕,jvm会等待非守护线程完成后关闭,但是jvm不会等待守护线程.守护线程最典型的例子就是GC线程



**多线程上下文切换**

多线程的上下文切换是指CPU控制权由一个已经正在运行的线程切换到另外一个就绪并等待获取CPU执行权的线程的过程。



**java.lang.Runnable比java.lang.Thread优势？**

1. Java不支持多继承.因此继承Thread类就代表这个子类不能扩展其他类.而实现Runnable接口的类还可能扩展另一个类.
2. 类可能只要求可执行即可,因此继承整个Thread类的开销过大.



**Thread类中的start()和run()方法有什么区别?**

start()方法被用来启动新创建的线程，而且start()内部调用了run()方法，这和直接调用run()方法的效果不一样。==当你调用run()方法的时候，只会是在原来的线程中调用，没有新的线程启动==，start()方法才会启动新线程。



**怎么检测一个线程是否持有对象监视器**

Thread类提供了一个`·holdsLock(Object obj)`方法，当且仅当对象obj的监视器被某条线程持有的时候才会返回true，注意这是一个`static`方法，这意味着”某条线程”指的是当前线程。

**对象监视器**

监视器是==一种同步结构，它基于互斥锁==，允许线程同时互斥（使用锁）和协作，·

当一个线程需要数据在某一个状态下它才能执行，那么另一个线程负责将数据改变到此状态，

常见的如生产者/消费者的问题，当读线程需要缓冲区处于“不空”的状态它才可以从缓冲区中读取任何数据，如果它发现缓冲区为空，则进入wait-set等待。待写线程用数据填充缓冲区，再通知读线程进行读取。这种机制被称为“**Wait and Notify**”或“**Signal and Continue**”





**Callable接口中的call()方法是有返回值的，是一个泛型，和Future、FutureTask配合可以用来获取异步执行的结果**。



**什么导致线程阻塞**

> 阻塞指的是暂停一个线程的执行以等待某个条件发生（如某资源就绪）

**sleep()**：被用在等待某个资源就绪的情形：测试发现条件不满足后，让线程阻塞一段时间后重新测试，直到条件满足为止

**suspend() 和 resume()**：suspend()使得线程进入阻塞状态，并且不会自动恢复，必须其对应的resume() 被调用，才能使得线程重新进入可执行状态。

**yield()**：使当前线程放弃当前已经分得的CPU 时间，==但不使当前线程阻塞==，即线程仍处于可执行状态，随时可能再次分得 CPU 时间

**wait() 和 notify()**：wait() 使得线程进入阻塞状态，它有两种形式，一种允许指定以==毫秒==为单位的一段时间作为参数，另一种没有参数，前者当对应的 notify() 被调用或者超出指定时间时线程重新进入可执行状态，后者则必须对应的 notify() 被调用.



**wait(),notify()和suspend(),resume()之间的区别**

+ **wait(),notify()**属于`Object`类，所有对象都拥有这一对方法；（因为锁是任何对象具有的）其他方法属于`thread`类。其他方法阻塞时都不会释放占用的锁（如果占用了的话），这一对会释放占用锁
+ **wait(),notify()**必须在 `synchronized `方法或块中调用，其他所有方法可在任何位置调用。（因为在`synchronized `方法或块中当前线程才占有锁，才有锁可以释放。同样的道理，调用这一对方法的对象上的锁必须为当前线程所拥有，这样才有锁可以释放）如果没有放在同步方法或同步块中，会报`IllegalMonitorStateException`



**关于 wait() 和 notify() 方法最后再说明两点：**
第一：调用` notify()` 方法导致解除阻塞的线程是从因调用该对象的 `wait()` 方法而阻塞的线程中==随机选取==的，我们无法预料哪一个线程将会被选择，所以编程时要特别小心，避免因这种不确定性而产生问题。

第二：除了 `notify()`，还有一个方法 `notifyAll() `也可起到类似作用，唯一的区别在于，调用 `notifyAll() `方法将把因调用该对象的 `wait() `方法而阻塞的所有线程一次性全部解除阻塞。当然，只有获得锁的那一个线程才能进入可执行状态。

> 特别注意：uspend() 方法和不指定超时期限的 wait() 方法的调用都可能产生死锁



**wait()方法和notify()/notifyAll()方法在放弃对象监视器时有什么区别**

wait()方法==立即释放对象监视器==，notify()/notifyAll()方法则会==等待线程剩余代码执行完==毕才会放弃对象监视器。



**标准使用wait示例**

```java
 synchronized (obj) {
 while (condition does not hold)
      obj.wait(); // (Releases lock, and reacquires on wakeup)
 ... // Perform action appropriate to condition
 }
```

#### 八、产生死锁的条件

1. ==互斥条件==：一个资源每次只能被一个进程使用。
2. ==请求与保持条件：==一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. ==不剥夺条件:进程已获得的资源==，在末使用完之前，不能强行剥夺。
4. ==循环等待条件==:若干进程之间形成一种头尾相接的循环等待资源关系。



**synchronized和ReentrantLock的区别·**

`synchronized`是和if、else、for、while一样的==关键字==，`ReentrantLock`是==类==，这是二者的本质区别。既然`ReentrantLock`是类，那么它就提供了比`synchronized`更多更灵活的特性，可以被继承、可以有方法、可以有各种各样的类变量，`ReentrantLock`比`synchronized`的扩展性体现在几点上：
（1）`ReentrantLock`可以对获取锁的等待时间进行设置，这样就==避免了死锁==
（2）`ReentrantLock`可以获取各种锁的信息
（3）`ReentrantLock`可以灵活地实现==多路通知==
另外，二者的锁机制其实也是不一样的:`ReentrantLock`底层调用的是`Unsafe`的`park`方法加锁，`synchronized`操作的应该是对象头中`mark ``word`.

[Java对象结构与锁实现原理及MarkWord详解](https://blog.csdn.net/scdn_cp/article/details/86491792)



**一个线程如果出现了运行时异常怎么办?**

如果这个异常没有被捕获的话，这个线程就停止执行了。另外重要的一点是：如果这个线程持有某个某个对象的监视器，那么这个对象监视器会被立即释放



**线程共享数据方法**

通过在线程之间共享对象就可以了，然后通过wait/notify/notifyAll、await/signal/signalAll进行唤起和等待，比方说阻塞队列BlockingQueue就是为线程之间共享数据而设计的





#### 九、java中锁种类

锁提供了两种主要特性：**互斥（mutual exclusion） 和可见性（visibility）**

锁的状态

+ **自旋锁**

  ==共享数据的锁定状态==只会持续很短的时间，为了这一小段时间而去挂起和恢复线程有点浪费，所以这里就做了一个处理，让后面请求锁的那个线程在稍等一会，但是不放弃处理器的执行时间，看看持有锁的线程能否快速释放

  ==为了让线程等待，所以需要让线程执行一个忙循环也就是自旋操作==

  在jdk6之后，引入了自适应的自旋锁，也就是等待的时间不再固定了，而是由上一次在同一个锁上的自旋时间及锁的拥有者状态来决定

  ==好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。==

+ **偏向锁**

  目的是消除数据在无竞争情况下的同步原语。进一步提升程序的运行性能。

  ==这个锁会偏向第一个获得他的线程，如果接下来的执行过程中，该锁没有被其他线程获取，则持有偏向锁的线程将永远不需要再进行同步。==

  偏向锁可以提高带有同步但无竞争的程序性能，也就是说他并不一定总是对程序运行有利，如果程序中大多数的锁都是被多个不同的线程访问，那偏向模式就是多余的，在具体问题具体分析的前提下，可以考虑是否使用偏向锁。

+ **轻量级锁/重量级锁**

  为了减少获得锁和释放锁带来的性能消耗

  在Java SE1.6里锁一共有四种状态，**无锁状态**，**偏向锁状态**，**轻量级锁状态**和**重量级锁状态**，它会随着竞争情况逐渐升级。==锁可以升级但不能降级==，意味着偏向锁升级成轻量级锁后不能降级成偏向锁

  四种锁的状态是通过对象监视器在对象头中的字段来表明的。

  **偏向锁**是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。

  **轻量级锁**是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。

  **重量级锁**是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。==重量级锁会让他申请的线程进入阻塞，性能降低。==



锁的种类

+ **独享锁/共享锁**：独享锁是指==该锁一次只能被一个线程所持有==；共享锁是指==该锁可被多个线程所持有==。

  对于`Java ReentrantLock而言`，其是**独享锁**。但是对于`Lock`的另一个实现类`ReadWriteLock`，其**读锁是共享锁，其写锁是独享锁**。

  对于`Synchronized`而言，当然是**独享锁**。

  读锁的共享锁可保证并发读是非常高效的，读写，写读，写写的过程是互斥的。

  独享锁与共享锁也是通过`AQS`来实现的，通过实现不同的方法，来实现独享或者共享。

+ **互斥锁/读写锁**:即独享锁和共享锁的具体实现

+ **可重入锁**：又名**递归锁**，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁

  对于`Java ReetrantLock`而言，从名字就可以看出是一个重入锁，其名字是`Re entrant Lock` 重新进入锁。

  对于`Synchronized`而言，也是一个可重入锁。可重入锁的一个好处是==可一定程度避免死锁。==

  ```java
  synchronized void setA() throws Exception{
  　　Thread.sleep(1000);
  　　setB();
  }
  //如果不是可重入锁的话，setB可能不会被当前线程执行，可能造成死锁。
  synchronized void setB() throws Exception{
  　　Thread.sleep(1000);
  }
  ```

 + **公平锁和非公平锁**：

   **公平锁**是指多个线程按照==申请锁的顺序==来获取锁。

   **非公平锁**是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能，会造成优先级反转或者饥饿现象。

   对于`Java ReetrantLock`而言，通过构造函数指定该锁是否是公平锁，默认是**非公平锁**。非公平锁的优点在于==吞吐量比公平锁大==。

   对于`Synchronized`而言，也是一种**非公平锁**。由于其并不像`ReentrantLock`是通过`AQS`的来实现线程调度，所以==并没有任何办法使其变成公平锁==。



锁的设计

+ **乐观锁/悲观锁**：主要是指看待==并发同步==的角度

  *==悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。==*

  **乐观锁**：顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，==但是在更新的时候会判断一下在此期间别人有没有去更新这个数据==，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，在Java中`java.util.concurrent.atomic`包下面的==原子变量类==就是使用了乐观锁的一种实现方式==CAS(Compare and Swap)== 比较并交换)实现的。

  乐观锁在Java中的使用，是==无锁编程==，常常采用的是==CAS算法==，典型的例子就是原子类，通过==CAS自旋==实现原子操作的更新。

  + *数据版本机制*

    实现数据版本一般有两种，第一种是使用版本号，第二种是使用时间戳。以版本号方式为例。

  版本号方式：一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数，当数据被修改时，version值会加一。当线程A要更新数据值时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值为当前数据库中的version值相等时才更新，否则重试更新操作，直到更新成功。
    核心SQL代码：

    ```sql
    update table set xxx=#{xxx}, version=version+1 where id=#{id} and version=#{version};
    ```

  + *CAS操作*

    CAS（Compare and Swap 比较并交换），当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，==失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。==

    CAS操作中包含三个操作数——==**需要读写的内存位置(V)**==、==**进行比较的预期原值(A)**==和==**拟写入的新值(B)**==。如果内存位置V的值与预期原值A相匹配，那么处理器会自动将该位置值更新为新值B，否则处理器不做任何操作。

    以`java.util.concurrent`包中的`AtomicInteger`为例，看一下在不使用锁的情况下是如何保证线程安全的。主要理解`getAndIncrement`方法，该方法的作用相当于++i操作

    ```java
    public class AtomicInteger extends Number implements java.io.Serializable{
    　　private volatile int value;   //CAS中必须使用volatile变量，保证拿到的变量时主内存中最新值
    　　public final int get(){
    　　　　return value;
    　　}
    
    　 public final int getAndIncrement(){
    　　　　for (;;){
    　　　　　　int current = get();
    　　　　　　int next = current + 1;
    　　　　　　if (compareAndSet(current, next)) //获取值后查看值是否更新
    　　　　　　return current;
    　　　　}
    　　}
     
    　　public final boolean compareAndSet(int expect, int update){
    　　　　return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    　　}
    }
    ```

    

  **悲观锁**：总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁。比如Java里面的同步原语`synchronized`关键字的实现就是悲观锁。

  悲观锁在Java中的使用，就是利用各种锁

  + 在对任意记录进行修改前，先尝试为该记录加上**排他锁（exclusive locking）。**
  + 如果加锁失败，说明该记录正在被修改，那么当前查询可能要==等待==或者==抛出异常==。具体响应方式由开发者根据实际需要决定。
  + 如果成功加锁，那么就可以对记录做修改，事务完成后就会解锁了。
  + 期间如果有其他对该记录做修改或加排他锁的操作，都会==等待==我们解锁或直接抛出异常。

+ **分段锁**：对于`ConcurrentHashMap`而言，其并发的实现就是==通过分段锁的形式==来实现高效的并发操作

  以`ConcurrentHashMap`来说一下分段锁的含义以及设计思想，`ConcurrentHashMap`中的分段锁称为`Segment`，它即类似于`HashMap`（JDK7和JDK8中`HashMap`的实现）的结构，即==内部拥有一个`Entry`数组，数组中的每个元素又是一个链表；同时又是一个`ReentrantLock`（`Segment`继承了`ReentrantLock`）==。

  当需要`put`元素的时候，并不是对整个`hashmap`进行加锁，而是先通过`hashcode`来知道他要放在哪一个分段中，然后对这个分段进行加锁，所以当多线程`put`的时候，只要不是放在一个分段中，就实现了真正的并行的插入

  但是，在统计`size`的时候，可就是获取`hashmap`全局信息的时候，就需要获取所有的分段锁才能统计。

  分段锁的设计目的是==细化锁的粒度==，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。



##### **锁的使用**

[java锁的使用和种类](https://www.cnblogs.com/hustzzl/p/9343797.html)

预备知识

1. AQS：`AbstractQueuedSynchronized ` 抽象队列式的同步器，AQS定义了一套==多线程访问共享资源的同步器框架==，许多同步类实现都依赖于它，如常用的`ReentrantLock`/`Semaphore`/`CountDownLatch`…

   ![](http://cdn.zblade.top/qiniu_img/721070-20170504110246211-10684485.png)AQS维护了一个`volatile int state`(代表==共享资源)==和一个`FIFO`线程等待队列（==多线程争用资源被阻塞时会进入此队列==）。

   state的访问方式：

   ```java
   getState();
   setState();
   compareAndSetState();
   ```

   AQS定义两种资源共享方式：**`Exclusive`**（独占，只有一个线程能执行，如`ReentrantLock`）和**`Share`**（共享，多个线程可同时执行，如`Semaphore`/`CountDownLatch`）。

   自定义同步器实现时主要实现以下几种方法

   ```java
   isHeldExclusively()//该线程是否正在独占资源。只有用到condition才需要去实现它。
   tryAquire(int)//独占方式。尝试获取资源，成功则返回true，失败则返回false。
   tryRelease(int)//独占方式。尝试释放资源，成功则返回true，失败则返回false。
   tryAcquireShared(int)//共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
   tryReleaseShared(int)//共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。
   ```

   以`ReentrantLock`为例，state初始化为0，表示未锁定状态。A线程lock()时，会调用tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其他线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这就是可重入的概念。但要注意，获取多少次就要释放多少次，这样才能保证state是能回到零态的。

   再以`CountDownLatch`为例，任务分为N个子线程去执行，state为初始化为N（注意N要与线程个数一致）。这N个子线程是并行执行的，每个子线程执行完后countDown()一次，state会CAS减1。等到所有子线程都执行完后（即state=0），会unpark()主调用线程，然后主调用线程就会await()函数返回，继续后余动作。

> 注 ：AQS也支持自定义同步器同时实现独占和共享两种方式，如`ReentrantReadWriteLock`。



#### 十、 ThreadLocal

线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享，Java提供`ThreadLocal`类来支持线程局部变量，是一种实现线程安全的方式。

但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。==任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。==

作用：简单说`ThreadLocal`就是一种以==空间换时间==的做法在每个Thread里面维护了一个`ThreadLocal.ThreadLocalMap`把数据进行隔离，数据不共享，自然就没有线程安全方面的问题了.

![image-20200229114104244](documents/JAVA笔记/image-20200229114104244.png)

```
内部hash方法和hashtable类似

为什么要弱引用？
假如每个key都强引用指向threadlocal，也就是上图虚线那里是个强引用，那么这个threadlocal就会因为和entry存在强引用无法被回收！造成内存泄漏 ，除非线程结束，线程被回收了，map也跟着回收。

当把threadlocal实例置为null以后,没有任何强引用指向threadlocal实例,所以threadlocal将会被gc回收.
map里面的value却没有被回收.而这块value永远不会被访问到了. 所以存在着内存泄露,
因为存在一条从current thread连接过来的强引用.
只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收.
```





十一、生产者消费者模型

**作用：**

（1）通过==**平衡生产者的生产能力和消费者的消费能力来提升整个系统的运行效率**==，这是生产者消费者模型最重要的作用
（2）**解耦**，这是生产者消费者模型附带的作用，解耦意味着生产者和消费者之间的联系少，联系越少越可以独自发展而不需要收到相互的制约

写一个生产者-消费者队列方法

可以通过阻塞队列实现,也可以通过wait-notify来实现.

```java
//阻塞队列实现生产者消费者模型
//消费者
public class Producer implements Runnable{
 private final BlockingQueue<Integer> queue;
 public Producer(BlockingQueue q){
 this.queue=q;
 }
    
 @Override
 public void run() {
 try {
 while (true){

                Thread.sleep(1000);//模拟耗时
                queue.put(produce());
            }
        }catch (InterruptedException e){

        }
    }
 
 private int produce() {

 int n=new Random().nextInt(10000);
 System.out.println("Thread:" + Thread.currentThread().getId() + " produce:" + n);
 return n;
    }
}

//消费者
public class Consumer implements Runnable {
 private final BlockingQueue<Integer> queue;
    
 public Consumer(BlockingQueue q){
 this.queue=q;
    }

 @Override
 public void run() {
 while (true){
 try {
                Thread.sleep(2000);//模拟耗时
                consume(queue.take());
            }catch (InterruptedException e){
            }
        }
    }

 private void consume(Integer n) {

        System.out.println("Thread:" + Thread.currentThread().getId() + " consume:" + n);
 
    }
}

//测试
public class Main {
 
 public static void main(String[] args) {

        BlockingQueue<Integer> queue=new ArrayBlockingQueue<Integer>(100);
        Producer p=new Producer(queue);
        Consumer c1=new Consumer(queue);
        Consumer c2=new Consumer(queue);

 new Thread(p).start();
 new Thread(c1).start();
 new Thread(c2).start();
    }
}
```



十二、java中的线程调度算法

**抢占式**。一个线程用完CPU之后，操作系统会根据==线程优先级、线程饥饿情况==等数据算出一个==总的优先级==并分配下一个时间片给某个线程执行。

十三、Thread.sleep(0)的作用是什么

由于Java采用抢占式的线程调度算法，因此可能会出现某条线程常常获取到CPU控制权的情况，为了让某些优先级比较低的线程也能获取到CPU控制权，可以使用==`Thread.sleep(0)`手动触发一次操作系统分配时间片的操作==，这也是平衡CPU控制权的一种操作。



#### 十四、ConcurrentHashMap

**ConcurrentHashMap的并发度是什么?**

`ConcurrentHashMap`的并发度就是`segment`的大小，默认为==**16**==，这意味着最多同时可以有16条线程操作`ConcurrentHashMap`，这也是`ConcurrentHashMap`对`Hashtable`的最大优势，任何情况下，`Hashtable`能同时有两条线程获取`Hashtable`中的数据吗？



**ConcurrentHashMap的工作原理**

> jdk 1.6:

`ConcurrentHashMap`是==线程安全==的，但是与`Hashtablea`相比，实现线程安全的方式不同。

`Hashtable`是通过对`hash`表结构进行锁定，是==阻塞式==的，当一个线程占有这个锁时，其他线程必须阻塞等待其释放锁。

`ConcurrentHashMap`是采用==分离锁==的方式，它并没有对整个`hash`表进行锁定，而是局部锁定，也就是说当一个线程占有这个局部锁时，不影响其他线程对`hash`表其他地方的访问。

> jdk1.7

在JDK1.7版本中，ConcurrentHashMap的数据结构是由一个Segment数组和多个HashEntry组成

![](http://cdn.zblade.top/qiniu_img/5220087-8c5b0cc951e61398.webp)

> jdk 1.8

在jdk 8中，`ConcurrentHashMap`不再使用`Segment`分离锁，而是采用一种乐观锁`CAS`算法来实现同步问题，但其底层还是“==数组+链表->红黑树==”的实现,桶中的结构可能是链表，也可能是红黑树，红黑树是为了提高查找效率。

![](http://cdn.zblade.top/qiniu_img/5220087-63281d7b737f1109.webp)

总结：

相对而言，`ConcurrentHashMap`只是增加了同步的操作来控制并发，从JDK1.7版本的==ReentrantLock+Segment+HashEntry==，到JDK1.8版本中==synchronized+CAS+HashEntry+红黑树==,相对而言，总结如下思考:

+ JDK1.8的实现**降低锁的粒度**，JDK1.7版本锁的粒度是基于`Segment`的，包含多个`HashEntry`，而JDK1.8锁的粒度就是`HashEntry`（首节点）
+ JDK1.8版本的**数据结构变得更加简单**，使得操作也更加清晰流畅，因为已经使用`synchronized`来进行同步，所以不需要分段锁的概念，也就不需要`Segment`这种数据结构了，由于粒度的降低，实现的复杂度也增加了
+ JDK1.8使用**红黑树来优化链表**，基于长度很长的链表的遍历是一个很漫长的过程，而红黑树的遍历效率是很快的，代替一定阈值的链表，这样形成一个最佳拍档
+ JDK1.8为什么使用内置锁**synchronized**来代替重入锁**ReentrantLock**，我觉得有以下几点
  1. 因为粒度降低了，在相对而言的低粒度加锁方式，`synchronized`并不比`ReentrantLock`差，在==粗粒度加锁中`ReentrantLock`可能通过`Condition`来控制各个低粒度的边界，更加的灵活，而在低粒度中，`Condition`的优势就没有了==
  2. JVM的开发团队从来都没有放弃`synchronized`，而且==基于JVM的`synchronized`优化空间更大==，使用内嵌的关键字比使用API更加自然
  3. 在大量的数据操作下，对于JVM的内存压力==，基于API的`ReentrantLock`会开销更多的内存==，虽然不是瓶颈，但是也是一个选择依据



十五、`CyclicBarrier`和`CountDownLatch`区别

这两个类非常类似，都在`java.util.concurrent`下，都可以用来表示代码运行到某个点上，二者的区别在于：

- `CyclicBarrier`的某个线程运行到某个点上之后，==该线程即停止运行，直到所有的线程都到达了这个点，所有线程才重新运行==；`CountDownLatch`则不是，某线程运行到某个点上之后，==只是给某个数值-1而已，该线程继续运行==
- `CyclicBarrier`只能唤起一个任务，`CountDownLatch`可以唤起多个任务
- `CyclicBarrier`可重用，`CountDownLatch`不可重用，计数值为0该`CountDownLatch`就不可再用了

十六、java中的++操作符线程安全么?

不是线程安全的操作。它涉及到多个指令，如==读取变量值，增加，然后存储回内存，这个过程可能会出现多个线程交差==



十七、多线程开发良好习惯

1. 给线程命名
2. 最小化同步范围
3. 优先使用`volatile`
4. 尽可能使用更高层次的并发工具而非wait和notify()来实现线程通信,如`BlockingQueue`,`Semeaphore`
5. 优先使用**并发容器**而非同步容器.
6. 考虑使用线程池



#### 十八、volatile关键字

指令重排序和内存可见性，volatile 类型变量即使在没有同步块的情况下赋值也不会与其他语句重排序。 volatile 提供 happens-before 的保证，确保一个线程的修改能对其他线程是可见的。

==Volatile 变量具有 synchronized 的可见性特性，但是不具备原子特性==

**可以创建Volatile数组吗?**

Java 中可以创建 volatile类型数组，不过**只是一个指向数组的引用，而不是整个数组**。如果改变引用指向的数组，将会受到volatile 的保护，但是如果多个线程同时改变数组的元素，volatile标示符就不能起到之前的保护作用了

**如何使非原子操作变成原子操作**

典型案例：

1. double 和 long 都是64位宽，因此对这两种类型的读是分为两部分的，第一次读取第一个 32 位，然后再读剩下的 32 位，这个过程不是原子的。如果知道要被多线程访问，应该加`volatile`关键字

2. 提供内存屏障（memory barrier）

   在写一个 volatile 变量之前，Java 内存模型会插入一个写屏障（write barrier），读一个 volatile 变量之前，会插入一个读屏障（read barrier）即在==你写一个 volatile 域时，能保证任何线程都能看到你写的值==，同时，在==写之前，也能保证任何数值的更新对所有线程是可见的==，因为内存屏障会将其他所有写的值更新到缓存

使用条件

- 对变量的写操作不依赖于当前值。
- 该变量没有包含在具有其他变量的不变式中。



#### 十九、异常

[白话异常机制](https://link.zhihu.com/?target=http%3A//blog.csdn.net/dd864140130/article/details/42504189)

**throw和throws的区别**

throw用于主动抛出java.lang.Throwable 类的一个实例化对象，意思是说你可以通过关键字 throw 抛出一个 Error 或者 一个Exception，如：

`throw new IllegalArgumentException(“size must be multiple of 2″)`

而throws 的作用是作为方法声明和签名的一部分，方法被抛出相应的异常以便调用者能处理。**Java 中，任何未处理的受检查异常强制在 throws 子句中声明**。



二十、Java 中，Serializable 与 Externalizable 的区别

`Serializable `接口是一个序列化 Java 类的接口，以便于它们可以在网络上传输或者可以将它们的状态保存在磁盘上，是==JVM 内嵌的默认序列化方式==，成本高、脆弱而且不安全。`Externalizable `允许你控制整个序列化过程，指定特定的二进制格式，增加安全机制。



#### 二十、重载重写原理 各自的应用场景

**方法重写的应用场景：**当父类的方法不能完全满足子类使用的时候，既可以保留父类的功能（沿袭、传承），还可以有自己特有的功能。

**方法重写的注意事项：** 不可以重写父类私有的成员方法，压根就看不到父类的私有成员。子类重写父类方法，权限必须大于等于父类方法的权限。



### 二十一、int和Integer区别

1.int是基本的数据类型； 
2.Integer是int的封装类； 
3.int和Integer都可以表示某一个数值； 
4.int和Integer不能够互用，因为他们两种不同的数据类型；

**引入的原因**：

+ 为了在各种类型间转化，通过各种方法的调用。否则 你无法直接通过变量转化。 
+ 封装一些常用方法，方便进行数据转换
+ 更加有利于理解，更加符合面向对象的程序设计



##### Integer使用注意实现

在java中，Integer具有一个-128至127的缓冲池，在这个区间内赋值，不创建新的对象，而是直接在缓冲池中取值。（Character则是0-128）

若是使用new Integer(x)，则是在堆内存中创建新的对象，故(i5==i6)为假。``

`Integer.valueOf()`中有个内部类`IntegerCache`（类似于一个常量数组，也叫对象池），它维护了一个`Integer`数组`cache`，长度为`（128+127+1）=256`；Integer类中还有一个`Static Block`(静态块)

```java
static {
    for(int i = 0; i < cache.length; i++)
    cache[i] = new Integer(i - 128);
  }
```

从这个静态块可以看出，Integer已经默认创建了数值【-128-127】的Integer缓存数据。所以使用Integer i1=40时，JVM会直接在该在对象池找到该值的引用。  也就是说这种方式声明一个Integer对象时，JVM首先会在Integer对象的缓存池中查找有木有值为40的对象，如果有直接返回该对象的引用；如果没有，则使用New keyword创建一个对象，并返回该对象的引用地址。



### 二十二、Object类有哪些方法

![img](https://pic4.zhimg.com/80/v2-79939b2d0f1265772aa6c2eb80f292ff_720w.jpg)

**1、toString()**

返回一个String类型的字符串，用于描述当前对象的信息，使用频率极高，一般都有子类都有覆盖，即可以重写返回对自己有用的信息，**默认返回的是当前对象的类名+hashCode的16进制数字。**

**toString()作用：**碰到“`println（）`”之类的输出方法时会自动调用，不用显式打出来。

**2、wait()**

包装成ObjectWaiter节点，添加到Object监视器Monitor的WaitSet中

> ***看一下chapter2.md就知道了***



**3、notify()**

从WaitSet队列中释放



**4、finalize()**

java允许在类中定义一个名为finalize()的方法。

它的工作原理是：在垃圾回收器准备好释放对象占用的存储空间前，将首先调用其finalize()方法，并且在**下一次**垃圾回收动作发生时，才会真正回收对象占用的内存。

**5、equals()**

Object中的equals()方法是直接用来判断两个对象指向的内存空间是不是同一块。如果是同一块内存地址，则返回true。

**6、hashCode()**

**作用：返回字符串的哈希码值，用于判断两个对象的地址是不是同一个。注意与上面equals()方法的区别哈。**

> 很多java类自行计算自己的hashcode，比如String。

**在集合中 hashCode的存在主要是用于查找的快捷性，如Hashtable，HashMap等，hashCode是用来在散列存储结构中确定对象的存储地址的；**

设计规范要求重写equals的时候一定要重写hashcode



### 用户线程和守护线程

1.**用户线程和守护线程的区别**
用户线程和守护线程都是线程，区别是Java虚拟机在所有用户线程dead后，程序就会结束。而不管是否还有守护线程还在运行，若守护线程还在运行，则会马上结束。

2.**用户线程和守护线程的适用场景**
由两者的区别及dead时间点可知，守护线程不适合用于输入输出或计算等操作，因为用户线程执行完毕，程序就dead了，适用于辅助用户线程的场景，如JVM的垃圾回收，内存管理都是守护线程，还有就是在做数据库应用的时候，使用的数据库连接池，连接池本身也包含着很多后台线程，监听连接个数、超时时间、状态等。

3.**创建守护线程**	·
调用线程对象的方法`setDaemon(true)`，设置线程为守护线程。
1)`thread.setDaemon(true)`必须在`thread.start()`之前设置。
2)在`Daemon`线程中产生的新线程也是`Daemon`的。
3)不是所有的应用都可以分配给`Daemon`线程来进行服务，比如读写操作或者计算逻辑。因为Daemon Thread还没来得及进行操作，虚拟机可能已经退出了。

4**.Java守护线程和Linux守护进程**
两者不是一个概念。Linux守护进程是后台服务进程，没有控制台。

## 方法

一、switch 在1.7后支持String类型，支持byte类型但是不支持long类型



**二、a.hashCode()有什么用?与a.equals(b)有什么关系？**

hashCode() 方法是相应对象整型的 hash 值。它常用于基于 hash 的集合类，如 Hashtable、HashMap、LinkedHashMap等等。它与 equals() 方法关系特别紧密。根据 Java 规范，使用 equal() 方法来判断两个相等的对象，必须具有相同的 hashcode。



**三、a==b与a.equals(b)有什么区别**

如果a 和b 都是对象，则 a==b 是比较两个对象的引用，只有当 a 和 b 指向的是堆中的同一个对象才会返回 true，而 a.equals(b) 是进行逻辑比较，所以通常需要重写该方法来提供逻辑一致性的比较。例如，String 类重写 equals() 方法，所以可以用于两个不同对象，但是包含的字母相同的比较。



**四、`+=`操作符会进行隐式自动类型转换**



五、位运算

```java
name!=null&userName.equals("")
    //要报空指针异常
```



#### 六、日期计算

**`SimpleDateFormat`是线程安全的吗?**

**`DateFormat `的所有实现，包括 `SimpleDateFormat `都不是线程安全的**，因此你不应该在多线程序中使用，除非是在对外线程安全的环境中使用，如将 `SimpleDateFormat `限制在 `ThreadLocal `中。如果你不这么做，在解析或者格式化日期的时候，可能会获取到一个不正确的结果。因此，从日期、时间处理的所有实践来说，我强力推荐 joda-time 库。

### 七、多态

多态表示当同一个操作作用在不同对象时，会有不同的语义，从而产生不同的结果。3+4和“3”+“4”

Java的多态性可以概括成"一个接口,两种方法"分为两种

+ **编译时的多态**

  编译时的多态主要是指方法的重载（overload）

+ **运行时的多态。**

  运行时的多态主要是指方法的覆盖（override），接口也是运行时的多态

**运行时的多态**的三种情况：
1、父类有方法，子类有覆盖方法：编译通过，执行子类方法。
2、父类有方法，子类没覆盖方法：编译通过，执行父类方法（子类继承）。
3、父类没方法，子类有方法：编译失败，无法执行。
==方法带final、static、private时是编译时多态，因为可以直接确定调用哪个方法。==

## 集合

![](http://cdn.zblade.top/qiniu_img/v2-a571bdc9758656f2276298ef42a9b065_hd.jpg)





## 注解

@Target

+ `ElementType.ANNOTATION_TYPE` 可以应用于注释类型。
+ `ElementType.CONSTRUCTOR `可以应用于构造函数``
+ `ElementType.FIELD `可以应用于字段或属性。
+ `ElementType.LOCAL`_VARIABLE 可以应用于局部变量
+ `ElementType.METHOD `可以应用于方法级注释。
+ `ElementType.PACKAGE `可以应用于包声明
+ `ElementType.PARAMETER `可以应用于方法的参数。
+ `ElementType.TYPE `可以应用于类的任何元素。

**@Retention：**@Retention定义了该注解被保留的时间长短

1. `SOURCE`:在源文件中有效（即源文件保留）
2. `CLASS`:在class文件中有效（即class保留）
3. `RUNTIME`:在运行时有效（即运行时保留）

**@Inherited**：只是可控制对类名上注解是否可以被继承。不能控制方法上的注解是否可以被继承。

@Javadoc

...





## 数据库

一、使用Integer和Long进行数据库数据存值，因为这些是对象，如果使用int或者long会获取不到值

注意不要和mysql的关键字冲突了！！



### 二、三范式

**第一范式(1NF):**

指的是数据库表的中的每一列都是不可分割的基本数据项,同一列中不能有多个值。第一范式要求属性值是不可再分割成的更小的部分**。第一范式简而言之就是强调的是列的原子性，即列不能够再分成其他几列**。例如有一个列是电话号码一个人可能有一个办公电话一个移动电话。第一范式就需要拆开成两个属性。

**第二范式（2NF）：**

**第二范式首先是第一范式**，同时还需要包含两个方面的内容，**一是表必须要有一个主键；二是没有包含主键中的列必须完全依赖主键，而不能只是依赖于主键的一部分**。
例如在一个订单中可以订购多种产品，所以单单一个 OrderID 是不足以成为主键的，主键应该是（OrderID，ProductID）。显而易见 Discount（折扣），Quantity（数量）完全依赖（取决）于主键（OderID，ProductID），而 UnitPrice，ProductName 只依赖于 ProductID。所以 OrderDetail 表不符合 2NF。

不符合 2NF 的设计容易产生冗余数据。 可以把【OrderDetail】表拆分为【OrderDetail】（OrderID，ProductID，Discount，Quantity）和【Product】（ProductID，UnitPrice，ProductName）来消除原订单表中UnitPrice，ProductName多次重复的情况。

**第三范式（3NF）：**

**首先是第二范式，例外非主键列必须依赖于主键，不能存在传递**。也就是说不能存在非主键列A依赖于非主键列B，然后B依赖于主键列
考虑一个订单表【Order】（OrderID，OrderDate，CustomerID，CustomerName，CustomerAddr，CustomerCity）主键是（OrderID）。
其中 OrderDate，CustomerID，CustomerName，CustomerAddr，CustomerCity 等非主键列都完全依赖于主键（OrderID），所以符合 2NF。不过问题是 CustomerName，CustomerAddr，CustomerCity 直接依赖的是 CustomerID（非主键列），而不是直接依赖于主键，它是通过传递才依赖于主键，所以不符合 3NF。
通过拆分【Order】为【Order】（OrderID，OrderDate，CustomerID）和【Customer】（CustomerID，CustomerName，CustomerAddr，CustomerCity）从而达到 3NF。
==二范式（2NF）和第三范式（3NF）的概念很容易混淆，区分它们的关键点在于，2NF：非主键列是否完全依赖于主键，还是依赖于主键的一部分；3NF：非主键列是直接依赖于主键，还是直接依赖于非主键列。==



**第一范式：1NF是对属性的原子性约束，要求属性具有原子性，不可再分解；**

**第二范式：2NF是对记录的惟一性约束，要求记录有惟一标识，即实体的惟一性；**  

**第三范式：3NF是对字段冗余性的约束，即任何字段不能由其他字段派生出来，它要求字段没有冗余。**

>优点:

可以尽量得减少数据冗余，使得更新快，体积小

缺点:

对于查询需要多个表进行关联，减少写得效率增加读得效率，更难进行索引优化

三、内外连接

四、事务

1. **原子性**: 即事务是一个不可分割的整体,数据修改时要么都操作一遍要么都不操作
2. **一致性**: 一个事务执行前后数据库的数据必须保持一致性状态
3. **隔离性**: 当两个或者以上的事务并发执行时,为了保证数据的安全性,将一个事务的内部的操作与事务操作隔离起来不被其他事务看到
4. **持久性**: 更改是永远存在的

**隔离级别**

>**读未提交**：事务中的修改，即使没有提交，其他事务也可以看得到，脏读。如果一个事务已经开始写数据，则另外一个事务则不允许同时进行写操作，但允许其他事务读此行数据。该隔离级别可以通过“排他写锁”实现。一个在写事务另一个虽然不能写但是能读到还没有提交的数据
>
>**读已提交**：可以避免脏读但是可能出现不可重复读。允许写事务，读取数据的事务允许其他事务继续访问该行数据，但是未提交的写事务将会禁止其他事务访问该行。事务T1读取数据，T2紧接着更新数据并提交数据，事务T1再次读取数据的时候，和第一次读的不一样。即虚读
>
>**可重复读**：禁止写事务，读事务会禁止所有的写事务，但是允许读事务，避免了不可重复读和脏读，但是会出现幻读，即第二次查询数据时会包含第一次查询中未出现的数据
>
>**序列化**：禁止任何事务，一个一个进行；提供严格的事务隔离。它要求事务序列化执行，事务只能一个接着一个地执行，但不能并发执行。如果仅仅通过“行级锁”是无法实现事务序列化的，必须通过其他机制保证新插入的数据不会被刚执行查询操作的事务访问到。

### 索引

[索引](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)

**7.6.1优缺点：**

> 优点： 可以快速检索，减少I/O次数，加快检索速度；根据索引分组和排序，可以加快分组和排序
> 缺点： 索引本省也是表会占用内存，索引表占用的空间是数据表的1.5倍；索引表的创建和维护需要时间成本，这个成本随着数据量的增大而增大。

**7.6.2索引的底层实现原理：**

哈希索引：

> 只有memory（内存）存储引擎支持哈希索引，哈希索引用索引列的值计算该值的hashCode，然后在hashCode相应的位置存执该值所在行数据的物理位置，因为使用散列算法，因此访问速度非常快，但是一个值只能对应一个hashCode，而且是散列的分布方式，因此哈希索引不支持范围查找和排序的功能。

Btree索引：

> B树是一个平衡多叉树，设树的度为2d，高度为h，那么B树需要满足每个叶子节点的高度都一样等于h，每个非叶子节点由n-1个key和n个point组成，d< = n<=2d 。所有叶子节点指针均为空，非叶子结点的key都是[key,data]二元组，其中key表示作为索引的键，data为键值所在行的数据。

B+Tree索引

> B+Tree是BTree的一个变种，设d为树的度数，h为树的高度，B+Tree和BTree的不同主要在于：
> B+Tree中的非叶子结点不存储数据，只存储键值；
> B+Tree的叶子结点没有指针，所有键值都会出现在叶子结点上，且key存储的键值对应data数据的物理地址；B+Tree的每个非叶子节点由n个键值key和n个指针point组成；
> 优点：查询速度更加稳定，磁盘的读写代价更低

**聚簇索引与非聚簇索引**

> 聚簇索引的解释是:聚簇索引的顺序就是数据的物理存储顺序
> 非聚簇索引的解释是:索引顺序与数据物理排列顺序无关

MyISAM——非聚簇索引

> MyISAM存储引擎采用的是非聚簇索引，非聚簇索引的主索引和辅助索引几乎是一样的，只是主索引不允许重复，不允许空值，他们的叶子结点的key都存储指向键值对应的数据的物理地址。
> 非聚簇索引的数据表和索引表是分开存储的。

innoDB——聚簇索引

> 聚簇索引的主索引的叶子结点存储的是键值对应的数据本身，辅助索引的叶子结点存储的是键值对应的数据的主键键值。因此主键的值长度越小越好，类型越简单越好。
> 聚簇索引的数据和主键索引存储在一起。

**7.6.3 联合索引(顺丰)**

> 利用最左前缀原则

**7.7.数据库锁**

> 锁是计算机协调多个进程或者纯线程并发访问某一资源的机制

**7.7.1Mysql的锁种类**

> Mysql的锁机制比较简单，不同的搜索引擎支持不同的锁机制
> 表级锁：开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率高，并发度最低
> 行级锁：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突概率最低，并发度也最高
> 页面锁：开销和加锁速度位于表锁和行锁之间，会出现死锁，锁定粒度也位于表锁和行锁之间，并发度一般

**7.7.2Mysql表级锁的锁模式（MyISAM）**

> Mysql表级锁有两种模式：表共享锁（Table Read Lock）和表独占锁（Table Write Lock）

### 7.8.having 和group by

+ having是在group by之后，group by是在where之后：where的时候表示将数据从磁盘拿到内存，where之后的所有操作都是内存操作。

[查询一个部门中最高工资的人](https://www.cnblogs.com/kissdodog/p/3365789.html)

```mysql
#只生成了一次临时表
SELECT 姓名,T1.部门,工资 FROM 工资表 AS T1 INNER JOIN
(
    SELECT 部门,MAX(工资) AS 最高 FROM 工资表    --执行查询，先记录两个字段 部门-最高工资
    GROUP BY 部门
) AS T2        --衍生表T2
ON T1.部门 = T2.部门 AND 工资 = 最高
```

```mysql
#左连接然后去NULL，充分使用索引，不生成临时表
SELECT T1.姓名, T1.department, salary FROM table1 AS T1 
LEFT JOIN table2 AS T2 shiyong
ON T1.deapartment = T2.department AND T1.salary > T2.salary 
WHERE T2. id IS NULL
```



[mysql 查询一张表某几个字段 数据量重复次数最多的一条数据](https://developer.aliyun.com/ask/65736?spm=a2c6h.13159736)

[你必知必会的SQL语句练习-1](https://www.cnblogs.com/edisonchou/p/3878135.html)

[你必知必会的SQL语句练习-2](https://www.cnblogs.com/edisonchou/p/3886801.html)

[各种连接](https://www.cnblogs.com/wanglijun/p/8916790.html)



distinct  

join



### 四、不可重复读和幻读

一个事务A开启后，第一次读取到一些数据之后，就对这些数据进行加**行锁**，导致其他事务B无法修改（更新或者删除）数据，于是A事务不管怎么读，返回的都是一样的数据，这就实现了“**可重复读**”这个隔离级别

“其他事务B无法修改这些数据（更新或删除）”，不代表其他事务B不能insert一些记录并提交。这样一来事务A还是可以读取到一条之前没有出现的数据，这就产生了“**幻读**”。

**行级锁是无法解决幻读问题的。要想解决这个问题必须实现Serializable隔离级别。**

使用间隙锁可以解决插入导致的幻读

### 五、分布式ID生成方案总结

[分布式ID生成方案总结](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485590&idx=2&sn=9697a9fae7f4d319e2e92103186a2c26&chksm=cea2475df9d5ce4bb69770ce4934537db6bb1346457c861d51b0d18c130b0d66d35c2c17bc20&token=69811960&lang=zh_CN&scene=21#wechat_redirect)

生成全局 id 有下面这几种方式：

- **UUID**：不适合作为主键，因为太长了，并且无序不可读，查询效率低。比较适合用于生成唯一的名字的标示比如文件的名字。

- **数据库自增 id** : 两台数据库分别设置不同步长，生成不重复ID的策略来实现高可用。这种方式生成的 id 有序，但是需要独立部署数据库实例，成本高，还会有性能瓶颈。

- **利用 redis 生成 id :** 性能比较好，灵活方便，不依赖于数据库。但是，引入了新的组件造成系统更加复杂，可用性降低，编码更加复杂，增加了系统成本。

- **Twitter的snowflake算法** ：Github 地址：https://github.com/twitter-archive/snowflake。

  [雪花算法及其实现](https://blog.csdn.net/lq18050010830/article/details/89845790)

  ![img](https://img-blog.csdnimg.cn/2019050514590789.png)

  + 第一个部分，是 `1` 个 bit：0，这个是无意义的,表示生成ID是正数。
  + 第二个部分是 `41 `个 bit：表示的是时间戳，单位是毫秒，可以代表69年的时间。
  + 第三个部分是 `5` 个 bit：表示的是机房 id，10001。
  + 第四个部分是 `5 `个 bit：表示的是机器 id，1 1001。
  + 第五个部分是 `12 `个 bit：表示的序号，就是某个机房某台机器上这一毫秒内同时生成的 id 的序号，0000 00000000。

  **缺点：依赖与系统时间的一致性，如果系统时间被回调，或者改变，可能会造成id冲突或者重复。**

- **美团的[Leaf](https://tech.meituan.com/2017/04/21/mt-leaf.html)分布式ID生成系统** ：Leaf 是美团开源的分布式ID生成器，能保证全局唯一性、趋势递增、单调递增、信息安全，里面也提到了几种分布式方案的对比，但也需要依赖关系数据库、Zookeeper等中间件。感觉还不错。 

### 六、常用命令

[MySQL常用操作命令](https://segmentfault.com/a/1190000010979586)

[MySQL查看数据库性能常用命令](https://blog.csdn.net/qq_41455420/article/details/82802090)

### 七、把子查询优化为 join 操作

通常子查询在 in 子句中，且子查询中为简单 SQL(不包含 union、group by、order by、limit 从句) 时,才可以把子查询转化为关联查询进行优化。

**子查询性能差的原因：**

子查询的结果集无法使用索引，通常子查询的结果集会被存储到临时表中，不论是内存临时表还是磁盘临时表都不会存在索引，所以查询性能会受到一定的影响。特别是对于返回结果集比较大的子查询，其对查询性能的影响也就越大。

由于子查询会产生大量的临时表也没有索引，所以会消耗过多的 CPU 和 IO 资源，产生大量的慢查询。

### 八、临时表

**使用临时表的场景**

1)`ORDER BY`子句和`GROUP BY`子句不同， 例如：ORDERY BY price GROUP BY name；

2)在`JOIN`查询中，ORDER BY或者GROUP BY使用了**不是第一个表的列** 例如：

```mysql
SELECT * from TableA, TableB ORDER BY TableA.price GROUP by TableB.name
```

3)`ORDER BY`中使用了`DISTINCT`关键字 `ORDERY BY DISTINCT(price)`

4)`SELECT`语句中指定了`SQL_SMALL_RESULT`关键字

SQL_SMALL_RESULT的意思就是告诉MySQL，结果会很小，请直接使用内存临时表，不需要使用索引排序 `SQL_SMALL_RESULT`必须和`GROUP BY`、`DISTINCT`或`DISTINCTROW`一起使用 一般情况下，我们没有必要使用这个选项，让MySQL服务器选择即可。

5）`UNION`查询·

6）`FROM`中的子查询
7）子查询或者`semi-join`时创建的表

**直接使用磁盘临时表的场景**

1)表包含`TEXT`或者`BLOB`列；

2`)GROUP BY `或者 `DISTINCT `子句中包含长度大于`512`字节的1列；

3)使用`UNION`或者`UNION ALL`时，`SELECT`子句中包含大于`512`字节的列；



### 九、delete和drop

数据库中的delete 与drop的区别， 从下面的例子开始：

 delete ：  

```mysql
delete from 表名  where 条件
```



 drop  ：  

```mysql
alter table  表名  drop 字段
drop table  表
```

有的同学从从上面的例子，可以看出来，  delete  删除的是 数据，drop删除的是  表；

这个没错，  但是， delete  和 drop 还有其他的区别，如下：

**delete**：

1、`delete`是`DML`，执行`delete`操作时，每次从表中删除一行，并且同时将该行的的删除操作记录在`redo`和`undo`表空间中以便进行回滚（`rollback`）和重做操作，但要注意表空间要足够大，需要手动提交（`commit`）操作才能生效，可以通过`rollback`撤消操作。

2、`delete`可根据条件删除表中满足条件的数据，如果不指定`where`子句，那么删除表中所有记录。

3、`delete`语句不影响表所占用的`extent`，高水线(`high watermark)`保持原位置不变。

**drop**：

1、`drop`是`DDL`，会**隐式提交**，所以，不能回滚，不会触发触发器。

2、`drop`语句删除表结构及所有数据，并将表所占用的空间全部释放。

3、`drop`语句将删除表的结构所依赖的约束，触发器，索引，依赖于该表的存储过程/函数将保留,但是变为`invalid`状态。

 **总结**

1、在速度上，一般来说，drop> delete。

 2、在使用drop时一定要注意，虽然可以恢复，但为了减少麻烦，还是要慎重。

3、如果想删除部分数据用delete，注意带上where子句，回滚段要足够大；如果和事务有关，或者想触发trigger，还是用delete

4、如果想删除表，当然用drop；



### 十、char和varchar

一、长度不同

1、char类型：char类型的长度是固定的。

2、varchar类型：varchar类型的长度是可变的。

二、效率不同

1、char类型：char类型每次修改的数据长度相同，效率更高。

2、varchar类型：varchar类型每次修改的数据长度不同，效率更低。

三、存储不同

1、char类型：char类型存储的时候是初始预计字符串再加上一个记录字符串长度的字节，占用空间较大。

2、varchar类型：varchar类型存储的时候是实际字符串再加上一个记录字符串长度的字节，占用空间较小。



### 十一、数据库内联、左联和外联的区别

INNER JOIN（内联）：两个表a,b 相连接，取出符合连接条件的字段

LEFT JOIN（左联）：先返回左表的所有行，再加上符合连接条件的匹配行

RIGHT JOIN（右联）：先返回右表的所有行，再加上符合连接条件的匹配行

## JVM

#### **一、四种引用**

[引用详解](https://blog.csdn.net/dd864140130/article/details/49885811)

+ 强引用：如果一个对象具有强引用，==它就不会被垃圾回收器回收==。即使当前内存空间不足，JVM也不会回收它，而是==抛出 OutOfMemoryError 错误==，使程序异常终止。如果想中断强引用和某个对象之间的关联，可以==显式地将引用赋值为nul==l，这样一来的话，JVM在合适的时间就会回收该对象

```java
Person person=new Person();
```



+ 软引用：在使用软引用时，如果内存的空间足够，软引用就能继续被使用，而不会被垃圾回收器回收，==只有在内存不足时，软引用才会被垃圾回收器回收==。

```java
Person person=new Person(); 
SoftReference sr=new SoftReference(person);
```



+ 弱引用：具有弱引用的对象拥有的生命周期更短暂。因为当 JVM 进行垃圾回收，一旦发现弱引用对象，==无论当前内存空间是否充足，都会将弱引用回收==。不过由于垃圾回收器是一个优先级较低的线程，所以并不一定能迅速发现弱引用对象

```java
Person person=new Person(); 
WeakReference wr=new WeakReference(person);
```



+ 虚引用：顾名思义，就是形同虚设，如果一个对象仅持有虚引用，那么它相当于没有引用，在任何时候都可能被垃圾回收器回收。

  设置虚引用的目的是为了==被虚引用关联的对象在被垃圾回收器回收时，能够收到一个系统通知==

  ```java
  ReferenceQueue queue=new ReferenceQueue();
  PhantomReference pr=new PhantomReference(object.queue);
  
  //GC在回收一个对象时，如果发现该对象具有虚引用，那么在回收之前会首先该对象的虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否已经加入虚引用来了解被引用的对象是否被GC回收。
  ```

  

![](http://cdn.zblade.top/qiniu_img/20150620182522495.png)

**引用顺序**

>单条引用链的可达性以最弱的一个引用类型来决定；
>多条引用链的可达性以最强的一个引用类型来决定；

应用场景

1. **利用软引用和弱引用解决OOM问题：**

   例：用一个HashMap来保存图片的路径和相应图片对象关联的软引用之间的映射关系，在内存不足时，JVM会自动回收这些缓存图片对象所占用的空间，从而有效地避免了OOM的问题.

2. **通过软引用实现Java对象的高速缓存:**

   例：比如我们创建了一Person的类，如果每次需要查询一个人的信息,哪怕是几秒中之前刚刚查询过的，都要重新构建一个实例，这将引起大量Person对象的消耗,并且由于这些对象的生命周期相对较短,会引起多次GC影响性能。此时,通过软引用和 HashMap 的结合可以构建高速缓存,提供性能.

#### 二、ReferenceQueue和Reference

**ReferenceQueue**

其作用在于Reference对象所引用的对象被GC回收时，该Reference对象将会被加入引用队列中（ReferenceQueue）的队列末尾,这相当于是一种通知机制.当关联的引用队列中有数据的时候，意味着引用指向的堆内存中的对象被回收。通过这种方式，JVM允许我们在对象被销毁后，做一些我们自己想做的事情

```java
ReferenceQueue< Person> rq=new ReferenceQueue<Person>();
Person person=new Person();
SoftReference sr=new SoftReference(person,rq);
```



**Reference**

Reference是SoftReference，WeakReference,PhantomReference类的父类，其内部通过一个next字段来构建了一个Reference类型的单向列表，而queue字段存放了引用对象对应的引用队列，若在Reference的子类构造函数中没有指定，则默认关联一个ReferenceQueue.NULL队列。



#### 三、垃圾回收算法

> GC主要完成三项任务：**分配内存**，**确保被引用的对象的内存不被错误的回收**以及**回收不再被引用的对象的内存空间**

1. 标记-清除
2. 标记-复制
3. 标记-整理
4. 分代回收
5. 增量收集  不用stop the world

判断对象存活：1.引用计数法;**2:对象可达性分析**

**简单的解释一下垃圾回收**

Java 垃圾回收机制最基本的做法是分代回收。

内存中的区域被划分成不同的世代，对象根据其存活的时间被保存在对应世代的区域中。一般的实现是划分成3个世代：年轻、年老和永久。内存的**分配**是发生在年轻世代中的。当一个对象存活时间足够长的时候，它就会被复制到年老世代中**。对于不同的世代可以使用不同的垃圾回收算法**。

进行世代划分的出发点是对应用中对象存活时间进行研究之后得出的统计规律。一般来说，一个应用中的大部分对象的存活时间都很短。比如局部变量的存活时间就只在方法的执行过程中。基于这一点，对于年轻世代的垃圾回收算法就可以很有针对性.



四、System.gc()：通知GC开始工作,但是GC真正开始的时间不确定.



五、JVM的平台五无关性

Java语言的一个非常重要的特点就是与平台的无关性。而使用Java虚拟机是实现这一特点的关键。一般的高级语言如果要在不同的平台上运行，至少需要编译成不同的目标代码。而引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。Java语言使用模式Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。Java虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。



#### 六、类加载机制

![类加载生命周期图](documents/JAVA笔记.assets/20151022160855857.jpg)

[深入理解JVM加载器](https://link.zhihu.com/?target=http%3A//blog.csdn.net/dd864140130/article/details/49817357)。

初始化阶段  以下情况才会对类立即初始化：

1. 使用new关键字实例化对象、访问或者设置一个类的静态字段（==被final修饰、编译器优化时已经放入常量池的例外==）、调用类方法，都会初始化该静态字段或者静态方法所在的类。
2. 初始化类的时候，如果其父类没有被初始化过，则要先触发其父类初始化。
3. 使用java.lang.reflect包的方法进行反射调用的时候，如果类没有被初始化，则要先初始化。·
4. 虚拟机启动时，用户会先初始化要执行的主类（含有main）
5. jdk 1.7后，如果`java.lang.invoke.MethodHandle`的实例最后对应的解析结果是 `REF_getStatic`、`REF_putStatic`、`REF_invokeStatic`方法句柄，并且这个方法所在类没有初始化，则先初始化。





## 其他

**一、XML解析的几种方式和特点**

DOM,SAX,PULL三种解析方式:

- DOM:消耗内存：先把xml文档都读到内存中，然后再用DOM API来访问树形结构，并获取数据。这个写起来很简单，但是很消耗内存。要是数据过大，手机不够牛逼，可能手机直接死机
- SAX:解析效率高，占用内存少，基于事件驱动的：更加简单地说就是对文档进行顺序扫描，当扫描到文档(document)开始与结束、元素(element)开始与结束、文档(document)结束等地方时通知事件处理函数，由事件处理函数做相应动作，然后继续同样的扫描，直至文档结束。
- PULL:与 SAX 类似，也是基于事件驱动，我们可以调用它的next（）方法，来获取下一个解析事件（就是开始文档，结束文档，开始标签，结束标签），当处于某个元素时可以调用XmlPullParser的getAttributte()方法来获取属性的值，也可调用它的nextText()获取本节点的值。

二、版本特性

> JDK 1.7特性

 JDK 1.7 不像 JDK 5 和 8 一样的大版本，但是，还是有很多新的特性，如 

+ `try-with-resource `语句，这样你在使用流或者资源的时候，就不需要手动关闭，Java 会自动关闭。
+ `Fork-Join` 池某种程度上实现 Java 版的 Map-reduce。
+ 允许 Switch 中有 String 变量和文本。
+ 菱形操作符(\<>)用于类型推断，不再需要在变量声明的右边申明泛型，因此可以写出可读写更强、更简洁的代码

> JDK 1.8特性

java 8 在 Java 历史上是一个开创新的版本，下面 JDK 8 中 5 个主要的特性：

+ **Lambda 表达式**，允许像对象一样传递匿名函数
+ **Stream API**，充分利用现代多核 CPU，可以写出很简洁的代码
+ **Date 与 Time API**，最终，有一个稳定、简单的日期和时间库可供你使用
+ **扩展方法**，现在，接口中可以有静态、默认方法。
+ **重复注解**，现在你可以将相同的注解在同一类型上使用多次。





## redis

[redi知识集合](https://github.com/xbox1994/Java-Interview/blob/master/MD/数据库-Redis.md)

> Redis 中只包含“键”和“值”两部分，只能通过“键”来查询“值”。正是因为这样简单的存储结构，也让 Redis 的读写效率非常高
>
> 数据是存储在内存中的。尽管它经常被用作内存数据库，但是，它也支持将数据存储在硬盘中



**解决高并发和高性能的问题**

高性能：直接处理缓存也就是处理内存很快

高并发：直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中 去，这样用户的一部分请求会直接到缓存这里而不用经过数据库

### 数据结构

建议看数据结构部分：[redis设计与实现](https://redisbook.readthedocs.io/en/latest/index.html#id3)

1. **String**
   常用命令: `set`,`get`,`decr`,`incr`,`mget `等。 `String`数据结构是简单的`key-value`类型，`value`其实不仅可以是`String`，也可以是数字。 常规`key-value`缓存应用； 常规计数：微博数，粉丝数等。

2. **Hash**
   常用命令： `hget`,`hset`,`hgetall `等。
   `Hash `是一个 `string `类型的 `ﬁeld `和 `value `的映射表，`hash `特别适合用于**存储对象**，后续操作的时候，你可以直接仅 仅修改这个对象中的某个字段的值。 比如我们可以Hash数据结构来存储用户信息，商品信息等等。比如下面我就用 `hash `类型存放了我本人的一些信息：

   ```
   key=JavaUser293847 value={ “id”: 1, “name”: “SnailClimb”, “age”: 22, “location”: “Wuhan, Hubei” }
   ```

3. **List**
   常用命令: `lpush`,`rpush`,`lpop`,`rpop`,`lrange`等list 就是链表，`Redis list` 的应用场景非常多，也是`Redis`重要的数据结构之一，比如微博的关注列表，粉丝列表， 消息列表等功能都可以用`Redis`的 `list `结构来实现。` Redis list `的实现为**一个双向链表**，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。
   另外可以通过 `lrange `命令，就是从某个元素开始读取多少个元素，可以基于 `list `实现分页查询，这个很棒的一个功 能，**基于 redis 实现简单的高性能分页**，可以做类似微博那种下拉不断分页的东西（一页一页的往下走），性能高。

4. **Set**
   常用命令： `sadd`,`spop`,`smembers`,`sunion `等 set 对外提供的功能与list类似是一个列表的功能，特殊之处在于 set 是可以**自动排重**的。
   当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了**判断某个成员是否在 一个set集合内的重要接口**，这个也是list所不能提供的。可以基于 set 轻易实现交集、并集、差集的操作。 比如：在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis可以非常 方便的实现如共同关注、共同粉丝、共同喜好等功能。这个过程也就是求交集的过程，具体命令如下：

   ```
   sinterstore key1 key2 key3 将交集存在key1内
   ```

5. **Sorted Set**
   常用命令： `zadd`,`zrange`,`zrem`,`zcard`等 和set相比，sorted set增加了一个权重参数score，使得集合中的元素能够按`score`进行有序排列。
   举例： 在直播系统中，实时排行信息包含直播间在线用户列表，各种礼物排行榜，弹幕消息（可以理解为按消息维 度的消息排行榜）等信息，适合使用 Redis 中的 SortedSet 结构进行存储。



### **底层结构**

#### string

在Redis内部，String类型通过`int`、`SDS`作为结构存储,int用来存放整型数据，`sds`存放字 节/字符串和浮点型数据。

> 因为 `char*` 类型的功能单一， 抽象层次低， 并且不能高效地支持一些 Redis 常用的操作（比如追加操作和长度计算操作）， 所以在 Redis 程序内部， 绝大部分情况下都会使用 sds 而不是 `char*` 来表示字符串。

#### **list**

压缩列表和双向循环链表

> Redis3.2之后，采用的一种叫`quicklist`的数据结构来存储`list`，列表的底层都由`quicklist`实现。

当列表中存储的数据量比较小的时候，列表就可以采用压缩列表的方式实现。具体需要同时满足下面两个条件：

+ 列表中保存的单个数据（有可能是字符串类型的）小于 64 字节；
+ 列表中数据个数少于 512 个。



Redis 实现了自己的双端链表结构。

- 双端链表主要有两个作用：
  - 作为 Redis 列表类型的底层实现之一；
  - 作为通用数据结构，被其他功能模块所使用；（事务模块保存命令、服务器模块、订阅发送模块保存客户端、事件模块）
- 双端链表及其节点的性能特性如下：
  - 节点带有前驱和后继指针，访问前驱节点和后继节点的复杂度为 O(1) ，并且对链表的迭代可以在从表头到表尾和从表尾到表头两个方向进行；
  - 链表带有指向表头和表尾的指针，因此对表头和表尾进行处理的复杂度为 O(1) ；
  - 链表带有记录节点数量的属性，所以可以在 O(1) 复杂度内返回链表的节点数量（长度）；

`quicklist`**仍然是一个双向链表，只是列表的每个节点都是一个ziplist，其实就是linkedlist和ziplist的结合，quicklist 中每个节点ziplist都能够存储多个数据元素**

#### **hash**

压缩列表和字典（散列表）（渐进式扩容缩容策略、链地址法）

同样，只有当存储的数据量比较小的情况下，Redis 才使用压缩列表来实现字典类型。具体需要满足两个条件：

+ 字典中保存的键和值的大小都要小于 64 字节；
+ 字典中键值对的个数要小于 512 个。



> 注意 `dict` 类型使用了两个指针，分别指向两个哈希表。
>
> 其中， 0 号哈希表（`ht[0]`）是字典主要使用的哈希表， 而 1 号哈希表（`ht[1]`）则只有在程序对 0 号哈希表进行 rehash 时才使用。

为了在字典的键值对不断增多的情况下保持良好的性能， 字典需要对所使用的哈希表（`ht[0]`）进行 rehash 操作： 在不修改任何键值对的情况下，对哈希表进行扩容， 尽量将比率维持在 1:1 左右。

`dictAdd` 在每次向字典添加新键值对之前， 都会对哈希表 `ht[0]` 进行检查， 对于 `ht[0]` 的 `size` 和 `used` 属性， 如果它们之间的比率 `ratio = used / size` 满足以下任何一个条件的话，rehash 过程就会被激活：

1. 自然 rehash ： `ratio >= 1` ，且变量 `dict_can_resize` 为真。（后台持久化时为false）
2. 强制 rehash ： `ratio` 大于变量 `dict_force_resize_ratio` （目前版本中， `dict_force_resize_ratio` 的值为 `5` ）。



rehash执行过程（rehash后的大小至少为原来的两倍）

1. 创建一个比 `ht[0]->table` 更大的 `ht[1]->table` ；
2. 将 `ht[0]->table` 中的所有键值对迁移到 `ht[1]->table` ；
3. 将原有 `ht[0]` 的数据清空，并将 `ht[1]` 替换为新的 `ht[0]` ；

字典的缩容

在默认情况下， `REDIS_HT_MINFILL` 的值为 `10` ， 也即是说， 当字典的填充率低于 10% 时， 程序就可以对这个字典进行收缩操作了。

字典收缩和字典扩展的一个区别是：

- 字典的扩展操作是自动触发的（不管是自动扩展还是强制扩展）；
- 而字典的收缩操作则是由程序手动执行。



#### **set**

一种是基于有序数组(整数集合)和散列表。

当要存储的数据，同时满足下面这样两个条件的时候，Redis 就采用有序数组，来实现集合这种数据类型。

+ 存储的数据都是整数；
+ 存储的数据元素个数不超过 512 个。



添加新元素时，如果 `intsetAdd` 发现新元素，不能用现有的编码方式来保存，便会将升级集合和添加新元素的任务转交给 `intsetUpgradeAndAdd` 来完成：

`intsetUpgradeAndAdd` 需要完成以下几个任务：

1. 对新元素进行检测，看保存这个新元素需要什么类型的编码；
2. 将集合 `encoding` 属性的值设置为新编码类型，并根据新编码类型，对整个 `contents` 数组进行内存重分配。
3. 调整 `contents` 数组内原有元素在内存中的排列方式，从旧编码调整为新编码。
4. 将新元素添加到集合中。

**升级**

> 第一，从较短整数到较长整数的转换，并不会更改元素里面的值。
>
> 第二，集合编码元素的方式，由元素中长度最大的那个值来决定

inset(有序数组)，set本身是无序的

- Intset 用于有序、无重复地保存多个整数值，会根据元素的值，自动选择该用什么长度的整数类型来保存元素。
- 当一个位长度更长的整数值添加到 intset 时，需要对 intset 进行升级，新 intset 中每个元素的位长度，会等于新添加值的位长度，但原有元素的值不变。
- 升级会引起整个 intset 进行内存重分配，并移动集合中的所有元素，这个操作的复杂度为 O(N） 。
- **Intset** **只支持升级，不支持降级。**
- Intset 是有序的，程序使用**二分查找**算法来实现查找操作，复杂度为 O(lgN)O(lg⁡N) 。

#### **zset**

跳表和压缩列表

当数据量比较小的时候，Redis 会用压缩列表来实现有序集合。

+ 所有数据的大小都要小于 `64 `字节；
+ 元素个数要小于 `128 `个。



- 跳跃表是一种随机化数据结构，查找、添加、删除操作都可以在对数期望时间下完成。
- 跳跃表目前在 Redis 的唯一作用，就是作为有序集类型的底层数据结构（之一，另一个构成有序集的结构是字典）。
- 为了满足自身的需求，Redis 基于 William Pugh 论文中描述的跳跃表进行了修改，包括：
  1. `score` 值可重复。
  2. 对比一个元素需要同时检查它的 `score` 和 `memeber` 。在Redis的skiplist实现中，数据本身的内容唯一标识这份数据，而不是由key来唯一标识。另外，当多个元素分数相同的时候，还需要根据数据内容来进字典排序。
  3. 每个节点带有高度为 `1` 层的后退指针，用于从表尾方向向表头方向迭代。



#### 压缩列表

![image-20200226010900804](documents/JAVA笔记/image-20200226010900804.png)

| 域        | 长度/类型  | 域的值                                                       |
| :-------- | :--------- | :----------------------------------------------------------- |
| `zlbytes` | `uint32_t` | 整个 ziplist 占用的内存字节数，对 ziplist 进行内存重分配，或者计算末端时使用。 |
| `zltail`  | `uint32_t` | 到达 ziplist 表尾节点的偏移量。 通过这个偏移量，可以在不遍历整个 ziplist 的前提下，弹出表尾节点。 |
| `zllen`   | `uint16_t` | ziplist 中节点的数量。 当这个值小于 `UINT16_MAX` （`65535`）时，这个值就是 ziplist 中节点的数量； 当这个值等于 `UINT16_MAX` 时，节点的数量需要遍历整个 ziplist 才能计算得出。 |
| `entryX`  | `?`        | ziplist 所保存的节点，各个节点的长度根据内容而定。           |
| `zlend`   | `uint8_t`  | `255` 的二进制值 `1111 1111` （`UINT8_MAX`） ，用于标记 ziplist 的末端。 |

**节点entry结构**

```
area        |<------------------- entry -------------------->|

            +------------------+----------+--------+---------+
component   | pre_entry_length | encoding | length | content |
            +------------------+----------+--------+---------+
```

**pre_entry_length**

`pre_entry_length` 记录了前一个节点的长度，通过这个值，可以进行指针计算，从而跳转到上一个节点。

根据编码方式的不同， `pre_entry_length` 域可能占用 `1` 字节或者 `5` 字节：

- `1` 字节：如果前一节点的长度小于 `254` 字节，便使用一个字节保存它的值。
- `5` 字节：如果前一节点的长度大于等于 `254` 字节，那么将第 `1` 个字节的值设为 `254` ，然后用接下来的 `4` 个字节保存实际长度。

**encoding 和 length**

`encoding` 和 `length` 两部分一起决定了 `content` 部分所保存的数据的类型（以及长度）。

其中， `encoding` 域的长度为两个 bit ， 它的值可以是 `00` 、 `01` 、 `10` 和 `11` ：

- `00` 、 `01` 和 `10` 表示 `content` 部分保存着字符数组。
- `11` 表示 `content` 部分保存着整数。

**content**

`content` 部分保存着节点的内容，类型和长度由 `encoding` 和 `length` 决定。



> **添加和删除 ziplist 节点有可能会引起连锁更新，因此，添加和删除操作的最坏复杂度为 O(N2) ，不过，因为连锁更新的出现概率并不高，所以一般可以将添加和删除操作的复杂度视为 O(N)) 。**

操作原理

+ 在最后添加一个节点
+ 在节点间添加一个节点 

#### 特殊数据结构

##### HyperLogLog 

用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。

##### Geo

这个功能可以将用户给定的地理位置信息储存起来， 并对这些信息进行操作

##### Pub/Sub

1. 一个Redis client发布消息,其他多个redis client订阅消息,发布的消息“即发即失”,
2. redis不会持久保存发布的消息;
3. 消息订阅者也将只能得到订阅之后的消息,通道中此前的消息将无从获得。

例如：哨兵机制推送活跃消息

注意：在消费者下线的情况下，生产的消息会丢失

 **redis过期时间**

> 有些数据是有时间限制的例如一些登陆信息，尤其是短信验证码都是有时间限制的。
> 定期删除+惰性删除
> **定期删除**要点：默认每隔1000ms就==随机抽取==一些设置了过期时间的key。
> **惰性删除**：定期删除会导致很多过期的key到了时间并没有被删除掉。假如过期的key靠定期删除没有删除掉，还停留在内存中，除非你的系统去查一下那个key，才会被redis删除

### redis的持久化机制

**1.RDB**

> BGSAVE、SAVE、 save 60 10000 、SHUTDOWN  、SYNC
>
> 快照时间：1.配置、2.用户调用save/BGSAVE、3.flushALL、4.主从复制初始化时
>
> BGSAVE原理：fork and cow

也就是**快照持久化**,通过创建快照来获得存储在内存里面的数据在某个时间节点上的副本（生成`dump.rdb`文件）。redis创建快照后可以对快照进行备份，可以将快照复制到其他服务器从而创建出具有相同数据的服务器副本（**redis主从结构，主要用来提高redis的性能**），还可以将快照留在原地以便重启服务器的时候使用。*注意：如果系统发生崩溃，会丢失最近快照之后的所有数据*

**场景**

+ 日志聚合计算
+ 大数据

缺点：
1 数据的**完整性和一致性**不高，因为RDB可能在最后一次备份时宕机了。
2 备份时占用内存，因为Redis 在备份时会独立创建一个子进程，将数据写入到一个临时文件（此时内存中的数据是原来的两倍哦），最后再将临时文件替换之前的备份文件。

**2.AOF只追加文件**

> appendonly  yes
>
> appendfsync everysec  #每秒钟同步一次，显示地将多个写命令同步到硬盘
>
> appendfsync always    #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速
>
> appendfsync no           \#让操作系统决定何时进行同步

与快照相比AOF的**实时性**更好，开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件

AOF文件的保存位置和RDB文件的位置相同，都是通过`dir`参数设置的，默认的文件名是`appendonly.aof`。

**优化**

在执行 `BGREWRITEAOF `命令时，Redis 服务器会维护一个 **AOF 重写缓冲区**，该缓冲区会在子进程创建新AOF文件期间，**记录服务器执行的所有写命令**。当子进程完成创建新AOF文件的工作之后，服务器会将重写缓冲区中的所有内容追加到新AOF文件的末尾，使得新旧两个AOF文件所保存的数据库状态一致。最后，服务器用新的AOF文件替换旧的AOF文件，以此来完成AOF文件重写操作

> 设置 auto-aof-rewrite-percentage 和auto-aof-rewrite-min-size

恢复时redis-check-aof --fix appendonly.aof 校验文件完整性,修复破碎文件

### **缓存雪崩和缓存穿透**

**缓存穿透**：一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。

**解决办法**： 

+ 有很多种方法可以有效地解决缓存穿透问题，常见的则是采用**布隆过滤器**，将所有可能存在的数据哈希到一个足够大的`bitmap`中，一个一定不存在的数据会被这个`bitmap`拦截掉，从而避免了对底层存储系统的查询压力。
+ 另外也有一个更为简单粗暴的方法（我们采用的就是这种），如果一个查询返回的数据为空（不管是数 据不存 在，还是系统故障），我们仍然把这个**空结果进行缓存**（缓存过程上锁），但它的过期时间会很短，长不超过五分钟。



**缓存雪崩**：缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。

**有哪些解决办法？**

- 事前：尽量保证整个 redis 集群的高可用性，发现机器宕机尽快补上。选择合适的内存淘汰策略。**缓存预热**
- 事中：本地ehcache缓存 + hystrix限流&降级，避免MySQL崩掉
- 事后：利用 redis 持久化机制保存的数据尽快恢复缓存

**其他方法**

1，在设置Redis键的过期时间时，加上一个随机数，这样可以避免。
2，部署分布式的Redis服务，当一个Redis服务器挂掉了之后，进行故障转移。



使用锁或队列、设置过期标志更新缓存、为key设置不同的缓存失效时间，还有一各被称为“二级缓存”的解决方法

**缓存降级**

当访问量剧增、服务出现问题（如响应时间慢或不响应）或非核心服务影响到核心流程的性能时，仍然需要保证服务还是可用的，即使是有损服务。系统可以根据一些关键数据进行自动降级，也可以配置开关实现人工降级。

降级的最终目的是保证核心服务可用，即使是有损的。而且有些服务是无法降级的（如加入购物车、结算）。

在进行降级之前要对系统进行梳理，看看系统是不是可以丢卒保帅；从而梳理出哪些必须誓死保护，哪些可降级；比如可以参考日志级别设置预案





**缓存击穿**":  "就是说某个key非常热点，访问非常频繁，处于集中式高并发访问的情况，当这个 key 在失效的瞬间，大量的请求就击穿了缓存，直接请求数据库

**解决方法**

+ 可以将热点数据设置为永远不过期；

  *我们把过期时间存在key对应的value里，如果发现要过期了，通过一个后台的异步线程进行缓存的构建，也就是“逻辑”过期*

+ 基于 redis or zookeeper 实现互斥锁，等待第一个请求构建完缓存之后，再释放锁，进而其它请求才能通过该 key 访问数据





### 热点key

缓存中的某些Key(可能对应用与某个促销商品)对应的value存储在集群中一台机器，使得所有流量涌向同一机器，成为系统的瓶颈，该问题的挑战在于它无法通过增加机器容量来解决。

1. 客户端热点key缓存：将热点key对应value并缓存在客户端本地，并且设置一个失效时间。
2. 将热点key分散为多个子key，然后存储到缓存集群的不同机器上，这些子key对应的value都和热点key是一样的。



### 数据库与缓存数据一致性

[数据和缓存一致性问题](https://blog.kido.site/2018/11/24/db-and-cache-preface/)

写完数据库后是否需要马上更新缓存还是直接删除缓存？

（1）如果写数据库的值与更新到缓存值是一样的，不需要经过任何的计算，可以马上更新缓存，但是如果对于那种写数据频繁而读数据少的场景并不合适这种解决方案，因为也许还没有查询就被删除或修改了，这样会浪费时间和资源

（2）如果写数据库的值与更新缓存的值不一致，写入缓存中的数据需要经过几个表的关联计算后得到的结果插入缓存中，那就没有必要马上更新缓存，只有删除缓存即可，等到查询的时候在去把计算后得到的结果插入到缓存中即可。

**所以一般的策略是当更新数据时，先删除缓存数据，然后更新数据库，而不是更新缓存，等要查询的时候才把最新的数据更新到缓存**
<img src="http://cdn.zblade.top/qiniu_img/20170903171144693.png" style="zoom:150%;" />



**加入队列策略**(我们利用MQ将所有**“读数据库” + “写缓存”**的步骤串行化)

（1）更新数据库数据；
（2）缓存因为种种问题删除失败
（3）将需要删除的key发送至消息队列
（4）自己消费消息，获得需要删除的key
（5）继续重试删除操作，直到成功



订阅binlog

> 通过解析binlog来刷新缓存，这样即使刷新失败，依然可以进行日志回放，再次刷新缓存



（1）更新数据库数据
（2）数据库会将操作信息写入binlog日志当中
（3）订阅程序提取出所需要的数据以及key
（4）另起一段非业务代码，获得该信息
（5）尝试删除缓存操作，发现删除失败
（6）将这些信息发送至消息队列
（7）重新从消息队列中获得该数据，重试操作。

 



### 线程模型

[Redis线程模型](https://www.cnblogs.com/barrywxx/p/8570821.html)

![](documents/JAVA笔记/graphviz-f0d024ca2782cbbe20e2cd1e52540d92f64b3a37.png)

redis 内部使用文件事件处理器 `file event handler`，这个文件事件处理器是单线程的，所以 redis 才叫做单线程的模型。它采用 **IO 多路复用机制**同时监听多个 socket，根据 socket 上的事件来选择对应的事件处理器进行处理。

文件事件处理器的结构包含 4 个部分：

- 多个 socket
- IO 多路复用程序
- 文件事件分派器
- 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器）



I/O **多路复用程序**

I/O 多路复用程序可以监听多个套接字的 `ae.h/AE_READABLE` 事件和 `ae.h/AE_WRITABLE` 事件， 这两类事件和套接字操作之间的对应关系如下：

- 当套接字变得可读时（客户端对套接字执行 `write` 操作，或者执行 `close` 操作）， 或者有新的可应答（acceptable）套接字出现时（客户端对服务器的监听套接字执行 `connect` 操作）， 套接字产生 `AE_READABLE` 事件。
- 当套接字变得可写时（客户端对套接字执行 `read` 操作）， 套接字产生 `AE_WRITABLE` 事件。

I/O 多路复用程序允许服务器同时监听套接字的 `AE_READABLE` 事件和 `AE_WRITABLE` 事件， 如果一个套接字同时产生了这两种事件， 那么文件事件分派器会优先处理 `AE_READABLE` 事件， 等到 `AE_READABLE` 事件处理完之后， 才处理 `AE_WRITABLE` 事件。

> 这也就是说， 如果一个套接字又可读又可写的话， 那么服务器将先读套接字， 后写套接字。



**一次完整的客户端与服务器连接事件示例**

让我们来追踪一次 Redis 客户端与服务器进行连接并发送命令的整个过程， 看看在过程中会产生什么事件， 而这些事件又是如何被处理的。

假设一个 Redis 服务器正在运作， 那么这个服务器的监听套接字的 `AE_READABLE` 事件应该正处于监听状态之下， 而该事件所对应的处理器为连接应答处理器。

如果这时有一个 Redis 客户端向服务器发起连接， 那么监听套接字将产生 `AE_READABLE` 事件， 触发连接应答处理器执行： 处理器会对客户端的连接请求进行应答， 然后创建客户端套接字， 以及客户端状态， 并将客户端套接字的 `AE_READABLE` 事件与命令请求处理器进行关联， 使得客户端可以向主服务器发送命令请求。

之后， 假设客户端向主服务器发送一个命令请求， 那么客户端套接字将产生 `AE_READABLE` 事件， 引发命令请求处理器执行， 处理器读取客户端的命令内容， 然后传给相关程序去执行。

执行命令将产生相应的命令回复， 为了将这些命令回复传送回客户端， 服务器会将客户端套接字的 `AE_WRITABLE` 事件与命令回复处理器进行关联： 当客户端尝试读取命令回复的时候， 客户端套接字将产生 `AE_WRITABLE` 事件， 触发命令回复处理器执行， 当命令回复处理器将命令回复全部写入到套接字之后， 服务器就会解除客户端套接字的 `AE_WRITABLE` 事件与命令回复处理器之间的关联。





**Redis是单线程模型为什么效率还这么高？**

+ 纯内存访问：数据存放在内存中，内存的响应时间大约是100纳秒，这是Redis每秒万亿级别访问的重要基础。
+ 非阻塞I/O：Redis采用epoll做为I/O多路复用技术的实现，再加上Redis自身的事件处理模型将epoll中的连接，读写，关闭都转换为了时间，不在I/O上浪费过多的时间。
+ 单线程避免了线程切换和竞态产生的消耗。

Redis采用单线程模型，每条命令执行如果占用大量时间，会造成其他线程阻塞，对于Redis这种高性能服务是致命的，所以Redis是面向高速执行的数据库



### redis事务

Redis事务有如下一些特点:

+ 事务中的命令序列执行的时候是原子性的,也就是说,其不会被其他客户端的命令中断. 这和传统的数据库的事务的属性是类似的.

+ 尽管Redis事务中的命令序列是原子执行的, 但是事务中的**命令序列执行可以部分成功**,这种情况下,Redis事务不会执行回滚操作. 这和传统关系型数据库的事务是有区别的.

  **redis同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚**

+ 尽管Redis有RDB和AOF两种数据持久化机制, 但是其设计目标是高效率的cache系统. **Redis事务只保证将其命令序列中的操作结果提交到内存中,不保证持久化到磁盘文件**. 更进一步的, Redis事务和RDB持久化机制没有任何关系, 因为RDB机制是对内存数据结构的全量的快照.由于AOF机制是一种增量持久化,所以事务中的命令序列会提交到AOF的缓存中.但是AOF机制将其缓存写入磁盘文件是由其配置的实现策略决定的,和Redis事务没有关系.



Redis事务涉及到`MULTI`, `EXEC`, `DISCARD`, `WATCH`和`UNWATCH`这五个命令:·

+ 事务开始的命令是`MULTI`, 该命令返回OK提示信息. **Redis不支持事务嵌套**,执行多次MULTI命令和执行一次是相同的效果.嵌套执行MULTI命令时,Redis只是返回错误提示信息.

+ `EXEC`是事务的提交命令,事务中的命令序列将被执行(或者不被执行,比如乐观锁失败等).该命令将返回响应数组,其内容对应事务中的命令执行结果.

+ `WATCH`命令是**开始执行乐观锁**,该命令的参数是key(可以有多个), Redis将执行WATCH命令的客户端对象和key进行关联,如果其他客户端修改了这些key,则执行WATCH命令的客户端将被设置乐观锁失败的标志.**该命令必须在事务开始前执行,即在执行MULTI命令前执行WATCH命令,否则执行无效,并返回错误提示信息.**

+ `UNWATCH`命令将**取消当前客户端对象的乐观锁key**,该客户端对象的事务提交将变成无条件执行.

+ `DISCARD`命令将结束事务,并且会丢弃全部的命令序列.

  > 需要注意的是,`EXEC`命令和`DISCARD`命令结束事务时,会调用`UNWATCH`命令,取消该客户端对象上所有的乐观锁key.

**事务的错误处理**

事务提交命令EXEC有可能会失败, 有三种类型的失败场景:

+ 在事务提交之前,**客户端执行的命令缓存失败**.比如命令的语法错误(命令参数个数错误, 不支持的命令等等).如果发生这种类型的错误,Redis将向客户端返回包含错误提示信息的响应.
+ 事务提交时,**之前缓存的命令有可能执行失败**.（==但Redis不会对事务做任何回滚补救操作==）
+ 由于**乐观锁失败**,事务提交时,将丢弃之前缓存的所有命令序列.

> 实际上，这就意味着只有程序错误才会导致Redis命令执行失败，这种错误很有可能在程序开发期间发现，一般很少在生产环境发现。
> Redis已经在系统内部进行功能简化，这样可以确保更快的运行速度，因为Redis不需要事务回滚的能力。

**乐观锁机制**·

关于乐观锁,需要注意的是:

+ `WATCH`命令必须在`MULTI`命令之前执行. `WATCH`命令可以执行多次.
+ `WATCH`命令可以指定乐观锁的多个`key`,如果在事务过程中,任何一个`key`被其他客户端改变,则当前客户端的乐观锁失败,事务提交时,将丢弃所有命令序列.
+ 多个客户端的`WATCH`命令可以指定相同的`key`.
+ `WATCH`命令指定乐观锁后,可以接着执行`MULTI`命令进入事务上下文,也可以在`WATCH`命令和`MULTI`命令之间执行其他命令. 具体使用方式取决于场景需求,不在事务中的命令将立即被执行.
+ 如果`WATCH`命令指定的乐观锁的`key`,被当前客户端改变,在事务提交时,乐观锁不会失败.
+ 如果`WATCH`命令指定的乐观锁的`key`具有超时属性,并且该`key`在`WATCH`命令执行后, 在事务提交命令`EXEC`执行前超时, 则乐观锁不会失败.如果该`key`被其他客户端对象修改,则乐观锁失败.

> **Redis事务其本质就是,以不可中断的方式依次执行缓存的命令序列,将结果保存到内存cache中**

[redis事务](https://redisbook.readthedocs.io/en/latest/feature/transaction.html)



### 无锁化编程

**一种实现思想**

假如有一个上述的post请求的URI部分是个覆盖写操作，reqid=abc123789def，服务部署在多台机器，在大前端将流量转发到Nginx之后根据reqid进行哈希,

经过 **Nginx负载均衡** 相同reqid的请求将被转发到一台机器上，当然你可能会说如果集群的机器动态调整呢？我只能说不要考虑那么多那么充分， **工程化去设计** 即可。

然而转发到一台机器仍然无法保证串行处理，因为单机仍然是多线程的，我们仍然需要将所有的reqid数据放到同一个线程处理，最终保证线程内串行，这个就需要借助于线程池的管理者Disper按照 **reqid哈希取模** 来进行多线程的负载均衡。

经过Nginx和线程内负载均衡，最终相同的reqid都将在线程内串行处理，有效避免了锁的使用，当然这种设计可能在reqid不均衡时造成 **线程饥饿** ，不过高并发大量请求的情况下还是可以的。

### 分布式锁

> 在 **分布式部署高并发场景** 下，经常会遇到资源的互斥访问的问题，最有效最普遍的方法是给共享资源或者对共享资源的操作加一把锁
>
> 分布式锁是 **控制分布式系统之间同步访问共享资源的一种方式** ，用于在分布式系统中协调他们之间的动作。

分布式锁一般有三种实现方式：

- 基于`数据库`在数据库中创建一张表，表里包含方法名等字段，并且在方法名字段上面创建唯一索引，执行某个方法需要使用此方法名向表中插入数据，成功插入则获取锁，执行结束则删除对应的行数据释放锁

- 基于缓存数据库`Redis Redis`性能好并且实现方便，但是单节点的分布式锁在故障迁移时产生安全问题

  *(在redis主从架构部署时，在`redis-master`实例宕机的时候，可能导致多个客户端同时完成加锁。极端情况下不能得到保证。作者都是这吗说的)*

  Redlock是Redis的作者 Antirez 提出的集群模式分布式锁，基于N个完全独立的Redis节点实现分布式锁的高可用

- 基于`ZooKeeper ZooKeeper `是以 Paxos 算法为基础的分布式应用程序协调服务，为分布式应用提供一致性服务的开源组件

[基于Redis的分布式锁和Redlock算法·](https://zhuanlan.zhihu.com/p/101599712)

[分布式锁正确实现](https://www.cnblogs.com/williamjie/p/9395659.html)

[redisession实现](http://blog.itpub.net/31545684/viewspace-2221023/)



**情景**

+ 锁无法释放问题：添加过期时间
+ 任务没有结束，锁提前过期：判断交易是否完成，如果没有就新开一条线程给key续过期时间
+ 单机锁无法保证时间的准确性



**简单应用**（单节点）

先拿`setnx`来争抢锁，抢到之后，再用`expire`给锁加一个过期时间防止锁忘记了释放

获取锁（一条命令）

```redis
SET resource_name my_random_value NX PX 30000
```

释放锁

 ```Lua
 if redis.call("get",KEYS[1]) == ARGV[1] then
     return redis.call("del",KEYS[1])
 else
     return 0
 end
 ```

多节点分布的问题

+ 持久化失败->**延迟重启**
+ 有一个稍微安全一点的方案是 **将锁的 `value` 值设置为一个随机数**，释放锁时先匹配随机数是否一致，然后再删除 key，这是为了 **确保当前线程占有的锁不会被其他线程释放**，除非这个锁是因为过期了而被服务器自动释放的。



多节点REDLOCK应用

```text
1. 客户端获取当前时间, 生成一个随机值作为锁的值
2. 依次尝试在所有5个redis上取得同一个锁 (使用类似单机redis锁的方法, 使用同样的key和同一个随机值)
   获取锁的操作本身需要设定一个比较小的超时时间 (如5-50ms), 防止在一个挂掉的redis上浪费太多时间
   如果一个redis不可用, 要尽快开始尝试下一个
3. 客户端计算获取锁一共用了多长时间, 通过用当前时间减去第1步得到的时间
   如果客户端获取了多数redis上的这个锁 (3 of 5), 并且这时还没有超过锁的超时时间, 
   这个锁就算是获取成功了
4. 如果锁获取成功了, 有效时间就按 锁超时时间-获取锁花费时间 算
5. 如果失败, 尝试在所有redis上解除锁
   (解除锁的操作是一  v段lua script, 删除一个key如果key的value是第1步生成的随机值)
```

+ 如果出现崩溃和重启

  如果我们的节点没有持久化机制，client 从 5 个 master 中的 3 个处获得了锁，然后其中一个重启了，这是注意 **整个环境中又出现了 3 个 master 可供另一个 client 申请同一把锁！** 违反了互斥性。如果我们开启了 AOF 持久化那么情况会稍微好转一些，因为 Redis 的过期机制是语义层面实现的，所以在 server 挂了的时候时间依旧在流逝，重启之后锁状态不会受到污染。但是考虑断电之后呢，AOF部分命令没来得及刷回磁盘直接丢失了，除非我们配置刷回策略为 fsnyc = always，但这会损伤性能。解决这个问题的方法是，**当一个节点重启之后，我们规定在 max TTL 期间它是不可用的，这样它就不会干扰原本已经申请到的锁，等到它 crash 前的那部分锁都过期了，环境不存在历史锁了，那么再把这个节点加进来正常工作。**

+ 防止GC时间过长导致锁失效：

  你需要在每次写操作时加入一个 fencing token。这个场景下，fencing token 可以是一个递增的数字（lock service 可以做到），每次有 client 申请锁就递增一次。

  client1 申请锁同时拿到 token33，然后它进入长时间的停顿锁也过期了。client2 得到锁和 token34 写入数据，紧接着 client1 活过来之后尝试写入数据，自身 token33 比 34 小因此写入操作被拒绝。注意这需要存储层来检查 token，但这并不难实现。



[关于分布式锁的一切](https://juejin.im/post/5bbb0d8df265da0abd3533a5#heading-22)

### 主从+哨兵

> 主从模式很好的解决了数据备份问题，并且由于主从服务数据几乎是一致的，因而可以将写入数据的命令发送给主机执行，而读取数据的命令发送给不同的从机执行，从而达到读写分离的目的
>
> **Redis可以使用主从同步，从从同步。第一次同步时，主节点做一次bgsave，并同时将后续修改操作记录到内存buffer，待完成后将rdb文件全量同步到复制节点，复制节点接受完成后将rdb镜像加载到内存。加载完成后，再通知主节点将期间修改的操作记录同步到复制节点进行重放就完成了同步过程。**

**问题**

- 同步故障

  - 复制数据延迟(不一致)   *----info  replication 查看延迟偏移量---zookeeper监听回调机制实现客户端通知*
  - 读取过期数据(Slave 不能删除数据)    *-----惰性删除和定期删除---Redis3.2版本中已经解决了这个问题，在此版本中slave节点读取数据之前会检查键过期时间来决定是否返回数据的。*
  - 从节点故障    --*哨兵机制*
  - 主节点故障

- 配置不一致

  - `maxmemory `不一致:丢失数据
  - 优化参数不一致:内存不一致.

- 避免全量复制

  - 选择小主节点(分片)、低峰期间操作.（防止第一次全量复制压力过大）
  - 如果节点运行 id 不匹配(如主节点重启、运行 id 发送变化)，此时要执行全量复制，应该配合哨兵和集群解决.
  - 主从复制挤压缓冲区不足产生的问题(网络中断，部分复制无法满足)，可增大复制缓冲区( `rel_backlog_size `参数).·

- 复制风暴

  > 复制风暴是指大量从节点对同一主节点或者同一台机器的多个主节点，在短时间内发起全量复制的过程。此时将导致被发起的主节点或机器产生大量开销，如 ：CPU、内存、硬盘、带宽等
  >
  > + 单节点复制风暴   ---**首先减少主节点挂在从节点的数量，或者采用树桩复制结构。**
  > + 单机复制风暴  ---集群和多节点部署---注意故障恢复机制，防止恢复时出现密集全量复制

  

**主从故障如何故障转移**·

a)主节点(master)故障，从节点slave-1端执行` slaveof no one`后变成新主节点；
b)其它的节点成为新主节点的从节点，并从新节点复制数据；
**c)需要人工干预，无法实现高可用。**

**配置**

```shell
#配置主从节点
127.0.0.1:6380> slaveof 127.0.0.1 6379
#配置哨兵（配置sentinel.conf）
#①每个sentinel的myid参数也要进行修改，因为sentinel之间是通过该属性来唯一区分其他sentinel节点的；
#②参数中sentinel monitor mymaster 127.0.0.1 6379 2这里的端口号6379是不用更改的，因为sentinel是通过检测主节点的状态来得知当前主节点的从节点有哪些的，因而设置为主节点的端口号即可
./src/redis-sentinel sentinel-26379.conf
#查看状态
info replication
```

**哨兵监控机制**

任务1：每个哨兵节点每`10`秒会向主节点和从节点发送`info`命令获取拓扑结构图

任务2：每个哨兵节点每隔`2`秒会向`redis`数据节点的**指定频道**上发送该哨兵节点对于主节点的判断以及当前哨兵节点的信息，同时每个哨兵节点也会订阅该频道，来了解其它哨兵节点的信息及对主节点的判断，其实就是通过消息`publish`和`subscribe`来完成的·

任务3：每隔`1`秒每个哨兵会向主节点、从节点及其余哨兵节点发送一次`ping`命令做一次心跳检测，这个也是哨兵用来判断节点是否正常的重要依据



**领导者哨兵选举流程**

a)每个在线的哨兵节点都可以成为领导者，当它确认（比如哨兵3）主节点下线时**（主观下线）**，会向其它哨兵发`is-master-down-by-addr`命令，征求判断并要求将自己·设置为领导者，由领导者处理故障转移；
b)当其它哨兵收到此命令时，可以同意或者拒绝它成为领导者；
c)如果哨兵3发现自己在选举的票数大于等于`num(sentinels)/2+1`时，将成为领导者，如果没有超过，继续选举…………**(客观下线)**



**故障转移**·

1. 选择 `slave-priority` 最高的节点。
2. 选择复制偏移量最大的节点(`同步数据最多`)。
3. 选择 `runId `最小的节点。

**部署建议**

a，sentinel节点应部署在多台物理机（线上环境）

b，至少三个且奇数个sentinel节点

c，通过以上我们知道，3个sentinel可同时监控一个主节点或多个主节点

  监听N个主节点较多时，如果sentinel出现异常，会对多个主节点有影响，同时还会造成sentinel节点产生过多的网络连接，

  **一般线上建议还是， 3个sentinel监听一个主节点**

 

 **数据同步**

redis 2.8版本以上使用·命令完成同步，过程分“全量”与“部分”复制

+ 全量复制：一般用于初次复制场景（第一次建立SLAVE后全量）
+ 部分复制：网络出现问题，从节点再次连接主节点时，主节点补发缺少的数据，每次数据增量同步
+ 心跳：主从有长连接心跳，主节点默认每`10S`向从节点发ping命令，`repl-ping-slave-period`控制发送频率·



### 集群

> 当遇到单机内存，并发和流量瓶颈等问题时，可采用Cluster方案达到负载均衡的目的。并且从另一方面讲，redis中sentinel有效的解决了故障转移的问题，也解决了主节点下线客户端无法识别新的可用节点的问题，但是如果是从节点下线了，sentinel是不会对其进行故障转移的，并且连接从节点的客户端也无法获取到新的可用从节点

 redis集群中数据是和槽（slot）挂钩的，其总共定义了`16384`个槽，所有的数据根据一致哈希算法会被映射到这`16384`个槽中的某个槽中。

> slot=CRC16（key）/16384

数据的存储只和槽有关，并且槽的数量是一定的，由于一致hash算法·是一定的，因而将这`16384`个槽分配给无论多少个redis实例，对于确认的数据其都将被分配到确定的槽位上。redis集群通过这种方式来达到redis的高效和高可用性目的。

#### **一致性哈希和哈希槽的区别**·

一致性哈希是创建虚拟节点来实现节点宕机后的数据转移并保证数据的安全性和集群的可用性的。

redis cluster<mark>是采用master节点有多个slave节点机制来保证数据的完整性的,master节点写入数据</mark>，slave节点同步数据。当master节点挂机后，slave节点会通过选举机制选举出一个节点变成master节点，实现高可用。**但是这里有一点需要考虑，如果master节点存在热点缓存，某一个时刻某个key的访问急剧增高，这时该mater节点可能操劳过度而死，随后从节点选举为主节点后，同样宕机，一次类推，造成缓存雪崩**

**配置**

```shell
#redis.conf配置文件设置
port 6379
cluster-enabled yes
cluster-node-timeout 15000
cluster-config-file "nodes-6379.conf"
pidfile /var/run/redis_6379.pid
logfile "cluster-6379.log"
dbfilename dump-cluster-6379.rdb
appendfilename "appendonly-cluster-6379.aof"
```

```shell
#启动配置文件
./src/redis-server cluster-6379.conf
...
```

```shell
#设置槽位
#先连接
./src/redis-cli -p 6379
#设置多个槽位
127.0.0.1:6379>cluster meet 127.0.0.1 6380
...
```

```shell
#查看状态
127.0.0.1:6379> cluster nodes
4fa7eac4080f0b667ffeab9b87841da49b84a6e4 127.0.0.1:6384 master - 0 1468073975551 5 connected
cfb28ef1deee4e0fa78da86abe5d24566744411e 127.0.0.1:6379 myself,master - 0 0 0 connected
be9485a6a729fc98c5151374bc30277e89a461d8 127.0.0.1:6383 master - 0 1468073978579 4 connected
40622f9e7adc8ebd77fca0de9edfe691cb8a74fb 127.0.0.1:6382 master - 0 1468073980598 3 connected
8e41673d59c9568aa9d29fb174ce733345b3e8f1 127.0.0.1:6380 master - 0 1468073974541 1 connected
40b8d09d44294d2e23c7c768efc8fcd153446746 127.0.0.1:6381 master - 0 1468073979589 2 connected
```

```shell
#添加虚拟槽(类似于一致性hash提供给虚拟节点)
127.0.0.1:6379>cluster addslots {0...5461}
#...之后我们还可以分配主从节点，进一步提高可靠性
```

**其他集群案例**

**Redis Sharding集群**

1. 采用`一致性哈希算法(consistent hashing)`，将key和节点name同时hashing，然后进行映射匹配，采用的算法是`MURMUR_HASH`。采用一致性哈希而不是采用简单类似哈希求模映射的主要原因是当增加或减少节点时，不会产生由于重新匹配造成的rehashing。一致性哈希只影响相邻节点key分配，影响量小。
2. 为了避免一致性哈希只影响相邻节点造成节点分配压力，ShardedJedis会对每个Redis节点根据名字(没有，Jedis会赋予缺省名字)会**虚拟化出160个虚拟节点进行散列**。根据权重`weight`，也可虚拟化出160倍数的虚拟节点。用虚拟节点做映射匹配，可以在增加或减少Redis节点时，key在各Redis节点移动再分配更均匀，而不是只有相邻节点受影响。
3. `ShardedJedis`支持`keyTagPattern`模式，即抽取key的一部分`keyTag`做`sharding`，这样通过合理命名key，可以将一组相关联的key放入同一个Redis节点，这在避免跨节点访问相关数据时很重要。

特点：`resharding`·，即预先根据系统规模尽量部署好多个`Redis`实例，这些实例占用系统资源很小，一台物理机可部署多个，让他们都参与`sharding`，当需要扩容时，选中一个实例作为主节点，新加入的`Redis`节点作为从节点进行数据复制。

presharding是预先分配好足够的分片，扩容时只是将属于某一分片的原Redis实例替换成新的容量更大的Redis实例。参与sharding的分片没有改变，所以也就不存在key值从一个区转移到另一个分片区的现象，只是将属于同分片区的键值从原Redis实例同步到新Redis实例。


### **使用场景**

+ **热点数据缓存**
+ **会话维持session**
+ **分布式锁SETNX**
+ **表缓存**
+ **消息队列 list**()提供阻塞方法
+ **计数器 string**

### **注意点**

[关注最后的开发建议：不要用keys](https://mp.weixin.qq.com/s/SGOyGGfA6GOzxwD5S91hLw)

大家知道 Redis 是单线程程序，是按照顺序执行指令的，如果说我们现在正在执行 keys 命令，那么其它指令必须等到当前的 keys 指令执行完了才可以继续，再加上 keys 操作是遍历算法，复杂度是 O (n)，乍一想就知道问题所在了，当实例中数据量过大的时候，Redis 服务可能会卡顿，其余指令可能会延时甚至超时报错....

使用：`scan - cursor [MATCH pattern] [COUNT count]·`

> 复杂度虽然也是 O (n)，但是它是通过游标分步进行的，不会阻塞线程；
>
> scan指令可以无阻塞的提取出指定模式的`key`列表，但是会有一定的重复概率，在客户端做一次去重就可以了，但是整体所花费的时间会比直接用keys指令长。

[scan用法](https://learnku.com/articles/25892)

## **IO**

[NIO浅析](https://zhuanlan.zhihu.com/p/23488863)

### 分类

**java提供的API IO模型:`IO NIO AIO`**

**Java中提供的IO有关的API，在文件处理的时候，其实依赖操作系统层面的IO操作实现的。**比如在Linux 2.6以后，Java中NIO和AIO都是通过`epoll`来实现的，而在Windows上，AIO是通过`IOCP`来实现的。

可以把Java中的BIO、NIO和AIO理解为是Java语言对操作系统的各种IO模型的封装。程序员在使用这些API的时候，不需要关心操作系统层面的知识，也不需要根据不同操作系统编写不同的代码。只需要使用Java的API就可以了。

**操作系统层面的IO模型**:

<mark>**阻塞IO模型**、**非阻塞IO模型**、**IO复用模型**、**信号驱动IO模型**以及**异步IO模型**。</mark>



**阻塞式IO**

![image-20200220131637020](documents/JAVA笔记/image-20200220131637020.png)

应用进程通过系统调用 `recvfrom` 接收数据，但由于内核还未准备好数据报，应用进程就会阻塞住，直到内核准备好数据报，`recvfrom` 完成数据报复制工作，应用进程才能结束阻塞状态。



**非阻塞式IO**

![image-20200220131703025](documents/JAVA笔记/image-20200220131703025.png)

应用进程通过 `recvfrom` 调用不停的去和内核交互，直到内核准备好数据。如果没有准备好，内核会返回`error`，应用进程在得到`error`后，过一段时间再发送`recvfrom`请求。在两次发送请求的时间段，进程可以先做别的事情。



**信号驱动IO模型**

![image-20200220131836101](documents/JAVA笔记/image-20200220131836101.png)

应用进程预先向内核注册一个**信号处理函数**，然后用户进程返回，并且**不阻塞**，当内核数据准备就绪时会发送一个信号给进程，用户进程便在信号处理函数中开始把数据拷贝的用户空间中。



**IO复用模型**

![image-20200220131932861](documents/JAVA笔记/image-20200220131932861.png)

IO多路转接是多了一个`select`函数，多个进程的IO可以注册到同一个`select`上，当用户进程调用该`select`，`select`会监听所有注册好的IO，如果所有被监听的IO需要的数据都没有准备好时，`select`调用进程会阻塞。当任意一个IO所需的数据准备好之后，`select`调用就会返回，然后进程在通过`recvfrom`来进行数据拷贝。

**这里的IO复用模型，并没有向内核注册信号处理函数，所以，他并不是非阻塞的。**进程在发出`select`后，要等到`select`监听的所有IO操作中至少有一个需要的数据准备好，才会有返回，并且也需要再次发送请求去进行文件的拷贝。



**异步IO模型**

**上述IO模型的数据拷贝过程，都是同步进行的**。(信号驱动IO模型数据准备阶段式异步的但是拷贝依然是同步的)

![image-20200220140943855](documents/JAVA笔记/image-20200220140943855.png)

用户进程发起`aio_read`操作之后，给内核传递**描述符**、**缓冲区指针**、**缓冲区大小**等，告诉内核当整个操作完成时，如何通知进程，然后就立刻去做其他事情了。当内核收到`aio_read`后，会立刻返回，然后内核开始等待数据准备，数据准备好以后，直接把数据拷贝到用户控件，然后再通知进程本次IO已经完成。



![image-20200220141040606](documents/JAVA笔记/image-20200220141040606.png)



### IO、NIO、AIO区别

**BIO**: 

用 **BIO 通信模型** 的服务端，通常由一个独立的 Acceptor 线程负责监听客户端的连接。我们一般通过在`while(true)` 循环中服务端会调用 `accept()` 方法等待接收客户端的连接的方式监听请求，请求一旦接收到一个连接请求，就可以建立通信套接字在这个通信套接字上进行读写操作，此时不能再接收其他客户端连接请求，只能等待同当前连接的客户端的操作执行完成， 不过可以通过多线程来支持多个客户端的连接



#### BIO缺点

1. 线程的创建和销毁成本很高，在Linux这样的操作系统中，线程本质上就是一个进程。创建和销毁都是重量级的系统函数。
2. 线程本身占用较大内存，像Java的线程栈，一般至少分配512K～1M的空间，如果系统中的线程数过千，恐怕整个JVM的内存都会被吃掉一半。
3. 线程的切换成本是很高的。操作系统发生线程切换的时候，需要保留线程的上下文，然后执行系统调用。如果线程数过高，可能执行线程切换的时间甚至会大于线程执行的时间，这时候带来的表现往往是系统load偏高、CPU sy使用率特别高（超过20%以上)，导致系统几乎陷入不可用的状态。
4. 容易造成锯齿状的系统负载。因为系统负载是用活动线程数或CPU核心数，一旦线程数量高但外部网络环境不是很稳定，就很容易造成大量请求的结果同时返回，激活大量阻塞线程从而使系统负载压力过大。

**NIO** :

NIO是一种同步非阻塞的I/O模型，在Java 1.4 中引入了 NIO 框架，对应 java.nio 包，提供了 Channel , Selector，Buffer等抽象。

NIO中的N可以理解为Non-blocking，不单纯是New。它支持面向缓冲的，基于通道的I/O操作方法。 NIO提供了与传统BIO模型中的 `Socket` 和 `ServerSocket` 相对应的 `SocketChannel` 和 `ServerSocketChannel` 两种不同的套接字通道实现,两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用就像传统中的支持一样，比较简单，但是性能和可靠性都不好；非阻塞模式正好与之相反。对于低负载、低并发的应用程序，可以使用同步阻塞I/O来提升开发速率和更好的维护性；对于高负载、高并发的（网络）应用，应使用 NIO 的非阻塞模式来开发。



#### NIO特点

- 事件驱动模型
- 避免多线程
- 单线程处理多任务
- **非阻塞I/O，I/O读写不再阻塞，而是返回0**（指channel操作的时候可以选择注册成非阻塞）
- 基于block的传输，通常比基于流的传输更高效
- 更高级的IO函数，zero-copy
- IO多路复用大大提高了Java网络应用的可伸缩性和实用性

NIO由**原来的阻塞读写（占用线程）变成了单线程轮询事件**，找到可以进行读写的网络描述符进行读写。除了事件的轮询是阻塞的（没有可干的事情必须要阻塞），剩余的I/O操作都是纯CPU操作，没有必要开启多线程。

**AIO**: 

也就是 NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2,它是**异步非阻塞**的IO模型。异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

AIO 是异步IO的缩写，虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是**同步**的。

**在 Windows 中 JDK 直接采用了 IOCP 的支持**

**linux中使用的epoll**





### NIO的三个主要组成部分

> **Channel（通道）、Buffer（缓冲区）、Selector（选择器）**

#### **Channel**

Channel（通道）：Channel是一个对象，可以通过它读取和写入数据。可以把它看做是IO中的流，不同的是：

- Channel是`双向`的，既可以读又可以写，而流是单向的
- Channel可以进行`异步`的读写
- 对Channel的读写必须通过`buffer对象`

在Java NIO中的Channel主要有如下几种类型：

- `FileChannel`：从文件读取数据的（没有异步模式）
- `DatagramChannel`：读写UDP网络协议数据
- `SocketChannel`：读写TCP网络协议数据
- `ServerSocketChannel`：可以监听TCP连接



##### FileChannel

**读取文件内容：**

```java
ByteBuffer buffer = ByteBuffer.allocate(1024);

int num = fileChannel.read(buffer);
```

前面我们也说了，所有的 Channel 都是和 Buffer 打交道的。

**写入文件内容：**

```java
ByteBuffer buffer = ByteBuffer.allocate(1024);
buffer.put("随机写入一些内容到 Buffer 中".getBytes());
// Buffer 切换为读模式
buffer.flip();
while(buffer.hasRemaining()) {
    // 将 Buffer 中的内容写入文件
    fileChannel.write(buffer);
}
```



##### SocketChannel

打开一个 TCP 连接：

```java
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("https://www.javadoop.com", 80));
```

当然了，上面的这行代码等价于下面的两行：

```java
// 打开一个通道
SocketChannel socketChannel = SocketChannel.open();
// 发起连接
socketChannel.connect(new InetSocketAddress("https://www.javadoop.com", 80));
```

SocketChannel 的读写和 FileChannel 没什么区别，就是操作缓冲区。

```java
// 读取数据
socketChannel.read(buffer);

// 写入数据到网络连接中
while(buffer.hasRemaining()) {
    socketChannel.write(buffer);   
}
```



##### ServerSocketChannel

之前说 SocketChannel 是 TCP 客户端，这里说的 ServerSocketChannel 就是对应的服务端。

ServerSocketChannel 用于监听机器端口，管理从这个端口进来的 TCP 连接。

```java
// 实例化
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
// 监听 8080 端口
serverSocketChannel.socket().bind(new InetSocketAddress(8080));

while (true) {
    // 一旦有一个 TCP 连接进来，就对应创建一个 SocketChannel 进行处理
    SocketChannel socketChannel = serverSocketChannel.accept();
}
```

ServerSocketChannel 不和 Buffer 打交道了，因为它并不实际处理数据，它一旦接收到请求后，实例化 SocketChannel，之后在这个连接通道上的数据传递它就不管了，因为它需要继续监听端口，等待下一个连接。



##### DatagramChannel

UDP 和 TCP 不一样，DatagramChannel 一个类处理了服务端和客户端。

**监听端口：**

```java
DatagramChannel channel = DatagramChannel.open();
channel.socket().bind(new InetSocketAddress(9090));
ByteBuffer buf = ByteBuffer.allocate(48);

channel.receive(buf);
```

**发送数据：**

```java
String newData = "New String to write to file..."
                    + System.currentTimeMillis();

ByteBuffer buf = ByteBuffer.allocate(48);
buf.put(newData.getBytes());
buf.flip();

int bytesSent = channel.send(buf, new InetSocketAddress("jenkov.com", 80));
```





#### **Buffer**

Buffer是一个对象，它包含一些要写入或者读到Stream对象的。应用程序不能直接对 Channel 进行读写操作，而必须通过 Buffer 来进行，即 Channel 是通过 Buffer 来读写数据的。

在NIO中，所有的数据都是用Buffer处理的，它是NIO读写数据的中转池。==Buffer实质上是一个数组，通常是一个字节数据，但也可以是其他类型的数组。但一个缓冲区不仅仅是一个数组，重要的是它提供了对**数据的结构化访问，而且还可以跟踪系统的读写进程**。==



使用 `Buffer `读写数据一zes般遵循以下四个步骤：

1.写入数据到 `Buffer`；

2.调用 `flip() `方法；

3.从 `Buffer `中读取数据；

4.调用 `clear() `方法或者` compact()` 方法。

当向 Buffer 写入数据时，Buffer 会记录下写了多少数据。一旦要读取数据，需要通过 `flip() `方法将 Buffer 从**写模式切换到读模式**。在读模式下，可以读取之前写入到 Buffer 的所有数据。

一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。有两种方式能清空缓冲区：**调用 clear()** 或 **compact() 方法**。

+ clear() 方法会清空整个缓冲区。
+ compact() 方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。



**Buffer主要有如下几种**

- **ByteBuffer**
- **CharBuffer**
- **DoubleBuffer**
- **FloatBuffer**
- **IntBuffer**
- **LongBuffer**
- **ShortBuffer**



##### 重要属性

 **capacity**，它代表这个缓冲区的容量，一旦设定就不可以更改。

**position** 的初始值是 0，每往 Buffer 中写入一个值，position 就自动加 1，代表下一次的写入位置。读操作的时候也是类似的，每读一个值，position 就自动加 1。

从写操作模式到读操作模式切换的时候（**flip**），position 都会归零，这样就可以从头开始读写了。

**Limit**：写操作模式下，limit 代表的是最大能写入的数据，这个时候 limit 等于 capacity。写结束后，切换到读模式，此时的 limit 等于 Buffer 中实际的数据大小，因为 Buffer 不一定被写满了。

```java
public final Buffer flip() {
    limit = position; // 将 limit 设置为实际写入的数据数量
    position = 0; // 重置 position 为 0
    mark = -1; // mark 之后再说
    return this;
}
```





**mark() & reset()**

除了 position、limit、capacity 这三个基本的属性外，还有一个常用的属性就是 mark。

mark 用于临时保存 position 的值，每次调用 mark() 方法都会将 mark 设值为当前的 position，便于后续需要的时候使用。reset用于返回保存值



**rewind() & clear() & compact()**

**rewind()**：会重置 position 为 0，通常用于重新从头读写 Buffer。

**clear()**：有点重置 Buffer 的意思，相当于重新实例化了一样

**compact()**：和 clear() 一样的是，它们都是在准备往 Buffer 填充新的数据之前调用。但是不同在于，调用这个方法以后，会先处理还没有读取的数据，也就是 position 到 limit 之间的数据（还没有读过的数据），先将这些数据移到左边，然后在这个基础上再开始写入。很明显，此时 limit 还是等于 capacity，position 指向原来数据的右边。





#### Selector（选择器对象）

首先需要了解一件事情就是线程上下文切换开销会在高并发时变得很明显，这是同步阻塞方式的低扩展性劣势·

> `Selector`是一个对象，它可以注册到很多个`Channel`上，监听各个`Channel`上发生的事件，并且能够根据事件情况决定Channel读写。这样，通过一个线程管理多个`Channel`，就可以处理大量网络连接了。
>
> 有了`Selector`，我们就可以利用一个线程来处理所有的channels。线程之间的切换对操作系统来说代价是很高的，并且每个线程也会占用一定的系统资源。所以，对系统来说使用的线程越少越好。

**1.如何创建一个Selector**

Selector 就是您注册对各种 I/O 事件兴趣的地方，而且当那些事件发生时，就是这个对象告诉您所发生的事件。

```java
Selector selector = Selector.open();
```

**2.注册Channel到Selector**

为了能让`Channel`和`Selector`配合使用，我们需要把`Channel`注册到`Selector`上。通过调用 `channel.register（）`方法来实现注册：

```java
channel.configureBlocking(false);
SelectionKey key =channel.register(selector,SelectionKey.OP_READ);
```

注意，注册的Channel 必须设置成**非阻塞模式** 才可以,否则异步IO就无法工作，这就意味着我们不能把一个`FileChannel`注册到`Selector`，因为`FileChannel`没有非阻塞模式，但是网络编程中的SocketChannel是可以的。

**3.调用 `select() `方法获取通道信息。用于判断是否有我们感兴趣的事件已经发生了。**

事件

- SelectionKey.OP_READ

  > 对应 00000001，通道中有数据可以进行读取

- SelectionKey.OP_WRITE

  > 对应 00000100，可以往通道中写入数据

- SelectionKey.OP_CONNECT

  > 对应 00001000，成功建立 TCP 连接

- SelectionKey.OP_ACCEPT

  > 对应 00010000，接受 TCP 连接

我们可以同时监听一个 Channel 中的发生的多个事件，比如我们要监听 ACCEPT 和 READ 事件，那么指定参数为二进制的 000**1**000**1** 即十进制数值 17 即可。

注册方法返回值是 **SelectionKey** 实例，它包含了 Channel 和 Selector 信息，也包括了一个叫做 Interest Set 的信息，即我们设置的我们感兴趣的正在监听的事件集合。



**SelectionKey**

请注意对`register()`的调用的返回值是一个`SelectionKey`。 `SelectionKey `代表这个通道在此 `Selector `上注册。当某个 `Selector `通知您某个传入事件时，它是通过提供对应于该事件的 `SelectionKey `来进行的。`SelectionKey `还可以用于**取消通道的注册**。

`SelectionKey`中包含如下属性：

- The interest set
- The ready set
- The Channel
- The Selector
- An attached object (optional)



#### 新技术

NIO的特性

1. IO是面向流的，NIO是面向缓冲的；
2. IO是阻塞的，NIO是非阻塞的；
3. IO是单线程的，NIO 是通过选择器来模拟多线程的；

##### 内存映射

内存映射文件(memory-mappedfile)能让你创建和修改那些大到无法读入内存的文件。有了内存映射文件，你就可以认为文件已经全部读进了内存，然后把它当成一个非常大的数组来访问了。将文件的一段区域映射到内存中，比传统的文件处理速度要快很多。内存映射文件它虽然最终也是要从磁盘读取数据，但是它并不需要将数据读取到OS内核缓冲区，而是直接将进程的用户私有地址空间中的一部分区域与文件对象建立起映射关系，就好像直接从内存中读、写文件一样，速度当然快了。

NIO中内存映射主要用到以下两个类：

1. java.nio.MappedByteBuffer
2. java.nio.channels.FileChannel

支持三种模式:**只读,只写,私有**



内存映射文件的优点：

+ 用户进程将文件数据视为内存，因此不需要发出read()或write()系统调用。
+ 当用户进程触摸映射的内存空间时，将自动生成页面错误，以从磁盘引入文件数据。 如果用修改映射的内存空间，受影响的页面将自动标记为脏，并随后刷新到磁盘以更新文件。
+ 操作系统的虚拟内存子系统将执行页面的智能缓存，根据系统负载自动管理内存。
+ 数据始终是页面对齐的，不需要缓冲区复制。
+ 可以映射非常大的文件，而不消耗大量内存来复制数据。

##### 字符和编码

**大部分的操作系统在I/O与文件存储方面仍是以字节为导向的，所以无论使用何种编码，Unicode或其他编码，在字节序列和字符集编码之间仍需要进行转化。**

在NIO中提供了两个类CharsetEncoder和CharsetDecoder来实现编码转换方案

CharsetEncoder类是一个状态编码引擎。实际上，编码器有状态意味着它们**不是线程安全**的

```java
//nio字符集编码
public class testCharacter {
    public static void main(String[] a){
       //设置编码器
        Charset charset = Charset.forName("GBK");
        //获取缓冲器
        CharBuffer charBuffer = CharBuffer.allocate(1024);
        charBuffer.put("skdfns史可法你00");
        //编码
        charBuffer.flip();
        ByteBuffer byteBuffer = charset.encode(charBuffer);
        for(int i = 0;i < byteBuffer.limit();i ++){
            System.out.println(byteBuffer.get());
        }
        //解码
        byteBuffer.flip();
        CharBuffer charBuffer1 = charset.decode(byteBuffer);
        for(int i = 0;i < charBuffer1.limit();i ++){
            System.out.println(charBuffer1.get());
        }
    }
}
```



##### 非阻塞IO

NIO 的非阻塞 I/O 机制是围绕 **选择器**和 **通道**构建的。 Channel 类表示服务器和客户机之间的一种通信机制。Selector 类是 Channel 的多路复用器。 Selector 类将传入客户机请求多路分用并将它们分派到各自的请求处理程序。NIO 设计背后的基石是**反应器(Reactor)设计模式。**

Reactor负责IO事件的响应，一旦有事件发生，便广播发送给相应的handler去处理

在Reactor模式中，包含如下角色：

- Reactor 将I/O事件发派给对应的Handler
- Acceptor 处理客户端连接请求
- Handlers 执行非阻塞读/写

```java
public class NIOServer {
    private static final Logger LOGGER = LoggerFactory.getLogger(NIOServer.class);

    public static void main(String[] args) throws IOException {
        Selector selector = Selector.open();
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        serverSocketChannel.configureBlocking(false);
        serverSocketChannel.bind(new InetSocketAddress(1234));
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        while (true) {
            if(selector.selectNow() < 0) {
                continue;
            }
            //获取注册的channel
            Set<SelectionKey> keys = selector.selectedKeys();
            //遍历所有的key
            Iterator<Selection	Key> iterator = keys.iterator();
            while(iterator.hasNext()) {
                SelectionKey key = iterator.next();
                iterator.remove();
                //如果通道上有事件发生
                if (key.isAcceptable()) {
                    //获取该通道
                    ServerSocketChannel acceptServerSocketChannel = (ServerSocketChannel) key.channel();
                    SocketChannel socketChannel = acceptServerSocketChannel.accept();
                    socketChannel.configureBlocking(false);
                    LOGGER.info("Accept request from {}", socketChannel.getRemoteAddress());
                    //同时将SelectionKey标记为可读，以便读取。
                    SelectionKey readKey = socketChannel.register(selector, SelectionKey.OP_READ);
                    //利用SelectionKey的attache功能绑定Acceptor 如果有事情，触发Acceptor
                    //Processor对象为自定义处理请求的类
                    readKey.attach(new Processor());
                } else if (key.isReadable()) {
                    Processor processor = (Processor) key.attachment();
                    processor.process(key);
                }
            }
        }
    }
}

/**
 * Processor类中设置一个线程池来处理请求，
 * 这样就可以充分利用多线程的优势
 */
class Processor {
    private static final Logger LOGGER = LoggerFactory.getLogger(Processor.class);
    private static final ExecutorService service = Executors.newFixedThreadPool(16);

    public void process(final SelectionKey selectionKey) {
        service.submit(new Runnable() {
            @Override
            public void run() {
                ByteBuffer buffer = null;
                SocketChannel socketChannel = null;
                try {
                    buffer = ByteBuffer.allocate(1024);
                    socketChannel = (SocketChannel) selectionKey.channel();
                    int count = socketChannel.read(buffer);
                    if (count < 0) {
                        socketChannel.close();
                        selectionKey.cancel();
                        LOGGER.info("{}\t Read ended", socketChannel);
                    } else if(count == 0) {
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
                LOGGER.info("{}\t Read message {}", socketChannel, new String(buffer.array()));
            }
        });
    }
}

```

多路复用IO为何比非阻塞IO模型的效率高是因为在非阻塞IO中，不断地询问socket状态时通过用户线程去进行的，而在多路复用IO中，轮询每个socket状态是内核在进行的，这个效率要比用户线程要高的多。



##### 文件锁定

```java
// 如果请求的锁定范围是有效的，阻塞直至获取锁
 public final FileLock lock()  
// 尝试获取锁非阻塞，立刻返回结果  
 public final FileLock tryLock()  
   
// 第一个参数：要锁定区域的起始位置  
// 第二个参数：要锁定区域的尺寸,  
// 第三个参数：true为共享锁，false为独占锁  
 public abstract FileLock lock (long position, long size, boolean shared)  
 public abstract FileLock tryLock (long position, long size, boolean shared) 

```



#### NIO AsynchronousFileChannel异步文件通道



### AIO

Java 异步 IO 提供了两种使用方式，分别是**返回 Future 实例**和**使用回调函数**。

**返回Future实例**

- `future.isDone();`

  判断操作是否已经完成，包括了**正常完成、异常抛出、取消**

- `future.cancel(true);`

  取消操作，方式是中断。参数 true 说的是，即使这个任务正在执行，也会进行中断。

- `future.isCancelled();`

  是否被取消，只有在任务正常结束之前被取消，这个方法才会返回 true

- `future.get(); `

  这是我们的老朋友，获取执行结果，阻塞。

- `future.get(10, TimeUnit.SECONDS);`

  如果上面的 get() 方法的阻塞你不满意，那就设置个超时时间。



**提供 CompletionHandler 回调函数**

用法

> 注意，参数上有个 attachment，虽然不常用，我们可以在各个支持的方法中传递这个参数值

```java
AsynchronousServerSocketChannel listener = AsynchronousServerSocketChannel.open().bind(null);

// accept 方法的第一个参数可以传递 attachment
listener.accept(attachment, new CompletionHandler<AsynchronousSocketChannel, Object>() {
    public void completed(
      AsynchronousSocketChannel client, Object attachment) {
          // 
      }
    public void failed(Throwable exc, Object attachment) {
          // 
      }
});
```



#### Channel

##### **AsynchronousFileChannel**

AIO 的读写主要也还是与 Buffer 打交道，这个与 NIO 是一脉相承的。

另外，还提供了用于将内存中的数据刷入到磁盘的方法：

```java
public abstract void force(boolean metaData) throws IOException;
```

> 因为我们对文件的写操作，操作系统并不会直接针对文件操作，系统会缓存，然后周期性地刷入到磁盘。如果希望将数据及时写入到磁盘中，以免断电引发部分数据丢失，可以调用此方法。参数如果设置为 true，意味着同时也将文件属性信息更新到磁盘。

还有，还提供了对文件的锁定功能，我们可以锁定文件的部分数据，这样可以进行排他性的操作。

```java
public abstract Future<FileLock> lock(long position, long size, boolean shared);
```

> position 是要锁定内容的开始位置，size 指示了要锁定的区域大小，shared 指示需要的是共享锁还是排他锁

**注意: AsynchronousFileChannels 不属于 group**。但是它们也是关联到一个线程池的，如果不指定，会使用系统默认的线程池，如果想要使用指定的线程池，可以在实例化的时候使用以下方法：

```java
public static AsynchronousFileChannel open(Path file,
                                           Set<? extends OpenOption> options,
                                           ExecutorService executor,
                                           FileAttribute<?>... attrs) {
    ...
}
```

##### AsynchronousServerSocketChannel

##### AsynchronousSocketChannel

##### Asynchronous Channel Groups

> 异步 IO 一定存在一个线程池，这个线程池负责接收任务、处理 IO 事件、回调等。这个线程池就在 group 内部，group 一旦关闭，那么相应的线程池就会关闭。
>
> AsynchronousServerSocketChannels 和     AsynchronousSocketChannels 是属于 group 的，当我们调用 AsynchronousServerSocketChannel 或 AsynchronousSocketChannel 的 open() 方法的时候，相应的 channel 就属于默认的 group，这个 group 由 JVM 自动构造并管理。



想要使用自己定义的 group，这样可以对其中的线程进行更多的控制，使用以下几个方法即可：

- `AsynchronousChannelGroup.withCachedThreadPool(ExecutorService executor, int initialSize)`
- `AsynchronousChannelGroup.withFixedJava 非阻塞 IO 和异步 IOThreadPool(int nThreads, ThreadFactory threadFactory)`
- `AsynchronousChannelGroup.withThreadPool(ExecutorService executor)`





[Java 非阻塞 IO 和异步 IO](https://javadoop.com/post/nio-and-aio)



### 事件驱动模型和消息驱动模型

事件驱驱动架构由三个基本组件构成，事件、事件处理器、事件循环。事件产生后发送给事件循环，事件循环将每个事件分派给个各个事件处理器。事件A由处理器A处理，事件B将被处理器B处理。

select

poll

epoll



## Servlet

### Web容器

[servlet工作原理之tomcat篇](https://blog.csdn.net/guzhangyu12345/article/details/91047750#servlet容器的启动过程)

> Web容器是一种服务程序，给处于其中的应用程序组件提供环境，使其直接跟容器中的环境变量交互，不必关注其它系统问题。主要由应用服务器来实现，如Tomcat、JBoss、Weblogic、WebSphere等。

**Servlet容器的主要任务是管理Servlet的生命周期，而Web容器主要任务是管理Web应用程序。**



一个web应用对应一个context容器，添加一个应用时将会创建一个StandardContext容器，并且给这个context容器设置必要的参数，url和path分别代表这个应用在tomcat中的访问路径和这个应用实际的物理路径。其中最重要的一个配置是ContextConfig，这个类将会负责整个web应用配置的解析工作，最后将这个context容器加到父容器host中。

![img](https://img-blog.csdnimg.cn/20190606175306985.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1emhhbmd5dTEyMzQ1,size_16,color_FFFFFF,t_70)



**servlet**

![å¾ 5.Servlet é¡¶å±ç±»å³èå¾](documents/JAVA笔记/image010.jpg)

1. 抽象类`HttpServlet`继承抽象类`GenericServlet`，其有两个比较关键的方法，`doGet()`和`doPost()`

2. `GenericServlet`实现接口`Servlet`,`ServletConfig`,`Serializable`

3. `MyServlet`(用户自定义`Servlet`类)继承`HttpServlet`，重写抽象类`HttpServlet`的`doGet()`和`doPost()`方法

注：任何一个用户自定义`Servlet`，只需重写抽象类`HttpServlet`的`doPost()`和`doGet()`即可

### 容器中的执行过程

 Servlet只有放在容器中，方可执行，且Servlet容器种类较多，如Tomcat,WebLogic等。下图为简单的 请求响应 模型。

![img](https://img2018.cnblogs.com/blog/1066923/201902/1066923-20190210235114200-2361046.png)

分析：

1.浏览器向服务器发出GET请求(请求服务器ServletA)

2.服务器上的容器逻辑接收到该url,根据该url判断为Servlet请求，此时容器逻辑将产生两个对象：请求对象(`HttpServletRequest`)和响应对象(`HttpServletResponce`)

3.容器逻辑根据url找到目标Servlet(本示例目标Servlet为ServletA),且创建一个线程A

4.容器逻辑将刚才创建的请求对象和响应对象传递给线程A

5.容器逻辑调用Servlet的`service()`方法

6.service()方法根据请求类型(本示例为GET请求)调用doGet()(本示例调用doGet())或doPost()方法

7.doGet()执行完后，将结果返回给容器逻辑

8.线程A被销毁或被放在线程池中

**注意：**

**1.在容器中的每个Servlet原则上只有一个实例**

**2.每个请求对应一个线程**

**3.多个线程可作用于同一个Servlet(这是造成Servlet线程不安全的根本原因)**

**4.每个线程一旦执行完任务，就被销毁或放在线程池中等待回收**

### 在JavaWeb中扮演的角色

> Servlet在JavaWeb中，扮演两个角色：**页面角色**和**控制器角色**(更多)。

### 生命周期

![image-20200219115839114](documents/JAVA笔记/image-20200219115839114.png)

第一步：容器先加载`Servlet`类

第二步：容器实例化`Servlet`(`Servlet`无参构造函数执行)

第三步：执行`init()`方法（在Servlet生命周期中，只执行一次，且在`service()`方法执行前执行）

第四步：执行`service()`方法，处理客户请求，`doPost()或doGet()`

第五步：执行`destroy()`，销毁线程





## BigDecimal

 保留两位小数的方法

+ BigDecimal的setScal()方法
+ System.out.println("%2f",a);
+ NumberFormat
+ DecimalFormat的format方法

```java
public class Format{
    double f = 111231.5585;
    public void m1(){
        BigDecimal bg  = new BigDecimal(f);
        double f1 = bg.setScale(2,BigDecimal.ROUND_HALF_UP).doubleValue();

    }
    public void m2(){
        DecimalFormat df = new DecimalFormat("#.00");
        Sytem.out.println(df.format(f));
        
    }
    public void m3(){
    	System.out.printlin(String.format()".%2f",f);
        
    }
    public void m4() {
            NumberFormat nf = NumberFormat.getNumberInstance();
            nf.setMaximumFractionDigits(2);
            System.out.println(nf.format(f));
        }

}
```



**当`double`必须用作`BigDecimal`的源时，**请使用`Double.toString(double)``转成String，然后`使用String构造方法，或使用BigDecimal的静态方法valueOf，如下

```java
public static void main(String[] args)
    {
        BigDecimal bDouble1 = BigDecimal.valueOf(2.3);
        BigDecimal bDouble2 = new BigDecimal(Double.toString(2.3));

        System.out.println("bDouble1=" + bDouble1);
        System.out.println("bDouble2=" + bDouble2);
        
    }
```



### 四则运算

```java
public BigDecimal divide(BigDecimal divisor, int scale, int roundingMode) 
```

```java
ROUND_CEILING    //向正无穷方向舍入

ROUND_DOWN    //向零方向舍入

ROUND_FLOOR    //向负无穷方向舍入

ROUND_HALF_DOWN    //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，向下舍入, 例如1.55 保留一位小数结果为1.5

ROUND_HALF_EVEN    //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，如果保留位数是奇数，使用ROUND_HALF_UP，如果是偶数，使用ROUND_HALF_DOWN

ROUND_HALF_UP    //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，向上舍入, 1.55保留一位小数结果为1.6

ROUND_UNNECESSARY    //计算结果是精确的，不需要舍入模式

ROUND_UP    //向远离0的方向舍入

```

(1)商业计算使用BigDecimal。

 (2)尽量使用参数类型为String的构造函数。

 (3) BigDecimal都是不可变的（immutable）的，在进行每一步运算时，都会产生一个新的对象，所以在做加减乘除运算时千万要保存操作后的值。





## 枚举



### EnumSet和EnumMap

EnumMap是专门为枚举类型量身定做的Map实现。虽然使用其它的Map实现（如HashMap）也能完成枚举类型实例到值得映射，但是使用EnumMap会更加高效：它只能接收同一枚举类型的实例作为键值，并且由于枚举类型实例的数量相对固定并且有限，所以EnumMap使用数组来存放与枚举类型对应的值。这使得EnumMap的效率非常高。EnumMap在内部使用枚举类型的ordinal()得到当前实例的声明次序，并使用这个次序维护枚举类型实例对应值在数组的位置。

> 1、父类为AbstractMap，未实现Map接口，只实现了Cloneable和Serializable接口。
> 2、非线程安全，所有方法和操作都未加锁。
> 3、采用key数组和vals数组共同实现key和value的关联。
> 4、不允许null key，但允许null value。
> 5、null值会被转换为Object的NULL实例占位替换。
> 6、元素的存储顺序按照枚举值的声明次序存储。





### 单例模式

> 单例模式的优点
>
> + 由于单例模式在内存中只有一个实例，减少了内存开支，特别是一个对象需要频繁地创建、销毁时，而且创建或销毁时性能又无法优化，单例模式的优势就非常明显。
> + 由于单例模式只生成一个实例，所以减少了系统的性能开销，当一个对象的产生需要比较多的资源时，如读取配置、产生其他依赖对象时，则可以通过在应用启动时直接产生一个单例对象，然后用永久驻留内存的方式来解决（在Java EE中采用单例模式时需要注意JVM垃圾回收机制）
> + 单例模式可以避免对资源的多重占用，例如一个写文件动作，由于只有一个实例存在内存中，避免对同一个资源文件的同时写操作
> + 单例模式可以在系统设置全局的访问点，优化和共享资源访问，例如可以设计一个单例类，负责所有数据表的映射处理
>
> 单例模式的缺点
>
> + 单例模式一般没有接口，扩展很困难，若要扩展，除了修改代码基本上没有第二种途径可以实现。单例模式为什么不能增加接口呢？因为接口对单例模式是没有任何意义的，它要求“自行实例化”，并且提供单一实例、接口或抽象类是不可能被实例化的。当然，在特殊情况下，单例模式可以实现接口、被继承等，需要在系统开发中根据环境判断单例模式对测试是不利的。在并行开发环境中，如果单例模式没有完成，是不能进行测试的，没有接口也不能使用mock的方式虚拟一个对象
> + 单例模式与单一职责原则有冲突。一个类应该只实现一个逻辑，而不关心它是否是单例的，是不是要单例取决于环境，单例模式把“要单例”和业务逻辑融合在一个类中
>   

**饿汉模式**

即无论是否使用了该对象，在一开始的时候就创建该对象

```java
public class Singleton {  
     private static Singleton instance = new Singleton();  
     private Singleton (){
     }
     public static Singleton getInstance() {  
     return instance;  
     }  
 } 


```

虽然线程安全，资源效率不高，可能`getInstance()`永远不会执行到，但执行该类的其他静态方法或者加载了该类（`class.forName)`，那么这个实例仍然初始化





**懒汉模式**·

```java
//线程不安全
public class Singleton {  
      private static Singleton instance;  
      private Singleton (){
      }   
      public static Singleton getInstance() {  
      if (instance == null) {  
          instance = new Singleton();  
      }  
      return instance;  
      }  
 }  
//线程安全，但是开销大
public class Singleton {  
      private static Singleton instance;  
      private Singleton (){
      }
      public static synchronized Singleton getInstance() {  
      if (instance == null) {  
          instance = new Singleton();  
      }  
      return instance;  
      }  
 }  
//线程安全，开销相对较小
/**
前面我们也说了，synchronized 在退出的时候，能保证 synchronized 块中对于共享变量的写入一定会刷入到主内存中。也就是说，上述代码中，内嵌的 synchronized 结束的时候，temp 一定是完整构造出来的，然后再赋给 instance 的值一定是好的。

可是，synchronized 保证了释放监视器锁之前的代码一定会在释放锁之前被执行（如 temp 的初始化一定会在释放锁之前执行完 ），但是没有任何规则规定了，释放锁之后的代码不可以在释放锁之前先执行。

也就是说，代码中释放锁之后的行为 instance = temp 完全可以被提前到前面的 synchronized 代码块中执行，那么前面说的重排序问题就又出现了。
**/
public class Singleton {  
      private volatile static Singleton instance;  
      private Singleton (){
      }   
      public static Singleton getInstance() {  
      if (instance== null) {  
          synchronized (Singleton.class) {  
          if (instance== null) {  
              instance= new Singleton();  
          }  
         }  
     }  
     return instance;  
     }  
 }  
```

> 一开始单例模式一旦出现并发，就可能出现初始化两个对象的问题
>
> 后来选择加锁，**加锁能解决问题，但是出现严重的性能开销**
>
> 后来就选择在加锁前加一层非空判断，这样就可以只在第一次初始化，后期不用加锁但是正是由于添加了一层非空判断，导致多线程在进行这个判断的时候，**读取操作跳过了等待时间直接读取对象，但此时由于锁内空间的重排序，导致此时对象还没有初始化完成。**从而造成严重的后果
>
> 所以最后要加volatile





**静态内部类实现**（嵌套实现）

```java
public class Singleton { 
    private Singleton(){
    }
      public static Singleton getInstance(){  
        return SingletonHolder.sInstance;  
    }  
    private static class SingletonHolder {  
        private static final Singleton sInstance = new Singleton();  
    }  
} 
```

利用静态内部类能访问外部类的静态变量和静态方法

1. 它的创建是不需要依赖外围类的创建。
2. 它不能使用任何外围类的非static成员变量和方法。



**枚举实现**

枚举很特殊，它在类加载的时候会初始化里面的所有的实例，而且 JVM 保证了它们不会再被实例化，所以它天生就是单例的。

```java
public enum Singleton {  
     INSTANCE;  
     public void doSomeThing() {  
     }  
 }  
```

这种方法可以防止反序列化和反射和克隆

原因

+ 不允许被克隆
+ 对于实例控制，枚举类型优于readResolve





**容器实现**

```java
public class SingletonManager { 
　　private static Map<String, Object> objMap = new HashMap<String,Object>();
　　private Singleton() { 
　　}
　　public static void registerService(String key, Objectinstance) {
　　　　if (!objMap.containsKey(key) ) {
　　　　　　objMap.put(key, instance) ;
　　　　}
　　}
　　public static ObjectgetService(String key) {
　　　　return objMap.get(key) ;
　　}
```



#### **破环单例模式的三种方式**

**反射，序列化，克隆**

以双重检测方式为例测试反射，序列化，克隆是否能破环单例模式:

```java
public class Singleton  implements Serializable,Cloneable{
    private static final long serialVersionUID = 6125990676610180062L;
    private static Singleton singleton;
    
    private Singleton(){
    }
    public void doAction(){
        //TODO 实现你需要做的事
    }
    public  static Singleton getInstance(){
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

```java
public class DestroySingleton {
    
    public static void main(String[] args) throws Exception {
        //通过getInstance()获取
        Singleton singleton = Singleton.getInstance();
        System.out.println("singleton的hashCode:"+singleton.hashCode());
        //通过反射获取
        Constructor<Singleton> constructor = Singleton.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        Singleton reflex = constructor.newInstance();
        System.out.println("reflex的hashCode:"+reflex.hashCode());
        //通过克隆获取
        Singleton clob = (Singleton) Singleton.getInstance().clone();
        System.out.println("clob的hashCode:"+clob.hashCode());
        //通过序列化，反序列化获取
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);
        oos.writeObject(Singleton.getInstance());
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bis);
        Singleton serialize = (Singleton) ois.readObject();
        if (ois != null)    ois.close();
        if (bis != null) bis.close();
        if (oos != null) oos.close();
        if (bos != null) bos.close();
        System.out.println("serialize的hashCode:"+serialize.hashCode());
    }
}
```

**如何防止反射、克隆、序列化对单例模式的破环**

**1、防止反射破环(虽然构造方法已私有化，但通过反射机制使用newInstance()方法构造方法也是可以被调用):**

- 首先定义一个全局变量开关isFristCreate默认为开启状态
- 当第一次加载时将其状态更改为关闭状态

**2、防止克隆破环**

- 重写clone()，直接返回单例对象

**3、防止序列化破环**

- 添加readResolve()，返回Object对象

```java
public class Singleton  implements Serializable,Cloneable{
    private static final long serialVersionUID = 6125990676610180062L;
    private static Singleton singleton;
    private static boolean isFristCreate = true;//默认是第一次创建
    
    private Singleton(){
            if (isFristCreate) {
                synchronized (Singleton.class) {　　　　　　　　　　　　if (isFristCreate) {　　　　　　　　　　　　　　isFristCreate = false;　　　　　　　　　　　　}
                }
            }else{
                throw new RuntimeException("已然被实例化一次，不能在实例化");
            }
    }
    public void doAction(){
        //TODO 实现你需要做的事
    }
    public  static Singleton getInstance(){
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
    @Override
    protected Singleton clone() throws CloneNotSupportedException {
        return singleton;
    }
    private Object readResolve() {
        return singleton;
    }
}
```





## 反射

### 原理

![img](https://uploadfiles.nowcoder.com/images/20190202/242025553_1549100782844_C375DEF54499BAA37941F93164F9C467)

### **1、获取Class对象**

1. Object ——> getClass(); 
2. 任何数据类型（包括基本数据类型）都有一个“静态”的class属性 
3. 通过Class类的静态方法：forName（String  className）(常用)

```java
Class stuClass3 = Class.forName("fanshe.Student");//注意此字符串必须是真实路径，就是带包名的类路径，包名.类名
```



通过Class对象可以获取某个类中的：构造方法、成员变量、成员方法；并访问成员；

### **2. 获取构造方法**

1.批量的方法：

`public Constructor[] getConstructors()`：所有"公有的"构造方法

` public Constructor[] getDeclaredConstructors()`：获取所有的构造方法(包括私有、受保护、默认、公有)

2.获取单个的方法，并调用：

`public Constructor getConstructor(Class... parameterTypes)`:获取单个的"公有的"构造方法：

`public Constructor getDeclaredConstructor(Class... parameterTypes)`:获取"某个构造方法"可以是私有的，或受保护、默认、公有；

调用构造方法：

`Constructor-->newInstance(Object... initargs)`

````java
public class Constructors {
    public static void main(String[] args) throws Exception {
		//1.加载Class对象
		Class clazz = Class.forName("fanshe.Student");
		
		//2.获取所有公有构造方法
		System.out.println("**********************所有公有构造方法*********************************");
		Constructor[] conArray = clazz.getConstructors();
		for(Constructor c : conArray){
			System.out.println(c);
		}
		
		
		System.out.println("************所有的构造方法(包括：私有、受保护、默认、公有)***************");
		conArray = clazz.getDeclaredConstructors();
		for(Constructor c : conArray){
			System.out.println(c);
		}
		
		System.out.println("*****************获取公有、无参的构造方法*******************************");
		Constructor con = clazz.getConstructor(null);
		//1>、因为是无参的构造方法所以类型是一个null,不写也可以：这里需要的是一个参数的类型，切记是类型
		//2>、返回的是描述这个无参构造函数的类对象。
	
		System.out.println("con = " + con);
		//调用构造方法
		Object obj = con.newInstance();
	//	System.out.println("obj = " + obj);
	//	Student stu = (Student)obj;
		
		System.out.println("******************获取私有构造方法，并调用*******************************");
		con = clazz.getDeclaredConstructor(char.class);
		System.out.println(con);
		//调用构造方法
		con.setAccessible(true);//暴力访问(忽略掉访问修饰符)
		obj = con.newInstance('男');
	}	
}
````



### 3.调用成员变量

1.批量的·

`Field[] getFields():`获取所有的"公有字段"Field[] 

`getDeclaredFields()`:获取所有字段，包括：私有、受保护、默认、公有；

2.获取单个的：

`public Field getField(String fieldName)`:获取某个"公有的"字段

`public Field getDeclaredField(String fieldName)`:获取某个字段(可以是私有的)

设置字段的值

`Field --> public void set(Object obj,Object value)`

参数说明：

1.obj:要设置的字段所在的对象；

 * 					2.value:要为字段设置的值；



### 4. 获取成员方法并调用

1.批量的·

`public Method[] getMethods()`:获取所有"公有方法"；（包含了父类的方法也包含Object类）

`public Method[] getDeclaredMethods()`:获取所有的成员方法，包括私有的(不包括继承的)

2.获取单个的：

`public Method getMethod(String name,Class<?>... parameterTypes)`:

`public Method getDeclaredMethod(String name,Class<?>... parameterTypes)`

> 参数：`name` : 方法名；`Class ... `: 形参的Class类型对象



调用方法：

`Method --> public Object invoke(Object obj,Object... args):`

> 参数说明：`obj` : 要调用方法的对象；`args` :调用方式时所传递的实参；



**实例**

反射main方法

```java
public static void main(String[] args) {
		try {
			//1、获取Student对象的字节码
			Class clazz = Class.forName("fanshe.main.Student");
			
			//2、获取main方法
			 Method methodMain = clazz.getMethod("main", String[].class);//第一个参数：方法名称，第二个参数：方法形参的类型，
			//3、调用main方法
			// methodMain.invoke(null, new String[]{"a","b","c"});
			 //第一个参数，对象类型，因为方法是static静态的，所以为null可以，第二个参数是String数组，这里要注意在jdk1.4时是数组，jdk1.5之后是可变参数
			 //这里拆的时候将  new String[]{"a","b","c"} 拆成3个对象。。。所以需要将它强转。
			 methodMain.invoke(null, (Object)new String[]{"a","b","c"});//方式一
			// methodMain.invoke(null, new Object[]{new String[]{"a","b","c"}});//方式二
			
		} catch (Exception e) {
			e.printStackTrace();
		}
}
```



[反射的其他用法](https://zhuanlan.zhihu.com/p/80519709)



**获取泛型信息**
很多人认为java类在编译的时候会把泛型信息给擦除掉，所以在运行时是无法获取到泛型信息的。其实在某些情况下，还是可以通过反射在运行时获取到泛型信息的。

获取到`java.lang.reflect.Method`对象，就有可能获取到某个方法的泛型返回信息。

**泛型方法返回类型**
下面的类中定义了一个返回值中有泛型的方法：

```java
public class MyClass {

  protected List<String> stringList = ...;

  public List<String> getStringList(){
    return this.stringList;
  }
}
```

下面的代码使用反射检测getStringList()方法返回的是`List<String>`而不是`List`

```java
Method method = MyClass.class.getMethod("getStringList", null);

Type returnType = method.getGenericReturnType();

if(returnType instanceof ParameterizedType){
    ParameterizedType type = (ParameterizedType) returnType;
    Type[] typeArguments = type.getActualTypeArguments();
    for(Type typeArgument : typeArguments){
        Class typeArgClass = (Class) typeArgument;
        System.out.println("typeArgClass = " + typeArgClass);
    }
}
```

上面这段代码会打印：`typeArgClass = java.lang.String`



## 设计模式

### 创建型模式

> 创建型模式总体上比较简单，它们的作用就是为了产生实例对象，算是各种工作的第一步了，因为我们写的是**面向对象**的代码，所以我们第一步当然是需要创建一个对象了。
>
> 

#### 简单工厂模式

简单地说，简单工厂模式通常就是这样，一个工厂类 `XxxFactory`，里面有一个静态方法，根据我们不同的参数，返回不同的派生自同一个父类（或实现同一接口）的实例对象。

```java
public class FoodFactory {

    public static Food makeFood(String name) {
        if (name.equals("noodle")) {
            Food noodle = new LanZhouNoodle();
            noodle.addSpicy("more");
            return noodle;
        } else if (name.equals("chicken")) {
            Food chicken = new HuangMenChicken();
            chicken.addCondiment("potato");
            return chicken;
        } else {
            return null;
        }
    }
}
```



#### 工厂模式

实现单一功能，通过选取合适的工厂，再通过这个工厂生产出具体对象

![factory-1](https://www.javadoop.com/blogimages/design-pattern/factory-1.png)

#### 抽象工厂模式

涉及到产品族的时候，可能出现产品不兼容的情况

![factory-1](https://www.javadoop.com/blogimages/design-pattern/abstract-factory-1.png)

当然，抽象工厂的问题也是显而易见的，比如我们要加个显示器，就需要修改所有的工厂，给所有的工厂都加上制造显示器的方法。这有点违反了**对修改关闭，对扩展开放**这个设计原则。

![abstract-factory-3](https://www.javadoop.com/blogimages/design-pattern/abstract-factory-3.png)



#### 单例模式

+ 饿汉模式
+ 懒汉模式
+ 静态内部类模式
+ 枚举实现
+ 容器实现



#### 建造者模式

先添加属性，然后build将属性赋值给对象

客户端调用方法

```java
Food food = new FoodBuilder().a().b().c().build();
Food food = Food.builder().a().b().c().build();
```

套路就是先 new 一个 Builder，然后可以链式地调用一堆方法，最后再调用一次 build() 方法，我们需要的对象就有了。

标准写法：

```java
class User {
    // 下面是“一堆”的属性
    private String name;
    private String password;
    private String nickName;
    private int age;

    // 构造方法私有化，不然客户端就会直接调用构造方法了
    private User(String name, String password, String nickName, int age) {
        this.name = name;
        this.password = password;
        this.nickName = nickName;
        this.age = age;
    }
    // 静态方法，用于生成一个 Builder，这个不一定要有，不过写这个方法是一个很好的习惯，
    // 有些代码要求别人写 new User.UserBuilder().a()...build() 看上去就没那么好
    public static UserBuilder builder() {
        return new UserBuilder();
    }

    public static class UserBuilder {
        // 下面是和 User 一模一样的一堆属性
        private String  name;
        private String password;
        private String nickName;
        private int age;

        private UserBuilder() {
        }

        // 链式调用设置各个属性值，返回 this，即 UserBuilder
        public UserBuilder name(String name) {
            this.name = name;
            return this;
        }

        public UserBuilder password(String password) {
            this.password = password;
            return this;
        }

        public UserBuilder nickName(String nickName) {
            this.nickName = nickName;
            return this;
        }

        public UserBuilder age(int age) {
            this.age = age;
            return this;
        }

        // build() 方法负责将 UserBuilder 中设置好的属性“复制”到 User 中。
        // 当然，可以在 “复制” 之前做点检验
        public User build() {
            if (name == null || password == null) {
                throw new RuntimeException("用户名和密码必填");
            }
            if (age <= 0 || age >= 150) {
                throw new RuntimeException("年龄不合法");
            }
            // 还可以做赋予”默认值“的功能
              if (nickName == null) {
                nickName = name;
            }
            return new User(name, password, nickName, age);
        }
    }
}
```

使用场景：当属性很多，而且有些必填，有些选填的时候，这个模式会使代码清晰很多。我们可以在 **Builder 的构造方法**中强制让调用者提供必填字段，还有，在 `build()` 方法中校验各个参数比在 `User `的构造方法中校验，代码要优雅一些。

#### 原型模式

有一个原型**实例**，基于这个原型实例产生新的实例，也就是“克隆”了。

Object 类中有一个 clone() 方法，它用于生成一个新的对象，当然，如果我们要调用这个方法，java 要求我们的类必须先**实现 Cloneable 接口**，此接口没有定义任何方法，但是不这么做的话，在 clone() 的时候，会抛出 `CloneNotSupportedException `异常。

```java
protected native Object clone() throws CloneNotSupportedException;
```

> java 的克隆是浅克隆，碰到对象引用的时候，克隆出来的对象和原对象中的引用将指向同一个对象。通常实现深克隆的方法是将对象进行序列化，然后再进行反序列化。

### 结构型模式

> **代理模式**是做方法增强的
>
> **适配器模式**是把鸡包装成鸭这种用来适配接口的
>
> **桥梁模式**做到了很好的解耦，
>
> **装饰器模式**从名字上就看得出来，适合于装饰类或者说是增强类的场景
>
> **门面模式**的优点是客户端不需要关心实例化过程，只要调用需要的方法即可
>
> **组合模式**用于描述具有层次结构的数据
>
> **享元模式**是为了在特定的场景中缓存已经创建的对象，用于提高性能。



#### 代理模式

> 代理就是要对客户端隐藏真实实现，由代理来负责客户端的所有请求。当然，代理只是个代理，它不会完成实际的业务逻辑，

```java
public interface FoodService {
    Food makeChicken();
    Food makeNoodle();
}

public class FoodServiceImpl implements FoodService {
    public Food makeChicken() {
          Food f = new Chicken()
        f.setChicken("1kg");
          f.setSpicy("1g");
          f.setSalt("3g");
        return f;
    }
    public Food makeNoodle() {
        Food f = new Noodle();
        f.setNoodle("500g");
        f.setSalt("5g");
        return f;
    }
}

// 代理要表现得“就像是”真实实现类，所以需要实现 FoodService
public class FoodServiceProxy implements FoodService {

    // 内部一定要有一个真实的实现类，当然也可以通过构造方法注入
    private FoodService foodService = new FoodServiceImpl();

    public Food makeChicken() {
        System.out.println("我们马上要开始制作鸡肉了");

        // 如果我们定义这句为核心代码的话，那么，核心代码是真实实现类做的，
        // 代理只是在核心代码前后做些“无足轻重”的事情
        Food food = foodService.makeChicken();

        System.out.println("鸡肉制作完成啦，加点胡椒粉"); // 增强
          food.addCondiment("pepper");

        return food;
    }
    public Food makeNoodle() {
        System.out.println("准备制作拉面~");
        Food food = foodService.makeNoodle();
        System.out.println("制作完成啦")
        return food;
    }
}
```

```java
// 这里用代理类来实例化
FoodService foodService = new FoodServiceProxy();
foodService.makeChicken();
```

代理模式说白了就是做 **“方法包装”** 或做 **“方法增强”**。在面向切面编程中，其实就是动态代理的过程。比如 Spring 中，我们自己不定义代理类，但是 Spring 会帮我们动态来定义代理，然后把我们定义在 @Before、@After、@Around 中的代码逻辑动态添加到代理中。

```java
public interface FoodService{
    Food makeChicken();
    Food makeNoodle();
}
public class FoodServiceImp implements FoodService{
    public Food makeChicken(){
        System.out.println("制作鸡肉");
    }
}
public class FoodServiceProxy implements FoodService{
    FoodService FoodserviceImp  = new FoodServiceImp();
    
}
```



#### 适配器模式

> 适配器模式做的就是，有一个接口需要实现，但是我们现成的对象都不满足，需要加一层适配器来进行适配。
>
> 适配器模式总体来说分三种：**默认适配器模式**、**对象适配器模式**、**类适配器模式**

##### 默认适配器模式

实现抽象类，然后再适配器中实现所有方法，具体的逻辑只需要继承这个适配器即可，不需要再全部实现抽象类的方法

##### 对象适配模式

```java
//之前还存在鸡鸭的接口
// 毫无疑问，首先，这个适配器肯定需要 implements Duck，这样才能当做鸭来用
public class CockAdapter implements Duck {

    Cock cock;
    // 构造方法中需要一个鸡的实例，此类就是将这只鸡适配成鸭来用
      public CockAdapter(Cock cock) {
        this.cock = cock;
    }

    // 实现鸭的呱呱叫方法
    @Override
      public void quack() {
        // 内部其实是一只鸡的咕咕叫
        cock.gobble();
    }

      @Override
      public void fly() {
        cock.fly();
    }
}
```

客户端调用：

```java
public static void main(String[] args) {
    // 有一只野鸡
      Cock wildCock = new WildCock();
      // 成功将野鸡适配成鸭
      Duck duck = new CockAdapter(wildCock);
      ...
}
```

![adapter-1](https://www.javadoop.com/blogimages/design-pattern/adapter-1.png)

##### 类适配器模式

![adapter-1](https://www.javadoop.com/blogimages/design-pattern/adapter-2.png)

类适配和对象适配

+ 一个采用继承，一个采用组合；
+ 类适配属于静态实现，对象适配属于组合的动态实现，对象适配需要多实例化一个对象。
+ 总体来说，对象适配用得比较多。

适配器模式和代理模式的异同

+ 比较这两种模式，其实是比较对象适配器模式和代理模式，在代码结构上，它们很相似，都需要一个具体的实现类的实例。但是它们的目的不一样**，代理模式做的是增强原方法的活**；适配器做的是适配的活，为的是提供“把鸡包装成鸭，然后当做鸭来使用”，而**鸡和鸭它们之间原本没有继承关系**。







#### 桥梁模式

> 对象的解耦和抽象

![bridge-1](https://www.javadoop.com/blogimages/design-pattern/bridge-1.png)



#### 装饰模式

> 既然说是装饰，那么往往就是**添加小功能**这种，而且，我们要满足可以添加多个小功能。最简单的，代理模式就可以实现功能的增强，但是代理不容易实现多个功能的增强，当然你可以说用代理包装代理的多层包装方式，但是那样的话代码就复杂了。

![decorator-1](https://www.javadoop.com/blogimages/design-pattern/decorator-1.png)

具体案例

![decorator-2](https://www.javadoop.com/blogimages/design-pattern/decorator-2.png)

**javaIO中的装饰模式**

![decorator-3](https://www.javadoop.com/blogimages/design-pattern/decorator-3.png)

我们知道 `InputStream `代表了输入流，具体的输入来源可以是文件（`FileInputStream`）、管道（`PipedInputStream`）、数组（`ByteArrayInputStream`）等，这些就像前面奶茶的例子中的红茶、绿茶，属于基础输入流。

`FilterInputStream `承接了装饰模式的关键节点，它的实现类是一系列装饰器，比如 `BufferedInputStream `代表用缓冲来装饰，也就使得输入流具有了缓冲的功能，`LineNumberInputStream `代表用行号来装饰，在操作的时候就可以取得行号了，`DataInputStream `的装饰，使得我们可以从输入流转换为 java 中的`基本`类型值。



**装饰模式和代理模式的区别**

> 代理模式在代理类new一个新对象，隐藏旧对象的具体信息，全权代理就对象的实现
>
> 装饰模式则是在装饰类中传入原来对象，不用new一个新对象，只是在原有的对象上做增强，增加特殊的功能

#### 门面模式



> 门面模式（也叫外观模式，Facade Pattern）在许多源码中有使用，比如 slf4j 就可以理解为是门面模式的应用。这是一个简单的设计模式

我们先定义一个门面：

```java
public class ShapeMaker {
   private Shape circle;
   private Shape rectangle;
   private Shape square;

   public ShapeMaker() {
      circle = new Circle();
      rectangle = new Rectangle();
      square = new Square();
   }

  /**
   * 下面定义一堆方法，具体应该调用什么方法，由这个门面来决定
   */

   public void drawCircle(){
      circle.draw();
   }
   public void drawRectangle(){
      rectangle.draw();
   }
   public void drawSquare(){
      square.draw();
   }
}
```

看看现在客户端怎么调用：

```java
public static void main(String[] args) {
  ShapeMaker shapeMaker = new ShapeMaker();

  // 客户端调用现在更加清晰了
  shapeMaker.drawCircle();
  shapeMaker.drawRectangle();
  shapeMaker.drawSquare();        
}
```

门面模式的优点显而易见，客户端不再需要关注实例化时应该使用哪个实现类，直接调用门面提供的方法就可以了，因为门面类提供的方法的方法名对于客户端来说已经很友好了。

#### 组合模式

> 组合模式用于表示具有层次结构的数据，使得我们对单个对象和组合对象的访问具有一致性。

通常，一些类需要定义 add(node)、remove(node)、getChildren() 这些方法。这说的其实就是组合模式



#### 享元模式

> Flyweight 是轻量级的意思,复用已经生成的对象，享元分开来说就是 共享 元器件，也就是复用已经生成的对象
>
> 运用共享技术有效地支持大量细粒度的对象。

复用对象最简单的方式是，用一个 `HashMap `来存放每次新生成的对象。每次需要一个对象的时候，先到 `HashMap `中看看有没有，如果没有，再生成新的对象，然后将这个对象放入 `HashMap `中。

原理就是**用唯一标识码判断，如果在内存中有，则返回这个唯一标识码所标识的对象**

- 应用实例

1. JAVA 中的 String，如果有则返回，如果没有则创建一个字符串保存在字符串缓存池里面。
2. 数据库的数据池。
3. hibernate-validate自定义了基于弱引用的缓存容器[ConcurrentReferenceHashMap](https://github.com/hibernate/hibernate-validator/blob/master/engine/src/main/java/org/hibernate/validator/internal/util/ConcurrentReferenceHashMap.java)



### 行为型模式

> 行为型模式关注的是各个类之间的相互作用，将职责划分清楚，使得我们的代码更加地清晰。



#### 策略模式

下面设计的场景是，我们需要画一个图形，可选的策略就是用红色笔来画，还是绿色笔来画，或者蓝色笔来画。

首先，先定义一个策略接口：

```java
public interface Strategy {
   public void draw(int radius, int x, int y);
}
```

然后我们定义具体的几个策略：

```java
public class RedPen implements Strategy {
   @Override
   public void draw(int radius, int x, int y) {
      System.out.println("用红色笔画图，radius:" + radius + ", x:" + x + ", y:" + y);
   }
}
public class GreenPen implements Strategy {
   @Override
   public void draw(int radius, int x, int y) {
      System.out.println("用绿色笔画图，radius:" + radius + ", x:" + x + ", y:" + y);
   }
}
public class BluePen implements Strategy {
   @Override
   public void draw(int radius, int x, int y) {
      System.out.println("用蓝色笔画图，radius:" + radius + ", x:" + x + ", y:" + y);
   }
}
```

使用策略的类：

```java
public class Context {
   private Strategy strategy;

   public Context(Strategy strategy){
      this.strategy = strategy;
   }

   public int executeDraw(int radius, int x, int y){
      return strategy.draw(radius, x, y);
   }
}
```

客户端演示：

```java
public static void main(String[] args) {
    Context context = new Context(new BluePen()); // 使用绿色笔来画
      context.executeDraw(10, 0, 0);
}
```

放到一张图上，让大家看得清晰些：

![strategy-1](https://www.javadoop.com/blogimages/design-pattern/strategy-1.png)

#### 观察者模式

> 无外乎两个操作，观察者订阅自己关心的主题和主题有数据变化后通知观察者们。

首先，需要定义主题，每个主题需要持有观察者列表的引用，用于在数据变更的时候通知各个观察者：

```java
public class Subject {
    private List<Observer> observers = new ArrayList<Observer>();
    private int state;
    public int getState() {
        return state;
    }
    public void setState(int state) {
        this.state = state;
        // 数据已变更，通知观察者们
        notifyAllObservers();
    }
    // 注册观察者
    public void attach(Observer observer) {
        observers.add(observer);
    }
    // 通知观察者们
    public void notifyAllObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }
}
```

定义观察者接口：

```java
public abstract class Observer {
    protected Subject subject;
    public abstract void update();
}
```

其实如果只有一个观察者类的话，接口都不用定义了，不过，通常场景下，既然用到了观察者模式，我们就是希望一个事件出来了，会有多个不同的类需要处理相应的信息。比如，订单修改成功事件，我们希望发短信的类得到通知、发邮件的类得到通知、处理物流信息的类得到通知等。

我们来定义具体的几个观察者类：

```java
public class BinaryObserver extends Observer {
    // 在构造方法中进行订阅主题
    public BinaryObserver(Subject subject) {
        this.subject = subject;
        // 通常在构造方法中将 this 发布出去的操作一定要小心
        this.subject.attach(this);
    }
    // 该方法由主题类在数据变更的时候进行调用
    @Override
    public void update() {
        String result = Integer.toBinaryString(subject.getState());
        System.out.println("订阅的数据发生变化，新的数据处理为二进制值为：" + result);
    }
}

public class HexaObserver extends Observer {
    public HexaObserver(Subject subject) {
        this.subject = subject;
        this.subject.attach(this);
    }
    @Override
    public void update() {
        String result = Integer.toHexString(subject.getState()).toUpperCase();
        System.out.println("订阅的数据发生变化，新的数据处理为十六进制值为：" + result);
    }
}
```

客户端使用也非常简单：

```java
public static void main(String[] args) {
    // 先定义一个主题
    Subject subject1 = new Subject();
    // 定义观察者
    new BinaryObserver(subject1);
    new HexaObserver(subject1);

    // 模拟数据变更，这个时候，观察者们的 update 方法将会被调用
    subject.setState(11);
}
```

output:

```shell
订阅的数据发生变化，新的数据处理为二进制值为：1011
订阅的数据发生变化，新的数据处理为十六进制值为：B
```



实际生产过程中，观察者模式往往用消息中间件来实现，如果要实现单机观察者模式，笔者建议读者使用 `Guava `中的 `EventBus`，它有同步实现也有异步实现

还有，即使是上面的这个代码，也会有很多变种，大家只要记住核心的部分，**那就是一定有一个地方存放了所有的观察者，然后在事件发生的时候，遍历观察者，调用它们的回调函数。**



#### 责任链模式

>责任链通常需要先建立一个单向链表，然后调用方只需要调用头部节点就可以了，后面会自动流转下去。比如流程审批就是一个很好的例子，只要终端用户提交申请，根据申请的内容信息，自动建立一条责任链，然后就可以开始流转了。

有这么一个场景，用户参加一个活动可以领取奖品，但是活动需要进行很多的规则校验然后才能放行，比如首先需要校验用户是否是新用户、今日参与人数是否有限额、全场参与人数是否有限额等等。设定的规则都通过后，才能让用户领走奖品。





#### 模板方法

通常会有一个抽象类：

```java
public abstract class AbstractTemplate {
    // 这就是模板方法
    public void templateMethod() {
        init();
        apply(); // 这个是重点
        end(); // 可以作为钩子方法
    }

    protected void init() {
        System.out.println("init 抽象层已经实现，子类也可以选择覆写");
    }

    // 留给子类实现
    protected abstract void apply();

    protected void end() {
    }
}
```

模板方法中调用了 3 个方法，其中 apply() 是抽象方法，子类必须实现它，其实模板方法中有几个抽象方法完全是自由的，我们也可以将三个方法都设置为抽象方法，让子类来实现。也就是说，模板方法只负责定义第一步应该要做什么，第二步应该做什么，第三步应该做什么，至于怎么做，由子类来实现。

我们写一个实现类：

```java
public class ConcreteTemplate extends AbstractTemplate {
    public void apply() {
        System.out.println("子类实现抽象方法 apply");
    }

    public void end() {
        System.out.println("我们可以把 method3 当做钩子方法来使用，需要的时候覆写就可以了");
    }
}
```

客户端调用演示：

```java
public static void main(String[] args) {
    AbstractTemplate t = new ConcreteTemplate();
    // 调用模板方法
    t.templateMethod();
}
```



#### 状态模式





## 消息队列 

MQ（消息队列）是跨进程通信的方式之一，可理解为异步rpc，上游系统对调用结果的态度往往是重要不紧急。使用消息队列有以下好处：业务解耦、流量削峰、灵活扩展。接下来介绍消息中间件Kafka。

![Kafka系统架构](https://blog-article-resource.oss-cn-beijing.aliyuncs.com/kafka/kafka%E6%9E%B6%E6%9E%84.png)











## Zookeeper

> 分布式的、开源的分布式应用程序协调服务，原本是Hadoop、HBase的一个重要组件。它为分布式应用提供一致性服务的软件，包括：配置维护、域名服务、分布式同步、组服务等。

#### 应用场景

- zookeeper的功能很强大，应用场景很多，结合我实际工作中使用Dubbo框架的情况，Zookeeper主要是做注册中心用。基于Dubbo框架开发的提供者、消费者都向Zookeeper注册自己的URL，消费者还能拿到并订阅提供者的注册URL，以便在后续程序的执行中去调用提供者。而提供者发生了变动，也会通过Zookeeper向订阅的消费者发送通知。



## MyBatis

### 实现原理

**传统工作模式：**

1. 创建`SqlSessionFactoryBuilder`对象，调用`build(inputstream)`方法读取并解析配置文件，返回`SqlSessionFactory`对象
2. 由`SqlSessionFactory`创建`SqlSession `对象，没有手动设置的话事务默认开启
3. 调用`SqlSession`中的`api`，传入`Statement Id`和参数，内部进行复杂的处理，最后调用`jdbc`执行`SQL`语句，封装结果返回。

```java
public static void main(String[] args) {
		InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = factory.openSession();
		String name = "tom";
		List<User> list = sqlSession.selectList("com.demo.mapper.UserMapper.getUserByName",params);
}
```



**使用Mapper接口：**

```java
public static void main(String[] args) {
		//前三步都相同
		InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = factory.openSession();
		//这里不再调用SqlSession 的api，而是获得了接口对象，调用接口中的方法。
		UserMapper mapper = sqlSession.getMapper(UserMapper.class);
		List<User> list = mapper.getUserByName("tom");
}
```

**具体实现**

1. 初始化配置文件：创建Configuration对象，将解析的xml数据封装到Configuration内部的属性中
2. 执行sqlsession：SqlSession是MyBatis中用于和数据库交互的`顶层类`，通常将它与ThreadLocal绑定，一个会话使用一个SqlSession，并且在使用完毕后需要close。
3. 动态代理获得Mapper对象，运行Mapper映射的SQL语句，完成对数据库的CRUD和事务提交，之后关闭SqlSession



### 配置步骤

> 通过包名+方法名确定一个sqlsession

简单来说，Mybatis的配置主要分为以下几步： 

+ 编写POJO即JavaBean，最终的目的是将数据库中的查询结果映射到JavaBean上； 
+ 配置与POJO对应的Mapper接口：里面有各种方法，对应mapper.xml中的查询语句； 
+ 配置与POJO对应的XML映射：编写缓存，SQL查询等；
+  配置mybatis-config.xml主要的Mybatis配置文件：配置数据源、扫描mapper.xml等。



### Mybatis 的插件运行原理

答：Mybatis 仅可以编写针对 `ParameterHandler`、`ResultSetHandler`、`StatementHandler`、`Executor` 这 4 种接口的插件，Mybatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 `InvocationHandler` 的 `invoke()`方法，当然，只会拦截那些你指定需要拦截的方法。

实现 Mybatis 的 Interceptor 接口并复写`intercept()`方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。



举例

```java
@Intercepts({@Signature(type = Executor.class,
        method = "query",
        args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class})})
public class JunliPlugin  implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        MappedStatement mappedStatement = (MappedStatement) invocation.getArgs()[0];
        BoundSql boundSql = mappedStatement.getBoundSql(invocation.getArgs()[1]);
        System.out.println(String.format("plugin output sql = %s , param=%s", boundSql.getSql(),boundSql.getParameterObject()));
        return invocation.proceed();
    }
    @Override
    public Object plugin(Object o) {
        return Plugin.wrap(o,this);
    }
    @Override
    public void setProperties(Properties properties) {
    }
```



#### 动态 sql实现原理

答：Mybatis 动态 sql 可以让我们在 Xml 映射文件内，以标签的形式编写动态 sql，完成逻辑判断和动态拼接 sql 的功能，Mybatis 提供了 9 种动态 sql 标签 `trim|where|set|foreach|if|choose|when|otherwise|bind`。

其执行原理为，使用 `OGNL` 从 sql 参数对象中计算表达式的值，根据表达式的值动态拼接 sql，以此来完成动态 sql 的功能。·



### 延迟加载原理·

Mybatis 仅支持 `association `关联对象和 `collection` 关联集合对象的延迟加载，association 指的就是一对一，collection 指的就是一对多查询。在 Mybatis 配置文件中，可以配置是否启用延迟加载 `lazyLoadingEnabled=true|false。`

它的原理是，使用`CGLIB` 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 `a.getB().getName()`，拦截器 `invoke()`方法发现 `a.getB()`是 null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 `a.getB().getName()`方法的调用。这就是延迟加载的基本原理。



### 执行器Executor种类

**`SimpleExecutor`：**每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象。

**`ReuseExecutor`：**执行 update 或 select，以 sql 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后，不关闭 Statement 对象，而是放置于 `Map<String, Statement>`内，供下一次使用。简言之，就是重复使用 Statement 对象。

**`BatchExecutor`：**执行 update（没有 select，JDBC 批处理不支持 select），将所有 sql 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理。与 JDBC 批处理相同。

作用范围：Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内。



### 半自动ORM框架

>ORM是Object和Relation之间的映射，包括Object->Relation和Relation->Object两方面

Hibernate 属于全自动 ORM 映射工具，使用 Hibernate 查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。

而 Mybatis 在查询关联对象或关联集合对象时，需要手动编写 sql 来完成，所以，称之为半自动 ORM 映射工具。





### spring-mybatis的一级缓存失效问题

> spring帮助自动open和close session

1. `mybatis`的一级缓存生效的范围是`sqlsession`，是为了在`sqlsession`没有关闭时，业务需要重复查询相同数据使用的。一旦`sqlsession`关闭，则由这个`sqlsession`缓存的数据将会被清空。

2. spring对`mybatis`的`sqlsession`的使用是由`template`控制的，`sqlSessionTemplate`又被`spring`当作`resource`放在当前线程的上下文里（`threadlocal`),`spring`通过`mybatis`调用数据库的过程如下：

   ```markdown
   1. 我们需要访问数据
   2. spring检查到了这种需求，于是去申请一个mybatis的sqlsession（资源池），并将申请到的sqlsession与当前线程绑定，放入threadlocal里面
   3. sqlSessionTemplate从threadlocal获取到sqlsession，去执行查询
   4. 查询结束，清空threadlocal中与当前线程绑定的sqlsession，释放资源我们又需要访问数据
   5. 返回到步骤b
   ```

   通过以上步骤后发现，同一线程里面两次查询同一数据所使用的`sqlsession`是不相同的，所以，给人的印象就是结合`spring`后，`mybatis`的一级缓存失效了。

   解决方法：为`sqlsession`关联事务，就可以保持`sqlsession`不会失效



### #{}和${}的区别

在MyBatis 的映射配置文件中，动态传递参数有两种方式：

+ \#{} 占位符
+ ${} 拼接符

**区别**

>#{} 为参数占位符 ?，即sql 预编译
>
>${} 为字符串替换，即 sql 拼接

 

> #{}：动态解析 -> 预编译 -> 执
>
> ${}：动态解析 -> 编译 -> 执行







[mybatis没有实现接口就完成增删查改，怎么做到的？](https://www.cnblogs.com/cfas/p/7604668.html)



### Hibernate和mybatis的区别

3.1 开发方面 

在项目开发过程当中，就速度而言：

+ `hibernate`开发中，`sql`语句已经被封装，直接可以使用，加快系统开发；
+ `Mybatis` 属于半自动化，`sql`需要手工完成，稍微繁琐；
+ 但是，凡事都不是绝对的，如果对于庞大复杂的系统项目来说，发杂语句较多，选择`hibernate` 就不是一个好方案。

3.2 `sql`优化方面

+ `Hibernate `自动生成`sql`,有些语句较为繁琐，会多消耗一些性能；
+ `Mybatis` 手动编写`sql`，可以避免不需要的查询，提高系统性能；

3.3 对象管理比对

+ `Hibernate` 是完整的对象-关系映射的框架，开发工程中，无需过多关注底层实现，只要去管理对象即可；
+ `Mybatis` 需要自行管理 映射关系

3.4 缓存方面    

相同点：

+ `Hibernate`和`Mybatis`的二级缓存除了采用系统默认的缓存机制外，都可以通过实现你自己的缓存或为其他第三方缓 存方案，创建适配器来完全覆盖缓存行为。

不同点：

+ `Hibernate`的二级缓存配置在`SessionFactory`生成的配置文件中进行详细配置，然后再在具体的表-对象映射中配置是那种缓存。

+ `MyBatis`的二级缓存配置都是在每个具体的表-对象映射中进行详细配置，这样针对不同的表可以自定义不同的缓存机制。并且`Mybatis`可以在命名空间中共享相同的缓存配置和实例，通过`Cache-ref`来实现。



### [Mybatis与Spring集成时做了哪些事情](https://blog.csdn.net/qq_41737716/article/details/83685309)

第一个模块annotation：这里做了一个注解（MapperScan），用于扫描mapper。以及mapper扫描注册器（MapperScannerRegistrar），此扫描注册器实现了ImportBeanDefinitionRegistrar接口，在Spring容器启动时会运行所有实现了这个接口的实现类，而这个注册器内部会注册一系列MyBatis的信息。

+ **第一个模块annotation**：这里做了一个注解（`MapperScan`），用于扫描`mapper`。以及`mapper`扫描注册器（`MapperScannerRegistrar`），此扫描注册器实现了`ImportBeanDefinitionRegistrar`接口，在Spring容器启动时会运行所有实现了这个接口的实现类，而这个注册器内部会注册一系列MyBatis的信息。

+ **第二个模块batch**：这里没有深入研究这个包，估计是为了批量操作使用的？

+ **第三个模块config**：主要是为了解析处理读取到的配置信息。

+ **第四个模块mapper**：这里就是主要处理`mapper`的地方了，与编程式的MyBatis不同，与Spring集成后，每个`mapper`本来的生命周期为`method`级别，在集成后变成了`application`级别，主要原因是Spring将扫描到的每一个`mapper`都存入IOC容器，并且是**单例**的。

+ **第五个模块support**：只有一个模板辅助类，是`MapperFactoryBean`的父类，用于方便创建一个`SqlSession`（集成后的`SqlSession`变成了`SqlSessionTemplate`，下面会介绍）

+ **第六个模块transaction**：在集成后，事务的管理交给了Spring来做。



**SqlSessionTemplate到底在集成后有了什么作用**

+ `Spring`容器在启动后会扫描所有的Mapper信息，然后还是用`MapperProxy`给每一个`Mapper`接口做代理
+ 不同的是这次的`MapperProxy`中的`SqlSession`是`SqlSessionTemplate`代理的，也就是说，每次执行`mapper`接口的方法，都会先执行`MapperProxy`的`invoke`方法，然后去执行`SqlSessionTemplate`的`invoke`方法
+ 然后`SqlSessionTemplate`内部就会新`new`一个`DefaultSqlSession`，实际上底层还是使用的`DefaultSqlSession`的方法。

+ 可以从`invoke`方法看出，在集成后，设计者将为每一次查询都开启一个新的`SqlSession`，在集成后的`scope`为`method`级别，也就是每调用一次`mapper`接口的方法，都会创建一个`SqlSession`
+ 然后会将此`SqlSession`判断事务，因为是`Spring`管理事务，所以这里就可以用`SqlSessionTemplate`这样一个类去很方便的可以管理事务，也方便了`Spring`管理事务。
  

## git

### 获取Git仓库

有两种取得 Git 项目仓库的方法。

1. 在现有目录中初始化仓库: 进入项目目录运行 `git init` 命令,该命令将创建一个名为 `.git` 的子目录。
2. 从一个服务器克隆一个现有的 Git 仓库: `git clone [url]` 自定义本地仓库的名字: `git clone [url]` directoryname



### 撤销操作

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选项的提交命令尝试重新提交：

```shell
git commit --amendCopy to clipboardErrorCopied
```

取消暂存的文件

```shell
git reset filenameCopy to clipboardErrorCopied
```

撤消对文件的修改:

```shell
git checkout -- filenameCopy to clipboardErrorCopied
```

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：

```shell
git fetch origin
git reset --hard origin/master
```



### 分支

分支是用来将特性开发绝缘开来的。在你创建仓库的时候，*master* 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上。

我们通常在开发新功能、修复一个紧急 bug 等等时候会选择创建分支。单分支开发好还是多分支开发好，还是要看具体场景来说。

创建一个名字叫做 test 的分支

```console
git branch testCopy to clipboardErrorCopied
```

切换当前分支到 test（当你切换分支的时候，Git 会重置你的工作目录，使其看起来像回到了你在那个分支上最后一次提交的样子。 Git 会自动添加、删除、修改文件以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样）

```console
git checkout testCopy to clipboardErrorCopied
```

![img](https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/2019-3%E5%88%87%E6%8D%A2%E5%88%86%E6%94%AF.png)

你也可以直接这样创建分支并切换过去(上面两条命令的合写)

```console
git checkout -b feature_xCopy to clipboardErrorCopied
```

切换到主分支

```
git checkout masterCopy to clipboardErrorCopied
```

合并分支(可能会有冲突)

```java
 git merge testCopy to clipboardErrorCopied
```

把新建的分支删掉

```
git branch -d feature_xCopy to clipboardErrorCopied
```

将分支推送到远端仓库（推送成功后其他人可见）：

```
git push origin 
```





### 其他命令

> git rm --cached xxx.doc

删除记录，不删除源文件，用于删除错误上传到git远程库的文件

## Maven

Maven 主要服务于基于 Java 平台的项目构建、依赖管理和项目信息管理。

Maven 的主要功能主要分为 5 点：

- 依赖管理系统
- 多模块构建
- 一致的项目结构
- 一致的构建模型和插件机制

**Maven 规约是什么？**

- `/src/main/java/` ：Java 源码。
- `/src/main/resource` ：Java 配置文件，资源文件。
- `/src/test/java/` ：Java 测试代码。
- `/src/test/resource` ：Java 测试配置文件，资源文件。
- `/target` ：文件编译过程中生成的 `.class` 文件、jar、war 等等。
- `pom.xml` ：配置文件

Maven 要负责项目的自动化构建，以编译为例，Maven 要想自动进行编译，那么它必须知道 Java 的源文件保存在哪里，这样约定之后，不用我们手动指定位置，Maven 能知道位置，从而帮我们完成自动编译。

### Maven 常用命令

- `mvn archetype：create` ：创建 Maven 项目。
- `mvn compile` ：编译源代码。
- `mvn deploy` ：发布项目。
- `mvn test-compile` ：编译测试源代码。
- `mvn test` ：运行应用程序中的单元测试。
- `mvn site` ：生成项目相关信息的网站。
- `mvn clean` ：清除项目目录中的生成结果。
- `mvn package` ：根据项目生成的 jar/war 等。
- `mvn install` ：在本地 Repository 中安装 jar 。
- `mvn eclipse:eclipse` ：生成 Eclipse 项目文件。
- `mvn jetty:run` 启动 Jetty 服务。
- `mvn tomcat:run` ：启动 Tomcat 服务。
- `mvn clean package -Dmaven.test.skip=true` ：清除以前的包后重新打包，跳过测试类。

用到最多的命令

- `mvn eclipse:clean` ：清除 Project 中以前的编译的东西，重新再来。
- `mvn eclipse:eclipse` ：开始编译 Maven 的 Project 。
- `mvn clean package` ：清除以前的包后重新打包。

### Maven坐标

```xml
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>4.13-BETA</version>
```



- **groupId** ：定义当前 Maven 项目隶属的实际项目。首先，Maven 项目和实际项目不一定是一对一的关系。比如 Spring FrameWork 这一实际项目，其对应的 Maven 项目会有很多，如 `spring-core`、`spring-context` 等。这是由于 Maven 中模块的概念，因此，一个实际项目往往会被划分成很多模块。其次，groupId 不应该对应项目隶属的组织或公司。原因很简单，一个组织下会有很多实际项目，如果 groupId 只定义到组织级别，而后面我们会看到，artifactId 只能定义 Maven 项目(模块)，那么实际项目这个层次将难以定义。最后，groupId 的表示方式与 Java 包名的表达方式类似，通常与域名反向一一对应。上例中，groupId 为 `junit` ，是不是感觉很特殊，这样也是可以的，因为全世界就这么个 junit ，它也没有很多分支。
- **artifactId** ：该元素定义当前实际项目中的一个 Maven 项目(模块)。推荐的做法是使用实际项目名称作为 artifactId 的前缀。比如上例中的 junit ，junit 就是实际的项目名称，方便而且直观。在默认情况下，Maven 生成的构件，会以 artifactId 作为文件头。例如 `junit-3.8.1.jar` ，使用实际项目名称作为前缀，就能方便的从本地仓库找到某个项目的构件。
- **version** ：该元素定义了使用构件的版本。如上例中 junit 的版本是 `4.13-BETA` ，你也可以改为 `4.1.2` 表示使用 `4.1.2` 版本的 junit 。
- **packaging** ：定义 Maven 项目打包的方式，使用构件的什么包。打包方式通常与所生成构件的文件扩展名对应。如上例中没有 `packaging` ，则默认为 jar 包，最终的文件名为`junit-4.13-BETA.jar` 。当然，也可以打包成 war 等。
- **classifier** ：该元素用来帮助定义构建输出的一些附件。附属构件与主构件对应。如上例中的主构件为 `junit-4.13-BETA.jar` ，该项目可能还会通过一些插件生成如 `junit-4.13-BETA-javadoc.jar`、`junit-4.13-BETA-sources.jar`，这样附属构件也就拥有了自己唯一的坐标。

上述 5 个元素中：

- `groupId`、`artifactId`、`version` 是必须定义的。
- `packaging` 是可选的(默认为 jar )。
- 而 `classfier` 是不能直接定义的，需要结合插件使用。

**多模块如何聚合**

```xml
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.stear.orchid</groupId>
	<artifactId>orchid</artifactId>
	<version>0.1</version>
	<packaging>pom</packaging>
	<name>orchid</name>
	<description>The orchid project of stear</description>
	<url>http://orchid.stear.com</url>
	<modules>
		<module>orchid-server</module>
		<module>orchid-support</module>
  	</modules>
```

这个pom.xml的配置关键在两点：（1）packaging类型为`pom`；（2）`<modules>`模块的引入。·

```html
<module>orchid-server</module>
```

表示在当前目录（也就是上面pom.xml所在目录）包括一个文件夹orchid-server。如果orchid-server和orchid在同一目录的话，那么配置应该是

```xml
<module>../orchid-server</module>
```



### **继承**

```xml
<parent>
		<groupId>com.stear.orchid</groupId>
		<artifactId>orchid</artifactId>
		<version>1.0-SNAPSHOT</version>
		<relativePath>parent project orchid directory/pom.xml</relativePath>
</parent>
```




**聚合和继承的关系**

聚合和继承本质上没什么关系。聚合是为了快速构建，只要在总项目中执行命令就会自动构建包含的某块项目。继承是为了消除重复配置，各个子项目可以继承父项目中的配置

对于聚合的总项目来说，它知道有哪些项目被聚合在一起，而各个模块对总的项目是一无所知的。

对于继承的父项目来说，它不知道哪些项目会继承它，但是子项目明确指明了父项目。

通常来说，父项目和总项目是一个项目。也是为了结构的清晰可见。

事务re
