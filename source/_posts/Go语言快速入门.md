---
abbrlink: ''
categories:
- 程序开发
- Go
date: '2023-04-07T15:18:42+08:00'
tags:
- Go
title: Go语言快速入门
updated: 2023-11-20T16:57:36.254+8:0
---
## go语言的优势

1. 极其简单的部署方式
   * 可直接编译成机器码
   * 不依赖其他库
   * 直接运行既可部署
2. 静态类型语言
   * 编译时就能检查出隐藏的问题
3. 语言层面的并发
   * 天生的基因支持
   * 充分利用多核
4. 强大的标准库支持
   * runtime系统调度机制
   * 高效的GC垃圾回收
   * 丰富的标准库
5. 简单易学
   * 25个关键字
   * C语言基因，内嵌C语法支持
   * 面向对象特征（封装、继承、多态）
   * 跨平台

## go语言的缺点

1. 包管理，大部分都在github上
2. 无泛化类型
3. 所有Excepiton都用Error来处理
4. 对C的降级处理，并非无缝，没有C降级到asm那么完美（序列化问题）

## 安装go语言

解压源码包

```bash
sudo tar -zxvf gox.xx.x.linux-amd64.tar.gz -C /usr/local/
```

配置环境变量

```bash
vim ~/.bashrc
```

```bash
#设置go语言路径
export GOTOOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
#启动Go Modules
export GO11MODULE=on
```

```bash
source ~/.bashrc
```

检查开发环境

```bash
go version
go --help
```

## 基本语法

### 第一个go程序

```golang
package main //程序的包名

//导入一个包的写法
import "fmt"
//导入多个包的写法
import (
    "fmt"
    "time"
)

//main函数
func main() { //函数的左花括号必须和函数名在同一行
    //golang中的表达式，加不加“;”都可以
    fmt.Println("hello Go!")
}
```

### 变量的声明方式

```golang
func main() {
   //声明一个变量，默认值是0
   var a int
   //声明一个变量，初始化一个值
   var b int = 100
   //在初始化时候可以省去数据类型，通过值自动匹配当前变量的数据类型
   var c = 100
   fmt.Printf("type of c = %T\n", c)
   //省去var关键字，直接自动匹配，这个方法只能在函数内声明不能声明全局变量
   e := 100
   fmt.Printf("type of c = %T\n", c)
   //声明多个变量
   var xx, yy int = 100, 200
}
```

### 常量和枚举

```golang
//const 来定义枚举类型
const (
   BEIJING = 0
   SHANGHAI = 1
)
//可以使用关键字iota, 每行都会累加
const (
   SHENZHEN = iota //iota = 0
   WUHAN          //iota = 1
)

func main() {
   //只读属性常量，不允许修改
   cost length int = 10
}
```

### 函数返回值

```golang
//返回一个值
func fool(a string, b int) int { //a和b表示形参，最后一个int是返回值类型，没有返回值可以不写
   c := 100
   return c
}
//返回多个匿名值
func fool(a string, b int) (int, int) {
   c := 100
   return c, 2*c
}
//返回多个值，有形参名
func fool(a string, b int) (r1 int, r2 int) { //r1和r2是同一个类型还可以用另一种写法:(r1, r2 int)
   r1 = 1000
   r2 = 2000
   return
}
```

### init函数

init是初始化函数，在导入包时被调用

```golang
package lib

import "fmt"

//函数名大写，表示对外开放的接口
func LibTest() {
   fmt.Println("LibTest() ...")
}

func init() {
   fmt.Println("lib. init() ...")
}
```

在主函数导入lib包，就会执行lib的init函数

```golang
package main

import "lib"

func main() {
   lib.LibTest()
}
```

### import 导包

在导入其他包时，如果不使用该包的api则会报错，使用匿名导入则不会报错

```golang
package main

import (
   _ "lib"  //导入时加下划线就是匿名导入，只运行该包的init方法
   f "fmt"  //导入时可以加上别名
   . "lib2" //导入包的所有方法，相当于py的"from lib2 import *"
)

func main() {
   f.Println("lib Test")
   Lib2Test() //用点符号导入lib2中的方法可以直接使用
}
```

### 指针

指针可以传递内存地址

```golang
package main

import "fmt"

func changeValue(p int){
   p = 10
}

func changeValue2(p *int){ //*int是指针类型，存储内存地址
   *p = 10 //p是内存地址，*p表示p指向的内存地址的值
}

func main() {
   var a int = 1
   changeValue(a) //传递a的值拷贝
   fmt.Println("a = ", a) //a = 1
   changeValue2(&a) //传递a的内存地址
   fmt.Println("a = ", a) //a = 10
}
```

