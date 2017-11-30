### 如何编写自己的web框架

#### 组件
|作用|名称|
|:------|:------|
|路由|[mux](http://www.gorillatoolkit.org/pkg/mux)|
|中间件|[alice](https://godoc.org/github.com/justinas/alice)|
|上下文|[gorilla/context](http://www.gorillatoolkit.org/pkg/context)|
|数据校验|[validator](https://godoc.org/gopkg.in/go-playground/validator.v9)|
|日志记录|[logrus](https://github.com/Sirupsen/logrus)|
|数据库|[gorm](http://jinzhu.me/gorm/)|


#### 参考文章
- [Build You Own Web Framework In Go](https://www.nicolasmerouze.com/build-web-framework-golang/)
- [Custom Handlers and Avoiding Globals in Go Web Applications](https://elithrar.github.io/article/custom-handlers-avoiding-globals/)
- [http.Handler and Error Handling in Go](https://elithrar.github.io/article/http-handler-error-handling-revisited/)
- [How to pass arguments to router handlers in Golang using Gin web framework?](https://stackoverflow.com/questions/34046194/how-to-pass-arguments-to-router-handlers-in-golang-using-gin-web-framework)