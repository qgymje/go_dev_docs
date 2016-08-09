Database Go and JSON
====

在使用Go开发web项目的过程中, 数据库读写操作与JSON格式的输入输出是两块最基础的模块, Go的标准库已经帮我们做了很多, 熟悉```database/sql```与```encoding/json```这两个库能帮我们更自在地开发web应用.

但此篇文章抛开基础不说, 只说一些在开发中遇到一些真实存在的痛点.

如何处理Null值?
----
Go的一大特色就是zero value, 比如int类型的zero value是0, string为"", struct为每个field里各自类型的zero value. 因此在Go的很多ORM处理NULL值时, 都是通过zero value机制入库或出库的, 因此, 使用ORM操作的数据库, 如何没有明确指明, 基本上看不到NULL值. 一个可能为NULL的varchar字段, 存入了""(空字符串).

这里不讨论关于空字符串与NULL的优劣, 而是说明如何处理NULL值.

sql标准库里带了这么几个类型: ```sql.NullString, sql.NullInt64, sql.NullBool, sql.NullFloat64```, 如果在定义struct里field类型的时候, 使用sql.NullString代替string的话, 就可以在数据库里存入NULL值.

通过源码看到, NullString的结构与两个方法:

```go
type NullString struct{
    String string
    Valid bool
}

Scan(value interface{}) error
Value()(driver.Value, error)
```
Scan实现了```sql.Scanner```接口, Value实现了```driver.Valuer```接口. 这两个接口表示数据从Go端与DB端的转换. Scan用于数据从DB转到Go时被调用,也就是说select时用([查看源码](https://github.com/golang/go/blob/master/src/database/sql/convert.go#L202)); 而Value则由Go转到DB时被调用. 也就是说insert/update时候用([查看源码](https://github.com/golang/go/blob/master/src/database/sql/driver/types.go#L216)).

在两个不同领域做数据转换时, 通常用接口来定义, 这是接口的作用, JSON也是同样如此.

有了接口的帮助, 任何实现了这两个接口的类型, 都能在DB与Go之间做转换, 接口建立起了桥梁.

如何处理自定义类型?
----

由上一节可以想到, 如果在想要保存Go自定义类型到DB里, 只需要实现Scanner与Valuer接口, 就像```sql.NullString```一样.

典型如```NullTime```[查看源码](https://github.com/lib/pq/blob/master/encode.go#L572):
```go

type NullTime struct {
    Time  time.Time
    Valid bool
}

func (nt *NullTime) Scan(value interface{}) error {
    nt.Time, nt.Valid = value.(time.Time)
    return nil
}

func (nt NullTime) Value() (driver.Value, error) {
    if !nt.Valid {
        return nil, nil
    }
    return nt.Time, nil
}
```

为了让这个类型更好用, 添加一个帮助函数:
```go
func ToNullTime(t time.Time) NullTime {
    return NullTime{Time: t, Valid: !t.IsZero()}
}
```

自定义类型如何转化成JSON?
----

如果拿一个sql.NullString的类型, 做json.Marshal, 那么得到输出是这样的:
```go
{
    ...
    "column_name" : { "String" : "Hello?", "Valid" : true },
    ...
}
```
而我们想要的是:
```go
{
    ...
    "column_name" : "Hello?",
    ...
}
//or
{
    ...
    "column_name" : null,
    ...
}
```
我们知道, json库有两个标准的接口, ```json.Marshaler```[查看源码](https://github.com/golang/go/blob/master/src/encoding/json/encode.go#L203) , ``` json.Unmarshaler```[查看源码](https://github.com/golang/go/blob/master/src/encoding/json/decode.go#L105), 如果一个类型实现此两个接口, 则在使用json.Marshal/Unmarshal调用时, 调用此类型的自定义方法:

```go
type NullString struct {
    sql.NullString
}

func(v *NullString) MarshalJSON() ([]byte, error) {
    if v.Valid {
        return json.Marshal(v.String)
    } else {
        return json.Marshal(nil)
    }
}

func (v NullString) UnmarshalJSON(data []byte) error {
    var s *string
    if err := json.Unmarshal(data, &s); err != nil {
        return err
    }
    if s != nil {
        v.Valid = true
        v.String = *s
    } else {
        v.Valid = false
    }
    return nil
}

```

总结
----
Go语言的接口, 扮演了桥梁的角色, 连接起了Go与其它领域的数据转换, 通过实现标准库的接口, 我们可以让Go的数据类型轻松适应不同数据领域.


[参考]
http://dennissuratna.com/marshalling-nullable-string-db-value-to-json-in-go/

http://blog.carbonfive.com/2015/07/09/there-will-be-sql/

http://marcesher.com/2014/10/13/go-working-effectively-with-database-nulls/