二级指针可以找到一级指针的内存地址，依次类推

```golang
package main

import "fmt"

func swap(pa *int, pb *int){
   var temp int
   temp = *pa
   *pa = *pb
   *pb = temp
}

func main() {
   //a和b的值交换方法
   var a int = 10
   var b int = 20
   swap(&a, &b) 
   fmt.Println("a = ", a, "b = ", b) //a = 10

   //一级指针
   var p *int
   p = &a
   fmt.Println(&a)
   fmt.Println(p) // p == &a
   //二级指针
   var pp **int
   pp = &p
   fmt.Println(&p)
   fmt.Println(pp) // pp == &p
   //三级指针 ...
}
```

### defer 语句

defer在函数最后执行，相当于析构函数

```golang
package main

import "fmt"

func main() {
   //写入defer关键字
   defer fmt.Println("main end") //defer的执行顺序是先入后出，所以这句代码最后执行
   defer fmt.Println("main end2")
   fmt.Println("hello go") //最先执行没有被defer的语句
}
```

注意rerun不是原子操作，它分三步，第一步赋值，第二步执行defer，第三步才是真正的return

```golang
package main

import "fmt"

func deferFunc() int {
   fmt.Println("defer func called...")
   return 0
}

func returnFunc() int {
   fmt.Println("return func called...")
   return 0
}

func returnAndDefer() {
   defer deferFunc()
   return returnFunc()
}

func main() {
   returnAndDefer() //这里的执行顺序是先return再defer，实际上defer属于return的中间操作
   //return func called...
   //defer func called...
}
```

### 数组

动态数组比固定数组传参更方便

```golang
package main

import "fmt"

func printArray(myArray [10]int){ //固定长度数组传参方式，形参必须和实参长度相等
   for index, value := range myArray{
      fmt.Println("index = ", index, ", value = ", value)
   }
   myArray[0] = 100 //固定数组传参相当于值拷贝（值传递），不会影响到原来的变量
}

func printArray2(myArray []int){ //动态长度数组传参方式
   for _, value := range myArray{
      fmt.Println("index = ", index, ", value = ", value)
   }
   myArray[0] = 100 //动态数组传参相当于地址拷贝（引用传递），所以这里修改有效
   
   //按引用传递其实也可以称作"按值传递",只不过该副本是一个地址的拷贝，通过它可以修改这个值所指向的地址上的值
}

func main() {
   //固定长度数组，参数默认都是0
   var myArray1 [10]int
   //有默认值的数组
   myArray2 := [10]int{1,2,3,4}
   //动态数组，切片类型 slice
   myArray3 := []int{1,2,3,4}
}
```

### 切片 slice

#### 切片声明

声明切片时可以声明容量cap和长度len

```golang
package main

import "fmt"

func main() {
   //声明slice1是一个切片，并初始化
   slice1 := []int{1,2,3,4}
   //声明slice1是一给切片，但是不分配空间
   var slice1 []int
   slice1 = make([]int, 3) //开辟三个空间
   //声明slice1是一给切片，分配三个空间
   var slice1 []int = make([]int, 3)
   //通过:=推导slice1是一个切片
   slice1 := make(int[],3)
   //判断切片是否为空
   if slice1 == nil{
      fmt.Println("空切片")
   }
}
```

#### 切片容量的追加

当切片的容量超过时，会重新申请内存然后拷贝

```golang
package main

import "fmt"

func main() {
   //声明一个切片，长度3容量5
   var numbers = make([int], 3, 5)
   fmt.Printf("len=%d, cap=%d, slice=%v\n", len(numbers), cap(numbers), numbers)
   //给numbers追加一个元素1
   numbers = append(numbers, 1)
   //当追加的元素超过切片的容量，切片会将容量增加两倍（ps:对于长度比较大的切片golang会尝试扩容1/4，以节省内存）
   numbers = append(numbers, 2)
   numbers = append(numbers, 3)
   fmt.Printf("len=%d, cap=%d, slice=%v\n", len(numbers), cap(numbers), numbers)
}
```

#### 切片截取

截取元素和python一致，但是截取后的元素属于浅拷贝，修改会影响原始切片

```golang
package main

func main() {
   //声明切片
   s := []int{1,2,3}
   //截取元素0到2，不包括2
   s1 := s[0:2]
   s1 := s[:3]
   s1 := s[4:]
   s1 := s[::]
}
```

