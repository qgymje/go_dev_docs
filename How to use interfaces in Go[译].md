如何使用Go的接口
=====
[原文连接](http://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go)
	

在我开始写Go前, 我写Python, 我发现无法深刻理解Go的interface的概念; 基本使用是简单的, 我知道如何使用标准库里的接口, 但在了解如何设计自己的接口前, 还是练习了很久.在这里, 我将讨论Go的类型系统, 解释如何有效使用接口.

接口介绍
----
啥是接口?一个接口是这么个东西: 它是一组方法, 但它还是一个类型. 我们先讨论作为一组方法的相关方面.

一般来说, 我们会用一些设计好的例子来作介绍. 让我们来用Animal作为例子吧, 因为这是一个很现实的例子. Animal是一个接口, 定义一个speak方法, 任何实现了speak方法的类型, 都是一个Animal类型. 这是Go类型系统的核心概念: 与其根据什么数据我们的类型持有来设计我们的抽象, 不如根据什么动作(actions)我们的类型可以执行.(译者注: 就是接口只能定义方法(method), 不能定义属性(property))

定义Animal接口:
```
type Animal interface {
    Speak() string
}
```
相当简单: 我们定义了一个Animal接口, 任何类型(译者注: 如果更精准一点的话, 应该是concrete type)拥有一个Speak方法, 都是可当作Animal. Speak方法无参数, 返回一个string. 在Go里,不需要写明```implements```关键字, 一个类型是否实现接口方法会自动决定. 我们来创建几个实现了Animal接口的实例:

```
type Dog struct {}

func (d Dog) Speak() string {
    return "Woof!"
}

type Cat struct {}

func (c Cat) Speak() string {
    return "Meow!"
}

type Llama struct {}

func (l Llama) Speak() string {
    return "?????"
}

type JavaProgrammer struct {}

func (j JavaProgrammer) Speak() string {
    return "Design patterns!"
}
```

呐, 我们有四种不同类型的animals了: 狗, 猫, 羊驼, 爪哇程序员(译者注:Python程序员喜欢黑Java么?). 在main()里, 创建一个包含这四种类型的slice, 然后调用Speak():

```
func main() {
    animals := []Animal{Dog{}, Cat{}, Llama{}, JavaProgrammer{}}
    for _, animal := range animals {
        fmt.Println(animal.Speak())
    }
}
```

[点击去go playgourd](https://play.golang.org/p/yGTd4MtgD5)

棒, 现在你知道如何使用接口了吧, 然后我就不多说了, 对吧? Well, 也不是, 让我们继续看一些明显的事情.

作为类型的接口
----
接口类型, 尤其是空接口, 是混乱的来源. 接口类型是一个不拥有任何方法接口. 既然没有```implements```关键字, 所有类型都至少实现了空接口, 哪怕一个类型没有任何方法, 也就是说, 如果我们写一个函数接收一个空接口值作为参数, 那么任何值都可以传递给这个函数. 请看:

```
func DoSomething(v interface{}) {
    // ...
}
```

这里就有问题了: 在DoSomething函数体里, v到底是啥类型? 刚用Go的程序员倾向于去相信v是任意类型, 但这是错的. v不是任意类型, v是interface{}类型. 哈? 当我们传一个值到Dosomething函数时, Go运行时会执行一个类型转换, 将这个值转为interface{}值. 在运行时, 所有值都只有一个类型, v的那个唯一的静态的类型就是interface{}.

这就让人困惑了: ok, 如果发生了类型转换, 




