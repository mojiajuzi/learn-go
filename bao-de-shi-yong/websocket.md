### websocket
 [gorilla/websocket](http://www.gorillatoolkit.org/pkg/websocket)的简单使用


#### 安装
```bash
$ go get github.com/gorilla/websocket
```

#### 简介

```go
var upgrader = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
}

func handler(w http.ResponseWriter, r *http.Request) {
	conn, err := upgrader.Upgrade(w, r, nil)
	if err != nil {
		log.Println(err)
		return
	}

}
```
如上所示，`conn`代表着一个Ｗebsocket连接，过使用`Upgrade.Upgrade`方法，就可以从http请求中
获取一个Websocket的连接句柄，通过使用`WriteMessage`以及`ReadMessage`两个方法，就能发送和读取消息
```go
for {
    messageType, p, err := conn.ReadMessage()
    if err != nil {
        log.Println(err)
        return
    }
    if err := conn.WriteMessage(messageType, p); err != nil {
        log.Println(err)
        return
    }
}
```
在以上的代码中，
- p代表的是一个切片，其中的值可以是二进制消息(websocket.BinaryMessage)或者文本消息(websocket.TextMessage)
- messageType是一个整型

当然，在一个应用中，可以通过使用`io.WriteCloser`和`io.Reader`两个接口来发送和接受Ｗebsocket消息
对于发送一个消息来说，可以使用`NextWrite`方法来获取一个`io.WriteClose`的实例，并将消息写入，并使用`Close`方法关闭
对于接收一个消息来说，可以使用`NextReader`方法来获取`io.Reader`的实例，并且读取信息，知道读取完毕并返回，例如：
```go
for {
    messageType, r, err := conn.NextReader()
    if err != nil {
        return
    }
    w, err := conn.NextWriter(messageType)
    if err != nil {
        return err
    }
    if _, err := io.Copy(w, r); err != nil {
        return err
    }
    if err := w.Close(); err != nil {
        return err
    }
}
```

#### 消息控制
Ｗebsocket协议定义了三种消息控制语句，`close`, `ping`, `pong`, 
调用`WriteControl`,`WriteMessage`,`NextWrite`发送方法是等价的

通过`NextReader`,`ReadMessage`或者`Read`方法向连接句柄发送关闭客户端消息，并且返回`*CloseError`

使用`SetPingHandler`以及`SetPongHandler`方法设置回调函数