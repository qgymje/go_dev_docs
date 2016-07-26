golang语言笔记
=====

testing
-----
比如有一个pkga里有一个文件叫做m1.go,要对m1里的方法写单元测试,则需要新建立一个文件叫做m1_test.go

```
package pkga

import "testing"
```
任何测试方法需要以Test大写开始,最好加一个下划线,再加上函数名:

```
func Test_Add(t *testing.T){
	t.Error("your error mesg")
}
```
通过t.Log表示通过,t.Error表示测试失败

压力测试与单元测试差不多,只是需要将Test换成Benchmark:

```
func Benchmark_Fib10(b *testing.B) {
	for i := 0; i < b.N; i++ {
		fib(10)
	}
}
```

写好单元测试或者压力测试后,在命令行下执行:

```
go test -v

go test -bench=.
```

time
----
时间是开发中十分必要一块，golang提供了time模块
[play.go](http://play.golang.org/p/1rGxhRwbcw)

```
t := time.Now()//返回当前时间
t.Year()//获取年份其它是Month() Day() Minute() Second()

t1 := time.Now().Unix()
t1 := time.Noew.UnixNano()

t3 := time.Date(2014,1,31,0,0,0,0,time.UTC)//返回指定时间的time

layout := "2006/01/02 15:04:05"//1234567分别对应月，天，时，分秒，年
t.Format(layout)//将t格式化

t3, _ := time.Duration("2h")
t3.Seconds()//返回此时间持续的秒数

t4 := time.Since(t3)//返回time.Duration
t4.Seconds()

```



strings
----

array
----

slice
----

map
----
1. map is a reference type
2. var m map[KeyType]ValueType zero value is nil
   > 如果要对m进行操作会panic
3. 初始化
   > m := make(map[string]int)
   > m1 := map[string]int{}
3. KeyType必须comparable, 即支持==, != 操作
4. 使用"comma ok idimo"判断key是否存在, 返回两个值, 第二个值为bool
5. map无序, 如果要排序需要一个额外的slice保存key(或者根据value排序)的顺序
6. map默认不支持并发安全操作
7. 


interface
----
1. type assertion 与 type switch的区别?
> type assertion就是知道interface的concrete type, 是一个已知的操作
> type switch是不知道interface的类型, 因此需要switch case

func
----

struct
----

channel
----
