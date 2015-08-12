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


slice
----

map
----

interface
----
>既然Interface可以装任何数据类型，那它不就是一个所谓的泛型么？

func
----

struct
----

channel
----