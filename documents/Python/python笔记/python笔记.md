# python笔记

## 基础

### 语法区别

`r''`：表示`''`内部的字符串默认不转义 

`len()`:表示字符串长度或者`bytes`类型的字节数



### 编码问题

在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。 



`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符 

Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示：

```python
x = b'ABC'
```

在`bytes`中，无法显示为ASCII字符的字节，用`\x##`显示。 

如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节： 

```python
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```



保证源代码为UTF-8格式从而中文可以正常编译，开头这样写

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

申明了UTF-8编码并不意味着你的`.py`文件就是UTF-8编码的，必须并且要确保文本编辑器正在使用UTF-8 without BOM编码



### List和Tuple

list是可变对象



tuple是不可变对象，tuple的每个元素，指向永远不变 

+ 注意tuple存放单个元素的时候：`t = (1,)`
+ 嵌套tuple只保证外围指向不变，内部的元素值依然可以改变

### dict和set

> dict内部存放的顺序和key放入的顺序是没有关系的。
>
> 其实就是map

和list比较，dict有以下几个特点：

1. 查找和插入的速度极快，不会随着key的增加而变慢；
2. 需要占用大量的内存，内存浪费多。

而list相反：

1. 查找和插入的时间随着元素的增加而增加；
2. 占用空间小，浪费内存很少。



**特点**

+ dict的key必须是**不可变对象**



set使用

```python
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

**不可变对象**



### 函数

+ 返回值是一个tuple
+ 注意输入类型检查，类型一般是str

> inconsistent use of tabs and spaces in indentation
>
> 缩进问题

设置默认参数的时候

+ 一是必选参数在前，默认参数在后，否则Python的解释器会报错（思考一下为什么默认参数不能放在必选参数前面）；
+ 二是如何设置默认参数。

**定义默认参数要牢记一点：默认参数必须指向不变对象！**

 如果默认参数是list之类的话，对象会记住上一次的传参结果

```python
# L不能等于[]（可变对象）
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

**为什么要设计`str`、`None`这样的不变对象呢？**

因为不变对象一旦创建，对象内部的数据就不能修改，这样就减少了由于修改数据导致的错误。此外，由于对象不变，多任务环境下同时读取对象不需要加锁，同时读一点问题都没有。我们在编写程序时，如果可以设计一个不变对象，那就尽量设计成不变对象。

#### 函数的参数组合

参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。 

+ 可变参数自动组装成tuple
+ 关键字参数则是组装成dict

```python
#  *arg是可变参数   **kw是关键字参数
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

#   *可以限制关键字参数的名字，之后的参数都是命名关键字
def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

### 高级特性

+ 切片
+ 迭代
+ 列表生成式
+ 生成器
+ 迭代器



`enumerate函数 `将list变成元素索引队

G附加组

g主要组



#### **生成器**

> generator保存的是算法，每次调用`next(g)`，就计算出`g`的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出`StopIteration`的错误。

1. 只要把一个列表生成式的`[]`改成`()`，就创建了一个generator
2. 如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator

**yield**

而变成generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

如果是`send()`调用的话，结果是`send()`函数传入的值·

使用场景

+ 生成器
+ 上下文管理器
+ 协程



**yield from** 

一般用await多一点

主要用途是

+ 迭代地消耗生成器
+ 建立一条双向通道



#### 迭代器

Python的`for`循环本质上就是通过不断调用`next()`函数实现的，例如：

```python
for x in [1, 2, 3, 4, 5]:
    pass
```

实际上完全等价于：

```python
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```



### 函数式编程

常用函数

+ map、reduce
+ filter
+ sort

#### 返回函数

##### 闭包

> 一个持有外部环境变量的函数就是闭包

一个函数，如果函数名后紧跟一对括号，说明现在就要调用这个函数，如果不跟括号，只是一个函数的名字，里面存了函数所在位置的引用 

外函数把临时变量绑定给内函数 ，每次调用外部函数的时候，返回的内函数都是不同的对象，但是闭包变量是同一个

内函数修改外函数局部变量:

借鉴一般函数修改全局变量

+ global是全局变量
+ 全局变量是可变类型数据的时候可以修改

python3中，可以用nonlocal 关键字声明 一个变量， 表示这个变量不是局部变量空间的变量，需要向上一层变量空间找这个变量。 

在python2中，没有nonlocal这个关键字，可以把闭包变量改成可变类型数据进行修改 



**经典情况**

```python
def foo():
    a =1
    def bar():
        a = a+1
        return a
    return bar
