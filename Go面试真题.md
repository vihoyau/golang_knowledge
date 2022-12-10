# Go面试真题

## Go基础类

### 1.与其他语言相比，使用Go有什么好处？

- 与其他作为实验室开始的语言不同，Go代码的设计更加务实。每个功能和语法决策都旨在让程序员的生活更轻松
- golang针对并发进行了优化，并且在规模上运行良好。
- 由于单一的标准代码格式，Golang通常被认为比其他语言更具有可读性
- 自动垃圾收集明显比java或Python更有效，因为它与程序同时执行。

### 2.Golang使用什么数据类型？

- golang使用以下类型：

	- Method、Boolean、Numeric、String、Array、Slice、Struct、Pointer、Function、Interface、Map、Channel

### 3.Go程序中的包是什么？

- 包（pkg）是Go工作区中包含Go源文件或其他包的目录。源文件中的每个函数、变量和类型都存储在链接包中。每个Go源文件都属于一个包，该包在文件顶部使用以下命令声明：packege <packagename>
- 可以使用以下方法导入和导出包以重用导出的函数或类型：import <packagename>
- Golang的标准包是fmt，其中包含格式化和打印功能，如Println()

### 4.Go支持什么形式的类型转换？将整数转换为浮点数。

- Go支持显式类型转换以满足其严格的类型要求
-  i := 55 // int
- j := 67.8 // float64
- sum := i + int(j) // j is converted to int

### 5.什么是Goroutine？你如何停止它？

- 一个Goroutine是一个函数或方法执行同时旁边其他任何go程采用了特殊的Goroutine 线程。Goroutine线程比标准线程更轻量级，大多数Golang程序同时使用数千个g、goroutine
- 要创建Goroutine,请go在函数声明之前添加关键字。

	- go f(x, y, z)

- 你可以通过向Goroutine发送一个信号通道来停止它。goroutine只能在被告知检查时响应信号，因此你需要再逻辑位置包含检查。
- package main
func main() {
  quit : = make(chan bool)
  go func() {
     for {
       select {
         case <- quit:
            return
         default:
       }  
     }
  }()
  quit <- true
}

### 6.如何在运行时检查变量类型？

- 类型开关是在运行时检查变量的最佳方式。类型开关按类型而不是值来评估变量。每个Switch至少包含一个case，用作条件语句，和一个 default case，如果没有一个case为真，则执行。

### 7.Go两个接口之间可以存在什么关系？

- 如果两个接口有相同的方法列表，那么他们就是等价的，可以相互赋值。如果接口A的方法列表是接口B的方法列表的自己，那么接口B可以赋值给接口A。接口查询是否成功，要在运行期才能够确定。
- 一个类型可以实现多个接口。多个类型可以实现同一个接口。

### 8.Go当中同步锁有什么特点？作用是什么

https://static001.geekbang.org/resource/image/44/b8/44c08abdd0aff633ca932fc89386ebb8.jpg?wh=3747*1972

- 当一个Goroutine（协程）获得了Mutex后，其他Goroutine（协程）就只能乖乖等待，除非该goroutine释放了该Mutex.RWMutex 在读锁 占用的情况下，会阻止写，但不阻止读RWMutex在写锁占用情况下，会阻止任何其他goroutine（无论读和写）进来，整个锁相当于由该goroutine独占同步锁的作用是保证资源在使用时的独有性，不会因为并发而导致数据错乱，保证系统的稳定性。
- 一般情况下，struct有多个字段  我们会把mutex独立用空格分隔开使用。

	- // 线程安全的计数器类型
type Counter struct {
 CounterType int Name string mu 
 sync.Mutex count uint64
}

// 加1的方法，内部使用互斥锁保护
func (c *Counter) Incr() {
 c.mu.Lock()
 c.count++
 c.mu.Unlock()
}

- 谁申请谁释放

	- mutex容易出错的情况

		- Lock/unLock 不成对存在
		- Copy已使用的Mutex
		- 重入
		- 死锁

			- 互相等待
			- 只要破坏其中一种，即可解开

				- 互斥

					- 有的资源存在排他性。避免此事发生。

				- 持有和等待

					- goroutine持有一个资源，并在等待其他goroutine的资源的情况下。

				- 不可剥夺

					- 资源只能被持有它的goroutine释放。

				- 环路等待。

					- 避免形成环路的等待