深拷贝修改不会影响原始切片

```golang
package main

func main() {
   s := []int{1,2,3}
   s2 := make([]int, 3)
   //将s中的值，依次拷贝到s2, 修改s2不影响s
   copy(s2,s)
}
```

### 字典 map

#### map 的声明方式

```golang
package main

func main() {
   //声明一个空map
   var myMap1 map[string]string
   //声明一个map,开辟十个空间
   myMap2 = make(myMap1[string]string,10)
   myMap2['go'] = "hello go"
   //使用:=声明
   myMap3 := make(map[int]string)
   //声明并赋值
   myMap4 := map[string]string{
      "go": "hello go"
      "python": "hello python"
   }

}
```

#### map 的使用方式

```golang
package main

import "fmt"

func ChangeValue(langMap map[string]string) {
   for key, value := range langMap {
      fmt.Print("key = ", key)
      fmt.Print("value = ", value)
   }
   //可以直接修改影响原来的变量
   langMap['pyton'] = "byebye pyton"
}

func main() {
   myMap2 = make(langMap[string]string)
   //添加
   langMap['go'] = "hello go"
   langMap['pyton'] = "hello pyton"
   //遍历
   for key, value := range langMap {
      fmt.Print("key = ", key)
      fmt.Print("value = ", value)
   }
   //删除
   delete(langMap, "python")
   //修改
   langMap['pyton'] = "bye pyton"
   //传参,引用传递
   ChangeValue(langMap)
}
```

### 结构体 struct

```golang
package main

import "fmt"

//声明新的数据类型myint，是int的别名
type myint int

//定义结构体
type Book struct {
   title string
   auth string
}

func changeBook(book Book) {
   //修改不会影响到原来的变量
   book.auth = "li4"
}

func main() {
   //使用结构体
   var book1 Book
   book1.title = "golang"
   book1.auth = "zhang3"
   fmt.Print("%v\n", book1)
   //结构体传参是值传递
   changeBook(book1)
   fmt.Print("%v\n", book1)
}
```

### 面向对象

#### 面向对象的表示和封装

这段代码使用SetName修改属性并不能成功，因为this关键字相当于hero的值拷贝

```golang
package main

import "fmt"

type Hero struct {
   Name string
   Ad string
   Level
}

func (this Hero) Show() {
   fmt.Println("Name = ", this.Name)
   fmt.Println("Ad = ", this.Ad)
   fmt.Println("Level = ", this.Level)
}

func (this Hero) GetName() {
   return this.Name
}

func (this Hero) SetName(newName string) {
   this.Name = newName
}

func main() {
   //创建对象
   hero := Hero{Name: "zhang3", Ad: 100, Level: 1}
   hero.Show()
   hero.SetName("li4")
   hero.Show()
}
```

要修改属性，那么指针用起来

```golang
package main

import "fmt"

//定义类，首字母大写表示其他包也能访问
type Hero struct {
   //属性首字母大写，表示对外开放，小写表示私有，只能在类的内部访问
   Name string
   Ad int
   Level int
}

func (this *Hero) Show() {
   fmt.Println("Name = ", this.Name)
   fmt.Println("Ad = ", this.Ad)
   fmt.Println("Level = ", this.Level)
}

func (this *Hero) GetName() {
   return this.Name
}

func (this *Hero) SetName(newName string) {
   this.Name = newName
}

func main() {
   //创建对象
   hero := Hero{Name: "zhang3", Ad: 100, Level: 1}
   hero.Show()
   hero.SetName("li4")
   hero.Show()
}
```

#### 面向对象继承

```golang
package main

import "fmt"

type Human struct {
   name string
   sex string
}

func (this *Human) Eat() {
   fmt.Println("Human Eat()")
}

type SuperMan struct {
   Human //继承Human的方法
   level int
}

//重载
func (this *SuperMan) Eat() {
   fmt.Println("SuperMan Eat()")
}


func main() {
   h := Human{"zhang3", "man"}
   h.Eat()
   //子类定义
   //s := SuperMan{Human{"li4", "man"}}
   //子类定义方法2
   var s SuperMan
   s.name = "li4"
   s.sex = "man"
   s.level = 99
   s.Eat()
}
```

#### 面向对象多态

go语言通过接口interface实现多态