c=  foo()
print(c())
#local variable 'a' referenced before assignment
#python规则指定所有在赋值语句左面的变量都是局部变量，则在闭包bar()中，变量a在赋值符号"="的左面，被python认为是bar()中的局部变量。再接下来执行print c()时，程序运行至a = a + 1时，因为先前已经把a归为bar()中的局部变量，所以python会在bar()中去找在赋值语句右面的a的值，结果找不到，就会报错
```

#### 匿名函数

lamda表达式

```python
list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```



### python中_和__区别

`_`

> 在python的类中，没有真正的私有化，不管是方法还是属性，为了编程的需要，约定加了下划线 _ 的属性和方法不属于API，不应该在类的外面访问，也不会被from M import * 导入。下面的代码演示加了_ 的方法，以及在类外面对其的可访问性。

`__`

> python中的__和一项称为name mangling的技术有关，name mangling (又叫name decoration命名修饰).在很多现代编程语言中,这一技术用来解决需要唯一名称而引起的问题,比如命名冲突/重载等.
>
> 双下划线开头,是为了不让子类重写该属性方法.通过类的实例化时自动转换,在类中的双下划线开头的属性方法前加上”_类名”实现.

## 底层原理

### 可变对象和不可变对象

> python函数传参是引用传递

+ 可变对象修改后地址不变
+ 不可变对象修改后地址改变，传参的时候如果对入参对象修改，不会影响到外部的引用，因为地址已经被修改



### 内存管理

#### 引用计数法

引用计数增加的情况：

1. 一个对象分配一个新名称
2. 将其放入一个容器中（如列表、元组或字典）

引用计数减少的情况：

1. 使用del语句对对象别名显示的销毁
2. 引用超出作用域或被重新赋值

`sys.getrefcount( )`函数可以获得对象的当前引用计数

多数情况下，引用计数比你猜测得要大得多。对于不可变数据（如数字和字符串），解释器会在程序的不同部分共享内存，以便节约内存。

#### 垃圾回收机制

1. 当一个对象的引用计数归零时，它将被垃圾收集机制处理掉。
2. 当两个对象a和b相互引用时，del语句可以减少a和b的引用计数，并销毁用于引用底层对象的名称。然而由于每个对象都包含一个对其他对象的应用，因此引用计数不会归零，对象也不会销毁。（从而导致内存泄露）。为解决这一问题，解释器会定期执行一个**循环检测器**，搜索不可访问对象的循环并删除它们。

#### 内存池机制

Python提供了对内存的垃圾收集机制，但是它将不用的内存放到内存池而不是返回给操作系统。

1. `Pymalloc`机制。为了加速Python的执行效率，Python引入了一个内存池机制，用于管理对小块内存的申请和释放。
2. Python中所有小于`256`个字节的对象都使用`pymalloc`实现的分配器，而大的对象则使用系统的`malloc`。
3. 对于Python对象，如整数，浮点数和List，都有其独立的私有内存池，对象间不共享他们的内存池。也就是说如果你分配又释放了大量的整数，用于缓存这些整数的内存就不能再分配给浮点数。



## 面向对象编程

### \__slots__

限制实例的属性

使用`__slots__`要注意，`__slots__`定义的属性仅对当前类实例起作用，对继承的子类是不起作用的

```python
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

### @property

简化setter和getter

原来的做法

```python
class Student(object):

    def get_score(self):
         return self._score

    def set_score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```

现在的做法

```python
class Student(object):

    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    @property
    def age(self):
        return 2015 - self._birth
```

### 多重继承

#### MixIn

MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系

> MixIn只是在多重继承时的一种特殊约定，正文提到：由于Python允许使用多重继承，因此，MixIn就是一种常见的设计。



### 定制类	

特殊定义的类

+ \__str__

+ \__repr__

  两者的区别是`__str__()`返回用户看到的字符串，而`__repr__()`返回程序开发者看到的字符串

+ \__iter__

+ \__getitem__

+ \__getattr__

+ \__call__

+ 

```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1 # 初始化两个计数器a，b

    def __iter__(self):
        return self # 实例本身就是迭代对象，故返回自己

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b # 计算下一个值
        if self.a > 100000: # 退出循环的条件
            raise StopIteration()
for n in Fib():
     print(n)
```

### 枚举类

```python
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
```

```python
from enum import Enum, unique

@unique
class Weekday(Enum):
    Sun = 0 # Sun的value被设定为0
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
```





## IO

文件读写

内存读写：StringIO和byteIO

**序列化**

> 把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫pickling，在其他语言中也被称之为serialization，marshalling，flattening等等 

json的原始dumps和loads都不能序列化python的class，不管可以自己定义一个转化函数放入dumps和loads中去，这样就可以序列化了。

json.dumps序列化时对中文默认使用的ascii编码.想输出真正的中文需要指定ensure_ascii=False：



### 字符串问题

python的输入如果是字符串是不需要添加`''`的,在进行比较的时候，使用`==`可以字面意义比较字符串（value），但是`is`不行，它比较字符串的id值



### 上下文管理器

> 在python中实现了\_\_enter\_\_和\__exit__方法，即支持上下文管理器协议

基本语法

```python
with expression [as target]:

	with_body
```





## 进程和线程

因为Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。 

### 多进程

多进程实例

```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()
    p.join()
    print('Child process end.')
```

进程池

```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)
    for i in range(5):
        p.apply_async(long_time_task, args=(i,))
    print('Waiting for all subprocesses done...')
    p.close()
    #主进程让出CPU
    p.join()
    print('All subprocesses done.')
```

子进程

调用shell命令

可以通过`communicate()`方法输入

