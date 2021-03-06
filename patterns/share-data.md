### 请求中数据的传递
在一个应用程序中，有不少数据是需要通用的，这个时候就将变量在不同的请求中进行公用，而且对于
`func(ResponseWriter, *Reqponse)`签名来说，其只接受连个参数，无法通过函数参数进行数据的传递
因此为了进行数据的传递，我们可以通过实现一下方式进行传递

#### 通过结构体，进行数据的传递
使用结构体以及`http.HandlerFunc`函数签名,实现数据共享
```go
type MyHandler struct {
    MyDB *sql.DB
}

func (m MyHandler) IndexHandler(w http.ResponseWriter, r *http.Request){
    //TODO
}

func main(){
    db := MysqlConnect()
    myHandler := Myhandler{MyDB: db}
    http.HandlerFunc("/", myHandler.IndexHandler)
    http.ListenAndServe(":8080", nil)
}
```

#### 将数据写入请求中，使得变量在整个请求的生命周期中可用
通过使用类似于`gorilla/context`包，将需要传递的数据写入请求，然后就可以在后续的句柄中使用该数值
```go
func Logging(h http.Handler)http.Handler{
    return http.HandlerFunc(function(w http.ResponseWriter, r *http.Request){
        log.Println("before")
        context.Set(r, "database", *sql.DB)
        h.ServeHTTP(w,r)
        log.Println("after")
    })
}

func IndexHandle(w http.ResponseWriter, r *http.Request){
    database, ok := context.Get(r, "database")
    if !ok {
        //DO SOMETHING
    }
}
```