
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
```
sudo tar -zxvf gox.xx.x.linux-amd64.tar.gz -C /usr/local/
```

配置环境变量
```
vim ~/.bashrc
```
```
#设置go语言路径
export GOTOOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
#启动Go Modules
export GO11MODULE=on
```
```
source ~/.bashrc
```

检查开发环境
```
go version
go --help
```

## 语法

### 第一个go程序
```
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
```
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
```
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
```
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
```
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
```
package main

import "lib"

func main() {
   lib.LibTest()
}
```

### import导包

在导入其他包时，如果不使用该包的api则会报错，使用匿名导入则不会报错
```
package main

import {
   _ "lib"  //导入时加下划线就是匿名导入，只运行该包的init方法
   f "fmt"  //导入时可以加上别名
   . "lib2" //导入包的所有方法，相当于py的"from lib2 import *"
}

func main() {
   f.Println("lib Test")
   Lib2Test() //用点符号导入lib2中的方法可以直接使用
}
```

### 指针

指针可以传递内存地址
```
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
```
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

### defer语句

defer在函数最后执行，相当于析构函数
```
package main

import "fmt"

func main() {
   //写入defer关键字
   defer fmt.Println("main end") //defer的执行顺序是先入后出，所以这句代码最后执行
   defer fmt.Println("main end2")
   fmt.Println("hello go") //最先执行没有被defer的语句
}
```

rerun不是原子操作，它分三步，第一步赋值，第二步执行defer，第三步才是真正的return
```
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

### 数组和切片slice

切片比动态数组传参更方便
```
package main

import "fmt"

func printArray(myArray [10]int){ //固定长度数组传参方式，形参必须和实参长度相等
   for index, value := range myArray{
      fmt.Println("index = ", index, ", value = ", value)
   }
   myArray[0] = 100 //固定数组传递相当于值拷贝，这里修改无效
}

func printArray2(myArray []int){ //动态长度数组传参方式
   for _, value := range myArray{
      fmt.Println("index = ", index, ", value = ", value)
   }
   myArray[0] = 100 //动态数组传递虽然也是值传递，但是内部参数是指针，所以这里修改有效
}

func main() {
   //固定长度数组，参数默认都是0
   var myArray1 [10]int
   //有默认值的数组
   myArray2 := [10]int{1,2,3,4}
   //动态数组，切片类型
   myArray3 := []int{1,2,3,4}
}
```