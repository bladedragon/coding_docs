# http协议

## 2.通用规范和语法

+ Http定义CR（回车）LF（换行）作为行结束的标志
+ LWS即一个空格或者一个缩进tab
+ `()`:类似注释的存在





## 3.协议参数



### 协议版本

**代理**和**网关**接收的HTTP版本不能发送大于自身版本的请求，接收到的大于自身版本的请求后必须降级处理；除非切换到**tunnel**管道

### URL

HTTP中代表一种通过名字、位置和其他参数来定义的资源

协议本身对URL的长度没有限制，但是服务器自己约定长度时如果超时限制需要返回414

接收URL不含URL时`\`作为URI，代理接收到的URL不含域名的时候需要自己添加



### 时间

http支持三种日期格式

+ `Sun, 06 Nov 1994 08:49:38 GMT `
+ `Nov-94 -08:49:37 GMT`
+ `Sun Nov 6 08:48:37 1994`



### 字符集

字符集不区分大小写，必须要在IANA注册表中被标记的





### 内容编码

> Content-coding = taken (大小写不敏感)

一般用于Accept-Encoding 和Content-Encoding的值

IANA规定了以下几种编码规则:

+ gzip  (推荐使用x-gzip,因为程序名当作编码规则不太合理)
+ compress （推荐使用x-compress）
+ deflate
+ identity （只能使用在Accept-Encoding中，不能使用在Content-Encoding里）



### 传输编码

> Transfer-coding

和Content-Encoding的区别：Transfer-Encoding是Message的属性，不是entity的属性

transfer-coding的值一般用在TE header的属性和Transfer-Encoding的头域中

在分片场景中，chunked必须是唯一且最后一个transfer-coding，这有助于计算length

IANA规定了一下几种属性

+ chunked
+ gzip
+ identity
+ compress
+ deflate



### 块传输编码

这块不是很懂

````http
       Chunked-Body   = *chunk
                        last-chunk
                        trailer
                        CRLF

       chunk          = chunk-size [ chunk-extension ] CRLF
                        chunk-data CRLF
       chunk-size     = 1*HEX
       last-chunk     = 1*("0") [ chunk-extension ] CRLF

       chunk-extension= *( ";" chunk-ext-name [ "=" chunk-ext-val ] )
       chunk-ext-name = token
       chunk-ext-val  = token | quoted-string
       chunk-data     = chunk-size(OCTET)
       trailer        = *(entity-header CRLF)
````



###    媒体类型

在旧版本中不支持



### 标准以及文本缺省（默认）

文本媒体支持在entity-body中使用 字符集里等价于CR和LF的字节序列来表示换行符



### 多媒体类型

除了`multipart/byterange`类型会被缓存机制解析之外，其他类型都和一般类型一样看作是一种严格负载（不说人话）



## HTTP Message

HTTP消息由从客户到服务器的请求和从服务器到客户的响应组成

entity：message 的负载

请求和响应都是message的具体实现（message实际有点像接口）



开头的CRLF需要被忽略



### Message头、body和长度

+ Message头部的首尾LWS会被忽略，中间多个LWS会被空格（SP）替代
+ Message body和entity body只会在transfer-coding不同的时候才会有所区别
+ 请求中message body的存在是由content-Length和transfer-encoding决定的
+ 响应中message body的存在是由相应请求方法（HEAD不允许存在message body）和状态码（1xx，204，304不包含message body）决定的，其他的都会包含message body，即使长度可能是0



message length的定义（也就是transfer length的值）

+ 不存在message body的情况，transfer length的值会被忽略
+ 存在transfer encoding的情况，一般transfer length的定义根据chunked来处理
+ 如果content-length被定义，它就是transfer-length；如果如果entity length和transfer length的值不相同，content-length不会被发送；如果同时接收到transfer-length和content-length，会忽略掉content -length
+ 使用`multipart/byterange` 且transfer-length没有被指定，那么由媒体类型定义transfer-length
+ 服务器关闭连接的时候可以知道transfer length



如果发送到http1.0服务器，http1.1的请求一定要添加transfer length

content-length和transfer-length不能共存，否则前者会被忽略

无效的content-length必须要被立即通知到

```http
     general-header = Cache-Control            ; Section 14.9
                      | Connection               ; Section 14.10
                      | Date                     ; Section 14.18
                      | Pragma                   ; Section 14.32
                      | Trailer                  ; Section 14.40
                      | Transfer-Encoding        ; Section 14.41
                      | Upgrade                  ; Section 14.42
                      | Via                      ; Section 14.45
                      | Warning                  ; Section 14.46
