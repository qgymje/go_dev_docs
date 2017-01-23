# CORS golang setting

## CORS概述
1. 这是一个W3C标准，所有的CORS请求发起方都是由浏览器
2. 此标准针对服务器, 给出约束, 以符合浏览器的需求
3. 此标准是jsonp, iframe等方案的官方版

### same-origin policy
同源策略要点：同协议， 同域名， 同端口, 说好的三同，少一个都不行

### 浏览器行为

#### simple request

同时满足如下要求：
1. http method: GET POST HEAD
2. content type: application/x-www-form-urlencoded, multipart/form-data, text/plain
3. 无法使用自定义header

浏览器做了什么？
1. 浏览器会发起请求，自动添加名为Origin的header，值为网页当前域名(协议域名端口)
2. 浏览器会期待回应头带有Access-Control-Allow-Origin的值，是与当前域名一致，如果不一致，浏览器报错，js的API会走onerror的回调函数
3. 如果一致，则请求成功，拿到数据，js走onsuccess的回调函数

服务器要做什么？
1. 获取Reqeust里的Header的Origin的值，判断此域名是否符合要求，如果符合在Response头里添加Header名为Access-Control-Allow-Origin的值，为期待的域名
2. 如果没有特殊需求，此值可以设置为```*```, 但是无法设置如```*.example.com```，原因是同源策略要"三同"

#### preflight reqeust

不满足simple request的要求都发会起preflight reqeust, 它做了啥？


