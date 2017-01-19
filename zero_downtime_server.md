# 如何做到Zero Downtime重启Go服务?

## graceful的实践

使用endless库来实现，比如接入gin:
```
r := gin.Default()
r.GET("/", index)
endless.ListenAndServe(":3000", r)
```

我们编写一个带指定时间超时的处理函数：

```
func index(c *gin.Context) {
    duration := c.Query("duration")
    durationInt, _ := strconv.Atoi(duration)
    time.Sleep(time.Duration(durationInt) * time.Second)
    c.String(http.StatusOK, duration)
}
```

```
curl http://localhost:3000/?duration=10
```

测试时使用CTRL+C时候，会处理完所有请求才会退出；如果是后台运行，当我们获取进程的pid后，如果使用```kill -9 $PID```, endless无法catch信这个信号，需要使用```kill -s TERM $PID```

## 利用nginx upstream负载

nignx的简单配置

```
upstream graceful{
    server 127.0.0.1:8888;
    server 127.0.0.1:8889;
}

server {
    listen 8001;
    server_name localhost;

    location ^~ /{
        proxy_set_header X-Real-IP  $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header http_referer $http_referer;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://graceful/;
        proxy_redirect default;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

   location ~ /\..+ {
        deny all;
   }
}
```

作一个较为简单的负载均衡，在更新程序的时候顺序更新，当其实一个服务不再提供时，负载导到另外一台，顺序更新后，可满足服务热更新的需求,当然最好使用专门的部署脚本来实现。
