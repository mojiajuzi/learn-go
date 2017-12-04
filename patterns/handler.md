### Handler

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```
一个Handler表示对一个响应的请求，在该接口定义的方法`ServeHTTP`，需要将响应的头部和数据进行处理，并写入`ResponseWriter`中
当调用ServeHTTP之后，`ResponseWriter`变量以及`Request.Body`将会是不可用的

```go
type HandlerFunc func(ResponseWriter, *Request)
```
该类型是一个适配器，允许一些普通的函数能够像HTTP handlers一样使用，如果一个函数f拥有与`HandlerFunc`拥有相同的签名
那么当调用f的时候，将会等效于调用一个Handler

### 自定义Handler
由上面两个特性，我们就可以通过以下两种方法，自定义自己的Handler实现

#### 实现Handler接口
```go
type appHandler func(http.ResponseWriter, *http.Request)(int, error)

func (fn appHandler)ServeHTTP(w http.ResponseWriter, r *http.Request) {
    //TODO
}

func myHandler(w http.ResponseWriter, r *http.Request) (int, error){
    //TODO
}

func main(){
    http.Handle("/", appHandler(myHandler))
    http.ListenAndServe(":8000", nil)
}
```

#### 使用`HandlerFunc`签名
```go
func LogingHandler(w http.ResponseWriter, r *http.Request){
    //TODO
}

func main(){
    http.HandleFunc("/", LogingHandler)
    http.ListenAndServe(":8000", nil)
}
```

###中间件的实现
对于中间件来说，其分类一般为两种类型，前置中间件以及后置中间件,前置中间件对于请求进入处理程序之前进行预处理
后置中间件对于程序运行结束后，进行一些清理处理

为了对于处理请求进行拦截，我们需要实现如下签名的函数
```go
func (http.Handler) http.Handler
```

例如，实现程序一个简单功能的日志记录
```go
func Logging(h http.Handler)http.Handler{
    return http.HandlerFunc(function(w http.ResponseWriter, r *http.Request){
        log.Println("before")
        h.ServeHTTP(w,r)
        log.Println("after")
    })
}
```

#### 参考文章
- [medium](https://medium.com/@matryer/the-http-handler-wrapper-technique-in-golang-updated-bc7fbcffa702)
- [package http](https://godoc.org/net/http#Handler)
- [http.Handler and Error Handling in Go](https://elithrar.github.io/article/http-handler-error-handling-revisited/)