### 9.Go语言当中Channel（通道）有什么特点, 需要注意什么？

- 执行Goroutine通信，不要采用内存共享，要通过channel通信的方式分享数据。
- Channel原理

	- 数据交流
	- 数据传递
	- 信号通知
	- 任务编排
	- 锁

- 基本用法

	- 只能接收

		- <- chan int

	- 只能发送

		- chan <- struct{}

	- 既能接收，也能发送

		- chan string

- Channel的实现原理

	- send
	- recv
	- close

- 使用channel容易犯的错误

	- 常见的错误是panic和Goroutine泄漏
	- 三种情况

		- close 为nil的chan
		- send 已经关闭的chan
		- close已经close的chan

- channel不是唯一的，使用并发原语更简单

	- 共享资源使用传统的并发原语
	- 复杂的任务编排和消息传递使用channel
	- 消息通知机制使用channel，除非只使用一个采用Cond。
	- 简单等待所有任务采用WaitGroup,也可以使用channel
	- 需要和select语句结合，使用Channel
	- 需要和超时结合的，使用Channel和Context

### 10.Go语言当中Channel缓冲有什么特点？

- 无缓冲的是同步的，有缓冲的是异步的。

### 11.Go语言中cap函数可以作用于哪些内容？

- array数组
- slice切片
- cahnnel通道

### 12.go convey是什么？一般用来做什么？

- go convey是一个支持golang的单元测试框架
- go convey能够自动监控文件修改并启动测试，并可以将测试结果实时输出到web界面。
- go convey提供了丰富的断言简化测试用例的编写。

### 13.go语言中new作用是什么？

- 使用new函数来分配空间
- new的是一个类型不是一个值
- 返回值是指向这个新分配的地址的指针

### 14.Go语言中make的作用是什么？

- make的作用是为slice、map、channel的初始化，然后返回引用make函数是内建函数，函数定义
- make和new不同，仅仅用于创建map、slice、chan，而且返回的是实例
- func make(type, size integerType) Type

### 15.Printf、Sprintf、FprintF都是格式化输出，有什么不同？

- Printf是标准输出，一般用于屏幕，也可以重定向。
- Sprintf是格式化字符串输出到指定的字符串中。
- Fprintf是格式化字符串输出到文件中。

### 16.Go语言当中数组和切片的区别是什么？

- 数组

	- 数组有固定长度，[3]int是两种不同的数组类型数组需要指定大小，不指定也会根据初始化的自动推算出大小，不可改变数组是通过值传递的。

- 切片

	- 切片可以改变长度切片是轻量级的数据结构，三个属性，指针，长度，容量不需要指定大小切片是地址传递（引用传递）可以通过数组来初始化，也可以通过内置函数make()来初始化，初始化的时候len=cap，然后进行扩容。

### 17.Go语言中值传递和地址传递（引用传递）如何运用？有什么区别？举例说明

- 1.值传递只会把参数的值复制一份放进对应的函数，两个变量的地址不同，不可相互修改。
- 2.地址传递（引用传递）会将变量本身传入对应的函数，在函数中可以对该变量进行值内容的修改。

### 18.Go语言当中数组和切片在传递的时候的区别是什么？

- 数组是值传递
- 切片是引用传递

### 19.Go语言是如何实现切片扩容的？

- apend的时候，自动扩容。

### 20.看下面代码的defer的执行顺序是什么？defer的作用和特点是什么？

- 作用

	- 加defer关键字即可。多个defer的执行顺序与声明反着来。
	- 无论中途遇到panic，还是正常return。都会执行。

- defer的常用场景

	- defer语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。
	- 通过defer机制，不论函数逻辑多复杂，都能保证在任何执行路径下，资源被释放。
	- 释放资源的defer应该直接跟在请求资源的语句后。

### 21.Golang Slice 的底层实现

