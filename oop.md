### 面向对象编程

#### 使用struct实现类的功能
```go
package employee

import "fmt"

type employee struct {
	FirstName   string
	LastName    string
	TotalLeaves int
	LeavesTaken int
}

func (e employee) LeavesRemaining() {
	fmt.Printf("%s %s has %d leaves remaining", e.FirstName, e.LastName, (e.TotalLeaves - e.LeavesTaken))
}

func New(firstName string, lastName string, totalLeaves int, leavesTaken int) employee {
	e := employee{firstName, lastName, totalLeaves, leavesTaken}
	return e
}
```
解释：
- 首先在包中定义一个结构体，如上面的`employee`并且对包外不可见
- 然后定义一个`New`方法，用来实现类的初始化(construct)功能，并且返回一个结构体的对象

然后就可以如下使用
```go
	e := employee.New("Sam", "Adolf", 30, 20)
    e.LeavesRemaining()
```

#### 使用类型组合替代继承
由于Golang不支持继承，因此我们需要使用组合来实现继承的功能，假设现在需要实现一个文章详情功能

首先创建一个作者信息的结构体,该结构体包含一个获取作者全名的简单方法
```go
type author struct {
	firstName string
	lastName  string
	bio       string
}

func (a author) fullName() string {
	return fmt.Sprintf("%s %s", a.firstName, a.lastName)
}
```

然后再创建一个文章结构体，该结构体中我们定义一下文章字段,需要注意的是，这里我们使用了`Author`类型作为`Post`结构体的字段
因此就可以在`Post`中使用`Author`中的方法(`p.AUthor.fullname()`或者`p.fullName()`)
```go
type Post struct {
	title   string
	content string
	Author
}

func (p Post) details() {
	fmt.Println("Title:", p.title)
	fmt.Println("Content:", p.content)
	fmt.Println("Author:", p.Author.fullName())
	fmt.Println("Bio:", p.Author.bio)
}
```

接着在创建一个将切片嵌入到结构体中，来保存文章的列表
```go
type Website struct {
	post []Post
}

func (w Website) contents() {
	fmt.Println("Contents of website\n")
	for _, p := range w.post {
		p.details()
		fmt.Println()
	}
}
```

#### 面向对象中的多态

##### 使用接口实现多态
假设有一家公司需要计算其净资产,该公司有两个业务，分别是`FixedBilling`和`TimeAndMaterimal`，这两个业务的资产计算方式是不一样的

首先我们创建一个接口，该接口需要实现两个方法`calculate`计算总额，以及`source`返回项目的名称
```go
type Income interface {
	calculate() int //计算总的金额
	source() string //返回项目名称
}
```

然后分别定义两个业务的结构并实现接口

`FixedBilling`实现
```go
type FixedBilling struct {
	projectName  string
	biddedAmount int
}

func (f FixedBilling) calculate() int {
	return f.biddedAmount
}

func (f FixedBilling) source() string {
	return f.projectName
}
```

`TimeAndMaterimal`　实现
```go
type TimeAndMaterimal struct {
	projectName  string
	noOfHours    int
	noHourlyRate int
}

func (tm TimeAndMaterimal) calculate() int {
	return tm.noHourlyRate * tm.noOfHours
}

func (tm TimeAndMaterimal) source() string {
	return tm.projectName
}
```

最后创建一个总的总计方法，这样就可以使用了
```go
func calculateNetIncome(ic []Income) int {
	var netincome int = 0
	for _, income := range ic {
		fmt.Printf("Income from %s = $%d\n", income.source(), income.calculate())
		netincome += income.calculate()
	}
	return netincome
}
```

使用
```go
func main() {
	project1 := FixedBilling{projectName: "Project 1", biddedAmount: 5000}
	project2 := FixedBilling{projectName: "Project 2", biddedAmount: 10000}
	project3 := TimeAndMaterimal{projectName: "Project 3", noOfHours: 160, noHourlyRate: 25}
	incomeStreams := []Income{project1, project2, project3}
	total := calculateNetIncome(incomeStreams)
	fmt.Println(total)
}
```

加入该公司开始了一项新的业务，因此，我们只要创建相对应的类型，并实现`Income`接口，就可以直接添加了
```go
type Advertisement struct {  
    adName     string
    CPC        int
    noOfClicks int
}

func (a Advertisement) calculate() int {  
    return a.CPC * a.noOfClicks
}

func (a Advertisement) source() string {  
    return a.adName
}
```