```golang
package main

import "fmt"

//接口，本质是一个指针
type AnimalIF interface {
   Sleep()
   GetColor() string
   GetType() string
}

//具体的类
type Cat struct {
   color string
}

//接口的实现
func (this *Cat) Sleep() {
   fmt.Println("Cat is Sleep")
}
func (this *Cat) GetColor() {
   return this.color
}
func (this *Cat) GetType() {
   return "Cat"
}

type Dog interface {
   color string
}

func (this *Dog) Sleep() {
   fmt.Println("Dog is Sleep")
}
func (this *Dog) GetColor() {
   return this.color
}
func (this *Dog) GetType() {
   return "Dog"
}


func main() {
   var animal AnimalIF //接口类型，父类指针
   animal = &Cat{"Green"}
   animal.Sleep() //调用的就是Cat的Sleep方法

   animal = &Cat{"Yellow"}
   animal.Sleep() //调用Dog的Sleep方法，多态现象
}
```

#### 万能类型 interface

接口是一种通用的万能类型

```golang
package main

import "fmt"

func myFunc(arg interface{}) {
   fmt.Println("myFunc is called...")
   fmt.Println(arg)
   //类型断言机制
   value, ok = arg.(srting)
   if ok {
      fmt.Println("arg is not string type")
   } else {
      fmt.Println("arg is string type, value = ", value)
      fmt.Println("value type is %T\n", value)
   }
}

type Book struct {
   auth sting
}


func main() {
   book := Book{"Golang"}
   //传任何类型都可以
   myFunc(book)
   myFunc(100)
   myFunc("abc")
   myFunc(3.14)
}
```

### 反射

go语言的变量类型分为静态类型和具体类型，反射就是通过变量得到当前变量的具体类型

```golang
package main

import "fmt"

func main() {
   var a string
   //静态类型：pair<statictype:string, value:"aceld">
   a = "aceld"
   //pair<type:string, value:"aceld">
   var allType interface{}
   allType = a
   //通过断言获取类型
   str, _ := allType.(string)
   fmt.Println(str)
}
```

无论怎么传值变量的类型都不会变

```golang
package main

import (
   "fmt"
   "os"
   "io"
)

func main() {
   //tty: pair<type:*os.File, value:"/dev/tty">
   tty, err := os.OpenFile("/dev/tty", os.O_RDWR, 0)
   if err != nil {
      fmt.Println("open file error", err)
      return
   }

   var r io.Reader
   //r: pair<type:*os.File, value:"/dev/tty">
   r = tty
   //w: pair<type: , value:>
   var w io.Writer
   //w: pair<type:*os.File, value:"/dev/tty">
   w = r.(io.Writer) //断言为io.Writer，此时w的具体类型依然是*os.File
   w.Wrriter([]byte("HELLO THIS is A TEST!!!\n"))
}
```

不同的接口type是一致的

```golang
package main

import "fmt"

type Reader interface {
   ReadBook()
}

type Writer interface {
   WriterBook()
}

//具体类型
type Book struct {
}

func (this *Book) ReadBook() {
   fmt.Println("Read a Book")
}

func (this *Book) WriterBook() {
   fmt.Println("Write a Book")
}

func main() {
   //b: pair<type:Book, value:book{}地址>
   b := &Book{}

   var r Reader
   r = b
   r.ReadBook()

   var w Writer
   //b: pair<type:Book, value:book{}地址>
   w = r.(Writer) // 此次断言成功是因为r和w具体类型都是Book
   w.WriteBook()
}
```

#### reflect 包

通过reflect包的接口可以获取类型

```golang
package main

import (
   "fmt"
   "reflect"
)

type User struct {
   Id int
   Name string
   Age int
}

func (this User) Call() {
   fmt.Println("user is called ..")
   fmt.Printf("%v\n", this)
}

func reflectNum(arg interface{}) {
   fmt.Println("type: ", reflect.TypeOf(arg)) //获取type
   fmt.Println("value: ", reflect.ValueOf(arg)) //获取value
}

func main() {
   //获取基本类型
   var num foat64 = 1.2345
   reflectNum(num)
   
   //获取复杂类型
   inputType := reflect.TypeOf(input)
   fmt.Println("inputType is :", inputType.Name())
   inputValue := reflect.TypeOf(input)
   fmt.Println("inputType is :", inputType)

   //通过type获取字段
   for r := 0; i < inputType.NumField(): i++ {
      field := inputType.Field(i)
      value := inputValue.Field(i).Interface()
      fmt.Printf("%s: %v = %v\n", field.Name, field.Type, value)
   }
   //通过type获取方法
   for r := 0; i < inputType.NumMethod(): i++ {
      m := inputType.Method(i)
      fmt.Printf("%s: %v\n", m.Name, m.Type)
   }
}
```

