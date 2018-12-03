---
layout: post
title: Java和Golang的区别
subtitle: 从几方面比较java和golang的差别
date: 2017-05-01 12:00:00
author: Silence
header-img: ""
catalog: true
header-bg-css: "linear-gradient(to right, #404040, #404040);"
catalog: true
tags:
  - Java
  - golang
---


## 1. 结构体 -> 类

{% highlight golang %}
// 包名即为包含该文件的目录名字
package collection

// 声明一个结构体，类似Java中的类
type Stack struct {
    data []string
}

// 声明一个Push函数，并通过一个Stack的指针对象实现该方法
// 类似声明了Stack的成员方法
func (s *Stack) Push(x string) {
    s.data = append(s.data, x)
}

func (s *Stack) Pop() string {
    n := len(s.data) - 1
    res := s.data[n]
    s.data[n] = "" // to avoid memory leak
    s.data = s.data[:n]
    return res
}

func (s *Stack) Size() int {
    return len(s.data)
}

{% endhighlight %}

* 结构体对应Java里的类, 但结构体里只能有变量，不能有方法

* 方法名称前面的 `(stack *Stack)` 声明了该方法的接收者，对应Java中的 `this`

* `:=` 操作符声明并初始化了一个变量，变量的类型取决于操作符右边的表达式

{% highlight golang %}
package collection_test

import (
    "fmt"
    "lambert.com/collection" // 引入包
)

func ExampleStack() {
    var s collection.Stack  // 可以通过包名+结构体的名称使用
    s.Push("world!")
    s.Push("Hello, ")

    for s.Size() > 0 {
        fmt.Print(s.Pop())
    }

    fmt.Println()
}
{% endhighlight %}

### 主要区别

* 面向对象

    * Go中没有带有构造函数的类，没有实例方法，也没有类继承；只有动态的方法查找，Go只提供了结构体和接口

    * Go允许将方法置于任何类型上，不需要封装；方法的接收者对应Java中的this, 可以是值或指针

    * Go提供了两种访问级别，对应Java中的public和private。以大写字母开头的声明是public的，其他都是包私有的

* 函数式编程

    * 函数是Go中的一等公民，函数可以在方法的参数上传递

* 指针和引用

    * Go提供了任意类型的指针，不只是对象和数组

    * Go用nil指向无效指针

    * 数组在Go中是值，当数组作为函数的参数时，函数接受到的是一个数组的拷贝，并不是一个指针。一般，使用slices作为函数参数，slices是引用

    * maps, slices, channels 传递的是引用

* 内置类型

    * string -> string

    * maps -> HashTable

* 错误处理

    * Go不使用异常，而使用erros来表示异常情况

* 缺少的功能

    * 不支持隐式类型转换

    * 不支持函数重载，同一作用域中的函数和方法必须具有唯一的名称

    * 没有泛型

* 变量声明

    |  Go                  |  Java                        |
    | :-:                  | :-:                          |
    |  var v int           |   int v = 1;                 |
    |  var v *int          |  Integer v = null;           |
    |   var v string       |   String v = "";             |
    |   var v4 [10]int     |   int[] v4 = new int[10];    |
    |   var v5 []int       |   int[] v5 = null;           |
    |   var v6 *struct{ a int }                   |   class C { int a; }  C v6 = null;    |
    |   var v7 map[string]int      |   HashMap<String, Integer> v7 = null;    |
    |   var v8 func(a int) int                 |  interface F { int f(int a); } F v8 = null;

* 多个变量同时声明

{% highlight golang %}
var (
    n int
    x float64
)
{% endhighlight %}

* 函数类型

    在Go中函数是一等公民，可以作为变量

* for 循环的区别

    * 普通循环

    {% highlight golang %}
        for i := 0; i < len(a); i++ {

        }
    {% endhighlight %}

    * 循环，对于数组，slice和字符串来说，i为index，v为值；循环map时，i和v为键值对；循环channel是只有值

    {% highlight golang %}
    for i, v := range a {
        ...
    }
    {% endhighlight %}

* switch 的区别

    ```
    switch n {
        case 0: // 当n为0时不执行任何操作
        case 1:
            f() // 仅当n为1时执行
    }

    switch n {
        case 0, 1:
            f() // f is called if n == 0 || n == 1.


    switch { // case后面可以是表达式
        case n < 0:
            f1()
        case n == 0:
            f2()
        default:
            f3()
    }}
    ```

* 自增和自减

    Go不允许  n = i++ 等类似的操作

* Defer (延迟)
    * 一个deferred的函数会在它所包围的函数执行完之前调用

    ```
    func main() {
        defer fmt.Println("World")
        fmt.Println("Hello")
    }

    Hello
    World
    ```

    * deferred调用会在函数panics的时候提前执行

    ```
    func main() {
        defer fmt.Println("World")
        panic("Stop")
        fmt.Println("Hello")
    }

    World
    Stop

    ```

    * 多个deffered函数调用时遵循后进先出的规则

    ```
    func main() {
        fmt.Println("Hello")
        for i := 1; i <= 3; i++ {
            defer fmt.Println(i)
        }
        fmt.Println("World")
    }

    Hello
    World
    3
    2
    1
    ```

    * deffered函数可以修改包裹它的函数的返回值

    ```
    func foo() (result string) {
        defer func() {
            result = "Change World" // change value at the very last moment
        }()
        return "Hello World"
    }
    ```

    * deffered的一般使用场景是来关闭开启的资源

    ```
    func CopyFile(dstName, srcName string) (written int64, err error) {
        src, err := os.Open(srcName)
        if err != nil {
            return
        }
        defer src.Close()   // 再执行它

        dst, err := os.Create(dstName)
        if err != nil {
            return
        }
        defer dst.Close()   // 函数执行完时先执行它

        return io.Copy(dst, src)
}

    ```

* Struct

    * 一个结构体指针类似Java中的对象

    ```
    type MyStruct struct {
        s string
        n int64
    }

    var x MyStruct     // x is initialized to MyStruct{"", 0}.
    var px *MyStruct   // px is initialized to nil.
    px = new(MyStruct) // px points to the new struct MyStruct{"", 0}.

    x.s = "Foo"
    px.s = "Bar"
    ```