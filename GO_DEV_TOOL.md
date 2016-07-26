GO_DEV_TOOL
=====

sublime text2
-----
[下载](http://www.sublimetext.com/2)

安装[package control](https://sublime.wbond.net/installation#st2)
----
打开后,按ctrl + ~打开命令行,或者在view->show console,复制如下代码,执行

```
import urllib2,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```

安装gosublime,配置
----
```
{
    "env": {
        "GOPATH": "/Users/qgm/workspace:$GS_GOPATH"
    },
    "autocomplete_builtins": true,
    "autocomplete_suggest_imports": true
}
```

安装golint
----
golint用于检测语法问题

```
go get github.com/golang/lint
```

```
golint filename
```
会提示一些规范错误,可将其部署到gosublime或者fswatch中去


安装goimporter
----
```
go get code.google.com/p/go.tools/cmd/goimports
```
编译好之后,设置一个ln -s到$GOBIN目录(或者复制过去),并且将gosublime的user-setting添加一行

```
"fmt_cmd": ["goimports"]
```
>注:ln -s 需要绝对路径


fswatch热编译
----
```
go get github.com/codeskyblue/fswatch
```
build好之后连接到/usr/bin/fswatch
在需要目录执行fswatch,生成.fswatch.json
然后在comman里写需要执行的命令,参考如下


```
{
    "paths": [
        "."
    ],
    "depth": 2,
    "exclude": [],
    "include": [
        "\\.(go|py|php|java|cpp|h|rb)$"
    ],
    "command": [
        "bash",
        "-c",
		"pgrep server | xargs kill && golint ./ && go build server.go && ./server"
    ],
    "env": {
        "POWERD_BY": "github.com/codeskyblue/fswatch"
    },
    "kill-signal": "KILL"
}
```
>注意,faswtch不会自动杀掉运行的那个进程,因此需要手动杀一下,再研究一下

>将golint并入到监控目录中去,方便修改内容

supervisor
----
```
[program:vehiclestat]
command=/Users/qgm/workspace/vehicleStat/src/vehicleStat/vehicleStat
autostart = true
autorestart = true
startsecs = 5
user = root
redirect_stderr = true
stdout_logfile = /var/log/supervisord/vehicleStat.log
```

其它
----
设置当前目录为$GOPATH

```
alias gopath='export GOPATH=$GOPATH:`pwd` && echo $GOPATH'
```


