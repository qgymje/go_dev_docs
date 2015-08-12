gocode tips
=====

当程序退出,但还有一些任务要去完成
----
```
c := make(chan os.Signal, 1)
signal.Notify(c, os.Interrupt)
go func() {
    for sig := range c {
        log.Println(sig)
        log.Println("jobs to do...")
        time.Sleep(time.Second * 2)
        log.Println("exit")
        os.Exit(1)
    }
}()

select {}
```

mysql初始化
----
```
import (
    "database/sql"
    "log"

    _ "github.com/go-sql-driver/mysql" //mysql driver
)

//in models package global private db conn
var db *sql.DB

/*
//如果多db使用,则使用dbflag选择
func getDB(flag string) {
    return db
}
*/

func init() {
    var err error
    db, err = sql.Open("mysql", "root:123456@tcp(127.0.0.1:3306)/cartype_pool?charset=utf8")
    if err != nil {
        panic(err.Error())
    }

    //设置最大连接数
    db.SetMaxOpenConns(1000)

    err = db.Ping()
    if err != nil {
        panic(err.Error())
    }

    //set log flag
    log.SetFlags(log.LstdFlags | log.Lshortfile)
    log.Println("db inited")
}
```

mysql select
----
```
rows, err := db.Query(sql)
if err != nil {
    return nil, err
}
defer rows.Close()
result := make([]*CarmodelShort, len(ids))

i := 0
for rows.Next() {
    c := &CarmodelShort{}

    err := rows.Scan(&c.IDKYX, &c.IDLY, &c.FactoryName, &c.BrandName, &c.SeriesName, &c.ModelName, &c.SaleName, &c.YearType, &c.FuelTyep, &c.FuelLabel, &c.MaxHousePower)

    if err != nil {
        log.Println(err)
        return nil, err
    }
    result[i] = c
    i++
    //result = append(result, c)
}
return result, nil

```

mysql exec
----
```
    stmt, err := db.Prepare(sqlInsertCarQuery)
    if err != nil {
        return err
    }
    defer stmt.Close()

    res, err := stmt.Exec(query)
    if err != nil {
        return err
    }

    if _, err := res.RowsAffected(); err != nil {
        return err
    }
```

md5
----
```
func Calmd5(text string) string {
    hashMd5 := md5.New()
    io.WriteString(hashMd5, text)
    return fmt.Sprintf("%x", hashMd5.Sum(nil))
}

func GenSign() string {
    hashMd5 := md5.New()
    //Md5(ver+channelid+timestamp+key)
    text := fmt.Sprintf("%s%d%d%s", APIVERSION, CHANNELID, time.Now().Unix(), APPKEY)
    hashMd5.Write([]byte(text))
    //io.WriteString(hashMd5, text)
    //r := fmt.Sprintf("%x", hashMd5.Sum(nil))
    r := hex.EncodeToString(hashMd5.Sum(nil))
    return strings.ToUpper(r)
}
```

格式化时间
----
```
format := "2006-01-02-15:04:05" //1月2号下午3点4分5秒2006年,这是典型的英国人思维
fmt.Println(time.Now().Format(format))

format2 := "2006-01-02-15:04:05"
t := time.Unix(1500227728, 0)
fmt.Println(t.Format(format2))
```

设置日志输出
----
```
log.SetFlags(log.LstdFlags | log.Lshortfile)
log.Println("db inited")
```

排序
----
```
type ByLength []string

func (s ByLength) Len() int {
    return len(s)
}

func (s ByLength) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

func (s ByLength) Less(i, j int) bool {
    return len(s[i]) < len(s[j])
}

func sortByLength() {
    sth := []string{"abcd", "abc", "abcde"}
    sort.Sort(ByLength(sth)) //这里有一个类型转换操作
}
```

IP地址转数字
----
```
func Ip2long(ipstr string) (ip uint32) {
    r := `^(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})`
    reg, err := regexp.Compile(r)
    if err != nil {
        return
    }
    ips := reg.FindStringSubmatch(ipstr)
    if ips == nil {
        return
    }

    ip1, _ := strconv.Atoi(ips[1])
    ip2, _ := strconv.Atoi(ips[2])
    ip3, _ := strconv.Atoi(ips[3])
    ip4, _ := strconv.Atoi(ips[4])

    if ip1 > 255 || ip2 > 255 || ip3 > 255 || ip4 > 255 {
        return
    }

    ip += uint32(ip1 * 0x1000000)
    ip += uint32(ip2 * 0x10000)
    ip += uint32(ip3 * 0x100)
    ip += uint32(ip4)

    return
}

func Long2ip(ip uint32) string {
    return fmt.Sprintf("%d.%d.%d.%d", ip>>24, ip<<8>>24, ip<<16>>24, ip<<24>>24)
}

```

设置content-type为json
----
```
res.Header().Set("Content-Type", "application/json")
```

返回json数据
----
```
type _return struct {
    Code  int                     `json:"code"`
    Data  []*models.CarmodelShort `json:"data"`
    Total int                     `json:"total"`
    Msg   string                  `json:"msg"`
}

func returnJSON(res http.ResponseWriter, data *_return) {
    body, err := json.Marshal(&data)
    if err != nil {
        log.Println(err)
        return
    }
    res.Header().Set("Content-Type", "application/json")
    res.Write(body)
    return
}
```

匿名struct
----
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

watiGroup
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

testing
----
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

保留float小数点后几位
----
```
float64(int(untruncated * 100)) / 100//先转int* n*10 再除掉 n*10
```

empty struct channel
----
```
func main() {
    done := make(chan struct{})

    go func() {
        for {
            select {
            case <-time.After(1 * time.Hour):
                log.Println("1 hour after")
            case <-done:
                log.Println("done")
                return
            }
        }
    }()

    close(done)
    time.Sleep(1 * time.Second)
}
```

解析json
----

[stackvoerflow](http://stackoverflow.com/questions/21197239/decoding-json-in-golang-using-json-unmarshal-vs-json-newdecoder-decode)
```
//如果数据量大的话,使用流式解析先Open一个资源
err = json.NewDecoder(io.Reader).Decode(&configs)
//如果数据量不大的话直接UnMarshal
```

HTTP POST
----

[request with json](http://stackoverflow.com/questions/24455147/go-lang-how-send-json-string-in-post-request)
```
[response with json](http://stackoverflow.com/questions/15672556/handling-json-post-request-in-go)
func HttpPost(url string, jsonStr string) (string, error) {
    req, err := http.NewRequest("POST", url, bytes.NewBuffer(jsonStr))
    req.Header.Set("Content-Type", "application/json")

    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        return nil, err
    }
    return string(body), nil
}
```

Cookie
[check](http://liubin.org/2013/06/04/cookie-process-in-go-lang/)
----
```
expire := time.Now().AddDate(0, 0, 1)
cookie := http.Cookie{Name: "testcookiename", Value: "testcookievalue", Path: "/", Expires: expire, MaxAge: 86400}
 
http.SetCookie(w, &cookie)

var cookie,err = req.Cookie("testcookiename")
var cookievalue = cookie.Value
```
