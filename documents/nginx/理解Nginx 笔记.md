# 深入理解Nginx 笔记

 

 

 

 

 

 

 

## 第三章

 

 

 

+ 字符串结构只有两个成员变量，数据指针和长度，使用的时候一定要通过指针使用

 

+ 链表结构的节点是数组元素，内存由内存池分配

 

+ table_elt_t KV对，额外存了全小写的key

 

## 第七章

nginx提供的高级数据结构

 

 

 

+ `ngx_queue_t` 双向链表

 

+ `ngx_array_t` 动态数组

 

+ `ngx_list_t` 单向链表

 

+ `ngx_rbtree_t` 红黑树

 

+ `ngx_radix_t` 基数树

 

+ 支持通配符的散列表

 

 

 

 

 

 

 

·

 

 

 

### 双向链表 ngx_queue_t

 

 

 

同时封装了访问容器和访问元素的方法

 

 

 

#### 特点

 

 

 

+ 不负责分配内存

 

+ 实现轻量级排序算法

 

+ 支持链表合并

 

 

 

 

 

 

 

#### 结构

 

 

 

初始化维护一个ngx_queue_t容器，容器的next指向第一个元素，prev指向最后一个元素

 

 

 

添加节点之后，最后一个节点的next指向容器

 

 

 

> 因此ngx_queue_after(&queueContainer,&node[0].qEle)第一个参数如果是容器指针的话，该函数的作用实际就是插入头节点

 

 

 

 

 

 

 

### 动态数组 ngx_array_t

 

 

 

 

 

 

 

#### **特点**

 

 

 

+ 内存由内存池分配，负责元素占用内存的分配，支持扩容

 

+ 访问速度快

 

+ 允许元素个数具备不确定性

 

 

 

 

 

 

 

> ngx_array_destroy必须和ngx_array_create配对使用，同时这里处理的内存指的是内存池中的内存

 

 

 

 

 

 

 

#### 扩容机制

+ 如果当前内存池中的剩余空间大于等于需要新增空间，只扩容新增的部分，不涉及数组的赋值

+ 如果当前内存池中的剩余空间小于需要新增的空间，此时会涉及到数组的复制
+ 对于`ngx_array_push`方法会将原来容量扩容一倍
+ 对于`ngx_array_push_n`方法，如果n小于原先数组容量，会扩容一倍，如果大于，会分配2*N大小空间，



 

### 单项链表 ngx_list_t

 

 

 

相当于ngx_array_t的单向链表版本

 

 

 

 

 

 

 

### 红黑树 ngx_rbtree_t

 

 

 

 

 

 

 

#### 特性

 

 

 

+ 节点只有红黑两色

 

+ 根节点是黑色的

 

+ 叶子节点都是黑色的

 

+ 红节点的子节点都是黑色的（没有红色节点和红色节点相邻的情况）

 

+ 从任一节点到叶子节点的路径上黑色节点的数量一样多

 

 

 

> 红黑树的特性决定了最长的路径不会大于最短路径的两倍

 

 

 

#### **结构**

 

 

 

```c

```

 

struct ngx_rbtree_s{

 

  //根节点

 

  ngx_rbtree_node_t        *root;

 

  //哨兵节点，指向NULL，这里表示叶子节点

 

  ngx_rbtree_node_t        *sentinel;

 

  //插入指针，用来表示插入节点的时候是新增还是覆盖，这是一个函数指针

 

  //nginx自实现了三种插入时的比较方法，通过insert来指向这些函数

 

  ngx_rbtree_node_pt     insert;

 

}

 

```

```

 

 

 

*将函数指针变量作为结构体成员变量以达成可以把结构体当做类来使用（既有成员变量又有成员方法）的效果*

 

 

 

 

 

 

 

### 基数树 ngx_radix_tree

 

 

 

#### 特点

 

 

 

+ 存储节点必须以32位整型作为区别任意两个节点的唯一标识

 

+ 负责分配每一个节点的内存，每个节点只可能包含一个指针，指向实际的数据

 

+ 拥有二叉查找树的性质，但是不需要保持树的平衡

 

 

 

 

 

 

 

**结构** 

通过32位整型判断节点在树中的位置，同时通过掩码来决定树的高度

