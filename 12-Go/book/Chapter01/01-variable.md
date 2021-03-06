
变量
=========

Go 旨在静态类型语言的高性能和动态语言的高效率之间寻求一个平衡点，但其还是静态类型语言，在编译时确定每一个变量的类型，不可在运行期改变变量类型。

- 使用关键字 var 定义变量，全局变量及局部变量均有默认初始值，如 int 为 0，string 为 ""。
- C 中局部变量未初始化值不确定。C++，Java 中局部变量不做初始化操作会报错。
- 若提供初始化值，可省略变量类型，由编译器自动推断。
```go
var x int
var f float32 = 1.6
var s = "abc"
var ptr *int // C 中是 int *ptr;
```

- 在`函数内部`，可用更简洁的 ":=" 方式定义变量。

```go
func main() {
    x := 123 // 注意是定义新的局部变量还是修改全局变量，防止出错
}
```

- 可一次定义多个变量。
```go
var x, y, z int
var s, n = "abc", 123
var (
    a int
    b float32 )

func main() {
    n, s := 0x1234, "Hello, World!"
    println(x, s, n)
}
```

- 多变量赋值时，先计算所有相关值，然后再从左到右依次赋值。

```go
￼data, i := [3]int{0, 1, 2}, 0
i, data[i] = 2, 100
```

- 占位符 "_" 有两个作用：

```go
// 1. 用于忽略值占位，即显式忽略函数或其他多值赋值表达式中的某些的返回值。
func test() (int, string) {
    return 1, "abc"
}
func main() {
    _, s := test()
    println(s) // 不要 fmt 包也可，内建函数
}

// 2. "_" 也常用于导入包不使用包，仅会调用包的初始化函数。
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)
```


- 编译器会将未使用的局部变量当做错误。

```go
var s string   // 全局变量没问题
func main() {
    i := 0     // Error: i declared and not used。(可使⽤用 "_ = i" 规避)
}
```

- 注意重新赋值与定义新同名变量的区别。

```go
s := "abc"
println(&s)
s, y := "hello", 20
println(&s, y) {
    s, z := 1000, 30
    println(&s, z)
}

// 输出如下
0x2210230f30
0x2210230f30 20
0x2210230f18 30

// 重新赋值: 与前 s 变量在同一层次的代码块中，且有新的变量被定义。通常函数返回值 err 会被重复使用。
// 定义新同名变量: 不在同一层次代码块，即只要带 {}，包括如 for 循环，块级作用域 {}。
```

- 布尔类型不支持自动或强制类型转换。

```go
var b
bool b = 1   // 编译错误
b = bool(1)  // 编译错误
```

- go 中的 float32 和 float64 分别等价于 C 中的 float 和 double 类型。