- 切片是基于数组实现的，它的底层是数组，它自己本身非常小，可以理解为对底层数组的抽象。因为基于数组实现，所以它的底层的内存是连续分配的，效率非常高。还可以通过索引获得数据，可以迭代以及垃圾回收优化。
- 切片本身并不是动态数组或者数组指针。它内部实现的数据结构通过指针引用底层数组，设定相关属性将数据读写操作限定在指定的区域内。切片本身是一个只读对象，其工作机制类似数组指针的一种封装。
- 切片对象非常小，是因为它是只有3个字段的数据结构：

	- 指向底层数组的指针
	- 切片的长度
	- 切片的容量

### 22.Golang Slice的扩容机制，有什么注意点？

- 首先判断，如果新申请容量大于2倍的旧容量，最终容量就是新申请的容量。
- 否则判断，如果旧切片的长度小于1024，则最终容量就是旧容量的两倍
- 否则判断，如果旧切片的长度大于等于1024，则最终容量从旧容量开始循环增加原来的1/4，直到最终容量大于等于新申请的容量。
- 如果最终容量计算值溢出，则最终容量就是新申请容量。

### 23.扩容前后的Slice是否相同？

- 情况一：

	- 原数组还有容量可以扩容（实际容量没有填充完），这种情况下，扩容以后的数组还是指向原来的数组，对一个切片的操作可能影响多个指针指向相同的地址slice

- 情况二：

	- 原来数组的容量已经达到了最大值，再想扩容，Go默认会开一片内存区域，把原来的值拷贝过来，然后再执行append()操作。这种情况下丝毫不影响原数组。
	- 要复制一个Slice，最好使用Copy函数

### 24.Golang的参数传递、引用类型

- Go语言中所有的传参都是值传递（传值），都是一个副本，一个拷贝。因为拷贝的内容有时候是非引用类型（int、string、struct等这些），这样就在函数中就无法修改原内容数据；有的是引用类型（指针、map、slice、chan等这些），这样就可以修改原内容数据。
- Golang的引用类型包括slice、map、channel。他们有复杂的内存结构，除了申请内存外，还需要初始化相关属性。内置函数new 计算类型大小，为其分配零值内存，返回指针。而make会被编译器翻译成具体的创建函数，由其分配内存和初始化成员结构，返回对象而非指针。

### 25.Golang Map 底层实现

- Golang中map的底层实现是一个散列表，因此实现map的过程实际上就是实现散列表的过程。在这个散列表中，主要出现的结构体有两个，一个叫hmap，一个叫bmap。

### 26.Golang Map如何扩容

- 装载因子：count/2^b
- 触发条件：

	- 1.装填因子是否大于6.5
	- 2.overflow bucket 是否太多

- 解决方法：

	- 双倍扩容：扩容采取了一种称为“渐进式”地方式，原有的key并不会一次性搬迁完毕，每次最多只会搬迁2个bucket
	- 等量扩容：重新排列，极端情况下，重新排列也解决不了，map成了链表，性能大大降低，此时哈希种子hash()的设置，可以降低此类极端场景的发生。

### 27.Golang Map 查找

- 哈希查找表，由一个Key通过哈希函数得到哈希值，64位系统中就生成一个64bit的哈希值，由这个哈希值将key对应到不同的桶（bucket）中，当有多个哈希映射到相同的桶中时，使用链表解决哈希冲突。key经过hash后共64位，根据hmap中B的值，计算它到底要落到哪个桶时，桶的数量为2^B,如B=5，那么用64位最后5位表示第几号桶，在用hash值的高8位确定在bucket中存储位置，当前bmap中的bucket未找到，则查询对应的overflow bucket，对应位置有数据则对比完整的哈希值，确定是否是要查找的数据。
- 如果两个不同的key落在同一个桶上，hash冲突使用链表法解决，遍历bucket中的key如果当前处于map进行了扩容，处于数据搬移状态，则优先从oldbuckets查找。

### 28.介绍一下Channel

- 不要通过共享内存来通信，而要通过通信来实现内存共享。Go的CSP并发模型，中文可以叫做通信顺序进程，是通过goroutine和channel来实现的。
- channel收发遵循先进先出FIFO，分为有缓存和无缓存，channel中大致有buffer（当缓冲区大小部位0-时，是个ring buffer）、sendx和recvx收发的位置、sendq、recvq当前channel因为缓冲区不足而阻塞的队列、使用双向链表存储、还有一个mutex锁控制并发、其他原属等。