```c
typedef struct {
  //指向根节点
  ngx_radix_node_t  *root;
  //内存池，负责给基数树的节点分配内存
  ngx_pool_t           *pool;
  //管理分配但暂未使用的节点，实际上是一个单链表
  ngx_radix_node_t    *free;
  //已分配内存中还未使用内存的首地址
  char               *start;
  //已分配内存中还未使用内存的大小
  size_t              size;
}ngx_radix_tree_t;
```


### 散列表 ngx_hash_t
#### 特性
+ 支持通配符匹配
#### 避免hash碰撞

 

 

 

采用开放寻址法中的非空槽存储碰撞元素方法

 

 

 

插入时检查指定槽是否为空或是否为同一元素，如果不是，以此检查之后连续的槽

 

 

 

 

 

 

 

#### 结构

 

 

 

+ 初始化的时候就分配好了槽的长度

 

+ 出现通配符匹配的时候需要新建多个散列表，为了防止相同关键字匹配需要重新遍历散列表

 

+ 定义数组后如果出现同名关键字就可以通过散列码快速得到关键字进行匹配比较

 

 

 

 

 

 

 

表中元素的结构

```c
typedef struct {
  /* 指向用户自定义元素数据的指针，如果当前 ngx_hash_elt_t 槽为空，则 value 的值为 0 */
  void       *value;
  /* 元素关键字的长度 */
  u_short      len;
  /* 元素关键字的首地址 */
  u_char      name[1];
} ngx_hash_elt_t;
```

表的结构

 

```c
typedef struct {
  /* 指向散列表的首地址，也是第 1 个槽的地址 */
  ngx_hash_elt_t **buckets;
  /* 散列表中槽的总数 */
  ngx_uint_t    size;
} ngx_hash_t;
```

同时设计了`ngx_hash_key_pt `散列方法指针，可以模仿此自定义散列函数
```c
typedef ngx_uiny_t (*ngx_hash_key_pt) (u_char *data, size_t len)

```

官方的散列方法
+ 使用BKDR算法将任意长度字符串映射成整型
+ 先将字符串小写之后再用BKDR算法

#### 通配符实现
+ 实现前置通配符散列表和后置通配符散列表
+ 将字符串转义，如果`*.test.com`会转换成`com.test.`然后进行匹配   
+ 注意严格匹配和前置后置匹配之间具有严格匹配顺序

**实现过程**
+ 初始化散列表
+ 将字符串传到预添加的array里
+ 判断三个散列表是否为空，不为空就分配内存，把字符串放到真正的散列表中
+ 注意最后要销毁临时内存池，临时内存池作用是配合散列算法将字符串全小写

## 第八章
从网络角度衡量性能：网络性能（吞吐量和带宽）、单次请求的延迟性、网络效率
#### 模块化设计
```c
struct ngx_module_s {
  ngx_uint_t      ctx_index;//模块在所属类型模块中所处的位置
  ngx_uint_t      index;//模块在所有模块中所处的位置
  char         *name;//模块名称
  ngx_uint_t      spare0-3;//未使用
  const char      *signature;//未使用
  void         *ctx;//模块上下文，一般保存模块实现的接口规范地址
  ngx_command_t    *commands;//模块指令列表
  ngx_uint_t      type;//模块类型
  ngx_int_t      (*init_master)(ngx_log_t *log);
  ngx_int_t      (*init_module)(ngx_cycle_t *cycle);
  ngx_int_t      (*init_process)(ngx_cycle_t *cycle);
  ngx_int_t      (*init_thread)(ngx_cycle_t *cycle);
  void        (*exit_thread)(ngx_cycle_t *cycle);
  void        (*exit_process)(ngx_cycle_t *cycle);
  void        (*exit_master)(ngx_cycle_t *cycle);
  uintptr_t       spare_hook0-7;//以下均未使用
}

```


| 回调函数   | 具体含义                           |
| ------------ | ------------------------------------------------------------ |
| init_master | 在master进程初始化时调用，目前未使用             |
| init_module | 模块初始化。由master进程在执行ngx_init_cycle时调用，早于init_process |
| init_process | 初始化worker进程，由worker进程在进程开始工作前调用，晚于init_module |
| init_thread | 开启线程模式后才调用，目前未使用               |
| exit_thread | 开启线程模式才调用，目前未使用                |
| exit_process | 在单进程模式下，由进程在退出时调用；在master-worker模式下，由worker进程在退出时调用 |
| exit_master | 在单进程模式下，由进程在退出时调用，在master-worker模式下，由master进程在退出时调用。该函数在exit_process之后被调用 |