```



## 5.Request请求

> Request-Line   = Method SP Request-URI SP HTTP-Version CRLF



**method**

GET和HEAD要求所有通用目的的服务器接收，其他方法都是可选的

**Request-URI**

> ​    Request-URI    = "*" | absoluteURI | abs_path | authority

absoluteURI(绝对路径)在代理服务器提交请求的时候是必须的

authority（认证）只出现在CONNECT方法的时候

abs_path（相对路径）是最常用的，注意不能为空，至少要`/`开头

+ 代理不能重写abs_path



### 请求资源

资源定位根据请求行来执行



如果是绝对定位就会屏蔽Host字段，如果是相对定位就会参考Host字段，如果缺少Host就会报错400

**请求头**

无法识别的请求头会被认作是entity header

```http
      request-header = Accept                   ; Section 14.1
                      | Accept-Charset           ; Section 14.2
                      | Accept-Encoding          ; Section 14.3
                      | Accept-Language          ; Section 14.4
                      | Authorization            ; Section 14.8
                      | Expect                   ; Section 14.20
                      | From                     ; Section 14.22
                      | Host                     ; Section 14.23
                      | If-Match                 ; Section 14.24
                      | If-Modified-Since        ; Section 14.25
                      | If-None-Match            ; Section 14.26
                      | If-Range                 ; Section 14.27
                      | If-Unmodified-Since      ; Section 14.28
                      | Max-Forwards             ; Section 14.31
                      | Proxy-Authorization      ; Section 14.34
                      | Range                    ; Section 14.35
                      | Referer                  ; Section 14.36
                      | TE                       ; Section 14.39
                      | User-Agent               ; Section 14.43
```



## 6.Response

```http
      Response      = Status-Line               ; Section 6.1
                       *(( general-header        ; Section 4.5
                        | response-header        ; Section 6.2
                        | entity-header ) CRLF)  ; Section 7.1
                       CRLF
                       [ message-body ]          ; Section 7.2
```



### 状态栏

> ​     Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF 

Reason-Phrase就是响应码



**响应头**

无法识别的响应头会被认作是entity-header

```http
     response-header = Accept-Ranges           ; Section 14.5
                       | Age                     ; Section 14.6
                       | ETag                    ; Section 14.19
                       | Location                ; Section 14.30
                       | Proxy-Authenticate      ; Section 14.33
                       | Retry-After             ; Section 14.37
                       | Server                  ; Section 14.38
                       | Vary                    ; Section 14.44
                       | WWW-Authenticate        ; Section 14.47

```



## 7.Entity

+ 由HTTP请求或响应发送的实体主体（如果存在的话）的格式与编码方式应由实体的头域决定

+ 实体主题（entity-body)只有当消息主体存在时候才存在。实体主题（entity-body）从消息主题通过传输译码头域（Transfer-Encoding）译码得到，传输译码用于确保消息的安全和合适的传输



entity header

```http
     entity-header  = Allow                    ; Section 14.7
                      | Content-Encoding         ; Section 14.11
                      | Content-Language         ; Section 14.12
                      | Content-Length           ; Section 14.13
                      | Content-Location         ; Section 14.14
                      | Content-MD5              ; Section 14.15
                      | Content-Range            ; Section 14.16
                      | Content-Type             ; Section 14.17
                      | Expires                  ; Section 14.21
                      | Last-Modified            ; Section 14.29
                      | extension-header

       extension-header = message-header

