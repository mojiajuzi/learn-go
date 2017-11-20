### govendor
[kardianos/govendor](https://github.com/kardianos/govendor)
#### 安装
1. 下载相对应的包
```bash
go get -u github.com/kardianos/govendor
```

1. 添加到命令倒环境变量
```bash
export $PATH=$PATH:/$GOPATH/bin
```

#### 命令介绍

##### 主命令
```bash
-govendor-licenses 显示govendor的版权
-version 显示版本
-cpuprofile 'file' 将debug时的cpu信息输出到指定的文件
-memprofile 'file' 将debug时的内存使用情况写入指定文件
```

##### 子命令
|命令|说明|
|:------|:------|
|init| 创建新的vendor文件夹以及｀vendor.json｀文件
|list |显示和过滤存在的的依赖以及包
|add |从$GOPATH中添加一个包
|update| 从$GOPATH中更新一个包
|remove| 从vendor目录中移除一个包
|status |显示缺失的包以及本地的修改
|fetch| 从远程更新vendor里面的包
|sync|依据`vendor.json`文件中定义的包名称，从远程仓库拉取包
|migrate|依据元数据从其他的工具将包转移到vendor目录中
|get|类似于`go get`命令，不过区别是将包安装倒vendor目录
|license|依据给定的状态以及导入的路径显示协议信息
|shell|执行shell命令，对于大的项目来说，这将会提高多个子命令的效率

##### 命令状态
|状态|说明|
|:------|:------|
|local|项目中的包|
|external|项目中引用在GOPATH中且不在当前项目中的包
|vendor|在vendor中的包|
|std|标准库中的包|
|excluded|从vendor中执行的外部包|
|unused|vendor中未使用的包|
|missing|未找到的参照包|
|program|在main包中的包|
|outside|等效`+external +missing`
|all|所有的包|