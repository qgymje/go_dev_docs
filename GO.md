GOLANG开发建议
=====

环境
----
下载golang配置环境变量：

```
GOROOT go的安装目录
GOBIN go的执行目录
GOPATH 所写项目的路径，go get会自动安装在gopath的第一个路径
GOARCH 系统架构，用于编译时生成的二进制文件
GOOS 同上
```


开发
----
####开发工具

建议sublime text2，通过package install安装gosublime
设置用户自定义配置：
```
Preferences>Package Settings> Gosublime > Settings User
```

```
{
    "env": {
        "GOPATH": "~/workspace:$GS_GOPATH"
    },
    "autocomplete_builtins": true,
    "autocomplete_suggest_imports": true
}
```
gosublime会自动提示package里的公开方法，开发十分方便，并且通过```ctrl-shift+rightclick```直接中转到方法或者包的源代码。

其它还有idea + go plugin, vim + vim-golang，emacs...


####代码规范
使用go fmt与golint来格式化代码以及检查不规范的命名

####单元测试
自带go test可以实现单元测试，在持续集成里面调用go test进行测试


部署
----
####持续集成



####自动重启

使用supvervisord来保证go程序正常运行
[参考文档](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/12.3.md)

热启动[参考文档]()