#### 事件驱动架构
**特点**
+ 事件消费者不是进程而是模块，这样事件消费者只是被事件收集、分发者短期调用，从而不会长期独占进程资源
 + 缺点：对模块开发要求提高，一个模块不能长时间阻塞
+ 请求的多阶段异步处理。一个请求的处理过程按照事件触发的方式划分成多个阶段，由事件收集、分发器去调用模块触发
 + 优点：配合异步事件驱动架构，使得每个进程都能充分运行

> 如何防止进程阻塞？
> + 将阻塞方法分阶段改成非阻塞调用和返回通知
> + 根据时间分解阻塞方法调用，使得每个阶段都能无休眠执行
> + 使用定时器针对需要等待和无所事事的时候


### nginx启动流程
cycle启动各个模块
### event事件

1. 创建一个事件
2. 释放event_free
3. 注册event
4. event_assign为事件分配内存
    已经初始化或者处于 pending 的 event，首先需要调用 event_del() 后再调用 event_assign()。这个时候就可以重用这个event了。
5. 信号事件，对信号进行事件的处理 

## 第九章

+ ngx_event_nodule是事件模块的通用类型，ngx_event_core_module是其中一个事件模块，用于初始化和选择其他事件模块


### ngx_event_module

> 是所有事件模块的通用模块

功能
+ 定义新的事件类型
+ 定义每个事件模块需要实现的ngx_event_module_t接口
+ 管理事件模块生成的配置结构体，解析事件类配置项
+ 调用回调方法


管理事件模块
1. 初始化所有事件模块的ctx_index序号
2. 分配指针数组
3. 调用所有事件模块的create_conf方法
4. 为所有事件模块解析nginx.conf配置文件
5. 调用所有事件模块的init_conf方法

### ngx_event_core_module
+ 创建连接池
+ 选择事件驱动方式
+ 初始化事件模块

 

启动时的工作流程
1. 选择是否打开ngx_accept_mutx锁
2. 初始化红黑树定时器
3. 调用use配置项指定的事件驱动模块的init方法
4. 指定时间精度回调方法
5. 使用epoll，预分配句柄
6. 预分配连接数组作为连接池
7. 预分配事件数组作为读写事件池
8. 将读写事件池和连接池连接起来
9. 连接空闲连接链表
10. 为监听对象分配连接
11. 将监听到的读事件添加到事件驱动模块，这样epoll事件模块就开始检测监听服务

#### epoll事件驱动模块
+ epoll_create
+ epoll_ctl
+ epoll_wait


### 事件驱动模块处理过程

1. 使用timer_resolution设置时间精度；没有使用则调用ngx_event_find_timer()获取最近将要触发的事件距离现在的毫秒数
2. 判断是否打开accept_mutex，获取锁才能进行负载均衡和解决惊群效应
3. 调用ngx_process_events，计算执行时间
4. 处理新
6. 连接队列，解除mutex锁，处理旧连接队列


## 文件IO
文件异步IO的优劣
优点：
+ 通过内核级别的异步IO，nginx提交IO请求之后，内核会通知IO设备独立执行IO操作，同时nginx进程可以完全占用CPU
+ 同时大量读事件来临的时候可以发挥电梯算法的优势

> 电梯算法：即IO调度器使用的算法，因为IO调度器的总体目标是希望让磁头能够总是往一个方向移动,移动到底了再往反方向走，像电梯。
> 1. NOOP：合并相邻请求，按先来先处理的思路，不过容易造成后面的请求饿死
> 2. CFq（默认）：CFQ为每个进程/线程，单独创建一个队列来管理该进程所产生的请求，也就是说每个进程一个队列，各队列之间的调度使用时间片来调度；但是先来的IO请求不一定先满足，因此会存在饿死现象
> 3. Deadline：在cfq基础上，额外增加读IO和写IO的FIFIO队列；Deadline确保了在一个可调整的截止时间内服务请求，解决了IO请求饿死的极端情况
> 4. anticipatory ：为了满足随机IO和顺序IO混合情景，为每个读IO设置等待时间窗口，并将小的写入流合并成大的写入流，用写入延时换取最大的写入吞吐量。

 

 

 

缺点：

+ 不支持缓存操作，所有读操作都在磁盘中进行
+ 对于单个请求可能会造成实际处理速度的降低