### 29.Go语言的Channel特性？

- 给一个nil channel发送数据，造成永远阻塞
- 从一个nil channel接收数据，造成永远阻塞
- 给一个已经关闭的channel 发送数据，引起panic
- 从一个已经关闭的 channel接收数据，如果缓冲区中为空，则返回一个零值
- 无缓冲的channel同步的，而有缓冲的channel是非同步的。
- 关闭一个nil channel 将会发生 panic

### 30.Channel 的ring buffer 实现

- channel中使用了ring buffer(环形缓冲区)来缓存写入的数据。ring buffer 有很多好处，而且非常适合用来实现FIFO 式的固定长度队列。
- 在channel中，ring buffer的实现如下

	- recvx sendx

## Go并发编程

### 0.如何解决资源的并发问题？

- 被保护起来的程序，叫做临界区。
- 使用Mutex互斥锁，限定临界区只能被一个线程资源拥有。
- Mutex是使用最广泛的同步原语
- 同步原语适用的场景

	- 共享资源。并发地读写资源，所以需要Mutex、RWMutex这种同步原语来保护。
	- 任务编排。需要Goroutine按照一定的规律执行，而goroutine之间有相互等待或者依赖的顺序关系。我们往往使用channel或waitGroup来实现。
	- 消息传递。goroutine的线程安全，往往采用Channel来实现。

- go tool compile trace -S 

	- 通过编译时，插入一些命令，检测并发读写，从而发现data race

### 1.Mutex几种状态

- mutex Locked 表示互斥锁的锁定状态
- mutexWoken 表示从正常模式被从唤醒
- mutexStarving 当前的互斥锁进入饥饿状态
- waitersCount 当前互斥锁上等待的Goroutine 个数

### 2.Mutex正常模式和饥饿模式

- 正常模式（非公平锁）

	- 正常模式下，所有等待锁的goroutine按照FIFO（先进先出）顺序等待。唤醒的goroutine不会直接拥有锁，而是会和新请求锁的goroutine竞争锁的拥有。
	- 新请求锁的Goroutine具有优势:

		- 它正在CPU上执行，而且可能有好几个，所以刚刚唤醒的Goroutine有很大可能在锁竞争中失败。在这种情况下，这个被唤醒的Goroutine会加入到等待队列的前面。如果一个等待的goroutine超过1ms没有获取锁，那么它将会把锁变为饥饿模式。

- 饥饿模式（公平锁）

	- 为了解决了等待G队列的长尾问题，饥饿模式下，直接由unlock把锁交给等待队列中排在第一位的G（队头），同时，饥饿模式下，新进来的G不会参与抢锁也不会进入自旋状态，会直接进入等待队列的尾部，这样很好的解决了老的g一直抢不到锁的场景。
	- 饥饿模式的触发条件，当一个G等待锁时间超过1毫秒时，或者当前队列只剩下一个g的时候，Mutex切换到饥饿模式。

- 总结

	- 对于两种模式，正常模式下的性能是最好的，goroutine可以连续多次获取锁，饥饿模式解决了取锁公平的问题，但是性能会下降，其实是性能和公平的一个平衡模式。

### 3.Mutex允许自旋的条件

- 1.锁已被占用，并且锁不处于饥饿模式。
- 2.积累的自旋次数小于最大自旋次数（active_spin=4）
- 3.cpu核数大于1
- 4.有空闲的P
- 5.当前Goroutine所挂载的P下，本地待运行队列为空。

### 4.RWMutex实现

- 通过记录readerCount读锁的数量来进行控制，当有一个写锁的时候，会将读锁数量设置为负数1<<30.目的是让新进入的读锁等待写锁之后释放通知读锁。同样的写锁也会等待之前的读锁都释放完毕，才会开始进行后续的操作。而等写锁释放之后，会将值重新加入1<<30，并通知刚才新进入的读锁(rw.readerSem),两者互相限制。

### 5.RWMutex注意事项

