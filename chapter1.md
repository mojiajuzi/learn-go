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



#### GOPATH

`GOPATH`环境变量标识的是Ｇo的工作空间