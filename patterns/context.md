### Gorilla/context
> 在请求的生命周期中，存储并共享数据

需要注意的是`gorilla/context`与标准库中的`context.Context`不能同时使用
举例来说，可以从一个路由中提取参数并设置其值，然后在之后的处理句柄中使用该值，或者用来存储用户会话的值

#### 基础用法
1. 定义一个任意类型的key,
    ```go
    package foo

    import (
        "github.com/gorilla/context"
    )
    type key int

    const Mykey key = 0
    ```

1. 为定义好的key,设置相对应的值，并添加到请求中,其函数签名为`func(*Request, key, value)`
    ```go
    context.Set(r, Mykey, "bar")
    ```

1. 然后就可以在其他的句柄中，获取该请求信息
    ```go
    func MyHandler(w http.ResponseWriter, r *http.Request) {
        val := context.Get(r, foo.MyKey)
    }
    ```

1. 在使用完后，需要清除请求中所存储的值
    ```go
    context.Clear(r)
    ```
当然，除了`Clear(r)`方法之外，还可以使用`ClearHandler()`方法来包含`http.Handler`类型的处理句柄
如果使用了`gorilla/mux`或者`gorilla/pat`库的话，那么就无需再执行相对应的清除操作，因为对应的库里面已经进行了相应的操作

#### API
1. 清除请求中的所有值
    ```go
    func Clear(r *http.Request)
    ```

1. 在请求的生命周期最后环节，清除设置的值
    ```go
    func ClearHandler(h http.Handler) http.Handler
    ```
1. 通过key移除指定的值
    ```go
    func Delete(r *http.Request, key interface{})
    ```
1. 通过key，获取指定的值
    ```go
    func Get(r *http.Request, key interface{}) interface{}
    ```

1. 获取请求中的所有的值,如果请求错误，将会直接返回一个`nil`
    ```go
    func GetAll(r *http.Request) map[interface{}]interface{}
    ```

1. 获取请求中的所有值，并增加一个bool值来校验请求是否被注册
    ```go
    func GetAllOk(r *http.Request)(map[interface{}]interface{}, bool)
    ```
1. 获取某个值，并返回一个bool值来校验，该值是否存在
    ```go
    func GetOk(r *http.Request, key interface{})(interface{},bool)
    ```
1. 移除超过`maxAge`所限定的最大长度的值，并且返回被移除的数量，如果`maxAge < 0`那么，所有的值都将会被移除
    ```go
    func Purge(maxAge int) int
    ```

1. 设置一个值
    ```go
    func Set(r *http.Request, key, val interface{})
    ```




