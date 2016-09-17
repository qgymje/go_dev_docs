# Go语言AST尝试

Go语言有很多工具, goimports用于package的自动导入或者删除, golint用于检查源码中不符合Go coding style的地方, 比如全名,注释等. 还有其它工具如gorename, guru等工具. 作为工具它们都是使用go语言([查看](https://github.com/golang/tools/tree/master/cmd))开发的, 这些工具都有一个共同点就是: 读取源代码, 分析源代码, 修改或生成新代码.

## 简述

很多编程语言/库/框架等都能生成代码, 比如使用rails, 可以轻松地new一个project出来, 生成项目基本代码, 我们其为boilerplate, 或者template, 这已经习以为常了. 像ruby的动态语言通常能在运行时生成代码, 我们称之为meta programming(元编程), 比如rails的resources可以生成restful的router出来.因为是运行时动态生成, 因此可能会遇到exception, 以及性能方面有所损失.

像elixir这种编程语言的macro则比ruby的元编程方面向"前"一步, 它在编译期生成代码, 而不在运行时生成, 好处是可以生成大量的代码而对性能几乎没有太大影响. 像phoenix框架的router([查看](https://github.com/phoenixframework/phoenix/blob/master/lib/phoenix/router.ex#L375))部分, 则通过macro生成大量的函数, 利用BEAM的高效的pattern matching机制高效路由.elixir的macro是写在源代码里的, 而Go则可以分离.

Go语言可以通过reflect包同样做到ruby的运行时生成代码(比如创建对象), 但更强大的一点是, 它通过读取源码, 再修改源码, 生成新的代码.我们可以将这个过程单独写作一个工具, 这个工具可以适用于不同的项目.


## 例子

### [stringer](https://github.com/golang/tools/tree/master/cmd/stringer)
```
package game

//go:generate stringer -type=GameStatus
// 注意//与go:generate字符之间不能有空格
// GameStatus 表示比赛的状态
type GameStatus int

const (
	Unvalid GameStatus = iota
	ValidFailed
	Valid
	Register
	Start
	Running
	End
)
```
运行```go generate```会生成gamestatus_stirng.go文件, 并且实现了Stringer接口.

同样的例子在gRPC中也出现过[code](https://github.com/grpc/grpc-go/blob/master/codes/codes.go#L41), 生成的[string](https://github.com/grpc/grpc-go/blob/master/codes/code_string.go).

正如Rob Pike所说:
> *let the mechine do the work.*
> [source](https://blog.golang.org/generate)


### [gen_columns](https://github.com/qgymje/gen_columns)

很多项目在使用数据库时, 通过tag指定数据库里的字段名字, 在写SQL时, 又只能通过字符串来表示字段名, 因此如果某一个字段名修改时, 则意味着涉及到此字段的SQL都面临着修改, 而我们希望只需要修改一个地方.

有一个结构作为数据库表结构如下:
```
type User struct {
    ID int `json:"id" bson:"id"`
    Name string `json:"name" bson:"name"`
}
```

当使用这个model里的字段进行sql查询时, 通常使用:

```
map[string]interface{}{
    "id":123456,
}

```

作为查询条件, 如果当字段名更改时, 不得不修改这个map里的key值
如果能够自动生成一个结构体, 用于表示这些column name值, 那么只需修改一处:

```
map[string]interface{}{
    UserColumns.ID: 123456
}
```

*使用方法*
```
gen_columns -tag="bson" -path="./models/user.go"
```

会生成一个独立的文件, 里面的内容为:
```
package models

type _UserColumn struct {
	ID   string
	Name string
}

var UserColumns _UserColumn

func init() {
	UserColumns.ID = "id"
	UserColumns.Name = "name"

}
```

### 其它例子
* [impl](https://github.com/josharian/impl) 指定一个接口, 生成接口所需方法
* [goa](https://goa.design/) 一套用于写web service的DSL

### 总结
gen_columns是自己在项目中遇到问题所给出的解决办法, 第一版本是通过reflect做的, 总共需要好几个步骤; 使用ast做就只需在编译时多加一个go generate, 而这命令基本上可以集成在build的脚本里, 因此不需要再额外担心代码生成的问题.
让我们用Go创造更多生成代码的工具吧.
