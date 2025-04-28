# java命名规范

### PO

> persistant object 持久对象
>
> 可以看成是与数据库中的表相映射的java对象。最简单的PO就是对应数据库中某个表中的一条记录，多个记录可以用PO的集合。PO中应该不包含任何对数据库的操作



### VO

>value object值对象。
>
>通常用于业务层之间的数据传递，和PO一样也是仅仅包含数据而已



### DAO

> data access object数据访问对象
>
> 此对象用于访问数据库。通常和PO结合使用，DAO中包含了各种数据库的操作方法。通过它的方法,结合PO对数据库进行相关的操作.



###  BO

> business object 业务对象
>
> 封装业务逻辑的java对象,通过调用DAO方法,结合PO,VO进行业务操作;

### **POJO**

> plain ordinary java object 简单无规则java对象



### **Java RMI**

> Java Remote Method Invocation , Java远程方法调用
>
> 是Java编程语言里，一种用于实现远程过程调用的应用程序编程接口。它使客户机上运行的程序可以调用远程服务器上的对象。远程方法调用特性使Java编程人员能够在网络环境中分布操作。RMI全部的宗旨就是尽可能简化远程接口对象的使用。





### DTO

> Data Transfer Object，数据传送对象
>
> DTO是一个普通的Java类，它封装了要传送的批量的数据。当客户端需要读取服务器端的数据的时候，服务器端将数据封装在DTO中，这样客户端就可以在一个网络调用中获得它需要的所有数据。