#### 结构体标签

标签可以对结构体说明，是可以用反射获取到的信息

```golang
package main

import (
   "fmt"
   "reflect"
)

type resume struct {
   Name string `info:"name",doc:"我的标签"`
   Sex  string `info:"sex"`
}

func findTab(str interface{}) {
   t := reflect.TypeOf(str).Elem() //指针使用Elem方法获取type
   for i := 0; i < t.NumField(); i++ {
      taginfo := t.Field(i).Tag.Get("info")
      tagdoc := t.Field(i).Tag.Get("doc")
      fmt.Println("info: ", taginfo, "doc: ", tagdoc)
   }
}

func main() {
   var re resume
   findTab(&re)
}
```

结构体标签在json中的应用

```golang
package main

import (
   "fmt"
   "encoding/json"
)

type Movie struct {
   Tittle string `json:"title"`
   Year int `json:"year"`
   price int `json:"rmb"`
   Actors []string `json:"actors"`
}

func main() {
   movie := Movie{"喜剧之王",2000,10,[]string{"xingye","zhangbozhi"}}
   //编码
   jsonStr, err := json.Marshal(movie)
   if err != nil {
      fmt.Println("json marshal error", err)
   }
   fmt.Printf("jsonStr = %s\n", jsonStr)
   //解码
   myMovie := Movie{}
   err = json.Unmarshal(jsonStr, &myMovie)
   if err != nil {
      fmt.Println("json unmarshal error", err)
      return
   }
   fmt.Printf("%v\n", myMovie)
}
```

## 并发

在讲解并发概念时，总会涉及另外一个概念并行。下面让我们来了解并发和并行之间的区别。

* 并发（concurrency）：把任务在不同的时间点交给处理器进行处理。在同一时间点，任务并不会同时运行。
* 并行（parallelism）：把每一个任务分配给每一个处理器独立完成。在同一时间点，任务一定是同时运行。

并发不是并行。并行是让不同的代码片段同时在不同的物理处理器上执行。并行的关键是同时做很多事情，而并发是指同时管理很多事情，这些事情可能只做了一半就被暂停去做别的事情了。

在很多情况下，并发的效果比并行好，因为操作系统和硬件的总资源一般很少，但能支持系统同时做很多事情。这种“使用较少的资源做更多的事情”的哲学，也是指导 Go语言设计的哲学。

### 协程 goroutine

goroutine 是一种非常轻量级的实现，可在单个进程里执行成千上万的并发任务，它是Go语言并发设计的核心。

说到底 goroutine 其实就是线程，但是它比线程更小，十几个 goroutine 可能体现在底层就是五六个线程，而且Go语言内部也实现了 goroutine 之间的内存共享。

#### 创建 goroutine

在普通函数前使用关键字go来创建goroutine。

```golang
func main() {
   go newTask()
}
```

匿名函数创建goroutine。

```golang
func main() {
   go func() {
      defer fmt.Println("A.defer")
      func() {
         defer fmt.Println("B.defer")
         runtie.Goexit()//终止当前goroutime
         fmt.Println("B")
      }()
      fmt.Println("A")
   }()

   go func(a int, b int) bool {
      fmt.Println("a =", a, ",b =", b)
   }(10, 10)
}
```

### 通道 channel

channel 是Go语言在语言级别提供的 goroutine 间的通信方式。我们可以使用 channel 在两个或多个 goroutine 之间传递消息。

channel有同步效果，会在等待返回值时阻塞。

```golang
func main() {
   //定义一个channel
   c := make(chan int)
   go func() {
      defer fmt.Println("goroutine结束")
      fmt.Println("goroutine正在运行...")
      c <- 666 //将666发送给c
   }()

   num := <-c //从c中接受数据
   fmt.Println("num =", num)
   fmt.Println("main goroutine结束")
}
```

#### 有缓冲的 channel

无缓冲通道保证收发过程同步，在无缓冲通道的基础上，为通道增加一个有限大小的存储空间形成带缓冲通道。带缓冲通道在发送时无需等待接收方接收即可完成发送过程，并且不会发生阻塞，只有当存储空间满时才会发生阻塞。同理，如果缓冲通道中有数据，接收时将不会发生阻塞，直到通道中没有数据可读时，通道将会再度阻塞。

