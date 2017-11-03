## 如何编写Ｇo代码

### 简介

该文档示例简单的介绍了Ｇo的包以及Ｇo tool工具的使用的标准方式比如:更新,构建;以及包的安装和命令

go tool工具需要你正确的组织Ｇo代码

### 代码组织

#### 预览

* Go语言的所有代码都放置于一个工作空间中
* 工作空间中包含多个版本控制的仓库
* 每一个仓库都包含一个或者多个包
* 每一个包都是一个文件目录，该目录下包含一个或者多个Ｇo源文件
* 包的路径决定了文件包的导入路径

#### 工作空间

在工作空间中包含三个目录

* src　Ｇo的源文件
* pkg 包对象
* bin 可执行的二进制文件

经过go tool将会把资源包编译倒pkg,将可执行的二进制文件放到bin目录

```
bin/
hello # command executable
outyet # command executable
pkg/
linux_amd64/
github.com/golang/example/
stringutil.a # package object
src/
github.com/golang/example/
.git/ # Git repository metadata
hello/
hello.go # command source
outyet/
main.go # command source
main_test.go # test source
stringutil/
reverse.go # package source
reverse_test.go # test source
golang.org/x/image/
.git/ # Git repository metadata
bmp/
reader.go # package source
writer.go # package source
... (many more repositories and packages omitted) ...
```

如上的例子包含了两个仓库`example`和`image`，其中example中包含可两个命令\(hello, outyet\)因为他们已经被编译到了bin目录,以及一个库\(stringutil\),因为它被编译到了pkg目录

#### GOPATH环境变量

`GOPATH`环境变量标识的是Ｇo的工作空间,默认情况下会在你的家目录下创建一个`go`的文件夹来作为你的Ｇo的工作空间，当然可以通过设置来变更工作空间，  
当然工作空间的路径不能和Ｇo的安装路径一致

1. 增加Go的工作空间的`bin`子目录倒到path中

   ```bash
   $ export PATH=$PATH:$(go env GOPATH)/bin
   ```

2. 设置Ｇo的工作空间倒命令行

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

2. 然后在该文件下面创建一个新的文件叫做`hello.go`

   ```go
   package main

   import "fmt"

   func main(){
   fmt.Println("Hello, world")
   }
   ```

3. 接着使用的Ｇo的工具进行构建

   ```go
   $ go install github.com/user/hello
   ```

4. 当然你也可以在包的目录下面执行

   ```go
   go install
   ```

5. 执行构建命令以后，将会在工作空间下的`bin`子目录里面发现一个义包目录名称作为命名的二进制可执行文件

#### 你的第一个库

1. 首先在基础路径下面创建一个新的目录

   ```go
   mkdir $GOPATH/src/github.com/user/stringutil
   ```

2. 然后在该目录下面创建一个新的文件`reverse.go`

   ```go
   package stringutil

   func Reverse(s string) string {
       r := []rune(s)
       for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
           r[i], r[j] = r[j], r[i]
       }
       return string(r)
   }
   ```

3. 执行构建语句

```bash
   $ go build $GOPATH/src/github.com/user/stringutil
```

该语句并不会输出倒一个文件，只有在使用`go install`命令的时候，才会产生一个文件到工作目录下面的pkg子目录下面产生一个文件

1. 然后我们就可以在`hello.go`文件中使用

   ```go
   package main

   import (
       "fmt"

       "github.com/user/stringutil"
   )

   func main() {
       fmt.Printf(stringutil.Reverse("!oG ,olleH"))
   }
   ```

2. 此时再对hello进行编译，并执行其二进制文件的时候，就会进行相对应的输出

#### 包的命名

Ｇo源文件的第一行声明，必须是

```go
package name
```

`name`标识的是导入该包的时候的默认名称，所有在该包内的文件必须声明相同的包名称

Go会将该名字作为导入路径的最后一个圆素，比如导入的一个包名称为`crypto/rot13`,那么该包的名称为`rot13`

当生成二进制文件的时候，并不要求包的名称是唯一，只需要保证包的完整路径是唯一的就可以了

#### 测试

Go自带了一个轻量机的测试框架，可以使用`go test`命令以及`testing`包就可以进行简单的测试，

编写一个与测试文件相同文件名前缀，但是以`_test.go`结尾的文件，并且对需要进行测试的函数添加`Test`前缀

比如:

```go
package stringutil

import "testing"

func TestReverse(t *testing.T) {
    cases := []struct {
        in, want string
    }{
        {"Hello, world", "dlrow ,olleH"},
        {"Hello, 世界", "界世 ,olleH"},
        {"", ""},
    }

    for _, c := range cases {
        got := Reverse(c.in)

        if got != c.want {
            t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
        }
    }
}
```

然后就可以进行测试

```bash
$ go test $GOPATH/src/github.com/user/stringutil
```



