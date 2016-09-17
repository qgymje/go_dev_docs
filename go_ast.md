# Go语言AST尝试

Go语言有很多工具, goimports用于package的自动导入或者删除, golint用于检查源码中不符合Go coding style的地方, 比如全名,注释等. 还有其它工具如gorename, guru等工具. 作为工具它们都是使用go语言([查看](https://github.com/golang/tools/tree/master/cmd))开发的, 这些工具都有一个共同点就是: 读取源代码, 分析源代码, 修改或生成新代码.

## 生成代码

很多编程语言/库/框架等都能生成代码, 比如使用rails, 可以轻松地new一个project出来, 生成项目基本代码, 我们其为boilerplate, 或者template, 这已经习以为常了. 像ruby的动态语言通常能在运行时生成代码, 我们称之为meta programming(元编程), 比如rails的resources可以生成restful的router出来.因为是运行时动态生成, 因此可能会遇到exception, 以及性能方面有所损失.

像elixir这种编程语言的macro则比ruby的元编程方面向"前"一步, 它在编译期生成代码, 而不在运行时生成, 好处是可以生成大量的代码而对性能几乎没有太大影响. 像phoenix框架的router([查看](https://github.com/phoenixframework/phoenix/blob/master/lib/phoenix/router.ex#L375))部分, 则通过macro生成大量的函数, 利用BEAM的高效的pattern matching机制高效路由.elixir的macro是写在源代码里的, 而Go则可以分离.

Go语言可以通过reflect包同样做到ruby的运行时生成代码(比如创建对象), 但更强大的一点是, 它通过读取源码, 再修改源码, 生成新的代码.我们可以将这个过程单独写作一个工具, 这个工具可以适用于不同的项目.


## 例子
