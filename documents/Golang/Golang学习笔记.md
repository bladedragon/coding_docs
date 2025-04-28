# Golang学习笔记

[idea安装go开发环境 并 搭建go项目 打包，这里显示了go项目的开发目录](https://www.cnblogs.com/jpfss/p/11760012.html)



**优点**

+ 高并发

  可以开进程、线程和协程。go是协程型语言

  可以自动分发请求，尽量占满CPU内存

+ 运行速度

+ 内存占用

+ Docker亲和度

+ 跨平台

+ 类型安全和内存安全

  存在指针，但不允许指针运算

+ 



## 安装



重要的环境变量

- **$GOROOT** 表示 Go 在你的电脑上的安装位置，它的值一般都是 `$HOME/go`，当然，你也可以安装在别的地方。
- **$GOARCH** 表示目标机器的处理器架构，它的值可以是 386、amd64 或 arm。
- **$GOOS** 表示目标机器的操作系统，它的值可以是 darwin、freebsd、linux 或 windows。
- **$GOBIN** 表示编译器和链接器的安装位置，默认是 `$GOROOT/bin`，如果你使用的是 Go 1.0.3 及以后的版本，一般情况下你可以将它的值设置为空，Go 将会使用前面提到的默认值。



## module部署

因为国内的网络环境，go mod tidy 获取依赖的 go module 要么经常失败，要么龟速。可以通过设置 go module 代理解决这个问题：https://goproxy.io/

**通过环境变量设置**：

```
export GOPROXY=https://goproxy.io
```

**在 Intelli Idea 中设置**：

Intelli Idea -> Preferences -> Languages & Frameworks -> Go -> Go Modules(vgo)，在 Proxy 中输入 https://goproxy.io：

### ==**代理路径[重要]**==

> https://goproxy.cn,direct
>
> [https://mirrors.aliyun.com/goproxy/](https://links.jianshu.com/go?to=https%3A%2F%2Fmirrors.aliyun.com%2Fgoproxy%2F)

[Go依赖模块版本之Module避坑使用详解](https://www.cnblogs.com/sunsky303/p/10710637.html)

## 运行期

垃圾回收器：标记-清除回收器



## 基本结构

### 基本标识符

下面列举了 Go 代码中会使用到的 25 个关键字或保留字：

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |



### 包的概念

包是结构化代码的一种方式：每个程序都由包（通常简称为 pkg）的概念组成，可以使用自身的包或者从其它包中导入内容。

在 Windows 下，标准库的位置在 Go 根目录下的子目录 `pkg\windows_386` 中；在 Linux 下，标准库在 Go 根目录下的子目录 `pkg\linux_amd64` 中（如果是安装的是 32 位，则在 `linux_386` 目录中）。一般情况下，标准包会存放在 `$GOROOT/pkg/$GOOS_$GOARCH/` 目录下。



### 函数

+ **main函数没有参数**



样例

```go
func functionName(parameter_list) (return_value_list) {
   …
}
```



Go 程序的执行（程序启动）顺序如下：

1. 按顺序导入所有被 main 包引用的其它包，然后在每个包中执行如下流程：
2. 如果该包又导入了其它的包，则从第一步开始递归执行，但是每个包只会被导入一次。
3. 然后以相反的顺序在每个包中初始化常量和变量，如果该包含有 init 函数的话，则调用该函数。
4. 
5. 指针（第 4.9 节）属于引用类型，其它的引用类型还包括 slices（第 7 章），maps（第 8 章）和 channel（第 13 章）。被引用的变量会存储在堆中，以便进行垃圾回收，且比栈拥有更大的内存空间。在完成这一切之后，main 也执行同样的过程，最后调用 main 函数开始执行程序。

### 变量

`var a`主要用于声明包级别的全局变量，



当你在函数体内声明局部变量时，应使用简短声明语法 `:=`

这是使用变量的首选形式，但是它只能被用在函数体内，而不可以用于全局变量的声明与赋值。使用操作符 `:=` 可以高效地创建一个新的变量，称之为初始化声明。



### 值类型和引用类型

值类型

+  int、float、bool 和 string等基本类型
+ 数组和结构

值拷贝

+ 将内存中的值拷贝到另一个值上
+ 值类型的变量值存储在栈上

引用类型

+ 指针、slices、maps、channel等

引用拷贝

+ 传递引用地址
+ 引用类型存储在堆中，以便进行垃圾回收，且比栈拥有更大的内存空间。



### 基本类型

+ `bool`
+ ``int``
  + `uint`
  + `int`
  + `uintptr` 一个指针
+ `float`(并没有)
  + `float32`
  + `float64`
+ complex复数
  + complex64
  + complex128
+ 字符类型
  + byte



### 指针

+ 你不能获取字面量或常量的地址

+  `c = *p++` 在 Go 语言的代码中是不合法的。容易导致内存泄漏

+ 你可以传递一个变量的引用（如函数的参数），这样不会传递变量的拷贝

+ 对一个空指针的反向引用是不合法的，并且会使程序崩溃：

  ```go
  package main
  func main() {
  	var p *int = nil
  	*p = 0
  }
  // in Windows: stops only with: <exit code="-1073741819" msg="process crashed"/>
  // runtime error: invalid memory address or nil pointer dereference
  ```

  





## 控制结构

### if-else

使用简短方式 `:=` 声明的变量的作用域只存在于 if 结构中（在 if 结构的大括号之间，如果使用 if-else 结构则在 else 代码块中变量也会存在）。如果变量在 if 结构之前就已经存在，那么在 if 结构中，该变量原来的值会被隐藏

```
if val := 10; val > max {
	// do something
}
```



### 异常返回

注意返回值一定要被接受

```go
if err := file.Chmod(0664); err != nil {
	fmt.Println(err)
	return err
}
```



### switch

```go
switch i {
	case 0: fallthrough
	case 1:
		f() // 当 i == 0 时函数也会被调用
}
```





### for

```go
package main

import "fmt"

func main() {
	var i int = 5

	for i >= 0 {
		i = i - 1
		fmt.Printf("The variable i is now: %d\n", i)
	}
}
```

无限循环



```go
for t, err = p.Token(); err == nil; t, err = p.Token() {
	...
}
```



### for-range

```go
package main

import "fmt"

func main() {
	str := "Go is a beautiful language!"
	fmt.Printf("The length of str is: %d\n", len(str))
	for pos, char := range str {
		fmt.Printf("Character on position %d is: %c \n", pos, char)
	}
	fmt.Println()
	str2 := "Chinese: 日本語"
	fmt.Printf("The length of str2 is: %d\n", len(str2))
	for pos, char := range str2 {
    	fmt.Printf("character %c starts at byte position %d\n", char, pos)
	}
	fmt.Println()
	fmt.Println("index int(rune) rune    char bytes")
	for index, rune := range str2 {
    	fmt.Printf("%-2d      %d      %U '%c' % X\n", index, rune, rune, rune, []byte(string(rune)))
	}
```



### 标签

## 函数

三种类型函数

+ 普通带有名字的函数
+ 匿名函数lamda函数
+ 方法



### 变长函数

如果参数被存储在一个 slice 类型的变量 `slice` 中，则可以通过 `slice...` 的形式来传递参数，调用变参函数。

```go
package main

import "fmt"

//格式
func myFunc(a, b, arg ...int) {}

func main() {
	x := min(1, 3, 2, 0)
	fmt.Printf("The minimum is: %d\n", x)
	slice := []int{7,9,3,5,1}
	x = min(slice...)
	fmt.Printf("The minimum in the slice is: %d", x)
}

func min(s ...int) int {
	if len(s)==0 {
		return 0
	}
	min := s[0]
	for _, v := range s {
		if v < min {
			min = v
		}
	}
	return min
}
```



不使用变长参数的例子

+ 使用结构

+ 使用空接口

  ```go
  func typecheck(..,..,values … interface{}) {
  	for _, value := range values {
  		switch v := value.(type) {
  			case int: …
  			case float: …
  			case string: …
  			case bool: …
  			default: …
  		}
  	}
  }
  ```



### defer

关键字 `defer` 允许我们推迟到函数返回之前（或任意位置执行 `return` 语句之后）一刻才执行某个语句或函数（为什么要在返回之后才执行这些语句？因为 `return` 语句同样可以包含一些操作，而不是单纯地返回某个值）

当有多个 defer 行为被注册时，它们会以逆序执行（类似栈，即后进先出）：

```go
func f() {
	for i := 0; i < 5; i++ {
		defer fmt.Printf("%d ", i)
	}
}
```



### 内置函数

| 名称               | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| close              | 用于管道通信                                                 |
| len、cap           | len 用于返回某个类型的长度或数量（字符串、数组、切片、map 和管道）；cap 是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map） |
| new、make          | new 和 make 均是用于分配内存：new 用于值类型和用户定义的类型，如自定义结构，make 用于内置引用类型（切片、map 和管道）。它们的用法就像是函数，但是将类型作为参数：new(type)、make(type)。new(T) 分配类型 T 的零值并返回其地址，也就是指向类型 T 的指针（详见第 10.1 节）。它也可以被用于基本类型：`v := new(int)`。make(T) 返回类型 T 的初始化之后的值，因此它比 new 进行更多的工作（详见第 7.2.3/4 节、第 8.1.1 节和第 14.2.1 节）**new() 是一个函数，不要忘记它的括号** |
| copy、append       | 用于复制和连接切片                                           |
| panic、recover     | 两者均用于错误处理机制                                       |
| print、println     | 底层打印函数（详见第 4.2 节），在部署环境中建议使用 fmt 包   |
| complex、real imag | 用于创建和操作复数（详见第 4.5.2.2 节）                      |



### 递归函数

因为存在栈溢出的问题，所以可以通过**懒惰求值**解决（channel和协程）



### 闭包





## 数组和切片

+ 数组是一种值类型，不是指向首元素的指针，所以拷贝是深拷贝
+ 修改原数组的方法
  + 传入原数组的指针
  + 生成数组切片并传递各给函数



**数组常量**

```go
var arrAge = [5]int{18, 20, 15, 22, 16}
```

```go
var arrLazy = [...]int{5, 6, 7, 8, 22}
```

```go
var arrKeyValue = [5]string{3: "Chris", 4: "Ron"}
```



**传递数组**

把一个大数组传递给函数会消耗很多内存。有两种方法可以避免这种现象：

- 传递数组的指针
- 使用数组的切片



### 切片

`var slice1 []type = arr1[start:end]`

+ 切片是连续片段的引用，是一个引用类型，可索引

+ 切片在内存中的组织方式实际上是一个有 3 个域的结构体：指向相关数组的指针，切片长度以及切片容量。下图给出了一个长度为 2，容量为 4 的切片y。

  + `y[0] = 3` 且 `y[1] = 5`。
  + 切片 `y[0:4]` 由 元素 3，5，7 和 11 组成。

+ 看起来二者没有什么区别，都在堆上分配内存，但是它们的行为不同，适用于不同的类型。

  - new(T) 为每个新的类型T分配一片内存，初始化为 0 并且返回类型为*T的内存地址：这种方法 **返回一个指向类型为 T，值为 0 的地址的指针**，它适用于值类型如数组和结构体；它相当于 `&T{}`。
  - make(T) **返回一个类型为 T 的初始值**，它只适用于3种内建的引用类型：切片、map 和 channel。

  换言之，new 函数分配内存，make 函数初始化



*如何理解new、make、slice、map、channel的关系*

*1.slice、map以及channel都是golang内建的一种引用类型，三者在内存中存在多个组成部分， 需要对内存组成部分初始化后才能使用，而make就是对三者进行初始化的一种操作方式*

*2. new 获取的是存储指定变量内存地址的一个变量，对于变量内部结构并不会执行相应的初始化操作， 所以slice、map、channel需要make进行初始化并获取对应的内存地址，而非new简单的获取内存地址*



相应方法

+ 切片操作

  + `apend`

    1. 将切片 b 的元素追加到切片 a 之后：`a = append(a, b...)`

    2. 复制切片 a 的元素到新的切片 b 上：

       ```
       b = make([]T, len(a))
       copy(b, a)
       ```

    3. 删除位于索引 i 的元素：`a = append(a[:i], a[i+1:]...)`

    4. 切除切片 a 中从索引 i 至 j 位置的元素：`a = append(a[:i], a[j:]...)`

    5. 为切片 a 扩展 j 个元素长度：`a = append(a, make([]T, j)...)`

    6. 在索引 i 的位置插入元素 x：`a = append(a[:i], append([]T{x}, a[i:]...)...)`

    7. 在索引 i 的位置插入长度为 j 的新切片：`a = append(a[:i], append(make([]T, j), a[i:]...)...)`

    8. 在索引 i 的位置插入切片 b 的所有元素：`a = append(a[:i], append(b, a[i:]...)...)`

    9. 取出位于切片 a 最末尾的元素 x：`x, a = a[len(a)-1], a[:len(a)-1]`

    10. 将元素 x 追加到切片 a：`a = append(a, x)`

  + `copy`

+ 搜索和排序切片

  + `sort.Ints(arri)`/`Floats()`

  

### 字符串和切片的内存结构

在内存中，一个字符串实际上是一个双字结构，即一个指向实际数据的指针和记录字符串长度的整数。因为指针对用户来说是完全不可见，因此我们可以依旧把字符串看做是一个值类型，也就是一个字符数组。	



### 切片的垃圾回收

切片的底层指向一个数组，该数组的实际容量可能要大于切片所定义的容量。只有在没有任何切片指向的时候，底层的数组内存才会被释放，这种特性有时会导致程序占用多余的内存

**所以使用切片最好能复制一份引用**



### rune

>int32的别名，几乎在所有方面等同于int32
>它用来区分字符值和整数值

> golang中string底层是通过byte数组实现的。中文字符在unicode下占2个字节，在utf-8编码下占3个字节，而golang默认编码正好是utf-8。



计算长度

+ `len()` :计算字节长度
+ `RuneCouintInString()` ： 计算元素长度
+ `rune() `： 计算元素长度

```go
func main() {

    var str = "hello 你好"

    //golang中string底层是通过byte数组实现的，座椅直接求len 实际是在按字节长度计算  所以一个汉字占3个字节算了3个长度
    fmt.Println("len(str):", len(str))
    
    //以下两种都可以得到str的字符串长度
    
    //golang中的unicode/utf8包提供了用utf-8获取长度的方法
    fmt.Println("RuneCountInString:", utf8.RuneCountInString(str))

    //通过rune类型处理unicode字符
    fmt.Println("rune:", len([]rune(str)))
}
```

- byte 等同于uint8，常用来处理ascii字符
- rune 等同于int32,常用来处理unicode或utf-8字符

## Map

map 是引用类型，内存由make分配

在声明的时候不需要知道 map 的长度，map 是可以动态增长的。

未初始化的 map 的值是 nil。

可以使用如下声明：

```go
var map1 map[keytype]valuetype
var map1 map[string]int
```



**不要使用 new，永远用 make 来构造 map**

**注意** 如果你错误的使用 new() 分配了一个引用对象，你会获得一个空引用的指针，相当于声明了一个未初始化的变量并且取了它的地址：

```go
mapCreated := new(map[string]float32)
```



**map容量**

```go
map2 := make(map[string]float32, 100)
```

当 map 增长到容量上限的时候，如果再增加新的 key-value 对，map 的大小会自动加 1。所以出于性能的考虑，对于大的 map 或者会快速扩张的 map，即使只是大概知道容量，也最好先标明。



**切片作为**map的值

```go
mp1 := make(map[int][]int)
mp2 := make(map[int]*[]int)
```



**for-range配套用法**

```go
for key, value := range map1 {
	...
}
```



**排序**

使用切片排序





## Package



**导入外部安装包:**

如果你要在你的应用中使用一个或多个外部包，首先你必须使用 `go install`（参见第 9.7 节）在你的本地机器上安装它们。

假设你想使用 `http://codesite.ext/author/goExample/goex` 这种托管在 Google Code、GitHub 和 Launchpad 等代码网站上的包。

你可以通过如下命令安装：

```shell
go install codesite.ext/author/goExample/goex
```

将一个名为 `codesite.ext/author/goExample/goex` 的 map 安装在 `$GOROOT/src/` 目录下。

通过以下方式，一次性安装，并导入到你的代码中：

```
import goex "codesite.ext/author/goExample/goex"
```

因此该包的 URL 将用作导入路径。

在 `http://golang.org/cmd/goinstall/` 的 `go install` 文档中列出了一些广泛被使用的托管在网络代码仓库的包的导入路径



**包的初始化:**

程序的执行开始于导入包，初始化 main 包然后调用 main 函数。

一个没有导入的包将通过分配初始值给所有的包级变量和调用源码中定义的包级 init 函数来初始化。一个包可能有多个 init 函数甚至在一个源码文件中。它们的执行是无序的。这是最好的例子来测定包的值是否只依赖于相同包下的其他值或者函数。

init 函数是不能被调用的。

导入的包在包自身初始化前被初始化，而一个包在程序执行中只能初始化一次。

​	

### 自定义包的目录结构

```go
/home/user/goprograms
	ucmain.go	(uc包主程序)
	Makefile (ucmain的makefile)
	ucmain
	src/uc	 (包含uc包的go源码)
		uc.go
	 	uc_test.go
	 	Makefile (包的makefile)
	 	uc.a
	 	_obj
			uc.a
		_test
			uc.a
	bin		(包含最终的执行文件)
		ucmain
	pkg/linux_amd64
		uc.a	(包的目标文件)
```





## 结构

**使用 new**

使用 **new** 函数给一个新的结构体变量分配内存，它返回指向已分配内存的指针：`var t *T = new(T)`，如果需要可以把这条语句放在不同的行（比如定义是包范围的，但是分配却没有必要在开始就做）。

```
var t *T
t = new(T)
```

写这条语句的惯用方法是：`t := new(T)`，变量 `t` 是一个指向 `T`的指针，此时结构体字段的值是它们所属类型的零值。

声明 `var t T` 也会给 `t` 分配内存，并零值化内存，但是这个时候 `t` 是类型T。在这两种方式中，`t` 通常被称做类型 T 的一个实例（instance）或对象（object）。





Go 语言中，==结构体和它所包含的数据在内存中是以连续块的形式存在的==，即使结构体中嵌套有其他的结构体，这在性能上带来了很大的优势。不像 Java 中的引用类型，一个对象和它里面包含的对象可能会在不同的内存空间中，这点和 Go 语言中的指针很像



### 强制使用工厂方法

```go
type matrix struct {
    ...
}

func NewMatrix(params) *matrix{
    m := new(matrix)
    return m
}
```





map使用make分配内存，但是不能使用new

struct使用new分配内存，但是不能使用make



### 方法

类型和作用在它上面定义的方法必须在同一个包里定义，这就是为什么不能在 int、float 或类似这些的类型上定义方法。试图在 int 类型上定义方法会得到一个编译错误：

```go
cannot define new methods on non-local type int
```



#### 函数和方法的区别

>函数将变量作为参数：**Function1(recv)**
>
>方法在变量上被调用：**recv.Method1()**

在接收者是指针时，方法可以改变接收者的值（或状态），这点函数也可以做到（当参数作为指针传递，即通过引用调用时，函数也可以改变参数的状态）。

如果想要方法改变接收者的数据，就在接收者的指针类型上定义该方法。否则，就在普通的值类型上定义方法。



使用类中的字段

```go
package person

type Person struct {
	firstName string
	lastName  string
}

func (p *Person) GetFirstName() string {
	return p.firstName
}

func (p *Person) SetFirstName(newName string) {
	p.firstName = newName
}
```



### 继承

因为一个结构体可以嵌入多个匿名类型，所以实际上我们可以有一个简单版本的多重继承，就像：`type Child struct { Father; Mother}`。在第 10.6.7 节中会进一步讨论这个问题。

结构体内嵌和自己在同一个包中的结构体时，可以彼此访问对方所有的字段和方法。



#### 内嵌和聚合

A：聚合（或组合）：包含一个所需功能类型的具名字段。

B：内嵌：内嵌（匿名地）所需功能类型，像前一节 10.6.5 所演示的那样。

A:

```go
package main

import (
	"fmt"
)

type Log struct {
	msg string
}

type Customer struct {
	Name string
	log  *Log
}

func main() {
	c := new(Customer)
	c.Name = "Barak Obama"
	c.log = new(Log)
	c.log.msg = "1 - Yes we can!"
	// shorter
	c = &Customer{"Barak Obama", &Log{"1 - Yes we can!"}}
	// fmt.Println(c) &{Barak Obama 1 - Yes we can!}
	c.Log().Add("2 - After me the world will be a better place!")
	//fmt.Println(c.log)
	fmt.Println(c.Log())

}

func (l *Log) Add(s string) {
	l.msg += "\n" + s
}

func (l *Log) String() string {
	return l.msg
}

func (c *Customer) Log() *Log {
	return c.log
}
```

B:

```go
package main

import (
	"fmt"
)

type Log struct {
	msg string
}

type Customer struct {
	Name string
	Log
}

func main() {
	c := &Customer{"Barak Obama", Log{"1 - Yes we can!"}}
	c.Add("2 - After me the world will be a better place!")
	fmt.Println(c)

}

func (l *Log) Add(s string) {
	l.msg += "\n" + s
}

func (l *Log) String() string {
	return l.msg
}

func (c *Customer) String() string {
	return c.Name + "\nLog:" + fmt.Sprintln(c.Log)
}
```



### 多重继承







### 比较

在如 C++、Java、C# 和 Ruby 这样的面向对象语言中，方法在类的上下文中被定义和继承：在一个对象上调用方法时，运行时会检测类以及它的超类中是否有此方法的定义，如果没有会导致异常发生。

在 Go 语言中，这样的继承层次是完全没必要的：如果方法在此类型定义了，就可以调用它，和其他类型上是否存在这个方法没有关系。在这个意义上，Go 具有更大的灵活性。

**总结**

在 Go 中，类型就是类（数据和关联的方法）。Go 不知道类似面向对象语言的类继承的概念。继承有两个好处：代码复用和多态。

在 Go 中，代码复用通过组合和委托实现，多态通过接口的使用来实现：有时这也叫 **组件编程（Component Programming）**。

许多开发者说相比于类继承，Go 的接口提供了更强大、却更简单的多态行为。



### 垃圾回收

Go 开发者不需要写代码来释放程序中不再使用的变量和结构占用的内存，在 Go 运行时中有一个独立的进程，即垃圾收集器（GC），会处理这些事情，它搜索不再使用的变量然后释放它们的内存。可以通过 `runtime` 包访问 GC 进程。

通过调用 `runtime.GC()` 函数可以显式的触发 GC，但这只在某些罕见的场景下才有用



查找内存状态

```go
// fmt.Printf("%d\n", runtime.MemStats.Alloc/1024)
// 此处代码在 Go 1.5.1下不再有效，更正为
var m runtime.MemStats
runtime.ReadMemStats(&m)
fmt.Printf("%d Kb\n", m.Alloc / 1024)
```



## 接口和反射

### 接口

Go 语言中的接口都很简短，通常它们会包含 0 个、最多 3 个方法。

不像大多数面向对象编程语言，在 Go 语言中接口可以有值，一个接口类型的变量或一个 **接口值** ：`var ai Namer`，`ai` 是一个多字（multiword）数据结构，它的值是 `nil`。它本质上是一个指针，虽然不完全是一回事。指向接口值的指针是非法的，它们不仅一点用也没有，还会导致代码错误。

此处的方法指针表是通过运行时反射能力构建的。



**类型不需要显式声明它实现了某个接口：接口被隐式地实现。多个类型可以实现同一个接口**。

​		。

**一个类型可以实现多个接口**。

**接口类型可以包含一个实例的引用， 该实例的类型实现了此接口（接口是动态类型）**。



> 接口变量里包含了接收者实例的值和指向对应方法表的指针。
>
> 想要实现接口注意方法的接收者是指针还是实例



### 接口断言

> 注意是指针类型还是实例类型

标准模板

```go
if v, ok := varI.(T); ok {  // checked type assertion
    Process(v)
    return
}
// varI is not of type T
```

或者

> 注意可以用 `type-switch` 进行运行时类型分析，但是在 `type-switch` 不允许有 `fallthrough` 。

```go
switch t := areaIntf.(type) {
case *Square:
	fmt.Printf("Type Square %T with value %v\n", t, t)
case *Circle:
	fmt.Printf("Type Circle %T with value %v\n", t, t)
case nil:
	fmt.Printf("nil value: nothing to check?\n")
default:
	fmt.Printf("Unexpected type %T\n", t)
}
```



### 方法集和接口

作用于变量上的方法实际上是不区分变量到底是指针还是值的。当碰到接口类型值时，这会变得有点复杂，原因是接口变量中存储的具体值是不可寻址的

**summary**

在接口上调用方法时，必须有和方法定义时相同的接收者类型或者是可以从具体类型 `P` 直接可以辨识的：

- 指针方法可以通过指针调用
- 值方法可以通过值调用
- 接收者是值的方法可以通过指针调用，因为指针会首先被解引用
- 接收者是指针的方法不可以通过值调用，因为存储在接口中的值没有地址

将一个值赋值给一个接口时，编译器会确保所有可能的接口方法都可以在此值上被调用，因此不正确的赋值在编译期就会失败

Go 语言规范定义了接口方法集的调用规则：

- 类型 *T 的可调用方法集包含接受者为 *T 或 T 的所有方法集
- 类型 T 的可调用方法集包含接受者为 T 的所有方法
- 类型 T 的可调用方法集不包含接受者为 *T 的方法



### 动态类型

> Go 没有类：数据（结构体或更一般的类型）和方法是一种松耦合的正交关系。

和其它语言相比，Go 是唯一结合了接口值，静态类型检查（是否该类型实现了某个接口），运行时动态转换的语言，并且不需要显式地声明类型是否满足某个接口。该特性允许我们在不改变已有的代码的情况下定义和使用新接口。



#### 动态方法调用

其他像python、Ruby是运行时才解析，Go是支持静态检查，可以在编译期就检查接口类型，这样的话就可以用**类型断言**来检查变量是否实现了相应接口



#### 接口提取

`提取接口` 是非常有用的设计模式，可以减少需要的类型和方法数量，而且不需要像传统的基于类的面向对象语言那样维护整个的类层次结构。

即添加功能的时候，不用更改实现类，只需要新定义接口并定义新的实现方法指向该实现类就可以实现这个接口



### 多重继承

当一个类型包含（内嵌）另一个类型（实现了一个或多个接口）的指针时，这个类型就可以使用（另一个类型）所有的接口方法。

例如：

```
type Task struct {
	Command string
	*log.Logger
}
```

这个类型的工厂方法像这样：

```
func NewTask(command string, logger *log.Logger) *Task {
	return &Task{command, logger}
}
```

当 `log.Logger` 实现了 `Log()` 方法后，Task 的实例 task 就可以调用该方法：

```
task.Log()
```

类型可以通过继承多个接口来提供像 `多重继承` 一样的特性：

```
type ReaderWriter struct {
	*io.Reader
	*io.Writer
}
```



## 面向对象总结

- 封装（数据隐藏）：和别的 OO 语言有 4 个或更多的访问层次相比，Go 把它简化为了 2 层（参见 4.2 节的可见性规则）:

  1）包范围内的：通过标识符首字母小写，`对象` 只在它所在的包内可见

  2）可导出的：通过标识符首字母大写，`对象` 对所在包以外也可见

类型只拥有自己所在包中定义的方法。

- 继承：用组合实现：内嵌一个（或多个）包含想要的行为（字段和方法）的类型；多重继承可以通过内嵌多个类型实现
- 多态：用接口实现：某个类型的实例可以赋给它所实现的任意接口类型的变量。类型和接口是松耦合的，并且多重继承可以通过实现多个接口实现。Go 接口不是 Java 和 C# 接口的变体，而且接口间是不相关的，并且是大规模编程和可适应的演进型设计的关键。



----

## 高级编程

## 读写数据

`bufio.NewReader()` 构造函数的签名为：`func NewReader(rd io.Reader) *Reader`

该函数的实参可以是满足 `io.Reader` 接口的任意对象（任意包含有适当的 `Read()` 方法的对象），函数返回一个新的带缓冲的 `io.Reader` 对象，它将从指定读取器（例如 `os.Stdin`）读取内容。

返回的读取器对象提供一个方法 `ReadString(delim byte)`，该方法从输入中读取内容，直到碰到 `delim` 指定的字符，然后将读取到的内容连同 `delim` 字符一起放到缓冲区。



> 注意结束符    // For Unix: test with delimiter "\n", for Windows: test with "\r\n"
>
> 但是IDEA好像对输入有优化，只要匹配\n就行了



### 文件读写

1. 变量 `inputFile` 是 `*os.File` 类型的。该类型是一个结构，表示一个打开文件的描述符（文件句柄）。然后，使用 `os` 包里的 `Open` 函数来打开一个文件。该函数的参数是文件名，类型为 `string`。在上面的程序中，我们以只读模式打开 `input.dat` 文件。

2. 如果文件不存在或者程序没有足够的权限打开这个文件，Open函数会返回一个错误：`inputFile, inputError = os.Open("input.dat")`。如果文件打开正常，我们就使用 `defer inputFile.Close()` 语句确保在程序退出前关闭该文件。然后，我们使用 `bufio.NewReader` 来获得一个读取器变量。

3. 通过使用 `bufio` 包提供的读取器（写入器也类似），如上面程序所示，我们可以很方便的操作相对高层的 string 对象，而避免了去操作比较底层的字节。

4. 接着，我们在一个无限循环中使用 `ReadString('\n')` 或 `ReadBytes('\n')` 将文件的内容逐行（行结束符 '\n'）读取出来。



读取文件的方式

1. 读取整个文件
2. 带缓冲的读取文件
3. 按列读取文件中的数据



写入文件的方式

```go
outputFile, outputError := os.OpenFile("output.dat", os.O_WRONLY|os.O_CREATE, 0666)
```

可以看到，`OpenFile` 函数有三个参数：文件名、一个或多个标志（使用逻辑运算符“|”连接），使用的文件权限。

我们通常会用到以下标志：

- `os.O_RDONLY`：只读
- `os.O_WRONLY`：只写
- `os.O_CREATE`：创建：如果指定文件不存在，就创建该文件。
- `os.O_TRUNC`：截断：如果指定文件已存在，就将该文件的长度截为0。

在读文件的时候，文件的权限是被忽略的，所以在使用 `OpenFile` 时传入的第三个参数可以用0。而在写文件时，不管是 Unix 还是 Windows，都需要使用 0666。



### 从命令行中读取参数

### flag



### 切片读取文件

### 格式化json数据

不是所有的数据都可以编码为 JSON 类型：只有验证通过的数据结构才能被编码：

- JSON 对象只支持字符串类型的 key；要编码一个 Go map 类型，map 必须是 map[string]T（T是 `json` 包中支持的任何类型）
- Channel，复杂类型和函数类型不能被编码
- 不支持循环数据结构；它将引起序列化进入一个无限循环
- 指针可以被编码，实际上是对指针指向的值进行编码（或者指针是 nil）



### XML数据格式

### Gob数据格式





## 异常处理

### 运行时异常

`panic` 可以直接从代码初始化：当错误条件（我们所测试的代码）很严苛且不可恢复，程序不能继续运行时，可以使用 `panic` 函数产生一个中止程序的运行时错误。`panic` 接收一个做任意类型的参数，通常是字符串，在程序死亡时被打印出来

**Go panicking：**

在多层嵌套的函数调用中调用 `panic`，可以马上中止当前函数的执行，所有的 `defer` 语句都会保证执行并把控制权交还给接收到 `panic` 的函数调用者。这样向上冒泡直到最顶层，并执行（每层的） `defer`，在栈顶处程序崩溃，并在命令行中用传给 panic 的值报告错误情况：这个终止过程就是 *`panicking`*。·

标准库中有许多包含 `Must` 前缀的函数，像 `regexp.MustComplie` 和 `template.Must`；当正则表达式或模板中转入的转换字符串导致错误时，这些函数会 panic。



#### Recover

`recover` 只能在 defer 修饰的函数（参见 [6.4 节](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/06.4.md)）中使用：用于取得 panic 调用中传递过来的错误值，如果是正常执行，调用 `recover` 会返回 nil，且没有其它效果。

总结：panic 会导致栈被展开直到 defer 修饰的 recover() 被调用或者程序中止。



这是所有自定义包实现者应该遵守的最佳实践：

1）*在包内部，总是应该从 panic 中 recover*：不允许显式的超出包范围的 panic()

2）*向包的调用者返回错误值（而不是 panic）。*



### 单元测试

名为 testing 的包被专门用来进行自动化测试，日志和错误报告。并且还包含一些基准测试函数的功能。

> 备注：gotest 是 Unix bash 脚本，所以在 Windows 下你需要配置 MINGW 环境；在 Windows 环境下把所有的 pkg/linux_amd64 替换成 pkg/windows。

用下面这些函数来通知测试失败：

1）`func (t *T) Fail()`

```
	标记测试函数为失败，然后继续执行（剩下的测试）。
```

2）`func (t *T) FailNow()`

```
	标记测试函数为失败并中止执行；文件中别的测试也被略过，继续执行下一个文件。
```

3）`func (t *T) Log(args ...interface{})`

```
	args 被用默认的格式格式化并打印到错误日志中。
```

4）`func (t *T) Fatal(args ...interface{})`

```
	结合 先执行 3），然后执行 2）的效果。
```



#### 实际测试

编写测试代码时，一个较好的办法是把测试的输入数据和期望的结果写在一起组成一个数据表：表中的每条记录都是一个含有输入和期望值的完整测试用例，有时还可以结合像测试名字这样的额外信息来让测试输出更多的信息。

实际测试时简单迭代表中的每条记录，并执行必要的测试。这在练习 [13.4](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/exercises/chapter_13/string_reverse_test.go) 中有具体的应用。

可以抽象为下面的代码段：

```go
var tests = []struct{ 	// Test table
	in  string
	out string

}{
	{"in1", "exp1"},
	{"in2", "exp2"},
	{"in3", "exp3"},
...
}

func TestFunction(t *testing.T) {
	for i, tt := range tests {
		s := FuncToBeTested(tt.in)
		if s != tt.out {
			t.Errorf("%d. %q => %q, wanted: %q", i, tt.in, s, tt.out)
		}
	}
}
```

如果大部分函数都可以写成这种形式，那么写一个帮助函数 verify 对实际测试会很有帮助：

```go
func verify(t *testing.T, testnum int, testcase, input, output, expected string) {
	if expected != output {
		t.Errorf("%d. %s with input = %s: output %s != %s", testnum, testcase, input, output, expected)
	}
}
```

TestFunction 则变为：

```go
func TestFunction(t *testing.T) {
	for i, tt := range tests {
		s := FuncToBeTested(tt.in)
		verify(t, i, “FuncToBeTested: “, tt.in, s, tt.out)
	}
}
```



### 性能调试

#### 时间和内存消耗：

可以用这个便捷脚本 *xtime* 来测量：

```
#!/bin/sh
/usr/bin/time -f '%Uu %Ss %er %MkB %C' "$@"
```

在 Unix 命令行中像这样使用 `xtime go progexec`，这里的 progexec 是一个 Go 可执行程序，这句命令行输出类似：56.63u 0.26s 56.92r 1642640kB progexec，分别对应用户时间，系统时间，实际时间和最大内存占用。



#### 用go test调试

使用方式：`go test -x -v -cpuprofile=prof.out -file x_test.go`

编译执行 x_test.go 中的测试，并向 prof.out 文件中写入 cpu 性能分析信息。



#### 用 pprof 调试

你可以在单机程序 progexec 中引入 runtime/pprof 包；这个包以 pprof 可视化工具需要的格式写入运行时报告数据。对于 CPU 性能分析来说你需要添加一些代码：

```go
var cpuprofile = flag.String("cpuprofile", "", "write cpu profile to file")

func main() {
	flag.Parse()
	if *cpuprofile != "" {
		f, err := os.Create(*cpuprofile)
		if err != nil {
			log.Fatal(err)
		}
		pprof.StartCPUProfile(f)
		defer pprof.StopCPUProfile()
	}
...
```

代码定义了一个名为 cpuprofile 的 flag，调用 Go flag 库来解析命令行 flag，如果命令行设置了 cpuprofile flag，则开始 CPU 性能分析并把结果重定向到那个文件。（os.Create 用拿到的名字创建了用来写入分析数据的文件）。这个分析程序最后需要在程序退出之前调用 StopCPUProfile 来刷新挂起的写操作到文件中；我们用 defer 来保证这一切会在 main 返回时触发。

现在用这个 flag 运行程序：`progexec -cpuprofile=progexec.prof`

然后可以像这样用 gopprof 工具：`gopprof progexec progexec.prof`



## 协程

### 通道阻塞

1. 对于同一个通道，发送操作（协程或者函数中的），在接收者准备好之前是阻塞的：如果ch中的数据无人接收，就无法再给通道传入其他数据：新的输入无法在通道非空的情况下传入。所以发送操作会等待 ch 再次变为可用状态：就是通道值被接收时（可以传入变量）。

2. 对于同一个通道，接收操作是阻塞的（协程或函数中的），直到发送者可用：如果通道中没有数据，接收者就阻塞了。

### 信号量

实现循环的并行运算

```go
for i, v := range data {
	go func (i int, v float64) {
		doSomething(i, v)
		...
	} (i, v)
}
```



信号量是实现互斥锁（排外锁）常见的同步机制，限制对资源的访问，解决读写问题，比如没有实现信号量的 `sync` 的 Go 包，使用带缓冲的通道可以轻松实现：

- 带缓冲通道的容量和要同步的资源容量相同
- 通道的长度（当前存放的元素个数）与当前资源被使用的数量相同
- 容量减去通道的长度就是未处理的资源个数（标准信号量的整数值）



#### 通道使用for循环

+ 通道迭代模式（闭包）
+ 生产者消费者模式





### 协程的同步

#### 检测通道是否关闭

1. `close(ch)`
2. `v,ok := <- ch`
3. 使用`for-range`读取通道，这样会自动检测关闭通道



#### 使用select切换协程

`select` 做的就是：选择处理列出的多个通信情况中的一个。

- 如果都阻塞了，会等待直到其中一个可以处理
- 如果多个可以处理，随机选择一个
- 如果没有通道操作可以处理并且写了 `default` 语句，它就会执行：`default` 永远是可运行的（这就是准备好了，可以执行）。



### 通道的方向

```go
var send_only chan<- int 		// channel can only receive data
var recv_only <-chan int		// channel can only send data
```

只接收的通道（<-chan T）无法关闭，因为关闭通道是发送者用来表示不再给通道发送值了，所以对只接收通道是没有意义的。

可以通过定义通道的方向来限制通道的操作



#### 管道和选择器



### 通道超时和计时器

```go
type Ticker struct {
    C <-chan Time // the channel on which the ticks are delivered.
    // contains filtered or unexported fields
    ...
}
```

**方法**

调用 `Stop()` 使计时器停止，在 `defer` 语句中使用。

`time.Tick()` 函数声明为 `Tick(d Duration) <-chan Time`，当你想返回一个通道而不必关闭它的时候这个函数非常有用:它以 d 为周期给返回的通道发送时间，d是纳秒数。

>定时器（Timer）结构体看上去和计时器（Ticker）结构体的确很像（构造为 `NewTimer(d Duration)`），但是它只发送一次时间，在 `Dration d` 之后。

`time.After(d)`: 在 `Duration d` 之后，当前时间被发到返回的通道；所以它和 `NewTimer(d).C` 是等价的；它类似 `Tick()`，但是 `After()` 只发送一次时间。

#### 简单超时模式

1.

```go
timeout := make(chan bool, 1)
go func() {
        time.Sleep(1e9) // one second
        timeout <- true
}()
```

```go
select {
    case <-ch:
        // a read from ch has occured
    case <-timeout:
        // the read from ch has timed out
        break
}
```

2.取消耗时很长的同步调用

```go
ch := make(chan error, 1)
go func() { ch <- client.Call("Service.Method", args, &reply) } ()
select {
case resp := <-ch
    // use resp and reply
case <-time.After(timeoutNs):
    // call timed out
    break
}
```

> 注意缓冲大小设置为 1 是必要的，可以避免协程死锁以及确保超时的通道可以被垃圾回收。此外，需要注意在有多个 `case` 符合条件时， `select` 对 `case` 的选择是伪随机的，如果上面的代码稍作修改如下，则 `select` 语句可能不会在定时器超时信号到来时立刻选中 `time.After(timeoutNs)` 对应的 `case`，因此协程可能不会严格按照定时器设置的时间结束。

3.使用切片

```go
func Query(conns []Conn, query string) Result {
    ch := make(chan Result, 1)
    for _, conn := range conns {
        go func(c Conn) {
            select {
            case ch <- c.DoQuery(query):
            default:
            }
        }(conn)
    }
    return <- ch
}
```



### 使用通道替代锁

- 使用锁的情景：
  - 访问共享数据结构中的缓存信息
  - 保存应用程序上下文和状态信息数据
- 使用通道的情景：
  - 与异步操作的结果进行交互
  - 分发任务
  - 传递数据所有权



### 惰性生成器

当生成器生成数据的过程是计算密集型且各个结果的顺序并不重要时，那么就可以将生成器放入到go协程实现并行化。但是得小心，使用大量的go协程的开销可能会超过带来的性能增益。



### Future模式

```go
func InverseProduct(a Matrix, b Matrix) {
    a_inv_future := InverseFuture(a)   // start as a goroutine
    b_inv_future := InverseFuture(b)   // start as a goroutine
    a_inv := <-a_inv_future
    b_inv := <-b_inv_future
    return Product(a_inv, b_inv)
}
```

```go
func InverseFuture(a Matrix) chan Matrix {
    future := make(chan Matrix)
    go func() {
        future <- Inverse(a)
    }()
    return future
}
```



### 复用

#### C/S模式





### 并发访问对象

为了保护对象被并发访问修改，我们可以使用协程在后台顺序执行匿名函数来替代使用同步互斥锁。





## 高级编程

### 并发的内存模型

理论上讲，多线程和基于消息的并发编程是等价的。

协程的好处：

+ 可以以很小的栈启动，根据需要弹性地伸缩栈的空间。因此启动的代价很小。
+ Go的调度器采用的是半抢占式调度，只有当当前协程阻塞才会调度；同时发生在用户态，调度器会根据具体函数只保存必要的寄存器，切换的代价比系统线程要低得多
+ `runtime.GOMAXPROCS`变量，用于控制当前运行正常非阻塞Goroutine的系统线程数目。



### 并发模式

#### 安全退出并发

```go
func worker(wg *sync.WaitGroup, cannel chan bool) {
    defer wg.Done()

    for {
        select {
        default:
            fmt.Println("hello")
        case <-cannel:
            return
        }
    }
}

func main() {
    cancel := make(chan bool)

    var wg sync.WaitGroup
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go worker(&wg, cancel)
    }

    time.Sleep(time.Second)
    close(cancel)
    wg.Wait()
}
```

**使用context包**

Go语言是带内存自动回收特性的，因此内存一般不会泄漏，但是当`main`函数不再使用管道时后台Goroutine有泄漏的风险。

```go
func worker(ctx context.Context, wg *sync.WaitGroup) error {
    defer wg.Done()

    for {
        select {
        default:
            fmt.Println("hello")
        case <-ctx.Done():
            return ctx.Err()
        }
    }
}

func main() {
    ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)

    var wg sync.WaitGroup
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go worker(ctx, &wg)
    }

    time.Sleep(time.Second)
    cancel()

    wg.Wait()
}
```



#### 素数筛

使用context包

```go
// 返回生成自然数序列的管道: 2, 3, 4, ...
func GenerateNatural(ctx context.Context) chan int {
    ch := make(chan int)
    go func() {
        for i := 2; ; i++ {
            select {
            case <- ctx.Done():
                return
            case ch <- i:
            }
        }
    }()
    return ch
}

// 管道过滤器: 删除能被素数整除的数
func PrimeFilter(ctx context.Context, in <-chan int, prime int) chan int {
    out := make(chan int)
    go func() {
        for {
            if i := <-in; i%prime != 0 {
                select {
                case <- ctx.Done():
                    return
                case out <- i:
                }
            }
        }
    }()
    return out
}

func main() {
    // 通过 Context 控制后台Goroutine状态
    ctx, cancel := context.WithCancel(context.Background())

    ch := GenerateNatural(ctx) // 自然数序列: 2, 3, 4, ...
    for i := 0; i < 100; i++ {
        prime := <-ch // 新出现的素数
        fmt.Printf("%v: %v\n", i+1, prime)
        ch = PrimeFilter(ctx, ch, prime) // 基于新素数构造的过滤器
    }

    cancel()
}
```

### 错误和异常

为了记录这种错误类型在包装的变迁过程中的信息，我们一般会定义一个辅助的`WrapError`函数，用于包装原始的错误，同时保留完整的原始错误类型。为了问题定位的方便，同时也为了能记录错误发生时的函数调用状态，我们很多时候希望在出现致命错误的时候保存完整的函数调用信息。同时，为了支持RPC等跨网络的传输，我们可能要需要将错误序列化为类似JSON格式的数据，然后再从这些数据中将错误解码恢出来。

为此，我们可以定义自己的`github.com/chai2010/errors`包，里面是以下的错误类型：

```go
type Error interface {
    Caller() []CallerInfo
    Wraped() []error
    Code() int
    error

    private()
}

type CallerInfo struct {
    FuncName string
    FileName string
    FileLine int
}
```

其中`Error`为接口类型，是`error`接口类型的扩展，用于给错误增加调用栈信息，同时支持错误的多级嵌套包装，支持错误码格式。为了使用方便，我们可以定义以下的辅助函数：

```go
func New(msg string) error
func NewWithCode(code int, msg string) error

func Wrap(err error, msg string) error
func WrapWithCode(code int, err error, msg string) error

func FromJson(json string) (Error, error)
func ToJson(err error) string
```

`New`用于构建新的错误类型，和标准库中`errors.New`功能类似，但是增加了出错时的函数调用栈信息。`FromJson`用于从JSON字符串编码的错误中恢复错误对象。`NewWithCode`则是构造一个带错误码的错误，同时也包含出错时的函数调用栈信息。`Wrap`和`WrapWithCode`则是错误二次包装函数，用于将底层的错误包装为新的错误，但是保留的原始的底层错误信息。这里返回的错误对象都可以直接调用`json.Marshal`将错误编码为JSON字符串。

我们可以这样使用包装函数:

```go
import (
    "github.com/chai2010/errors"
)

func loadConfig() error {
    _, err := ioutil.ReadFile("/path/to/file")
    if err != nil {
        return errors.Wrap(err, "read failed")
    }

    // ...
}

func setup() error {
    err := loadConfig()
    if err != nil {
        return errors.Wrap(err, "invalid config")
    }

    // ...
}

func main() {
    if err := setup(); err != nil {
        log.Fatal(err)
    }

    // ...
}
```

上面的例子中，错误被进行了2层包装。我们可以这样遍历原始错误经历了哪些包装流程：

```go
    for i, e := range err.(errors.Error).Wraped() {
        fmt.Printf("wraped(%d): %v\n", i, e)
    }
```

同时也可以获取每个包装错误的函数调用堆栈信息：

```go
    for i, x := range err.(errors.Error).Caller() {
        fmt.Printf("caller:%d: %s\n", i, x.FuncName)
    }
```

如果需要将错误通过网络传输，可以用`errors.ToJson(err)`编码为JSON字符串：

```go
// 以JSON字符串方式发送错误
func sendError(ch chan<- string, err error) {
    ch <- errors.ToJson(err)
}

// 接收JSON字符串格式的错误
func recvError(ch <-chan string) error {
    p, err := errors.FromJson(<-ch)
    if err != nil {
        log.Fatal(err)
    }
    return p
}
```

对于基于http协议的网络服务，我们还可以给错误绑定一个对应的http状态码：

```go
err := errors.NewWithCode(404, "http error code")

fmt.Println(err)
fmt.Println(err.(errors.Error).Code())
```





### CGO

+ 导入C模块
+ 编译成C模块



```Go
package main

/*
#include <stdio.h>

void printint(int v) {
    printf("printint: %d\n", v);
}
*/
import "C"

func main() {
    v := 42
    C.printint(C.int(v))
}
```

这个例子展示了cgo的基本使用方法。开头的注释中写了要调用的C函数和相关的头文件，头文件被include之后里面的所有的C语言元素都会被加入到”C”这个虚拟的包中。需要注意的是，**import "C"导入语句需要单独一行**，不能与其他包一同import。向C函数传递参数也很简单，就直接转化成对应C语言类型传递就可以。如上例中`C.int(v)`用于将一个Go中的int类型值强制类型转换转化为C语言中的int类型值，然后调用C语言定义的printint函数进行打印。

需要注意的是，**Go是强类型语言，所以cgo中传递的参数类型必须与声明的类型完全一致**，而且传递前必须用”C”中的转化函数转换成对应的C类型，不能直接传入Go中类型的变量。同时通过虚拟的C包导入的C语言符号并不需要是大写字母开头，它们不受Go语言的导出规则约束。



#### #cgo

build tag 是在Go或cgo环境下的C/C++文件开头的一种特殊的注释。条件编译类似于前面通过`#cgo`指令针对不同平台定义的宏，只有在对应平台的宏被定义之后才会构建对应的代码。但是通过`#cgo`指令定义宏有个限制，它只能是基于Go语言支持的windows、darwin和linux等已经支持的操作系统。如果我们希望定义一个DEBUG标志的宏，`#cgo`指令就无能为力了。而Go语言提供的build tag 条件编译特性则可以简单做到。



#### 类型转换

| C语言类型 | CGO类型    | Go语言类型 |
| --------- | ---------- | ---------- |
| int8_t    | C.int8_t   | int8       |
| uint8_t   | C.uint8_t  | uint8      |
| int16_t   | C.int16_t  | int16      |
| uint16_t  | C.uint16_t | uint16     |
| int32_t   | C.int32_t  | int32      |
| uint32_t  | C.uint32_t | uint32     |
| int64_t   | C.int64_t  | int64      |
| uint64_t  | C.uint64_t | uint64     |



在CGO生成的`_cgo_export.h`头文件中还会为Go语言的字符串、切片、字典、接口和管道等特有的数据类型生成对应的C语言类型：

```c
typedef struct { const char *p; GoInt n; } GoString;
typedef void *GoMap;
typedef void *GoChan;
typedef struct { void *t; void *v; } GoInterface;
typedef struct { void *data; GoInt len; GoInt cap; } GoSlice;
```

不过需要注意的是，其中只有**字符串**和**切片**在CGO中有一定的使用价值



CGO的C虚拟包提供了以下一组函数，用于Go语言和C语言之间数组和字符串的双向转换：

```go
// Go string to C string
// The C string is allocated in the C heap using malloc.
// It is the caller's responsibility to arrange for it to be
// freed, such as by calling C.free (be sure to include stdlib.h
// if C.free is needed).
func C.CString(string) *C.char

// Go []byte slice to C array
// The C array is allocated in the C heap using malloc.
// It is the caller's responsibility to arrange for it to be
// freed, such as by calling C.free (be sure to include stdlib.h
// if C.free is needed).
func C.CBytes([]byte) unsafe.Pointer

// C string to Go string
func C.GoString(*C.char) string

// C data with explicit length to Go string
func C.GoStringN(*C.char, C.int) string

// C data with explicit length to Go []byte
func C.GoBytes(unsafe.Pointer, C.int) []byte
```

其中`C.CString`针对输入的Go字符串，克隆一个C语言格式的字符串；返回的字符串由C语言的`malloc`函数分配，不使用时需要通过C语言的`free`函数释放。`C.CBytes`函数的功能和`C.CString`类似，用于从输入的Go语言字节切片克隆一个C语言版本的字节数组，同样返回的数组需要在合适的时候释放。`C.GoString`用于将从NULL结尾的C语言字符串克隆一个Go语言字符串。`C.GoStringN`是另一个字符数组克隆函数。`C.GoBytes`用于从C语言数组，克隆一个Go语言字节切片。

该组辅助函数都是以克隆的方式运行。当Go语言字符串和切片向C语言转换时，克隆的内存由C语言的`malloc`函数分配，最终可以通过`free`函数释放。当C语言字符串或数组向Go语言转换时，克隆的内存由Go语言分配管理。通过该组转换函数，转换前和转换后的内存依然在各自的语言环境中，它们并没有跨越Go语言和C语言。克隆方式实现转换的优点是接口和内存管理都很简单，缺点是克隆需要分配新的内存和复制操作都会导致额外的开销。

##### 指针转换

**指针间转换**

为了实现X类型指针到Y类型指针的转换，我们需要借助`unsafe.Pointer`作为中间桥接类型实现不同类型指针之间的转换。`unsafe.Pointer`指针类型类似C语言中的`void*`类型的指针。

下面是指针间的转换流程的示意图：

![img](https://chai2010.cn/advanced-go-programming-book/images/ch2-1-x-ptr-to-y-ptr.uml.png)

*图 2-1 X类型指针转Y类型指针*

任何类型的指针都可以通过强制转换为`unsafe.Pointer`指针类型去掉原有的类型信息，然后再重新赋予新的指针类型而达到指针间的转换的目的。

**数值与指针转换**

为了严格控制指针的使用，Go语言禁止将数值类型直接转为指针类型！不过，Go语言针对`unsafe.Pointr`指针类型特别定义了一个uintptr类型。我们可以uintptr为中介，实现数值类型到`unsafe.Pointr`指针类型到转换。再结合前面提到的方法，就可以实现数值和指针的转换了。

下面流程图演示了如何实现int32类型到C语言的`char*`字符串指针类型的相互转换：

![img](https://chai2010.cn/advanced-go-programming-book/images/ch2-2-int32-to-char-ptr.uml.png)

**切片间转换**

```go
var p []X
var q []Y

pHdr := (*reflect.SliceHeader)(unsafe.Pointer(&p))
qHdr := (*reflect.SliceHeader)(unsafe.Pointer(&q))

pHdr.Data = qHdr.Data
pHdr.Len = qHdr.Len * unsafe.Sizeof(q[0]) / unsafe.Sizeof(p[0])
pHdr.Cap = qHdr.Cap * unsafe.Sizeof(q[0]) / unsafe.Sizeof(p[0])
```





### RPC

#### protoBuf

> Protocol Buffers 是一种灵活，高效，自动化机制的结构数据序列化方法－可类比 XML



1. 编写数据结构
2. 通过protoc编译器生成对应的接口代码（实现数据的序列化）
3. 调用protoBuf



**优势**

+ XML、JSON、ProtoBuf 都具有**数据结构化**和**数据序列化**的能力

+ XML、JSON 更注重**数据结构化**，关注人类可读性和语义表达能力。ProtoBuf 更注重**数据序列化**，关注效率、空间、速度，人类可读性差，语义表达能力不足（为保证极致的效率，会舍弃一部分元信息）

+ ProtoBuf 的应用场景更为明确，XML、JSON 的应用场景更为丰富。



#### grpc

> gRPC是Google公司基于Protobuf开发的跨语言的开源RPC框架。gRPC基于HTTP/2协议设计，可以基于一个HTTP/2链接提供多个服务，对于移动设备更加友好。

技术栈

![img](https://chai2010.cn/advanced-go-programming-book/images/ch4-1-grpc-go-stack.png)





### go web

**请求路由**

实现原理：压缩字典树，是一种空间换时间的典型做法。压缩字典树在原来字典树的前提下，一个节点可以存放一个词而不是一个字母，做到了减少层数，平衡了字典树的优缺点



```go
// 略去了其它部分的 Router struct
type Router struct {
    // ...
    trees map[string]*node
    // ...
}
```



**中间件**

注意代码中的`middleware`数组遍历顺序，和用户希望的调用顺序应该是"相反"的。应该不难理解。



**校验请求**

采用深度遍历的方法



**流量服务限制**

计算机程序可依据其瓶颈分为磁盘IO瓶颈型，CPU计算瓶颈型，网络带宽瓶颈型，分布式场景下有时候也会外部系统而导致自身瓶颈

常见限流手段

1. 漏桶是指我们有一个一直装满了水的桶，每过固定的一段时间即向外漏一滴水。如果你接到了这滴水，那么你就可以继续服务请求，如果没有接到，那么就需要等待下一滴水。
2. 令牌桶则是指匀速向桶中添加令牌，服务请求时需要从桶中获取令牌，令牌的数目可以按照需要消耗的资源进行相应的调整。如果没有令牌，可以选择等待，或者放弃。



**表驱动开发**

就是将switch改成map的设计模式







## 内存模型



![img](https://images2017.cnblogs.com/blog/881857/201711/881857-20171122165637665-171579804.png)



+ arena即为所谓的堆区，应用中需要的内存从这里分配, 大小为512G，为了方便管理把arena区域划分成一个个的page，每个page为8KB,一共有512GB/8KB个页

+ spans区域存放span的指针，每个指针对应一个page，所以span区域的大小为`(512GB/8KB) * 指针大小8byte = 512M`

+ bitmap区域大小也是通过arena计算出来`512GB / (指针大小(8 byte) * 8 / 2) = 16G`，用于表示arena区域中哪些地址保存了对象, 并且对象中哪些地址包含了指针，主要用于GC。



**分配细节**

1. object size > 32K，则使用 mheap 直接分配。

2. object size < 16 byte，不包含指针使用 mcache 的小对象分配器 tiny 直接分配；包含指针分配策略与[16 B, 32 K]类似。

3. object size >= 16 byte && size <=32K byte 时，先使用 mcache 中对应的 size class 分配。

4. 如果 mcache 对应的 size class 的 span 已经没有可用的块，则向 mcentral 请求。

5. 如果 mcentral 也没有可用的块，则向 mheap 申请，并切分。

6. 如果 mheap 也没有合适的 span，则向操作系统申请。

**基本结构**

![img](https://upload-images.jianshu.io/upload_images/9905654-5daa9d30b44c0bcd.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/618/format/webp)

以size class 10为例，npages=1，nelems=56，spanclass=10，elemsize=144；startAddr指arena区位置；next和prev指spans区，span链表；allocBits是一个bitmap，标记分配块分配情况，这个设计我也用过，之前用redis bitmap实现了IPAM。



### 内存分配器



内存管理一般包含三个不同的组件，分别是用户程序（Mutator）、分配器（Allocator）和收集器（Collector）[1](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-memory-allocator/#fn:1)，当用户程序申请内存时，它会通过内存分配器申请新的内存，而分配器会负责从堆中初始化相应的内存区域。

![mutator-allocator-collector](https://img.draveness.me/2020-02-29-15829868066411-mutator-allocator-collector.png)

### 分级分配

线程缓存分配（Thread-Caching Malloc，TCMalloc）是用于分配内存的的机制，它比 glibc 中的 `malloc` 函数还要快很多。Go 语言的内存分配器就借鉴了 TCMalloc 的设计实现高速的内存分配，它的核心理念是使用多级缓存根据将对象根据大小分类，并按照类别实施不同的分配策略。

| 类别   | 大小          |
| ------ | ------------- |
| 微对象 | `(0, 16B)`    |
| 小对象 | `[16B, 32KB]` |
| 大对象 | `(32KB, +∞)`  |

**多级缓存**



**线程缓存**属于每一个独立的线程，它能够满足线程上绝大多数的内存分配需求，因为不涉及多线程，所以也不需要使用互斥锁来保护内存，这能够减少锁竞争带来的性能损耗。当线程缓存不能满足需求时，就会使用**中心缓存**作为补充解决小对象的内存分配问题；在遇到 32KB 以上的对象时，内存分配器就会选择**页堆**直接分配大量的内存。



**地址空间**

因为所有的内存最终都是要从操作系统中申请的，所以 Go 语言的运行时构建了操作系统的内存管理抽象层，该抽象层将运行时管理的地址空间分成以下的四种状态

| 状态       | 解释                                                         |
| ---------- | ------------------------------------------------------------ |
| `None`     | 内存没有被保留或者映射，是地址空间的默认状态                 |
| `Reserved` | 运行时持有该地址空间，但是访问该内存会导致错误               |
| `Prepared` | 内存被保留，一般没有对应的物理内存访问该片内存的行为是未定义的可以快速转换到 `Ready` 状态 |
| `Ready`    | 可以被安全访问                                               |

![memory-regions-states-and-transitions](https://img.draveness.me/2020-02-29-15829868066474-memory-regions-states-and-transitions.png)



### **内存管理组件**

![go-memory-layout](https://img.draveness.me/2020-02-29-15829868066479-go-memory-layout.png)

每一个处理器都会被分配一个线程缓存 [`runtime.mcache`](https://github.com/golang/go/blob/01d137262a713b308c4308ed5b26636895e68d89/src/runtime/mcache.go#L19) 用于处理微对象和小对象的分配，它们会持有内存管理单元 [`runtime.mspan`](https://github.com/golang/go/blob/921ceadd2997f2c0267455e13f909df044234805/src/runtime/mheap.go#L358)。

每个类型的内存管理单元都会管理特定大小的对象，当内存管理单元中不存在空闲对象时，它们会从 [`runtime.mheap`](https://github.com/golang/go/blob/8ac98e7b3fcadc497c4ca7d8637ba9578e8159be/src/runtime/mheap.go#L40) 持有的 134 个中心缓存 [`runtime.mcentral`](https://github.com/golang/go/blob/8ac98e7b3fcadc497c4ca7d8637ba9578e8159be/src/runtime/mcentral.go#L20) 中获取新的内存单元，中心缓存属于全局的堆结构体 [`runtime.mheap`](https://github.com/golang/go/blob/8ac98e7b3fcadc497c4ca7d8637ba9578e8159be/src/runtime/mheap.go#L40)，它会从操作系统中申请内存。

#### **内存管理单元**

`mspan`

#### 线程缓存

#### 中心缓存

#### 页堆



## 垃圾回收算法



## 调度器

**分类**

+ 单线程调度器

+ 多线程调度器

缺点：调度器和锁是全局资源，调度状态同样是**中心化存储**（全局仅有一份的意思吧），锁竞争问题严重；多线程之间需要互相传递goroutine，引入大量延迟；线程阻塞带来巨大的开销

+ 任务窃取调度器

引入GMP模型，基于工作窃取的多线程调度器将每一个线程绑定到了独立的 CPU 上，这些线程会被不同处理器管理，不同的处理器通过工作窃取对任务进行再分配实现任务的平衡，也能提升调度器和 Go 语言程序的整体性能

+ 抢占式调度器

使得gouroutine不会长期占用某个线程，导致其他协程饿死；同时解决stop the world；

但是协作式的抢占是通过编译器插入函数实现的，还是需要函数调用作为入口才能触发抢占，同时依然存在无法抢占的边缘情况，因此后期使用信号抢占来解决这个问题



1. **启动调度器**

2. **创建Goroutine**

   初始化goroutine的三种方法: 从处理器或者调度器缓存中获取新的结构体，也可以调用runtime.malg创建新的结构体

   运行队列：处理器本地的运行队列是一个使用数组构成的环形链表，它最多可以存储 256 个待执行任务。

   Go 语言中有两个运行队列，其中一个是**处理器本地的运行队列**，另一个是**调度器持有的全局运行队列**，只有在本地运行队列没有剩余空间时才会使用全局队列。

   调度信息：`pc` 寄存器的作用就是存储程序接下来运行的位置；`sp` 中存储了 `runtime.goexit`函数的程序计数器

3. **调度循环**

   1. 为了保证公平，当全局运行队列中有待执行的 Goroutine 时，通过 `schedtick` 保证有一定几率会从全局的运行队列中查找对应的 Goroutine；
   2. 从处理器本地的运行队列中查找待执行的 Goroutine；
   3. 如果前两种方法都没有找到 Goroutine，就会通过 `runtime.findrunnable`进行阻塞地查找 Goroutine；
   4. `findrunnable`:
      1. 从本地运行队列、全局运行队列中查找；
      2. 从网络轮询器中查找是否有 Goroutine 等待运行；
      3. 通过`runtime.runqsteal` 函数尝试从其他随机的处理器中窃取待运行的 Goroutine，在该过程中还可能窃取处理器中的计时器；

4. **触发调度**

   + 调度的时间点
     + 主动挂起
     + 系统调用
     + 协作式调度
     + 系统监控

   ![schedule-points](https://img.draveness.me/2020-02-05-15808864354679-schedule-points.png)

   

5. **线程管理**



整个 io 过程：

- io 与系统调用不能混淆，一个 io 过程可能包括多次系统调用。
- （经过一个系统调用发现文件描述符还未可用而）阻塞的 io 首先会导致 G 的挂起，此时 G 与 M 分离，且不在任何 P 的运行队列中，当前的 P 会调度下一个 G，这个阶段不涉及新线程的创建。
- 被 io 挂起的 G 由网络轮询器维护，直到文件描述符可用。
- 网络轮询器既会被（在独立线程中的）系统监控 Goroutine 触发，也会被其他各个活跃线程上的 Goroutine 触发。
- 当文件描述符可用时，G 会重新加入到原来的 P 中等待被调度。
- 当 G 被重新调度时，会重新发起读/写系统调用。
- 当 G 进行系统调用的时候，对应的 M 和 P 也阻塞在系统调用，并不会立刻发生抢占，只有当这个阻塞持续时间过长（10 ms）时，才会将 P（及之上的其他 G）抢占并分配到空闲的 M 上，此时如果没有空闲的，才会创建新的线程。

## 网络轮询器

多模块网络轮询器

```go
func netpollinit()  		//初始化网络轮询器
func netpollopen(fd uintptr, pd *pollDesc) int32 //监听文件描述符的边缘触发事件，创建并加入监听
func netpoll(delta int64) gList  		//轮询网络并返回准备好的Goroutine
func netpollBreak()  		//唤醒网络轮询器
func netpollIsPollDescriptor(fd uintptr) bool   	//判断文件描述符是否会被轮询器使用
```



**数据结构**

```go
//该结构体中包含用于监控可读和可写状态的变量
type pollDesc struct {
	link *pollDesc

	lock    mutex
	fd      uintptr
	...
	rseq    uintptr
	rg      uintptr
	rt      timer
	rd      int64
	wseq    uintptr
	wg      uintptr
	wt      timer
	wd      int64
}
```

`runtime.pollDesc`结构体会使用 `link` 字段串联成一个链表存储在 `runtime.pollCache` 中：

```go
type pollCache struct {
	lock  mutex
	first *pollDesc
}
```

`runtime.pollCache` 是运行时包中的全局变量，该结构体中包含一个用于保护轮询数据的互斥锁和链表头：



## 系统监控

守护进程是很有效的设计，它在整个系统的生命周期中都会存在，会随着系统的启动而启动，系统的结束而结束。在操作系统和 Kubernetes 中，我们经常会将数据库服务、日志服务以及监控服务等进程作为守护进程运行。

Go 语言的系统监控也起到了很重要的作用，它在内部启动了一个不会中止的循环，在循环的内部会轮询网络、抢占长期运行或者处于系统调用的 Goroutine 以及触发垃圾回收，通过这些行为，它能够让系统的运行状态变得更健康。



当程序趋于稳定之后，系统监控的触发时间就会稳定在 10ms。它除了会检查死锁之外，还会在循环中完成以下的工作：

- 运行计时器 — 获取下一个需要被触发的计时器；
- 轮询网络 — 获取需要处理的到期文件描述符；
- 抢占处理器 — 抢占运行时间较长的或者处于系统调用的 Goroutine；
- 垃圾回收 — 在满足条件时触发垃圾收集回收内存；





## 编译原理补充











## 面经

> struct可不可以比较？

- 可排序的数据类型有三种，Integer，Floating-point，和String
- 可比较的数据类型除了上述三种外，还有Boolean，Complex，Pointer，Channel，Interface和Array
- 不可比较的数据类型包括，Slice, Map, 和Function

> defer 函数

defer是包含return返回

```go
type _defer struct {
            siz     int32 
            started bool
            sp      uintptr // sp at time of defer
            pc      uintptr
            fn      *funcval
            _panic  *_panic // panic that is running defer
            link    *_defer
    }
```



> context包的用途

简单来说就是上下文，用来追踪信息的一棵树，我们可以对这棵树的节点进行删除和增加，在这个节点上保存数据

> CLient如何开启长连接

首先`server`必须支持长连接，其次，`client`开启`DisableKeepAlives`字段和增大`MaxIdleConns`最大连接数和`MaxIdleConnsPerHost`对每个`host`的最大链接数量；读完response之后才关闭连接

> slice的共享和扩容

slice设计为指向数组的指针，需要扩容的时候，会将底层数组拷贝一份到更大的数组然后指向那个数组

slice有个特性是允许多个slice指向同一个底层数组，这是一个有用的特性，在很多场景下都能通过这个特性实现 no copy 而提高效率。但共享同时意味着不安全。b在追加3时实际上覆盖了`a[1]`，导致c变成了`[3 4]`。

写法：1是make一个新slice，然后追加，2是直接定义最终cap，强迫复制新数组

> map顺序读取

key存slice



