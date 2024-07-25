# Go常用命令

```bash
#编译并运行Go源码文件
go run 

#编译Go程序为可执行文件或库
go build

#编译并安装Go程序或库
go install

#运行测试文件或包
go test

#下载并安装包及其依赖
go get

-d：只下载包，不安装
-u：强制使用网络来更新包和它的依赖
-v：显示详情

#使用 Go Modules 进行包管理
go module

go mod init：初始化当前目录为 Go Module
go mod tidy：整理并更新依赖
go mod vendor：将依赖复制到 vendor 目录

#静态分析 Go 代码中的错误和潜在问题
go vet

#清理生成的文件和缓存
go clean


```



goland 中默认go为gomule

![image-20240513145135310](https://raw.githubusercontent.com/yangyuwei6/photo/main/image/202407191405335.png)

goland引入自定义包

![image-20240513145812172](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240513145812172.png)



....

![image-20240513152351580](https://raw.githubusercontent.com/yangyuwei6/photo/main/image/202407191405080.png)

package+包名一定要放在第一行

# 包的代码执行顺序

## 多个包

![img](https://camo.githubusercontent.com/092cd0054d73cd3857dd6fc79310c7f9f52be591fae7951cda633fe3c3cc4cc3/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f696d675f636f6e766572742f62313735353861666562393862393762393066326334356532666165613761612e706e67)

## 单个包

main包-->常量-->全局变量-->init函数-->main函数-->Exit

# 变量

## 值类型和引用类型

值类型：int，float , bool,  string，数组，结构体//存储在栈中

引用类型：slice，map，channel，function，pointer//存储在堆中

make只能创建slice，map，channel

# 延迟调用

defer语句常用于释放资源、解除锁定以及错误处理等

defer语句遵循后进先出的原则

```go
  package main
  import "fmt"
  func main() {
	defer fmt.Println("我是第一个被注册的") // 3
	fmt.Println("main函数中调用的Println") // 1
	defer fmt.Println("我是第二个被注册的") // 2
  }
```



# 数组

```go
//var arryname [size]datatype
声明：var arr1[4]int
初始化：var arry1=[4]int{1,2,3,4}
       arry1:=[4]int{1,2,3,4}
```

```go
package main
import(
	"fmt"
)
func main(){

	var arr1=[4]int{1:23,3:46}//指定下标来初始化元素
	for k,v:=range arr1{
		fmt.Printf("下标%d,数值为 %d\n",k,v)
	}
}
[Running] go run "c:\Users\19517\hello go\demo.go"
下标0,数值为 0
下标1,数值为 23
下标2,数值为 0
下标3,数值为 46

```

如果数组长度不确定，可以使用 **...** 代替数组的长度，编译器会根据元素个数自行推断数组的长度

```go
func main(){

	var arr1=[...]int{1:23,3:46}//如果数组长度不确定，可以使用 **...** 代替数组的长度，编译器会根据元素个数自行推断数组的长度
	fmt.Println(len(arr1))
}
[Running] go run "c:\Users\19517\hello go\demo.go"
4
```

# 字符串

```go
//range可以用来枚举 Unicode 字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
for i, c := range "go" {
    fmt.Println(i, c)
}
//想输出字符用string
for _,c := range "hello"{
    fmt.Println(string(c))
}
```

# 切片

## 切片的定义

```go
var identifier []type
var slice []int=make([]int,len)
slice:=make([]int,len)
```

## 切片的初始化

```go
//直接初始化
func main(){
	var slice1=[]int{1,23,3,46}
	fmt.Println(slice1)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
[1 23 3 46]
```

```go
//由数组初始化
func main(){
	arr1:=[...]int{2,34,5,3,5,1,32}
    slice1:=arr1[0:4]//slice:=arr[startIndex:endIndex],从下标startIndex到endIndex-1
	slice2:=arr1[1:]
	slice3:=arr1[:len(arr1)]
	slice4:=arr1[:]
	fmt.Println(slice1)
	fmt.Println(slice2)
	fmt.Println(slice3)
	fmt.Println(slice4)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
[2 34 5 3]
[34 5 3 5 1 32]
[2 34 5 3 5 1 32]
[2 34 5 3 5 1 32]

```

```go
//由切片初始化
func main(){
	slice1:=[]int{2,34,5,3,5,1,32}
	slice2:=slice1[1:4]
	fmt.Println(slice2)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
[34 5 3]
```

```go
//make初始化
func main(){
	slice:=make([]int,4)
	fmt.Println(slice)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
[0 0 0 0]
```

不能使用“==”运算符判断切片是否相同

## 空切片

要检查切片是否为空，请始终使用`len(s) == 0`来判断，而不应该使用`s == nil`来判断

```go
package main
import "fmt"
func main() {
   var numbers []int//仅声明，未初始化
   printSlice(numbers)
    if(len(numbers)==0){
      fmt.Printf("切片是空的")
   }
}
func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
len=0 cap=0 slice=[]
切片是空的
```

## append() 

```go
package main
import "fmt"
func main() {
	slice1:=[]int{1,2,4,5,23}
	fmt.Printf("len:%d,cap:%d,slice=%v\n",len(slice1),cap(slice1),slice1)
	slice2:=append(slice1,33,34)
	fmt.Printf("len:%d,cap:%d,slice=%v\n",len(slice2),cap(slice2),slice2)
	slice3:=[]int{10,24,51}
	fmt.Printf("len:%d,cap:%d,slice=%v\n",len(slice3),cap(slice3),slice3)
	slice3=append(slice3,slice2...)  //不要忘记  ...
	fmt.Printf("len:%d,cap:%d,slice=%v\n",len(slice3),cap(slice3),slice3)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
len:5,cap:5,slice=[1 2 4 5 23]
len:7,cap:10,slice=[1 2 4 5 23 33 34]
len:3,cap:3,slice=[10 24 51]
len:10,cap:10,slice=[10 24 51 1 2 4 5 23 33 34]

```

## copy()

因为slice是引用类型

```go
package main
import "fmt"
func main() {
	slice1:=[]int{1,2,4,5,23}
	fmt.Printf("len:%d,cap:%d,slice1=%v\n",len(slice1),cap(slice1),slice1)
	slice2:=slice1
	fmt.Printf("len:%d,cap:%d,slice2=%v\n",len(slice2),cap(slice2),slice2)
	slice2[0]=1000  //改变slice2[0]的值会改变slice1[0]的值
	fmt.Printf("len:%d,cap:%d,slice2=%v\n",len(slice2),cap(slice2),slice2)
	fmt.Printf("len:%d,cap:%d,slice1=%v\n",len(slice1),cap(slice1),slice1)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
len:5,cap:5,slice1=[1 2 4 5 23]
len:5,cap:5,slice2=[1 2 4 5 23]
len:5,cap:5,slice2=[1000 2 4 5 23]
len:5,cap:5,slice1=[1000 2 4 5 23]
```



```go
package main
import "fmt"
func main() {
	slice1:=[]int{1,2,4,5,23}
	fmt.Printf("len:%d,cap:%d,slice1=%v\n",len(slice1),cap(slice1),slice1)
	slice2:=make([]int,5,5)
	copy(slice2,slice1)
	fmt.Printf("len:%d,cap:%d,slice=2%v\n",len(slice2),cap(slice2),slice2)
	slice2[1]=1000
	fmt.Printf("len:%d,cap:%d,slice=2%v\n",len(slice2),cap(slice2),slice2)
	fmt.Printf("len:%d,cap:%d,slice=1%v\n",len(slice1),cap(slice1),slice1)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
len:5,cap:5,slice1=[1 2 4 5 23]
len:5,cap:5,slice=2[1 2 4 5 23]
len:5,cap:5,slice=2[1 1000 4 5 23]
len:5,cap:5,slice=1[1 2 4 5 23]
```

## 切片元素的删除

Go语言中并没有删除切片元素的专用方法，我们可以使用切片本身的特性来删除元素。

```go
func main() {
	// 从切片中删除元素
	a := []int{30, 31, 32, 33, 34, 35, 36, 37}
	// 要删除索引为2的元素
	a = append(a[:2], a[3:]...)
	fmt.Println(a) //[30 31 33 34 35 36 37]
}
```

#  map

map是一种无序的基于key-value的数据结构，go语言中的map是引用类型，必须初始化才能使用

## 定义map

```go
//make函数定义
map_variable := make(map[KeyType]ValueType, initialCapacity)
func main() {
	map1:=make(map[string]int)
	map1["yang"]=1
	map1["wql"]=2
	fmt.Println(map1)
}
//map关键字定义
func main() {
	map1:=map[string]int{
		"yyw":1,
		"yj":2,
		"cs":3,
	}
	fmt.Println(map1)
}
```

## 获取元素

```go
func main() {
	map1:=map[string]string{
		"age":"二十岁",
		"sex":"male",
		"positin":"student",
	}
	name,ok:=map1["age"]
	if ok {
		fmt.Println("存在",name)
	}else{
		fmt.Println("不存在")
	}
}
[Running] go run "c:\Users\19517\hello go\demo.go"
存在 二十岁
```

## 删除元素

```go
func main() {
	map1:=map[string]int{
		"yyw":1,
		"yj":2,
		"cs":3,
	}
	fmt.Println(map1)
	delete(map1,"cs")
	fmt.Println(map1)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
map[cs:3 yj:2 yyw:1]
map[yj:2 yyw:1]


```

## 遍历map

```go
//遍历map时的元素顺序与添加键值对的顺序无关
func main() {
	map1:=map[string]string{
		"age":"二十岁",
		"sex":"male",
		"positin":"student",
	}
	for k,v:=range map1{
		fmt.Printf("key:%v,value:%v\n",k,v)
	}
}
[Running] go run "c:\Users\19517\hello go\demo.go"
key:sex,,value:male
key:positin,value:student
key:age,,value:二十岁
```

按顺序遍历的话：

```go
import "sort"
/*sort.Ints:对整数进行排序
sort.Strings:对字符串进行排序
sort.Float64s:对浮点数进行排序	
*/
```

## 判断键是否存在

```go
value,ok：=map[key]
```

```go
package main
import "fmt"
func main() {
	map1:=map[string]string{
		"name":"yyw",
		"age":"十八",
	}
	v,ok:=map1["name"]
	if ok{
		fmt.Println(v)
	}else{
		fmt.Println("查无此人")
	}
}
```



# 结构体

## 结构体的定义

```go
type struct_variable_type struct {
   member definition
   member definition
   ...
   member definition
}
```

## 结构体的初始化

```go
type Person struct{
	age int
	name string
	address string
}
func main() {
	p1:=Person{13,"yyw","changsha"}
	p2:=Person{age:1000,name:"www",address:"dwada"}
	p3:=new(Person)//new
	p3.age=12
	p3.name="xyz"
	p3.address="wuhan"
	fmt.Println(p1)
	fmt.Println(p2)
	fmt.Println(*p3)
}
[Running] go run "c:\Users\19517\hello go\demo.go"
{13 yyw changsha}
{1000 www dwada}
{12 xyz wuhan}
```

## 匿名结构体

```go
data := struct {
    field1 type1
    field2 type2
    // ...
}{
    field1: value1,
    field2: value2,
    // ...
}

```

简单示例

```go
package main

import "fmt"

func main() {
    student := struct {
        name string
        age  int
    }{
        name: "张三",
        age:  18,
    }

    fmt.Println(student)
}
```

## 结构体中的匿名字段

```go
type Person struct{
	int
	string  //匿名字段类型不能重复
}

func main() {
	p1:=Person{13,"yyw"}
	fmt.Println(p1.int,p1.string)
}//默认使用数据类型作为名字，那么匿名字段的类型不能重复
[Running] go run "c:\Users\19517\hello go\demo.go"
13 yyw
```

# 接口

如果接口中有方法，必须通过结构体或自定义类型实现其方法

只要定义的类型或接口体实现了该接口，只可以将相应类型变量赋值给接口，接口可以作为方法的参数来接收这些类型以实现多态

结构体值接收者（方法中的接收者为结构体值类型，实现接口时）实例化后的结构体值类型和结构体指针都可以赋值给接口变量

结构体指针接收者（方法中的接收者为结构体指针类型，实现接口时），实例化后的结构体值类型无法赋值给接口变量，结构体指针可以赋值给接口变量

空接口存储slice类型时不支持索引,想访问切片中某下标的数据时

```go
package main
import "fmt"
type person struct {
	name string
	age  int
}

func main() {
	var userInformation = make(map[string]interface{})
	p1 := person{
		name: "yyw",
		age:  12,
	}
	userInformation["info"] = p1
	userInformation["time"] = "11:00AM"
	userInformation["hobby"] = []string{"eat", "sleep"}
	fmt.Println(userInformation)
	fmt.Println(userInformation["hobby"])
	fmt.Println(userInformation["hobby"][1])
}
/*# command-line-arguments
.\空接口.go:22:38: invalid operation: cannot index userInformation["hobby"] (map index expression of type interface{})
*/
//解决方法，使用类型断言
v, _ := userInformation["hobby"].([]string)
	fmt.Println(v[1])
//sleep
```

空接口类型若存储的为结构体类型，访问结构体中的某个属性需要使用类型断言

```go
package main
import "fmt"
type person struct {
	name string
	age  int
}
func main() {
	var userInformation = make(map[string]interface{})
	p1 := person{
		name: "yyw",
		age:  12,
	}
	userInformation["info"] = p1
	userInformation["time"] = "11:00AM"
	fmt.Println(userInformation)
	fmt.Println(userInformation["info"])
	var xx = userInformation["info"].name
	fmt.Println(xx)
}
//# command-line-arguments
.\空接口.go:20:35: userInformation["info"].name undefined (type interface{} has no field or method name)
//解决方法
userInformation["info"] = p1
	userInformation["time"] = "11:00AM"
	userInformation["hobby"] = []string{"eat", "sleep"}
	pname, _ := userInformation["info"].(person)
	fmt.Println(pname.age)
//12
```



## 类型断言

## 判断空接口中值的类型

```go
x.[T]
//x:表示空接口变量,T：表示推测的变量类型
package main
import "fmt"
func main() {
	var a interface{}
	a = "hello golang"
	v, ok := a.(string)
	if ok {
		fmt.Println(v, "a就是一个string类型")
    }else{
        fmt.Println("断言失败")
    }
}
//hello golang a就是一个string类型
```

## 结合switch使用

```go
package main
import "fmt"
func justfyType(x interface{}) {
	switch v := x.(type) {
	case int:
		fmt.Println(v, "是int类型")
	case string:
		fmt.Println(v, "是string类型")
	case bool:
		fmt.Println(v, "是bool类型")
	default:
		fmt.Println("unknown type")
	}
}
func main() {
	x := "hello world"
	justfyType(x)
}
//hello world 是string类型
```

## 类型断言实现类型转化

```go
//将接口值转化为更具体的类型
func add(x interface{}){
	//Invalid operation: x+10 (mismatched types interface{} and untyped int)
	var sum int=x+10
	fmt.Println(sum)
}
//使用类型断言后
package main
import "fmt"
func add(x interface{}) {
	//实现类型转化，否则接口型与int型无法运算，会报错
	v := x.(int)
	var sum = v + 10
	fmt.Println(sum)
}
func main() {
	x := 10
	add(x)
}
```



# goroutine

实现并行

## sleep

```go
package main

import (
	"fmt"
	"time"
)

func test() {
	for i := 0; i < 10; i++ {
		fmt.Println(i, "test(),hello golang!")
		time.Sleep(time.Millisecond * 100)
	}
}
func main() {
	go test()//开启携程后，并行运行
	for i := 0; i < 10; i++ {
		fmt.Println(i, "main(),hello golang!")
		time.Sleep(time.Millisecond * 100)
	}
}
/*
0 main(),hello golang!
0 test(),hello golang!
1 test(),hello golang!
1 main(),hello golang!
2 main(),hello golang!
2 test(),hello golang!
3 test(),hello golang!
3 main(),hello golang!
4 test(),hello golang!
4 main(),hello golang!
5 main(),hello golang!
5 test(),hello golang!
6 test(),hello golang!
6 main(),hello golang!
7 main(),hello golang!
7 test(),hello golang!
8 test(),hello golang!
8 main(),hello golang!
9 main(),hello golang!
9 test(),hello golang!
*/
```

存在问题：若主进程结束快，可能导致携程未完成就程序终止

```go
package main

import (
	"fmt"
	"time"
)

func test() {
	for i := 0; i < 10; i++ {
		fmt.Println(i, "test(),hello golang!")
		time.Sleep(time.Millisecond * 100)
	}
}
func main() {
	go test()
	for i := 0; i < 10; i++ {
		fmt.Println(i, "main(),hello golang!")
		time.Sleep(time.Millisecond * 10)
	}
}
//此时main执行结束，test仅仅执行一次
/*
0 main(),hello golang!
0 test(),hello golang!
1 main(),hello golang!
2 main(),hello golang!
3 main(),hello golang!
4 main(),hello golang!
5 main(),hello golang!
6 main(),hello golang!
1 test(),hello golang!
7 main(),hello golang!
8 main(),hello golang!
9 main(),hello golang!
*/
```

## sync包中的Waitgroup

可实现协程完成后，主程序再结束

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func run() {
	for i := 0; i < 10; i++ {
		fmt.Println(i, "test(),hello golang!")
		time.Sleep(time.Millisecond * 100)
	}
	wg.Done()
}
func main() {
	wg.Add(1)
	go run()
	for i := 0; i < 10; i++ {
		fmt.Println(i, "main(),hello golang!")
		time.Sleep(time.Millisecond * 10) //此时main函数执行结束后，test函数还未执行完10次程序终止
	}
	wg.Wait()
	fmt.Println("主程序退出")
}
/*
0 test(),hello golang!
0 main(),hello golang!
1 main(),hello golang!
2 main(),hello golang!
3 main(),hello golang!
4 main(),hello golang!
5 main(),hello golang!
6 main(),hello golang!
1 test(),hello golang!
7 main(),hello golang!
8 main(),hello golang!
9 main(),hello golang!
2 test(),hello golang!
3 test(),hello golang!
4 test(),hello golang!
5 test(),hello golang!
6 test(),hello golang!
7 test(),hello golang!
8 test(),hello golang!
9 test(),hello golang!
主程序退出
*/
```

## runtime包里面一些函数    

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	//获取当前计算机上cpu的个数
	cpuNUM := runtime.NumCPU()
	fmt.Printf("cpuNUM = %d\n", cpuNUM)
	//设置具体使用多少个cpu，防止cpu被占满
	runtime.GOMAXPROCS(cpuNUM - 1)
	fmt.Println("ok")
}

```

## groutine在执行时间上的改善

```go
//未用goroutine时
package main
import (
	"fmt"
	"time"
)

func main() {
	start := time.Now().UnixMilli()
	for num := 1; num < 10000; num++ {
		flag := true
		for i := 2; i < num; i++ {
			if num%i == 0 {
				flag = false
				break
			}
		}
		if flag {
			fmt.Println(num, "是素数")
		}
	}
	end := time.Now().UnixMilli()//25
	fmt.Println(end - start)
}
```

```go
//使用goroutine后
package main
import (
	"fmt"
	"sync"
	"time"
)

var cc sync.WaitGroup

func calc(n int) {
	defer cc.Done()
	for num := (n-1)*2000 + 1; num < n*2000; num++ {
		flag := true
		for i := 2; i < num; i++ {
			if num%i == 0 {
				flag = false
				break
			}
		}
		if flag {
			fmt.Println(num, "是素数")
		}
	}
}
func main() {
	start := time.Now().UnixMilli()
	for i := 1; i <= 5; i++ {
		cc.Add(1)
		go calc(i)
	}
	cc.Wait()
	end := time.Now().UnixMilli()
	fmt.Println((start - end)) //15
}
//通过对比，使用goroutine可以减少执行时间
```

# channel

channel是golang中给goroutine之间提供通讯的桥梁，golang通过通信共享内存，而不是通过共享内存实现通信

## 管道的基本操作

```go
//管道的声明
var 变量 chan 元素类型
//管道的初始化，管道必须初始化才可以使用
ch:=make(chan 元素类型，容量)
//在管道中存储数据
ch<-20
//从管道中取数据
d:=<-ch
//获取管道长度和容量
len(ch),cap(ch)
//管道的遍历
管道没有key
for v:=range ch{}


```

```go
//channel是一个引用类型
package 
import "fmt"
func main() {
	ch1 := make(chan int, 4)
	fmt.Println(ch1) //0xc000076080
	ch1 <- 20
	<-ch
	ch2 := ch1       //改变ch2同样会改变ch1
	fmt.Println(ch2) //0xc000076080
	ch2 <- 50
	d := <-
	fmt.Println(d) //50
}
```

```go
//管道的遍历
package main
import "fmt"
func main() {
	ch := make(chan int, 6)
	ch <- 1
	ch <- 2
	ch <- 3
	close(ch)
	for v := range ch {
		fmt.Println(v)
	}
	//若遍历前没有关闭通道
	//fatal error: all goroutines are asleep - deadlock!
}

```

## goroutine实现管道写和读并行

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func write(ch chan int) {
	for i := 0; i < 10; i++ {
		ch <- i
		fmt.Println("write成功", i)
		time.Sleep(time.Millisecond * 50)
	}
	close(ch)
	wg.Done()
}
func read(ch chan int) {
	for val := range ch {
		fmt.Println("读取成功", val)
		time.Sleep(time.Millisecond * 10)
	}
	wg.Done()
}
func main() {
	var ch = make(chan int, 10)
	wg.Add(1)
	go write(ch)
	wg.Add(1)
	go read(ch)
	wg.Wait()
	fmt.Println("程序结束")
}
/*
write成功 0
读取成功 0
write成功 1
读取成功 1
write成功 2
读取成功 2
write成功 3
读取成功 3
write成功 4
读取成功 4
write成功 5
读取成功 5
write成功 6
读取成功 6
write成功 7
读取成功 7
write成功 8
读取成功 8
write成功 9
读取成功 9
程序结束
*/
```

## 单向channel

```go
//只写
var chan<- int
//只读
var <-chan int
```

## select多路复用

在 Go 语言中，`select` 语句用于处理一个或多个通信操作，它可以让一个 Go 程序同时等待多个通信操作完成。这里我为你准备了一个简单的示例来演示 `select` 的用法，包括超时处理和多通道选择。

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // 创建两个通道
    ch1 := make(chan string)
    ch2 := make(chan string)

    // 一个 goroutine 发送消息到 ch1
    go func() {
        time.Sleep(2 * time.Second)
        ch1 <- "消息来自通道1"
    }()

    // 一个 goroutine 发送消息到 ch2
    go func() {
        time.Sleep(3 * time.Second)
        ch2 <- "消息来自通道2"
    }()

    // 使用 select 语句等待多个通道的消息
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-ch1:
            fmt.Println("接收到:", msg1)
        case msg2 := <-ch2:
            fmt.Println("接收到:", msg2)
        }
    }

    // 增加一个超时处理的示例
    timeout := make(chan bool, 1)
    go func() {
        time.Sleep(2 * time.Second)
        timeout <- true
    }()

    select {
    case <-timeout:
        fmt.Println("超时")
    case msg3 := <-ch1:
        fmt.Println("接收到:", msg3)
    }
}
```

### 解释说明：

1. **创建通道：**
   - `ch1` 和 `ch2` 是两个用于通信的通道。
2. **并发发送消息：**
   - 使用两个匿名函数分别在 2 秒和 3 秒后向 `ch1` 和 `ch2` 发送消息。
3. **使用 `select` 语句：**
   - 在 `for` 循环中使用 `select` 语句，等待并处理来自 `ch1` 或 `ch2` 的消息。`select` 会选择其中一个通道进行接收操作，如果多个通道都准备好了，它会随机选择一个执行。
4. **超时处理：**
   - 增加了一个超时通道 `timeout`，在另一个 goroutine 中延迟 2 秒后向其中发送 `true`，然后使用 `select` 等待 `timeout` 或者 `ch1` 的消息。如果 `ch1` 没有在 2 秒内发送消息，`timeout` 分支会被执行。

### 示例运行流程：

- 程序首先创建了两个 goroutine，分别向 `ch1` 和 `ch2` 发送消息。
- 主程序使用 `select` 语句在循环中等待从 `ch1` 或 `ch2` 接收消息。
- 第一个 `select` 语句结束后，展示了一个超时处理的例子，演示了如何在一个 goroutine 中设置超时，并在主程序中使用 `select` 语句来处理超时事件。

这个示例展示了 `select` 语句在 Go 语言中的基本用法，包括等待多个通道的消息以及超时处理。

# 并发安全与互斥锁

## 互斥锁

给共享资源加锁，实现互斥访问

```go
var mutex=syc.Mutex
//上锁
mutex.Lock()
//解锁
mutex.UnLock()
```

## 读写锁



# golang反射

```go
import "reflect"
```

## reflect.typeof

`reflect.TypeOf()` 函数返回一个 `reflect.Type` 类型的对象。`reflect.Type` 是一个接口类型，定义了一些方法来获取类型的信息，例如类型的名称、种类（kind）、字段信息等。

### typename和typekind

`reflect.TypeOf().Kind()` 返回变量的底层类型的分类。

Go 语言中的基础类型（也称为内建类型）包括：

- 布尔类型：`bool`
- 整数类型：`int`, `int8`, `int16`, `int32`, `int64`, `uint`, `uint8` (byte), `uint16`, `uint32`, `uint64`, `uintptr`
- 浮点数类型：`float32`, `float64`
- 复数类型：`complex64`, `complex128`
- 字符串类型：`string`
- 字符类型：`rune`

`reflect.TypeOf().Name()` 返回变量的类型的名称作为字符串，如果是用户定义的类型则返回定义时的名称，否则返回 Go 语言中的名称或空字符串。

```go
package main

import (
	"fmt"
	"reflect"
)
type person struct {
	name string
	age  int
}
func reflectType(x interface{}) {
	v := reflect.TypeOf(x)
	//name:类型，kind：种类（底层类型）
	fmt.Printf("类型：%v,type:%v,kind:%v\n", v, v.Name(), v.Kind())
}
func main() {
	a := "hello world"
	b := 34
	c := [3]int{12, 14, 15}
	d := []int{1, 15, 145}
	e := map[string]int{"age": 100}
	f := person{name: "yyw", age: 18}
	reflectType(a) //string,type:string,kind:string
	reflectType(b) //int,type:int,kind:int
	//数组，切片，map的type.name返回都是'‘
	reflectType(c) //[3]int,type:,kind:array
	reflectType(d) //[]int,type:,kind:slice
	reflectType(e) //map[string]int,type:,kind:map
	reflectType(f) //main.person,type:person,kind:struct
}
```

## reflect.valueof

**功能：** `reflect.ValueOf()` 用于获取变量的值信息，返回一个 `reflect.Value` 类型的对象。

**参数：** 接受任意接口类型的参数，通常是变量或者接口类型的实例。

**返回值：** 返回一个 `reflect.Value` 对象，这个对象包含了变量的值和类型信息，可以通过方法获取和修改变量的值。

### 通过反射获取值

```go
//返回的是reflect.value 的类型 
func main() {
	var x interface{}
	fmt.Printf("%T\n", x)//<nil>
	v := reflect.ValueOf(x)
    //此时reflect.value该类型仍然无法与其它类型进行运算
	fmt.Printf("%T\n", v)//reflect.Value
    
}
```

```go
package main
import (
	"fmt"
	"reflect"
)
// reflect.value 类型转化成其它类型
func add(x interface{}) {
	v := reflect.ValueOf(x)
	fmt.Printf("%T\n", v)//reflect.Value
	//fmt.Println(v+10)  Invalid operation: v+10 (mismatched types Value and untyped int)
	fmt.Println(v.Int() + 12)
}
func main() {
	x := 10
	add(x)
}
```

reflect.Value类型，其中包含了原始值的值信息。reflect.Value与原始值之间可以互相转换。

reflect.Value类型提供的获取原始值的方法如下：

|           方法           |                             说明                             |
| :----------------------: | :----------------------------------------------------------: |
| Interface() interface {} | 将值以 interface{} 类型返回，可以通过类型断言转换为指定类型  |
|       Int() int64        |     将值以 int 类型返回，所有有符号整型均可以此方式返回      |
|      Uint() uint64       |     将值以 uint 类型返回，所有无符号整型均可以此方式返回     |
|     Float() float64      | 将值以双精度（float64）类型返回，所有浮点数（float32、float64）均可以此方式返回 |
|       Bool() bool        |                     将值以 bool 类型返回                     |
|     Bytes() []bytes      |               将值以字节数组 []bytes 类型返回                |
|     String() string      |                     将值以字符串类型返回                     |

```go
package main

import (
	"fmt"
	"reflect"
)

func refectValue(x interface{}) {
	v := reflect.ValueOf(x)
	k := v.Kind()
	switch k {
	case reflect.Int:
		fmt.Println("int", v.Int())
	case reflect.Float64:
		fmt.Println("int", v.Float())
	case reflect.String:
		fmt.Println("int", v.String())
	default:
		fmt.Println("type not supported")
	}
}
func main() {
	a := 100
	b := 3.14
	c := "hello world"
	d := []int{1, 2, 3}
	refectValue(a)
	refectValue(b)
	refectValue(c)
	refectValue(d)
}
/*
int 100
int 3.14
int hello world
type not supported
*/
```

### 通过反射设置对象的值

在 Go 语言中，`reflect.ValueOf()` 函数返回一个 `reflect.Value` 类型的对象，这个对象包含了传入变量的值信息和类型信息。`reflect.Value` 类型的对象有一个 `.Elem()` 方法，用于获取指向的实际值。

### `.Elem()` 方法的作用

- 如果 `reflect.Value` 对象持有的**是指针类型**，`.Elem()` 方法返回指针指向的值的 `reflect.Value` 对象。
- 如果 `reflect.Value` 对象不是指针类型，`.Elem()` 方法会引发 panic。

### 示例用法

以下是一个示例，演示了如何使用 `.Elem()` 方法获取指针指向的实际值：

```go
package main
import (
	"fmt"
	"reflect"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	// 创建一个指针变量
	p := &Person{Name: "Alice", Age: 30}

	// 使用 reflect.ValueOf 获取 p 的反射值
	v := reflect.ValueOf(p)

	// 使用 .Elem() 方法获取指针指向的值的反射值
	if v.Kind() == reflect.Ptr {
		v = v.Elem()
		fmt.Println("Type:", v.Type()) // 输出: Type: main.Person
		fmt.Println("Name:", v.FieldByName("Name").String()) // 输出: Name: Alice
		fmt.Println("Age:", v.FieldByName("Age").Int())      // 输出: Age: 30
	}
}
```

在上面的示例中：

- `reflect.ValueOf(p)` 返回了一个 `reflect.Value` 对象，表示指针 `p`。
- 因为 `p` 是一个指针类型（`*Person`），所以通过 `.Elem()` 方法获取了指针指向的 `Person` 结构体的值。
- 然后可以使用 `reflect.Value` 对象的方法获取结构体中字段的值，例如 `v.FieldByName("Name").String()` 获取 `Name` 字段的字符串值，`v.FieldByName("Age").Int()` 获取 `Age` 字段的整数值。

总结来说，`.Elem()` 方法在使用反射操作指针类型变量时非常有用，它使得可以直接访问和修改指针指向的实际值的字段和方法。

```go
package main
import (
	"fmt"
	"reflect"
)
func reflectSetValue(x interface{}) {
	v := reflect.ValueOf(x)
	switch v.Elem().Kind() {
	case reflect.String:
		v.Elem().SetString("Hello cpp")
	case reflect.Int:
		v.Elem().SetInt(100)
	default:
		fmt.Println("no such type supposed")
	}
}
func main() {
	a := "hello golang"
	fmt.Println(a)//hello golang
	reflectSetValue(&a)
	fmt.Println(a)//Hello cpp
	b := 300
	fmt.Println(b)//300
	reflectSetValue(&b)
	fmt.Println(b)//100
}

```

## 结构体反射

结构体标签应用：json编解码，gorm映射关系

### golang反射解析结构体标签Tag

```go
package main

import (
    "fmt"
    "reflect"
)

type resume struct {
    Name string `info:"name" doc:"我的名字"`
    Sex  string `mx:"sex"`
}

func findTag(str interface{}) {
    t := reflect.TypeOf(str).Elem()
    for i := 0; i < t.NumField(); i++ {
       taginfo := t.Field(i).Tag.Get("info")
       tagdoc := t.Field(i).Tag.Get("doc")
       tagmx := t.Field(i).Tag.Get("mx")
       fmt.Println(taginfo, tagdoc, tagmx)
    }
}
func main() {
    var re resume
    findTag(&re)//elem()接受的是一个指针，否则会panic
}
```

### 结构体标签在json中的应用

在 Go 语言中，`json.Marshal()` 函数用于**将数据结构序列化为 JSON 格式的字节流**。这个函数非常常见，通常用于将结构体、map、切片等数据类型转换为 JSON 格式的数据。

示例用法

假设我们有一个结构体 `Person`：

```go
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    // 创建一个 Person 对象
    p := Person{
        Name: "Alice",
        Age:  30,
    }

    // 使用 json.Marshal() 将 Person 对象转换为 JSON 格式的字节流
    jsonData, err := json.Marshal(p)
    if err != nil {
        fmt.Println("JSON encoding error:", err)
        return
    }

    // 打印序列化后的 JSON 数据
    fmt.Println(string(jsonData))
}
```

解释说明：

1. **定义结构体 `Person`**：
   - `Person` 结构体有两个字段 `Name` 和 `Age`，分别表示姓名和年龄。
   - 使用 `json:"name"` 和 `json:"age"` 标签指定 JSON 序列化时对应的字段名。
2. **创建 `Person` 对象**：
   - 在 `main` 函数中创建了一个 `Person` 对象 `p`，赋值了 `Name` 和 `Age`。
3. **使用 `json.Marshal()`**：
   - `json.Marshal(p)` 将 `p` 结构体实例序列化为 JSON 格式的字节流。
   - 返回的 `jsonData` 是一个 `[]byte` 类型的字节切片，包含了序列化后的 JSON 数据。
4. **错误处理**：
   - 在调用 `json.Marshal()` 后，通过检查 `err` 来处理可能发生的错误，例如字段名错误、类型不支持等情况。
5. **打印输出**：
   - 最后使用 `fmt.Println(string(jsonData))` 将字节切片 `jsonData` 转换为字符串并打印出来，展示了序列化后的 JSON 数据。

输出结果

运行上述代码，输出将是以下格式的 JSON 数据：

```json
{"name":"Alice","age":30}
```

这是将 `Person` 结构体实例 `p` 序列化为 JSON 字符串后的结果。

注意事项

- 在使用 `json.Marshal()` 进行序列化时，结构体字段必须是公开的（即首字母大写），否则 `json.Marshal()` 无法访问到这些字段。
- 使用 `json.Marshal()` 还可以处理其他类型的数据，如 map、切片、基本类型等，都可以被转换为对应的 JSON 格式数据。



当我们需要**将 JSON 数据解析为 Go 语言中的结构体**时，可以使用 `json.Unmarshal()` 函数。这个函数接收 JSON 格式的字节切片作为输入，然后将其解析并填充到指定的结构体变量中。

示例用法

假设我们有一个 JSON 字符串，表示一个 `Person` 对象的数据：

```json
{
    "name": "Alice",
    "age": 30
}
```

我们需要将这个 JSON 数据解析成 `Person` 结构体：

```go
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    // JSON 数据
    jsonData := []byte(`{"name":"Alice","age":30}`)

    // 定义一个 Person 类型的变量
    var p Person

    // 使用 json.Unmarshal() 解析 JSON 数据到结构体变量
    err := json.Unmarshal(jsonData, &p)
    if err != nil {
        fmt.Println("JSON decoding error:", err)
        return
    }

    // 打印解析后的结构体内容
    fmt.Println("Name:", p.Name)
    fmt.Println("Age:", p.Age)
}
```

解释说明：

1. **定义结构体 `Person`**：
   - `Person` 结构体和之前的例子中一样，有 `Name` 和 `Age` 两个字段。
2. **JSON 数据**：
   - `jsonData` 是一个 `[]byte` 类型的字节切片，包含了上面给出的 JSON 数据。
3. **使用 `json.Unmarshal()`**：
   - `json.Unmarshal(jsonData, &p)` 将 JSON 数据 `jsonData` 解析为结构体变量 `p`。
   - 第二个参数 `&p` 是结构体变量的指针，确保 `Unmarshal` 函数能够修改 `p` 的值。
4. **错误处理**：
   - 在调用 `json.Unmarshal()` 后，检查 `err` 变量来处理可能发生的解析错误，例如 JSON 数据格式不正确等情况。
5. **打印输出**：
   - 最后通过 `fmt.Println()` 打印解析后的结构体 `Person` 的字段 `Name` 和 `Age` 的值。

输出结果

运行上述代码，输出将会是：

```
Name: Alice
Age: 30
```

这是将 JSON 数据成功解析为 `Person` 结构体变量后，打印出的结果。

注意事项

- 在使用 `json.Unmarshal()` 进行解析时，确保目标结构体中的字段类型和 JSON 数据中的字段类型匹配，否则会导致解析失败或类型转换错误。
- JSON 数据中的字段名（如 `"name"` 和 `"age"`）需要和结构体中的字段标签（如 `json:"name"` 和 `json:"age"`）一致，否则 `Unmarshal()` 函数无法正确映射数据到结构体字段。



# 匿名函数和闭包

## 匿名函数

```go
//格式
func(参数)(返回值){
    函数体
}
//使用
func main() {
	// 将匿名函数保存到变量
	add := func(x, y int) {
		fmt.Println(x + y)
	}
	add(10, 20) // 通过变量调用匿名函数

	//自执行函数：匿名函数定义完加()直接执行
	func(x, y int) {
		fmt.Println(x + y)
	}(10, 20)
}
```

## 闭包

# 文件目录操作

## 文件路径名

```
/根目录
./ 代表当前文件目录
../上级文件目录
/*该目录下的任何文件名
```

## linux目录命令

```
//cd命令
cd~回到当前用户home目录
cd-回到上一次所在目录
cd..回到上一级目录
cd 绝对/相对路径 切换目录
//pwd命令
显示当前工作目录的绝对路径
```

## 文件的打开和关闭

```go
//Open函数
func Open(name string) (file *File, err error)
//close函数
func (f *File) Close() error
//一般与defer连用
```

## 文件读取

### read函数（不带缓冲区读取）

```go
//read方法从f中读取最多len（b）字节数据写入b
func (f *File) Read(b []byte) (n int, err error)
```

```go
//demo4
package main

import (
	"fmt"
	"io"
	"os"
)

func main() {
	fp, err := os.Open("C:\\Users\\Administrator\\Desktop\\To do list.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer fp.Close()
    //read()无缓冲，需要自己建缓冲存取数据
	buf := make([]byte, 1024)
	for {
		n, err := fp.Read(buf)
		if err == io.EOF {
			break
		}
		if err != nil {
			fmt.Println(err)
			return
		}
		fmt.Println(string(buf[:n]))

	}
}
```



```go
//示例
package main

import (
	"fmt"
	"os"
)

func main() {
	//获取当前包的工作路径
	fmt.Println(os.Getwd()) //C:\Users\Administrator\GolandProjects\project1 <nil>
	//修改相对路径
	erro := os.Chdir("C:\\Users\\Administrator\\GolandProjects\\project1\\src\\main\\文件目录")
	if erro != nil {
		fmt.Println(erro)
		return
	}
    fmt.Println(os.Getwd())
    /*C:\Users\Administrator\GolandProjects\project1\src\main\文件目录 <nil>*/
	fp, err := os.Open("./xx.txt")
	if err != nil {
		fmt.Printf("open file failed, err:%v\n", err)
		return
	}
	defer fp.Close()
	buf := make([]byte, 1024)
	n, err := fp.Read(buf)
	if err != nil {
		fmt.Printf("read file failed, err:%v\n", err)
		return
	}
	fmt.Println("read %d bytes", n)
	fmt.Println(string(buf[:n]))
}
```

### bufio读取文件

```go
func NewReader(rd io.Reader) *Reader
NewReader returns a new Reader whose buffer has the default size.
```



```go
//ReadBytes读取直到第一次遇到delim字节（指定的分隔符），返回一个包含已读取的数据和delim字节的切片。如果ReadBytes方法在读取到delim之前遇到了错误，它会返回在错误之前读取的数据以及该错误（一般是io.EOF）。当且仅当ReadBytes方法返回的切片不以delim结尾时，会返回一个非nil的错误。
func (b *Reader) ReadBytes(delim byte) (line []byte, err error)

//ReadString读取直到第一次遇到delim字节，返回一个包含已读取的数据和delim字节的字符串。如果ReadString方法在读取到delim之前遇到了错误，它会返回在错误之前读取的数据以及该错误（一般是io.EOF）。当且仅当ReadString方法返回的切片不以delim结尾时，会返回一个非nil的错误。
func (b *Reader) ReadString(delim byte) (line string, err error)
```

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)
func main() {
	fp, err := os.Open("C:\\Users\\Administrator\\Desktop\\11.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer fp.Close()
	r := bufio.NewReader(fp)
	for {
		buf, err := r.ReadBytes('\n')
        //根据读取的每行的数据的长度返回不同长度的切片
        fmt.Printlen(len(buf))
		fmt.Print(string(buf))
		if err == io.EOF {
			break
		}
	}
}
```

```go
//func (b *Reader) ReadString(delim byte) (line string, err error)
func main() {
	fp, err := os.Open("C:\\Users\\Administrator\\Desktop\\11.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer fp.Close()
	r := bufio.NewReader(fp)
	for {
		buf, err := r.ReadString('\n')
		fmt.Print(string(buf))
		if err == io.EOF {
			break
		}
	}
}
```

```go
func ReadFile(filename string) ([]byte, error)
/*
ReadFile reads the file named by filename and returns the contents. A successful call returns err == nil, not err == EOF. Because ReadFile reads the whole file, it does not treat an EOF from Read as an error to be reported.
*/
```

```go
package main
import (
	"fmt"
	"io/ioutil"
)

func main() {
    //文件路径名作为参数
	buf, err := ioutil.ReadFile("C:\\Users\\Administrator\\Desktop\\11.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(buf))
}

```

## 文件的创建

```go
func Create(name string) (*File, error)
/*
Create creates or truncates the named file. If the file already exists, it is truncated. If the file does not exist, it is created with mode 0666 (before umask). If successful, methods on the returned File can be used for I/O; the associated file descriptor has mode O_RDWR. If there is an error, it will be of type *PathError.
Create采用模式0666（任何人都可读写，不可执行）创建一个名为name的文件
如果文件存在会覆盖原有文件
该文件会在电脑上创建到你所给定的路径位置上
*/
```

```go
package main
import (
	"fmt"
	"os"
)
func main() {
	fp, err := os.Create("C:\\Users\\Administrator\\Desktop\\nihao.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer func() {
		err := fp.Close()
		if err != nil {
			fmt.Println(err)
		}
	}()
}

```

## 文件的写入

### 无缓冲写入

```go
func (f *File) Write(b []byte) (n int, err error)
/*Write writes len(b) bytes to the File. It returns the number of bytes written and an error, if any. Write returns a non-nil error when n != len(b).
*/
```

```go
func (f *File) WriteString(s string) (ret int, err error)
/*WriteString is like Write, but writes the contents of string s rather than a slice of bytes.
*/
```

```go
package main
import (
	"fmt"
	"os"
)

func main() {
	fp, err := os.Create("C:\\Users\\Administrator\\Desktop\\test.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer func() {
		err := fp.Close()
		if err != nil {
			fmt.Println(err)
		}
	}()
    //write()无缓冲自己创建切片
	bytes := []byte{'h', 'e', 'l', 'l', 'o', '\n'}
	fp.Write(bytes)
    //写字符串
	fp.WriteString("大傻叉")
}
```

### 有缓冲写入

```go
func OpenFile(name string(打开路径）, flag int（打开模式）, perm FileMode（指定权限) (file *File, err error)
/*
OpenFile is the generalized open call; most users will use Open or Create instead. It opens the named file with specified flag (O_RDONLY etc.). If the file does not exist, and the O_CREATE flag is passed, it is created with mode perm (before umask). If successful, methods on the returned File can be used for I/O. If there is an error, it will be of type *PathError.
*/
```

打开模式

```go
const (
  O_RDONLY int = syscall.O_RDONLY // 只读模式打开文件
  O_WRONLY int = syscall.O_WRONLY // 只写模式打开文件
  O_RDWR   int = syscall.O_RDWR   // 读写模式打开文件
  O_APPEND int = syscall.O_APPEND // 写操作时将数据附加到文件尾部
  O_CREATE int = syscall.O_CREAT  // 如果不存在将创建一个新文件
  O_EXCL   int = syscall.O_EXCL   // 和O_CREATE配合使用，文件必须不存在
  O_SYNC   int = syscall.O_SYNC   // 打开文件用于同步I/O
  O_TRUNC  int = syscall.O_TRUNC  // 如果可能，打开时清空文件
)
```

指定权限

- 0没有任何权限

- 1.执行权限(如果是可执行程序, 可以运行)

- 2.写权限

- 3.写权限和执行权限

- 4.读权限

- 5.读权限和执行权限

- 6.读权限和写权限

- 7.读权限和写权限以及执行权限

- ```go
  const (
    // 单字符是被String方法用于格式化的属性缩写。
    ModeDir        FileMode = 1 << (32 - 1 - iota) // d: 目录
    ModeAppend                                     // a: 只能写入，且只能写入到末尾
    ModeExclusive                                  // l: 用于执行
    ModeTemporary                                  // T: 临时文件（非备份文件）
    ModeSymlink                                    // L: 符号链接（不是快捷方式文件）
    ModeDevice                                     // D: 设备
    ModeNamedPipe                                  // p: 命名管道（FIFO）
    ModeSocket                                     // S: Unix域socket
    ModeSetuid                                     // u: 表示文件具有其创建者用户id权限
    ModeSetgid                                     // g: 表示文件具有其创建者组id的权限
    ModeCharDevice                                 // c: 字符设备，需已设置ModeDevice
    ModeSticky                                     // t: 只有root/创建者能删除/移动文件
    // 覆盖所有类型位（用于通过&获取类型位），对普通文件，所有这些位都不应被设置
    ModeType = ModeDir | ModeSymlink | ModeNamedPipe | ModeSocket | ModeDevice
    ModePerm FileMode = 0777 // 覆盖所有Unix权限位（用于通过&获取类型位）
  )
  ```

  

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {

	// 1.打开文件
	fp, err := os.OpenFile("C:\\Users\\Administrator\\Desktop\\test2.txt", os.O_CREATE|os.O_APPEND, 0666)
	if err != nil {
		fmt.Println(err)
	}
	// 2.关闭打开的文件
	defer func() {
		err := fp.Close()
		if err != nil {
			fmt.Println(err)
            
		}
	}()

	// 3.创建缓冲区
	w := bufio.NewWriter(fp)
	// 4.写入数据到缓冲区
	bytes := []byte{'l', 'o', 'v', 'e', '\n'}
	w.Write(bytes)
	w.WriteString("大傻叉\n")

	// 5.将缓冲区中的数据刷新到文件
	w.Flush()
}
```

```go
func WriteFile(filename string, data []byte, perm fs.FileMode) error
/*
WriteFile writes data to a file named by filename. If the file does not exist, WriteFile creates it with permissions perm (before umask); otherwise WriteFile truncates it before writing, without changing permissions.
*/
```

示例

```go
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {
	data := []byte{'y', 'y', 'w', 'h', 'h'}
	err := ioutil.WriteFile("C:\\Users\\Administrator\\Desktop\\demo10.txt", data, 0666)
	if err != nil {
		fmt.Println(err)
		return
	} else {
		fmt.Println("写入成功")
	}
}

```



## 判断文件是否存在

```go
func Stat(name string) (fi FileInfo, err error)
/*
Stat returns the FileInfo structure describing file. If there is an error, it will be of type *PathError.
*/
```

```go
type FileInfo interface {
    Name() string       // base name of the file
    Size() int64        // length in bytes for regular files; system-dependent for others
    Mode() FileMode     // file mode bits
    ModTime() time.Time // modification time
    IsDir() bool        // abbreviation for Mode().IsDir()
    Sys() interface{}   // underlying data source (can return nil)
}
```

```go
func IsNotExist(err error) bool
/*
IsNotExist returns a boolean indicating whether the error is known to report that a file or directory does not exist. It is satisfied by ErrNotExist as well as some syscall errors.

This function predates errors.Is. It only supports errors returned by the os package. New code should use errors.Is(err, fs.ErrNotExist).
*/
```

```go
package main
import (
	"fmt"
	"os"
)

func main() {

	info, err := os.Stat("C:\\Users\\Administrator\\Desktop\\11.txt")
	if err == nil {
		fmt.Println("文件存在")
		fmt.Println(info.Name())
	} else if os.IsNotExist(err) {
		fmt.Println("文件不存在")
	} else {
		fmt.Println("不确定")
	}
}

```

 
