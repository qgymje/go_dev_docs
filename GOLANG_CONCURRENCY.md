GO并发编程
=====

一些资源:

[Go Concurrency Pattern](https://www.youtube.com/watch?v=f6kdp27TYZs)

[Concurrency Is Not Parallelism](https://www.youtube.com/watch?v=cN_DpYBzKso)

[Advanced Go Concurrency Patterns](https://www.youtube.com/watch?v=QDDwwePbDtw)

[Origins of Go Concurrency style by Rob Pike](https://www.youtube.com/watch?v=3DtUzH3zoFo)


slides:

[go concurrency pattern](http://present.go-steel-programmers.org/real_world_concurrency/2013-04-25.slide#1)

[并发不是并行-zhcn](http://www.vaikan.com/docs/Concurrency-is-not-Parallelism/#hello)

显著特点：

1. 实现并行的算法
2. 利用多核

传统并发：

[知乎：多线程有什么用？](http://www.zhihu.com/question/19901763)


go的解决方案：

1.	CSP, a model of concurrency in which values are passed between independent activites(goroutines) but variables are for the most part confined to a single activity.
2.	goroutine, each concurrently executing activity
3. channel, if goroutines are the activites of a concurrent Go program, channels are the connections between them.
4.	gc

>目的是简化程序员的负担，直接从需要实现的功能考虑设计，而不是从底层锁级别考虑设计

关键概念
----
并发的概念是将一个处理过程切分成几块，让每一个协程各自负责一块，将主函数也当作一个协程，各个协程完成任务，或者将任务都完成后统一处理——有点类似**map-reduce**的概念


常见问题：
####程序无结果(提前退出)
>在写测试并发程序时经常发现，程序执行完没有任何结果，那是因为协程已经结束，其它协程也会自动退出。

####死锁(无法退出)
>1. 所有的协程都已经完成了任务，但是主协程无法获知任务协程的状态
2. 当两个不同的协程都锁定了自己的资源并且去获取对方的资源时，会发生deadlock(这在[mysql](http://hedengcheng.com/?p=844)里蛮常见的)

#####解决办法：
1. 让主协程等待一个done channel，用于判断任务协程是否都已经完成了任务
2. sync.WaitGroup

通道
----
默认是双向的，但也可以指定方向，比如:

```
c := make(chan<- int)//此通道只能发送数据，不能向此通道写入数据？why?
c1 := make(<-chan int)
```
>好处是提供编译检查 [example](https://gobyexample.com/channel-directions)


####线程安全
在channel里传输bool,int,float,string都是线程安全的，但是pointer,slice,map都不是线程安全，因为指针保存的内存地址或者引用的值可能在接收前已经被修改了，因此，保证这些值都只能被一个协程访问到——串行，除非这个指针指向的值的所有方法都不会修改这个值的内容
>[指针与引用的区别](http://golang.org/doc/faq#Pointers)

在channel里传递接口类型的值

解决办法:

1. 串行互斥
2. 规定界限
3. 所有的方法都不能修改值

[一个栗子](http://play.golang.org/p/rHiGzefLQY)

####一些小总结
>
* 无缓冲的channel是阻塞的,有大小的channel是不阻塞的,直到数据写满
* go语句马上返回,相当于在shell里执行一个命令后面加上&一样
* select{}默认是阻塞的,但加上default之后如果没有收到消息,则会执行default
* 当select{}分支里有超时(time.After()是一个channel),则没有接收到消息之后,执行超时分支
* 当for...range一个channel时,如果channel被close了,那么这个for就退出了
* channle是有方向的,可以通过规定channel的数据流向,compiler可以检查出错误
* channel的类型除了可以是interface{},当然也可以是struct{}
* 当一个select有default分支的时候,是比没有default分支的节省cpu资源的
* 如果channel里的值是引用或者指针类型,则在并发中必须保证访问这些值是串行的,或者是只读的
* 在发起channel流程中关闭channel,否则很大可能导致deadlock


####补充