- 分析

	- 当写少读多，使用Mutex串行浪费资源。改用RWMutex可以提高并发性能。
	- Goroutine持有锁，就是排外锁，会导致其他Goroutine的读写被阻塞。
	- RWMutex就是用来解决reader-writer的问题。

- 什么是RWMutex？

	- RWMutex是一个标准的reader/writer互斥锁。
	- 5个方法

		- Lock/Unlock:写操作时调用的方法。

			- 当读操作，Lock就会阻塞

		- RLock/Runlock:读操作时调用的方法。

			- 当写操作，RLock就会阻塞

		- 返回一个Locker对象。

			- Lock返回一个RLock方法。
			- UnLock返回一个RUnLock方法。

- 陷阱

	- 不可复制。

		- 永远等待。

	- 重入导致死锁

		- 基于互斥锁实现的并发内部访问。
		- 运行时，容易检测出来。

	- 释放未加锁的RWMutex

- 流行中的

	- docker中

		- 错误把Lock当做RLock使用。

	- k8s

		- GetCPUSetOrDefault,释放pod的时候，引用的方法存在请求读锁。
		- 但是上下文代码也使用了锁。

- 特性

	- RWMutex是单写多读锁，该锁可以加多个读锁或者一个写锁。
	- 读锁占用的情况下会阻止写，不会阻止读，多个goroutine可以同时获取读锁。
	- 写锁会阻止其他goroutine（无论读和写）进来，整个锁由该Goroutine独占。
	- 适用于读多写少的场景。
	- RWMutex类型变量的零值是一个未锁定状态的互斥锁。
	- RWMutex在首次被使用之后就不能再拷贝。
	- RWMutex 的读锁或写锁在未锁定状态，解锁操作都会引发panic。
	- RWMutex 的一个写锁Lock去锁定临界区的共享资源，如果临界区的共享资源已被（读锁或写锁）锁定，这个写锁操作的Goroutine将被阻塞直到解锁。
	- RWMutex的读锁不要用于递归调用，比较容易产生死锁。
	- RWMutex的锁定状态与特定的goroutine没有关联。一个goroutine可以RLock(Lock),另一个goroutine可以RWnlock
	- 死锁被解锁后，所有因操作锁定读锁而被阻塞的Goroutine会被唤醒，并都可以成功锁定读锁。
	- 读锁被解锁后，在没有被其他读锁锁定的前提下，所有因操作锁定写锁而被阻塞的goroutine，其中等待时间最长的一个goroutine会被唤醒。

### 6.Cond是什么

- 限定容量的队列

	- 当阻塞时，利用等待/通知机制。实现阻塞和唤醒。

- 标准库的Cond

	- 应用等待某个条件的一组goroutine，等变为true，其中一个Goroutine或其他Goroutine被唤醒。
	- 某个条件相关，这个条件需要一组Goroutine协作共同实现。
	- 开发过程中，很少使用Cond，一般情况下使用channel来解决。

- Cond的基本用法

	- 实现

		- Signal

			- 允许使用唤醒一个等待Cond的goroutine

		- Broadcast

			- 允许所有Caller唤醒所有等待Cond的goroutine

		- Wait

			- 把Caller放入Cond阻塞，唤醒Signal或者BroadCast

	- 知名使用

		- 同样的场景我们会使用其他同步原语来替代。例如:channel同样具有唤醒、通知。而不是使用Cond的wait/notify机制。
		- 对于简单的wait/notify场景，可以使用waitGroup来实现。而且不容易出错

- 特性

	- Cond实现了一种条件变量，可以使用在多个Reader等待共享资源ready的场景（如果只有已一个读写，一个锁或者channel就可以搞定了）
	- 每个Cond都会关联一个Lock（*sync.Mutex or *sync.RWMutex），当修改条件或者调用Wait方法时，必须加锁，保护condition

### 7.Broadcast和Signal区别

- Braodcast会唤醒所有等待的c的Goroutine
- Signal只唤醒1个等待c的Goroutine

	- 调用Signal可以加锁也可以不加锁

### 8.Cond中Wait使用

- Wait()会自动释放c.L，并挂起调用者的Goroutine。之后恢复执行，Wait()会在返回时对c.L加锁
- func (c *Cond) Wait()