```golang
func main() {
   c := make(chan int, 3) //带有缓冲的channel,大小超过3个时会阻塞
   fmt.Println("len(c) =", len(c), ", cap(c)", cap(c))
   go func() {
      defer fmt.Println("goroutine结束")
      for i:= 0, i<3; i++ {
         c <- i
         fmt.Println("goroutine正在运行：len(c) =", len(c), ", cap(c)", cap(c))
      }
   }()
   time.Sleep(2 * time.Second)
   for i := 0;i <3: i++ {
      num := <-c //从c中接受数据
      fmt.Println("num =", num)
   }
   fmt.Println("main goroutine结束")
}
```

#### 关闭 channel

channel 不像文件一样需要经常关闭，只有当你确实没有发送任何数据了，或者你想显式结束range循环之类的，才去关闭channel。

关闭channel后，无法向channel再发数据，可以继续接收channel数据。

对于nil channel（没有用make初始化）,无论收发都会被阻塞。

```golang
func main() {
   c := make(chan int)
   go func() {
      for i:= 0, i<5; i++ {
         c <- i
      }
      //close可以关闭一个channel
      close(c)
   }()
   for {
      //ok如果为true表示channel没有关闭，如果为false表示channel已经关闭
      if data, ok := <- c; ok {
         fmt.Println(data)
      }else{
         break
      }
   }
   fmt.Println("Main Finished..")
}
```

可以使用range来迭代操作channel

```golang
/*
for {
      //ok如果为true表示channel没有关闭，如果为false表示channel已经关闭
      if data, ok := <- c; ok {
         fmt.Println(data)
      }else{
         break
      }
   }
*/
//上面的代码可以使用range来迭代操作channel
for data := range c {
   fmt.Println(data)
}
```

#### 多路复用

单流程下go只能监控一个channel的状态，select可以完成监控多个channel的状态

```golang
select {
   case <- chan1:
   //如果chan1成功读取到数据，则进行case处理语句
   case chan2 <- 1:
   //如果成功向chan2写入数据，则进行case处理语句
   default:
   //如果上面都没有成功，则进入default处理语句
}
```

用法实例

```golang
func fibonacii(c, quit chan int) {
   x, y := 1, 1
   for { //for里面的select一般不加default
      select {
         case c <- x:
            x = y
            y = x + y
         case <- quit:
            fmt.Println("quit")
            return
      }
   }
}

func main() {
   c := make(chan int)
   quit := make(chan int)
   go func() {
      for i:=0;i<6;i++ {
         fmt.Println(<-c)
      }
      quit <- 0
   }()

   fibonacii(c, quit)
}
```

## Go Modules

GOPATH 模式下没有版本控制的概念，具有致命的缺陷，至少会造成以下问题：

* 在执行go get的时候，你无法传达任何的版本信息的期望，也就是说你也无法知道自己当前更新的是哪一个版本，也无法通过指定来拉取自己所期望的具体版本。
* 在运行 Go 应用程序的时候，你无法保证其它人与你所期望依赖的第三方库是相同的版本，也就是说在项目依赖库的管理上，你无法保证所有人的依赖版本都一致。
* 你没办法处理 v1、v2、v3 等等不同版本的引用问题，因为 GOPATH 模式下的导入路径都是一样的，都是github.com/foo/bar。

Go 语言官方从 Go1.11 起开始推进 Go modules（前身vgo），Go1.13 起不再推荐使用 GOPATH 的使用模式，Go modules 也渐趋稳定，因此新项目也没有必要继续使用GOPATH模式。

### go mod 命令

在 Go modules 中，我们能够使用如下命令进行操作：


| 命令            | 作用                             |
| --------------- | -------------------------------- |
| go mod init     | 生成 go.mod 文件                 |
| go mod download | 下载 go.mod 文件中指明的所有依赖 |
| go mod tidy     | 整理现有的依赖                   |
| go mod graph    | 查看现有的依赖结构               |
| go mod edit     | 编辑 go.mod 文件                 |
| go mod vendor   | 导出项目所有的依赖到vendor目录   |
| go mod verify   | 校验一个模块是否被篡改过         |
| go mod why      | 查看为什么需要依赖某模块         |

### 环境变量

可以通过 go env 命令来进行查看环境变量

GO111MODULE:

Go语言提供了 GO111MODULE 这个环境变量来作为 Go modules 的开关，其允许设置以下参数：

* auto：只要项目包含了 go.mod 文件的话启用 Go modules，目前在 Go1.11 至 Go1.14 中仍然是默认值。
* on：启用 Go modules，推荐设置，将会是未来版本中的默认值。
* off：禁用 Go modules，不推荐设置

