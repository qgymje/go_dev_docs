GOLANG_TIPS
=====

[参考资料](http://talks.golang.org/2012/10things.slide#1)

匿名struct
----
####将全局变量包起来,只有一个全局变量

```
var DBconfig struct{
	HOST string
	DATABASE_NAME string
	//...
}

DBconfig.HOST = "localhost"
DBconfig.DATABASE_NAME = "database_name"

```
使用var定义时:

```
var (
	db_host = "localhost"
	db_dbname = "database_name"
)
```
好处是减少全局变量

####临时数据
```
data := struct{
	Title string
	User []*User
}{
	title,
	users,
}

//传给模板的数据
err := tmpl.Execute(w,data)
```
相比较```map[string]interface{}```,更高效更安全


嵌入struct
----

watiGourp
----
```
finish := make(chan bool)
        var done sync.WaitGroup
        done.Add(1)
        go func() {
                select {
                case <-time.After(1 * time.Hour):
                case <-finish:
                }
                done.Done()
        }()
        t0 := time.Now()
        finish <- true // 发送关闭信号
        done.Wait()    // 等待 goroutine 结束
        fmt.Printf("Waited %v for goroutine to stop\n", time.Since(t0))
```

