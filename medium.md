### Golang中间键
> 该文章节选自[Mat Ryer-Medium](https://medium.com/@matryer/the-http-handler-wrapper-technique-in-golang-updated-bc7fbcffa702)

```go
func log(h http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		fmt.Println("Before")
		h.ServeHTTP(w, r)
		fmt.Println("After")
	})
}
```

> 以上函数接受一个`http.Handler`参数，并在返回一个新的`http.Handler`之前或者之后进行一些操作

一个中间键类似包含如下的签名
```go
func(http.Handler)http.Handler
```

#### 在什么情况下使用中间件
- 日志以及追踪
- 请求验证
- 编写通用的响应头部

#### 访问控制
```go
func checkAPIKey(h http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		if len(r.URL.Query().Get("key")) == 0 {
			http.Error(w, "missing key", http.StatusUnauthorized)
			return
		}
		h.ServeHTTP(w, r)
	})
}
```

#### Deferring
```go
func log(h http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		fmt.Println("Before")
		h.ServeHTTP(w, r)
		fmt.Println("After")
	})
}
```
利用`Defer`声明在goroutine发生panic后仍然可运行的特性，来捕获程序发生的异常

#### 在中间件中添加自定义参数
```go
func MustParams(h http.Handler, params ...string) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		q := r.URL.Query()
		for _, param := range params {
			if len(q.Get(param)) == 0 {
				http.Error(w, "missing"+param, http.StatusBadRequest)
				return
			}
		}
		h.ServeHTTP(w, r)
	})
}
```
通过预先设置的参数列表，来限制请求,然后我们就可以如下进行调用
```go
http.Handler("/user", MustParams(handleUser, "key", "auth"))
```

#### 在中间件中使用中间件
```go
func MustAuth(h http.Handler) http.Handler {
	checkauth := http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		err := validateAuth(r.URL.Query().Get("auth"))
		if err != nil {
			http.Error(w, "bad auth param", http.StatusUnauthorized)
		}
		h.ServeHTTP(w, r)
	})
	return MustParams(checkauth, "auth")
}
```

#### 在全局范围之内使用
```go
routes := mux.NewRouter()
http.ListenAndServe(addr, log(serve))
```