### 9.WaitGroup用法

- 作用

	- 解决一个并发-等待问题

		- 现在有一个GoroutineA在等待其他goroutine完成任务，如果没有全部完成，那么goroutineA就会阻塞在检查点，直到所有Goroutine完成。

- 基本用法

  package main
  
  import (
      "fmt"
      "sync"
      "time"
  )
  
  // 线程安全的计数器
  type Counter struct {
      mu  sync.Mutex
      count uint64
  }
  
  // 对计数值加一
  func (c *Counter) Incr() {
      c.mu.Lock()
      defer c.mu.Unlock()
      c.count++    
  }
  
  // 获取当前的计数值
  func (c *Counter) Count() uint64 {
      c.mu.Lock()
      defer c.mu.Unlock()
      return c.count
  }
  
  // sleep 睡眠1秒 ，然后计数加1、
  func worker(c *Counter, wg *sync.WaitGroup){
      defer wg.Done()
      time.Sleep(time.Second)
      c.Incr()
  }
  
  func main() {
      var counter Counter
      var wg sync.WaitGroup
      wg.Add(10)
      for i := 0; i < 10; i++ {
          go worker(&counter, &wg)
      }
      // 检查点，等待goroutine任务完成
      wg.Wait()
      // 输出当前计数器的值
      fmt.Println(counter.count)
  }

	- func (wg *waitGroup) Add(delate int)
func (wg *waitGroup) Done()
func (wg *waitGroup) Wait()

		- Add 用来计数值
		- Done用来减1，其实就是Add(-1)
		- Wait 调用这个Goroutine一直阻塞，直到waitGroup计数为0

	- 实现

		- 去看源码

- 需要注意的地方

	- 场景1：计数器的值为-1

		- 负值就会panic
		- 1.传入Add(负值)，要保障计数器>= 0
		- 2.调用Done方法过多，超过了waitGroup的计数值
		- 预先计算好Add次数，再给相同的Done

	- 场景2：不期望的Add时机

		- 等所有Add调用之后，再调用Wait，否则会panic
		- 另外的法子，再调用go之前Add

	- 场景3：前一个Wait还没结束就重用WaitGroup

		- waitGroup是可以重用的
		- 需要等上一轮go并发完成后，再重用。否则，panic

	- 辅助vet检查。静态检测

- 流行的Go项目遇到的坑

	- 违反了wg复制规则
	- K8s忘记在wait之前增加计数

- 避免错误发生

	- 不重用waitGroup
	- 保证所有Add方法都在wait之前
	- 不做多余的Done调用
	- 不遗漏Done方法调用

- 使用方法

	- main协程通过调用wg.Add(delta int)设置worker协程的个数，然后创建worker协程
	- worker协程执行结束之后，都要调用wg.Done()
	- main协程调用wg.Wait()且被block，直到所有worker协程全部执行结束后返回。

### 10.WaitGroup实现原理

- WaitGroup主要维护了2个计数器，一个是请求计数器v，一个是等待计数器w，二者组成一个64bit值，请求计数器占高32bit，等待计数器占低32bit。
- 时通过信号量唤醒wait()

### 11.什么是sync.Once

- 为什么使用Once？

	- Once执行且仅仅执行一次动作，常常用于单例对象的初始化场景

- 一般单例的场景有性能问题

  package main
  
  import (
    "net"
    "sync"
    "time"
  )
  
  // 使用互斥锁保证线程(goroutine)安全
  var connMu sync.Mutex
  var conn net.Conn
  
  func getConn() net.Conn {
    connMu.Lock()
    defer connMu.Unlock()
  
    // 返回已创建好的连接
    if conn != nil {
      return conn
    }
  
    // 创建连接
    conn, _ = net.DialTimeout("tcp", "baidu.com:80", 10*time.Second)
    return conn
  }
  
  // 使用连接
  func main() {
    conn := getConn()
    if conn == nil {
      panic("conn is nil")
    }
  }

	- 每次都要得竞争锁才能使用此资源