```



**Entity Length**

消息的实体主题查藏毒指的是消息主题在被应用于传输编码（transfer-coding）之前的长度



### type

任何包含entity body的请求都应该被设置content-type，如果没有设置，接收方需要通过检查media-type来猜测媒体类型url的内容和名称扩展名，用于标识资源；如果媒体类型仍然未知，收件人应该将content-type视为`application/octet-stream`



## 8.连接

### 持久连接

持久连接的优势

+ 减少打开和关闭tcp连接的次数，在路由器和主机上减少cpu时间
+ pipline式传输请求和响应，可以执行多个请求而不需要等待响应
+ 因为tcp连接后减少了包传输的数量，导致网络拥塞减少，并且允许tcp由足够时间来确定网络的拥塞状态
+ 因为tcp握手减少，导致后续请求的延迟减少了
+ 因为可以不用关闭tcp连接而报告错误，http可以更好的发展
+ 持久连接提供了一种机制可以使得客户段和服务端之间可以发送关闭连接的信号，这个信号通过连接头字段产生



### 协商

+ connection头域包含close标签的请求是持久连接的最后一个请求
+ 为了保持连接的持久性，连接上所有消息否必须具有自定义的消息长度



### 流水线

+ 希望在连接建立后之后立即进行持久连接和流水线传输的客户端必须做好在第一次尝试进行流水线传输失败的重试准备
+ 客户端在重试的时候必须确保在知道连接时持久连接后才进行流水线操作
+ 同时客户端也要做好服务端在传输数据之前关闭连接时进行重传的准备



+ 客户端不能流水线传输非幂等的方法和请求序列
+ 希望发送非幂等请求的客户端必须等待发送请求，接收到前一个请求的响应状态之后才行



### 代理服务器

代理服务器必须像客户端和服务端都发送建立持久连接的信号

代理服务器不能向http1.0的客户端建立http1.1.的持久连接



### 实际问题

客户端、服务端、和代理服务器都可以随时关闭连接，这意味着他们需要有能从异步关闭事件中恢复事件的能力

+ 只要请求序列是幂等的，客户端软件就应该重新打开传输连接，并在没有用户交互的情况下重新传输中止的请求序列
+ 非幂等的请求序列不能自动重试，但是用户可以通过操作选择进行重试
+ 如果第二个请求序列失败，则不应该重复自动重试

用持久连接的客户端应该限制与某一服务器同时连接的个数

+ 客户端和服务器之间的连接数应该不超过两个
+ 代理服务器应该使用2*N个连接到其他服务器。N是在线用户数



### Message传输要求

#### **持久连接和流量控制**

HTTP/1.1服务器应该保持持久连接并使用TCP流量控制机制来解决临时过载，而不是在终止连接后指望客户端的重试。后一方会恶化网络阻塞

#### **监视连接中出错状态的消息**

客户端需要监视连接中是否存在错误状态，如果存在，应该立即终止传输body，如果是chuked传输，需要用长度为0或者空尾部提前标记结束报文传输；如果是content-length，就要终止连接

#### **100状态码用途**

目的是在客户端发送请求之前，让客户端可以判断服务器是否愿意接收客户端发来的消息主机

**客户端要求**

+ 如果客户端期待收到100状态响应，它必须在请求头中添加`100-continue`
+ 如果客户端不打算发送请求body，请求头就不能携带`100-continue`

**服务端要求**

+ 如果请求头中包含`100-continue`，服务端必须回复100状态码并继续从输入流中读取，或者发送final状态码，在此之前，不能发送请求体。发送final状态码不能再执行请求的方法（如POST、PUT），可能关闭连接也可能继续接收剩余请求
+ 如果请求头中不包含`100-continue`，源服务器就不应该发送100状态码，或者请求来自更老版本的客户端时。只有一种例外情况：*为了与RFC 2068兼容，源服务器可能会发送一个100（继续）状态响应以响应HTTP/1.1的PUT或POST请求，虽然这些请求中没有包含值为`100-continue`的Expect请求头域。*这个例外的目的是为了减少任何客户端因为等待100状态响应的延时，但此例外只能应用于HTTP/1.1请求
+ 如果服务器已经收到全部或者部分请求的body，可以不发送100状态码
+ 一旦请求体body被接收或者处理，除非连接被提前中断，否则发送100状态码的服务器必须发送最终状态码
+ 倘若源服务器接收到不含值为`100-continue`的Expec请求头域的请求，同时服务端返回了最终状态码但还没有完全接收数据，此时服务端不能关闭连接而是应该继续接收整个请求直到客户端关闭连接。否则客户端不会信任接收到的响应信息



代理服务器要求：

+ 代理服务器接收到包含`100-continue`头部的请求，除非明确直到下一个服务器的协议版本低于1.1（此时应该响应417）；否则转发整个请求、
+ 代理服务器应该维护一个缓存，来记录下一个服务器的版本号
+ 如果接收到的请求来自低于1.1版本的客户端，同时该请求不包含`100-continue`，那么代理服务器不能转发100响应。这个要求可以覆盖100响应转发的一般规则



### 如果服务器提前关闭连接客户端行为

+ 如果客户端发送的请求不包含`100-continue`，并且直接和源服务器连接，这是如果客户端在接收响应之前看到了连接关闭，那应该重试请求
+ 重试时，通过一种算法（二进制指数退避算法）来保证客户端得到可靠响应







## 9.方法

方法可以被扩展但是不能造成歧义的语义

**安全方法**

GET和HEAD只能获取资源不能采取行动，这些方法必须是安全的。

 **幂等方法**

GET、HEAD、PUT和DELETE拥有幂等性因为多个请求和单个请求的效果一样。

OPTIONS和TRACE不应该有副作用，内在也是幂等的

但是一个序列不一定是幂等的（尽管一个序列中的多个方法都是幂等的）只要一个序列没有副作用，它就是幂等的

### OPTIONS

此方法允许客户端去判定请求资源的选项而不需要利用一个资源动作（PUT、POST、DELETE）或资源请求（GET、HEAD）

+ 对这个方法的响应是不可缓存的
+ 如果请求包含entity-body（即请求消息中存在content-Lengt或者Transfer-Encoding），媒体类型必须通过content-type指明，不然这个消息主体会被遗弃
+ 如果请求URI是一个星号（"\*"）,，OPTIONS请求将会应用于服务器的所有资源而不是特定资源。如果请求URI不是一个星号（"\*"）,，OPTIONS请求只能应用于请求URI指定资源的选项。
+ 对该方法的200响应需要包含任何指明选型性质的头域，这些选项性质由服务器实现并且只适合那个请求的资源（例如Allow），但也可能包一些扩展的在此规范里没有定义的头域。如果有响应主体也需要包含一些通信选选项，如果过没有那么content-length = 0
+ Max-Forwards请求头域可能会被用于针对请求链中特定的代理.收到OPTION后必须检查Max-Forwards头域（同时请求必须是可转发的）



### GET

+ 包含If系列的头域时候，语义变成了可选择的请求资源
+ 包含Range的头域时候，语义变成了部分请求资源
+ GET的响应式可缓存的



### HEAD

+ 服务器不能返回响应体，用来获取请求entity元信息而不传输entity body
+ 响应可缓存



### POST

+ 可缓存



### PUT

+ 修改版本，post是创建
+ PUT响应不应该被缓存
+ POST和PUT的请求含义不同，POST请求里的URI指示一个能处理请求实体的资源，而PUT方法请求里有一个实体一一用户代理知道URI意指什么，并且服务器不能把此请求应用于其他URI指定的资源。

> 网关和代理服务器的区别是：网关可以进行协议转换，而代理服务器不能，只是起代理的作用



### DELETE

此方法可能会在源服务器上被人为的干涉。客户端不能保证此操作能被执行，即使源服务器返回成功状态码。

如果响应里包含描述成功的实体，响应应该是200（Ok）；如果DELETE动作没有通过，应该以202（已接受）响应；如果DELETE方法请求已经通过了，但响应不包含实体，那么应该以204（无内容）响应。

响应不能被缓存



### TRACE

TRACE方法让客户端测试到服务器的网络通路，回路的意思如发送一个请返回一个响应，这就是一个请求响应回路

TRACE方法允许客户端知道请求链的另一端接收什么，并且利用那些数据去测试或诊断。

+ Via头域值（见14.45）有特殊的用途，因为它可以作为请求链的跟踪信息。
+ 利用Max-Forwards头域允许客户端限制请求链的长度去测试一串代理服务器是否在无限回路里转发消息。

如果请求是有效的，响应应该在响应实体主体里包含整个请求消息，并且响应应该包含一个Content-Type头域值为”message/http”的头域。TRACE方法的响应不能不缓存。

### CONNECT

此方法是为了能用于能动态切换到隧道的代理服务器



## 10.状态码



## 12.内容协商

因为有些用户不喜欢最容易得到的实体，所以提供不同的协商机制



有两种类型的内容协商在HTTP中：**服务器驱动协商**和**代理驱动协商**。这两种类型的协商具有正交性并且能被单独使用或联合使用。一个联合使用方法的协商会被叫做透明协商，当缓存利用代理驱动协商的信息的时候，此代理驱动协商的信息被为后续请求提供服务器驱动协商的源服务器提供。





### 服务器驱动协商

HTTP/1.1包含下面的请求头域来使服务器驱动协商启动，这些请求头域描述了用户代理的能力和用户喜好：`Accept`（14.1节），`Accept-Charset`（14.2节），`Accept-Encoding`（14.3节），`Accept-Language`（14.4节），和`User-Agent`（14.43节）。然而，一些源服务器并不只局限于这些维度，这些服务器能基于请求的任何方面来让响应不同，这些方面包括请求头域之外的信息或在此规范里没有定义的扩展头域。

·优点：协商更快，

缺点: 首部集不匹配，服务器要做猜测；同时可能限制共有缓存为多个客户端请求利用相同响应的能力

### 代理驱动协商

可以理解是客户端驱动，客户端发起请求，服务器发送可选项列表，客户端做出选择后发起第二次请求

优点：实现简单，可以使用共有缓存减少承载和网络使用的负担

缺点：需要两次请求

### 透明协商

当一个缓存被提供了构成响应的一系列可得的表现形式（就像在代理驱动协商里的响应一样）并且维度的差异能完全被缓存理解，那么此缓存变得有能力代表源服务器为那个资源的后续请求去执行服务器驱动协商（某个中间设备（通常是缓存代理）代表客户端进行协商）

优点：免除web服务器的协商开销

缺点：目前没有定义这样的机制



## 缓存

HTTP/1.1中缓存的目的是为了在很多情况下减少发送请求，同时在许多情况下可以不需要发送完整响应。

+ 前者减少网络回路的数量，通过**过期**机制（**expiration**）实现
+ 后者减少了网络应用的带宽，使用**验证**机制（**validation**）实现





### 过期模型

源服务器利用Expires头域或在Cache-Control头域里通过max-age缓存控制指令，来指定显示过期时间

**启发式过期**

**年龄计算**

**过期计算**

### 验证模型







### 博客解析

+ 强制缓存

使用`Expires`(基本不用，已经过时)和`Cache-Control`



+ 对比缓存

浏览器第一次请求数据时，服务器会将缓存标识与数据一起返回给客户端，客户端将二者备份至缓存数据库中。
再次请求数据时，客户端将备份的缓存标识发送给服务器，服务器根据缓存标识进行判断，判断成功后，返回304状态码，通知客户端比较成功，可以使用缓存数据。

请求header：

**Last-Modified / If-Modified-Since**

**Etag / If-None-Match**（优先级高于Last-Modified / If-Modified-Since）

ETag: 服务器响应请求时，告诉浏览器当前资源在服务器的唯一标识



## 头域定义



## 安全



 