```python
import subprocess

#print('$ nslookup www.python.org')
#r = subprocess.call(['nslookup', 'www.python.org'])
#print('Exit code:', r)

print('$ nslookup')
p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
print(output.decode('utf-8'))
print('Exit code:', p.returncode)

```

上面的代码相当于在命令行执行命令`nslookup`，然后手动输入：

```shell
set q=mx
python.org
exit
```



进程间通信

### 多线程

```python
import time, threading

# 新线程执行的代码:
def loop():
    print('thread %s is running...' % threading.current_thread().name)
    n = 0
    while n < 5:
        n = n + 1
        print('thread %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
    print('thread %s ended.' % threading.current_thread().name)

print('thread %s is running...' % threading.current_thread().name)
t = threading.Thread(target=loop, name='LoopThread')
t.start()
t.join()
print('thread %s ended.' % threading.current_thread().name)
```

**注意**

在Unix/Linux下，`multiprocessing`模块封装了`fork()`调用，使我们不需要关注`fork()`的细节。由于Windows没有`fork`调用，因此，`multiprocessing`需要“模拟”出`fork`的效果，父进程所有Python对象都必须通过pickle序列化再传到子进程去，所以，如果`multiprocessing`在Windows下调用失败了，要先考虑是不是pickle失败了。

**GIL锁**

因为Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。

GIL是Python解释器设计的历史遗留问题，通常我们用的解释器是官方实现的CPython，要真正利用多核，除非重写一个不带GIL的解释器。



### ThreadLocal

```java
import threading
    
# 创建全局ThreadLocal对象:
local_school = threading.local()

def process_student():
    # 获取当前线程关联的student:
    std = local_school.student
    print('Hello, %s (in %s)' % (std, threading.current_thread().name))

def process_thread(name):
    # 绑定ThreadLocal的student:
    local_school.student = name
    process_student()

t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
t1.start()
t2.start()
t1.join()
t2.join()
```

全局变量`local_school`就是一个`ThreadLocal`对象，每个`Thread`对它都可以读写`student`属性，但互不影响。你可以把`local_school`看成全局变量，但每个属性如`local_school.student`都是线程的局部变量，可以任意读写而互不干扰，也不用管理锁的问题，`ThreadLocal`内部会处理。

可以理解为全局变量`local_school`是一个`dict`，不但可以用`local_school.student`，还可以绑定其他变量，如`local_school.teacher`等等。



### 分布式进程



### 协程

实现方式

+ 使用yield from和@asyncio.coroutine实现协程
+ 使用async和await实现协程(3.5之后版本)

#### yield from

后面必须跟随生成器

实际等于:

```python
for title in titles:　# 等价于yield from titles
    yield title　　
```

同时封装了很多异常处理；用于建立调用方和子生成器之间的通道



#### asyncio

[一个通俗易懂的案例](https://blog.csdn.net/SL_World/article/details/86597738)

`asyncio`的编程模型就是一个消息循环。我们从`asyncio`模块中直接获取一个`EventLoop`的引用，然后把需要执行的协程扔到`EventLoop`中执行，就实现了异步IO。

#### async/await

请注意，`async`和`await`是针对coroutine的新语法，要使用新的语法，只需要做两步简单的替换：

1. 把`@asyncio.coroutine`替换为`async`；
2. 把`yield from`替换为`await`。

#### aiohttp

`asyncio`可以实现单线程并发IO操作。如果仅用在客户端，发挥的威力不大。如果把`asyncio`用在服务器端，例如Web服务器，由于HTTP连接就是IO操作，因此可以用单线程+`coroutine`实现多用户的高并发支持。

`asyncio`实现了TCP、UDP、SSL等协议，`aiohttp`则是基于`asyncio`实现的HTTP框架。



[Python实战异步爬虫](https://blog.csdn.net/SL_World/article/details/86633611)

| 协程           | 属性                                                     |
| -------------- | -------------------------------------------------------- |
| 所需线程       | **单线程** (因为仅定义一个loop，所有event均在一个loop中) |
| 编程方式       | 同步                                                     |
| 实现效果       | **异步**                                                 |
| 是否需要锁机制 | 否                                                       |
| 程序级别       | 函数级                                                   |
| 实现机制       | **事件循环＋协程**                                       |
| 总耗时         | 最耗时事件的时间                                         |
| 应用场景       | IO密集型任务等                                           |

## 网络编程

### TCP

创建`Socket`时，`AF_INET`指定使用IPv4协议，如果要用更先进的IPv6，就指定为`AF_INET6`。`SOCK_STREAM`指定使用面向流的TCP协议

```python
# 导入socket库:
import socket

# 创建一个socket:
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 建立连接:
s.connect(('www.sina.com.cn', 80))
```



### UDP



## virtualenv

### virtualenvwrapper

提供了一系列命令使得和虚拟环境工作变得愉快许多。它把您所有的虚拟环境都放在一个地方。

1. 将您的所有虚拟环境在一个地方。
2. 包装用于管理虚拟环境（创建，删除，复制）。
3. 使用一个命令来环境之间进行切换。