- 使用场景

	- sync.Once只暴露一个方法Do，你可以多次调用Do方法，但是只有使用一次Do方法时，f参数才会执行。这里的f是一个无参数无返回值的函数。

		- func (o *Once) Do(f func())

	- 常常用来初始化单例资源，或者并发只需要一次的共享资源、或测试需要一次的资源。

	  var addr = "baidu.com"
	  
	    var conn net.Conn
	    var err error
	  
	    once.Do(func() {
	      conn, err = net.Dial("tcp", addr)
	    })

- 如何实现一个Once？

	- 有人认为最多使用atomic原子操作这个flag

	  type Once struct {
	    done uint32
	  }
	  
	  func (o *Once) Do(f func()) {
	    if !atomic.CompareAndSwapUint32(&o.done, 0, 1) {
	      return
	    }
	    f()
	  }

	- 一个正确的Once实现使用一个互斥锁，这样的初始化的时候如果有并发的goroutine就会进入doSlow

		- 利用双检测机制

		  type Once struct {
		    done uint32
		    m  Mutex
		  }
		  
		  func (o *Once) Do(f func()) {
		    if atomic.LoadUint32(&o.done) == 0 {
		      o.doSlow(f)
		    }
		  }
		  
		  
		  func (o *Once) doSlow(f func()) {
		    o.m.Lock()
		    defer o.m.Unlock()
		    // 双检查
		    if o.done == 0 {
		      defer atomic.StoreUint32(&o.done, 1)
		      f()
		    }
		  }

- 使用Once可能出现的错误

	- 死锁：递归Do死锁
	- 未初始化，如果f被panic

	  // Once 是一个扩展的sync.Once类型，提供了一个Done方法
	  type Once struct {
	    sync.Once
	  }
	  
	  // Done 返回此Once是否执行过
	  // 如果执行过则返回true
	  // 如果没有执行过或者正在执行，返回false
	  func (o *Once) Done() bool {
	    return atomic.LoadUint32((*uint32)(unsafe.Pointer(&o.Once))) == 1
	  }
	  
	  func main() {
	    var flag Once
	    fmt.Println(flag.Done()) //false
	  
	    flag.Do(func() {
	      time.Sleep(time.Second)
	    })
	  
	    fmt.Println(flag.Done()) //true
	  }

		- 一般使用前做检查可以规避这个问题，go vet

### 12.什么操作叫做原子操作

- atomic要保证原子操作，一定要使用这几种方法
- 原始操作的基础知识

	- sync/atomic实现了同步算法的原子的内存操作原语。
	- CPU提供了基础的原子操作

		- 不同的CPU架构甚至不同的版本提供的原子操作的指令不同。要用一种编程语言支持不同架构的原子操作有一定的难度。

	- 原子操作的应用场景

		- 使用atomic实现自己基础的并发原语
		- atomic是实现lock-free的数据结构的基石

	- atomic提供的方法

		- 操作的一个对象是一个地址，把可寻址的变量的地址作为参数传递给方法。不是值。
		- Add
		- CAS
		- SWAP
		- Load
		- Store
		- Value类型

	- 使用atomic实现Lock-Free queue

- 特性

	- 一个或者多个操作在CPU执行过程中不被中断的特性，称为原子性。这些操作对外表现成一个不可分割的整体，他们要么都执行，要么都不执行，外界不会看到他们只执行到一半的状态。
	- 而在现实世界中，CPU不可能不中断的执行一系列操作，但如果我们在执行多个操作时，能让他们的中间状态对外不可见，那我们就可以宣称套门又有了“不可分割”的原子性。
	- 在Go中，一条普通的赋值语句其实不是一个原子操作。例如，在32位机器上写int64类型的变量就会有中间状态，因为他会被拆成两次写操作（MOV）--写低32位和写高32位

### 13.原子操作和锁的区别

- 原子操作由底层硬件支持，而锁则由操作系统的调度器实现。锁应当用来保护一些逻辑，对于一个变量更新的保护，原子操作通常会更有效率，并且更能利用计算多核的优势，如果要更新的是一个复合对象，则应当使用atomic.Value封装好的实现。

## Go RUNTIME

## 微服务

## 容器技术

## Redis

## MySQl

## LINUX

## 缓存

## 网络和操作系统

## 消息队列

## 分布式

*XMind - Trial Version*