GOPROXY:

这个环境变量主要是用于设置 Go 模块代理（Go module proxy），其作用是用于使 Go 在后续拉取模块版本时能够脱离传统的 VCS 方式，直接通过镜像站点来快速拉取。

GOPROXY 的默认值是：https://proxy.golang.org,direct，这有一个很严重的问题，就是 proxy.golang.org 在国内是无法访问的，因此这会直接卡住你的第一步，所以你必须在开启 Go modules 的时，同时设置国内的 Go 模块代理，执行如下命令：

```bash
go env -w GOPROXY=https://goproxy.cn,direct
```

GOPROXY 的值是一个以英文逗号 “,” 分割的 Go 模块代理列表，允许设置多个模块代理，假设你不想使用，也可以将其设置为 “off” ，这将会禁止 Go 在后续操作中使用任何 Go 模块代理。

而在刚刚设置的值中，我们可以发现值列表中有 “direct” 标识，它又有什么作用呢？

> 实际上 “direct” 是一个特殊指示符，用于指示 Go 回源到模块版本的源地址去抓取（比如 GitHub 等），场景如下：当值列表中上一个 Go 模块代理返回 404 或 410 错误时，Go 自动尝试列表中的下一个，遇见 “direct” 时回源，也就是回到源地址去抓取，而遇见 EOF 时终止并抛出类似 “invalid version: unknown revision…” 的错误。

GOSUMDB:

它的值是一个 Go checksum database，用于在拉取模块版本时（无论是从源站拉取还是通过 Go module proxy 拉取）保证拉取到的模块版本数据未经过篡改，若发现不一致，也就是可能存在篡改，将会立即中止。

GOSUMDB 的默认值为：sum.golang.org，在国内也是无法访问的，但是 GOSUMDB 可以被 Go 模块代理所代理（详见：Proxying a Checksum Database）。

因此我们可以通过设置 GOPROXY 来解决，而先前我们所设置的模块代理 goproxy.cn 就能支持代理 sum.golang.org，所以这一个问题在设置 GOPROXY 后，你可以不需要过度关心。
另外若对 GOSUMDB 的值有自定义需求，其支持如下格式：

* 格式 1：<SUMDB_NAME>+<PUBLIC_KEY>。
* 格式 2：<SUMDB_NAME>+<PUBLIC_KEY> <SUMDB_URL>。

也可以将其设置为“off”，也就是禁止 Go 在后续操作中校验模块版本。

GONOPROXY/GONOSUMDB/GOPRIVATE:

这三个环境变量都是用在当前项目依赖了私有模块，例如像是你公司的私有 git 仓库，又或是 github 中的私有库，都是属于私有模块，都是要进行设置的，否则会拉取失败。

更细致来讲，就是依赖了由 GOPROXY 指定的 Go 模块代理或由 GOSUMDB 指定 Go checksum database 都无法访问到的模块时的场景。

而一般建议直接设置 GOPRIVATE，它的值将作为 GONOPROXY 和 GONOSUMDB 的默认值，所以建议的最佳姿势是直接使用 GOPRIVATE。

并且它们的值都是一个以英文逗号 “,” 分割的模块路径前缀，也就是可以设置多个，例如：

```bash
go env -w GOPRIVATE="git.example.com,github.com/eddycjy/mquote"
```

设置后，前缀为 git.xxx.com 和 github.com/eddycjy/mquote 的模块都会被认为是私有模块。

如果不想每次都重新设置，我们也可以利用通配符，例如：

```bash
go env -w GOPRIVATE="*.example.com"
```

这样子设置的话，所有模块路径为 example.com 的子域名（例如：git.example.com）都将不经过 Go module proxy 和 Go checksum database，需要注意的是不包括 example.com 本身。

### 使用 Go Modules 初始化项目

在完成 Go modules 的开启后，我们需要创建一个示例项目来进行演示，执行如下命令：

```bash
mkdir -p $HOME/eddycjy/module-repo
cd $HOME/edddycjy/module-repo
```

然后进行 Go modules 的初始化，如下：

```bash
go mod init github.com/eddycjy/module-repo
```

在执行 go mod init命令时，我们指定了模块导入路径为 github.com/eddycjy/module-repo。接下来我们在该项目根目录下创建 main.go 文件，如下：

```golang
package main
import (
   "fmt"
   "github.com/eddycjy/mquote"
)

func main() {
   fmt.Println(mquote.GetHello())
}
```

