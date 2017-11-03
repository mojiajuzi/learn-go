### 基础知识

#### 文件操作权限

对于文件的操作为：可读（Read）,可写(Write),可执行(Execute),其各自的权重为：

- Read(4)
- Write(2)
- Execute(1)

假如在linux系统中执行如下命令：

```ba
ls -l filename second.jpg
```

将会得到如下的信息:

```ba
-rw-rw-r-- 1 gru gru 46228 11月  2 10:05 second.jpg
```

分解可得：

- `-`:标识文件类型，`-`标识常规文件，　‘d’标识目录
- `rw-`:拥有者(owner)的权限
- `rw-`:拥有者所在用户组(group)所拥有的权限
- `r--`:所有其他人(other)所拥有的权限
- `1`标识这是硬链接
- `gru`　拥有者
- `gru` 用户组
- 46228 文件的大小，单位为byte
- `11月　2 10:05`　标识文件最后一次写入的时间

### os
> os包中的文件的相关操作

### 参考文档

- [Linux Files and File Permission](http://www.comptechdoc.org/os/linux/usersguide/linux_ugfilesp.html)