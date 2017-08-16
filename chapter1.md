### 如何编写Ｇo代码

### 简介

该文档示例简单的介绍了Ｇo的包以及Ｇo tool工具的使用的标准方式比如:更新,构建;以及包的安装和命令

go tool工具需要你正确的组织Ｇo代码



### 代码组织

#### 预览

+ Go语言的所有代码都放置于一个工作空间中
+ 工作空间中包含多个版本控制的仓库
+ 每一个仓库都包含一个或者多个包
+ 每一个包都是一个文件目录，该目录下包含一个或者多个Ｇo源文件
+ 包的路径决定了文件包的导入路径



#### 工作空间

在工作空间中包含三个目录

+ src　Ｇo的源文件
+ pkg   包对象
+ bin 可执行的二进制文件

经过go tool将会把资源包编译倒pkg,将可执行的二进制文件放到bin目录

```
bin/
    hello                          # command executable
    outyet                         # command executable
pkg/
    linux_amd64/
        github.com/golang/example/
            stringutil.a           # package object
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
	hello/
	    hello.go               # command source
	outyet/
	    main.go                # command source
	    main_test.go           # test source
	stringutil/
	    reverse.go             # package source
	    reverse_test.go        # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
	bmp/
	    reader.go              # package source
	    writer.go              # package source
    ... (many more repositories and packages omitted) ...
```



如上的例子包含了两个仓库`example`和`image`，其中example中包含可两个命令(hello, outyet)因为他们已经被编译到了bin目录,以及一个库(stringutil),因为它被编译到了pkg目录



#### GOPATH环境变量

`GOPATH`环境变量标识的是Ｇo的工作空间,默认情况下会在你的家目录下创建一个`go`的文件夹来作为你的Ｇo的工作空间，当然可以通过设置来变更工作空间，
当然工作空间的路径不能和Ｇo的安装路径一致

1. 增加Go的工作空间的`bin`子目录倒到path中

```bash
$ export PATH=$PATH:$(go env GOPATH)/bin
```

1. 设置Ｇo的工作空间倒命令行

```bash
$ export GOPATH=$(go env GOPATH)
```


#### 导入路径

导入路径是包的唯一标识字符串，该包的导入路径与在工作空间的路径一致，或者和远程仓库的一致

如果导入的包来自标准库，那么则使用短的导入路径，比如`fmt`, `net/http`等，如果是来自你自己所处理的包
那么你必须选择一个基础路径来作为导入的路径，以此来避免和标准库以及其他库的冲突

如果你的代码是放在远程的仓库，那么应该使用仓库目录作为你的基础目录，比如你在github上面有一个帐号`github.com/user`
那么就可以使用它来作为你自己包的基础路径

需啊哟注意的是，在你构建你的代码之前，不需要将米的代码推送倒远程仓库，接下来将会使用`github.com/user`作为一个基础路径来解释

```bash
$ mkdir -p $GOPATH/src/github.com/user
```

#### 你得第一个项目

1. 首先在工作目录下创建一个新的子目录，作为包的源码目录

```bash
$ mkdir $GOPATH/src/github.com/user/hello
```

1. 然后在该文件下面创建一个新的文件叫做`hello.go`

```go
package main

import "fmt"

func main(){
    fmt.Println("Hello, world")
}
```

1. 接着使用的Ｇo的工具进行构建

```go
$ go install github.com/user/hello
```




















