### gorilla/mux



### 基础知识

#### URL标识

对于一个请求来说，其构成将包含一下几个部分

```go
http://www.example.com:80/path/to/myfile.html?key1=value1&key2=value2#SomewhereInTheDocument
```

##### 协议部分

在此处的协议部分指的是`http://`,所以常见的协议的方案如下:

|     协议      |    描述     |     协议     |     描述      |
| :---------: | :-------: | :--------: | :---------: |
|    data     | Data URIs |    file    | 指定主机上的文件名称  |
|     ftp     |  文件传输协议   | http/https |   超文本传输协议   |
|   mailto    |  电子邮件地址   |    ssh     |  安全的shell   |
|     tel     |    电话     |    urn     |   统一资源名称    |
| view-source |  资源的源代码   |   ws/wss   | websocket链接 |

##### 主机（域名）

主机指代的是网络上的地址，示例中的主机为`www.example.com`

##### 端口

对于常见的`http`协议，其端口通常为`80`, 而对于`https`协议而言，其端口号一般为`443`

##### 路径

路径为web服务器上资源的位置，示例中的路径为:`/path/to/myfile.html`

##### 查询

提供给web服务器的额外参数，通常由`&`分割符分割的k/v键值对

##### 片段

片段(也称作锚点)，标识的是资源的某一个部分

#### 请求头

##### 请求方式

1. `CONNECT` 开启客户端与所请求资源之间的双向沟通的通道
2. `DELETE` 删除指定的资源
3. `GET` 请求指定的资源
4. `HEAD` 请求资源的头部信息
5. `OPTIONS` 获取目的资源所支持的通信选项
6. `PATCH` 对资源的部分内容进行修改
7. `POST` 创建资源
8. `PUT` 新增或者替换资源

### 包的使用

> mux包就是对上面做出的基础只是进行相对应的控制，因此可以通过如下部分进行分解

#### 请求方式的控制

通过使用`Methods`方法，对请求的路由进行请求方式的控制

```go
r.Mehtods("GET", "POST") //该方法接受一个或者多个参数（...）
```

#### 请求协议的控制

使用`Schemes`来对请求的协议进行控制

```go
r.Schemes("https", "http") //该方法也是接受一个或者对各参数(...)
```

#### 对域名进行限定

使用`Host`方法，对域名进行限定

```go
r := mux.NewRouter()
r.Host("www.example.com") //对域名进行限定
r.Host("{subdomain:[a-z]+}.domain.com") //使用匹配符对域名进行限定
```

#### 对路径进行限定

1. 对于同一功能的路由来说，我们的请求路径中有一部分是相同的，所以可以使用`路由分组`来对代码的进行组织

   ```go
   r := mux.NewRouter()
   s := r.PathPrefix("/article").Subrouter()
   s.HandleFunc("/index",ArticleIndexHandler) // /article/index
   s.HandleFunc("/show/{id:[0-9]+", ArticleShowHandler) // /article/show/12
   ```

2. 路径的某些部分是动态的，这些动态的部分可以使用`{name}`或者`name:pattern`的格式来进行限定

   ```go
   //使用`{name}`的格式,为路径定义了一个key,便于在处理器中进行使用
   r.HandleFunc("/products/{key}", ProductHandler)

   //使用{name:pattern}格式，以便对于路径进行更加严格的控制
   r.HandleFunc("article/{id:[0-9]+}", ArticleHandler)

   //还可以对路径的选值范围进行控制,如下限定了sort的值范围是：asc, desc或者new
   r.HandleFunc("/article/{category}/{sort:(?:asc|desc|new)}")

   //如果只是使用了{name}的模式，那么name将会匹配任何的类型，直到遇到下一个反斜杠
   r.HandleFunc("/articles/{category}/", ArticlesCategoryHandler)
   r.HandleFunc("/articles/{category}/{id:[0-9]+}", ArticleHandler)
   ```


#### 对查询参数进行限制

```go
r.Queries("key", "value") //可以设定一对或者多对参数
```



#### 对请求头进行限制

```go
r.Headers("X-Requested-With", "XMLHttpRequest") //可以设定一对或者多对参数
```

#### 其他限制

1. 可以对路由进行命名，这样就可以使用`URL`方法获取请求路由的参数值

   ```go
   r.HandleFunc("articles/{category}/{id:[0-9]+", ArticleHandler).Name("article")

   //在处理器中可以如下使用，将会产生路由地址："/articles/technology/42"
   //不仅仅对于路径，对于查询参数以及子域名都是适用的
   func ArticleHandler(w http.ResponseWrite, r *http.Request){
     url, err := r.Get("article").URL("category", "technology", "id", "42")
   }
   ```

   ​

### 参考文档

- [Identifying_resources_on_the_Web](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web)
- [gorilla/mux doc](http://www.gorillatoolkit.org/pkg/mux)