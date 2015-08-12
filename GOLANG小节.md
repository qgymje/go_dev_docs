GOLANG小结
====
概述
----
go语言最主要的特性就是并发,并发是一种程序构建的一种方法,相比较于一般的过程式程序串行执行,并发的程序通常是很多过程同时执行,一个并发式任务函数,可能就是在一个goroutine里等待任务,执行任务,将结果发送给一个通道,而这个通道可能被其它任务函数监听着消息,一旦收到就执行任务了,这样一个程序是由多个这样的任务函数组成,每个任务函数监听某一个通道,处理数据,再将数据发送给其它通道,这种模式,叫做并发.通过消息传递共享数据.

并发基础概念
----
####goroutine
关键字go执行一个函数,会开启一个goroutine,程序接着执行下一步,效果相当于在shell下执行一个长时间运行的任务后面加了一个&一样,shell可以继续接受下一条命令;当看到代码里有```go func()```或者```go func(){}()```之后,想到的是这里开启了一个goroutine,在脑洞里想象开辟了一空间,用于执行这个方法或者匿名方法,这个空间与当前空间平行----所谓"平行空间",但是也会因为当前空间结束而结束这个"平行空间",比如main函数退出后,所有的goroutine都结束,这也是第一次用go写并发代码明明print了,但没有显示的原因,因为主空间被干掉了,其它空间都消失了.(btw,创建一个空间的感觉好像很爽的样子)

####channel
接着上面的开启一个协程就相当于开启一个空间的例子,A空间与B空间如何通信呢?在matrix里,Matrix系统与外部其它系统之间有一个火车站,trainman负责运输,这个火车站就像是一个channel,而火车上装的程序,就像channel里装的数据.channel里面可以装任何数据,func是first class, channel也是first class,所以channel里装func,装channel都不算什么事儿,重要的是它装了东西来回.
回到go的channel,当创建一个channel里面装的数据的数量只有1个的时候,它是阻塞的,什么意思?channle也是有容量的,所以装满了除了被拿掉,否则就无法做任何事情.当创建一个channel里有多个数量的时候,它会在装满后阻塞,道理是一样的,唯一一同的是,它没装装满前不阻塞.

####select
select顾名思义就是选择,它专门用于接收channl的数据,默认是阻塞的,因此可以用```select{}```让go程序当作守护进程一样持续运行着.通常有如下模式

```
for{
	select{
		case v := <-c:
			//haneld(v)
		case <-quit:
			return // or break or goto
		case <- time.After(time.Second*N)
			//超时处理
	}
}
```


####range
当for...range一个channel的时候,当channel里没有数据的时候,阻塞着的,因为在等着接收数据,因此一个协程里的处理方式,就是等着接收channle里传数据过来,然后处理,再将处理结果通过另一个channel发送出去,通常是一个返回的channel


数据类型基础
-----

####array
在go里,array是一个固定长度的,奇怪的是array是值传递,而slice的底层指向一个array

####slice
append是一个坑,当append一个slice超过其容量cap后,会创建一个新的slice,也就是说会在底层创建一个新的array

####map
通常是map[string]interface{}来使用

####struct
struct比map好的地方是它可以定义任何类型,这也是比php的array强大的地方,而一个struct里的类型可以是任何类型,可以是interface,可以是struct,可以是channel,可以是func,可以是map,slice,array,string,int,float64,它是如此地包容,以至于它是go的oop的基础,它只定义数据,但通过func的receiver可以为其定义方法

#####interface
interface底层是一个(type,value)的结构,可以使用interface{}作为类型作为参数,而可以接受任何参数,但是通过i.(type)来判断到底是哪个类型
interface作为接口只能定义action,而实现其方法的所有类型都算是is-a此接口,而这个接口可以当作类型作slice的时候,range它可以调用其定义的方法

####reflect
这是一个包,不是一个数据类型,但为何将其做为数据类型来讲,是因为它可以reflect任何类型,并且有一些方法可以操作这些被反射的类型,它就相当于一个深刻的观察者,可以洞察一切类型

函数基础
----

####first class
也就是说它能被当作参数传递,可以被return返回,可以被type定义

####clojure
一个函数可以记住定义它时的上下文变量

####高阶函数
函数返回函数


单元测试与性能测试
----
####testing


####benchmarking


####objectmocking


go工具
----

####pprof