然后在项目根目录执行 go get github.com/eddycjy/mquote 命令，如下：

```bash
go get github.com/eddycjy/mquote
```

### 查看 go.mod 文件

在初始化项目时，会生成一个 go.mod 文件，是启用了 Go modules 项目所必须的最重要的标识，同时也是 GO111MODULE 值为 auto 时的识别标识，它描述了当前项目（也就是当前模块）的元信息，每一行都以一个动词开头。

在我们刚刚进行了初始化和简单拉取后，我们再次查看 go.mod 文件，基本内容如下

```bash
module github.com/eddycjy/module-repo

go 1.13

require (
   example.com/apple v0.1.2
   example.com/banana v1.2.3
   example.com/banana/v2 v2.3.4
   example.com/pear // indirect
   example.com/strawberry // incompatible
)

exclude example.com/banana v1.2.4
replace example.com/apple v0.1.2 => example.com/fried v0.1.0
replace example.com/banana => example.com/fish
```

module：用于定义当前项目的模块路径。

go：用于标识当前模块的 Go 语言版本，值为初始化模块时的版本，目前来看还只是个标识作用。

* require：用于设置一个特定的模块版本。
* exclude：用于从使用中排除一个特定的模块版本。
* replace：用于将一个模块版本替换为另外一个模块版本。

另外你会发现 example.com/pear 的后面会有一个 indirect 标识，
indirect 标识表示该模块为间接依赖，也就是在当前应用程序中的 import 语句中，并没有发现这个模块的明确引用，有可能是你先手动 go get 拉取下来的，也有可能是你所依赖的模块所依赖的，情况有好几种。

### 查看 go.sum 文件

在第一次拉取模块依赖后，会发现多出了一个 go.sum 文件，其详细罗列了当前项目直接或间接依赖的所有模块版本，并写明了那些模块版本的 SHA-256 哈希值以备 Go 在今后的操作中保证项目所依赖的那些模块版本不会被篡改。

```bash
github.com/eddycjy/mquote v0.0.1 h1:4QHXKo7J8a6J/k8UA6CiHhswJQs0sm2foAQQUq8GFHM=
github.com/eddycjy/mquote v0.0.1/go.mod h1:ZtlkDs7Mriynl7wsDQ4cU23okEtVYqHwl7F1eDh4qPg=
github.com/eddycjy/mquote/module/tour v0.0.1 h1:cc+pgV0LnR8Fhou0zNHughT7IbSnLvfUZ+X3fvshrv8=
github.com/eddycjy/mquote/module/tour v0.0.1/go.mod h1:8uL1FOiQJZ4/1hzqQ5mv4Sm7nJcwYu41F3nZmkiWx5I=
...
```

我们可以看到一个模块路径可能有如下两种：

```bash
github.com/eddycjy/mquote v0.0.1 h1:4QHXKo7J8a6J/k8UA6CiHhswJQs0sm2foAQQUq8GFHM=
github.com/eddycjy/mquote v0.0.1/go.mod h1:ZtlkDs7Mriynl7wsDQ4cU23okEtVYqHwl7F1eDh4qPg=
```

h1 hash 是 Go modules 将目标模块版本的 zip 文件开包后，针对所有包内文件依次进行 hash，然后再把它们的 hash 结果按照固定格式和算法组成总的 hash 值。

而 h1 hash 和 go.mod hash 两者，要不就是同时存在，要不就是只存在 go.mod hash。那什么情况下会不存在 h1 hash 呢，就是当 Go 认为肯定用不到某个模块版本的时候就会省略它的 h1 hash，就会出现不存在 h1 hash，只存在 go.mod hash 的情况。

### 查看全局缓存

我们刚刚成功的将 github.com/eddycjy/mquote 模块拉取了下来，其拉取的结果缓存在 $GOPATH/pkg/mod和 $GOPATH/pkg/sumdb 目录下，而在mod目录下会以 github.com/foo/bar 的格式进行存放，如下：

```bash
mod
├── cache
├── github.com
├── golang.org
├── google.golang.org
├── gopkg.in
...
```

需要注意的是同一个模块版本的数据只缓存一份，所有其它模块共享使用。如果你希望清理所有已缓存的模块版本数据，可以执行 go clean -modcache命令。

## 参考资料

[8小时转职golang工程师](https://www.yuque.com/aceld/mo95lb/dsk886)

[Go语言并发](http://c.biancheng.net/golang/concurrent/)

[Go Modules详解](https://blog.csdn.net/u011069013/article/details/110114319)
