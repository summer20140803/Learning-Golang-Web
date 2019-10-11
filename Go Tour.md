# 一些零碎笔记



* 没有参数的 `return` 语句返回已命名的返回值。也就是 `直接` 返回。

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```



* 在函数中，简洁赋值语句 `:=` 可在类型明确的地方代替 `var` 声明。函数外的每个语句都必须以关键字开始（`var`, `func` 等等）。

```go
func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"
	k = 5

	fmt.Println(i, j, k, c, python, java)
}
```



* 一些数据变量的别名

byte = int8

rune = int32，表示一个 Unicode 码点



* 零值：没有明确初始值的变量声明会被赋予它们的 **零值**。

零值是：

​    a. 数值类型为 `0`，

​    b. 布尔类型为 `false`，

​    c. 字符串为 `""`（空字符串）。



* for 包含了 C 中的 for 和 while：

此时你可以去掉分号，因为 C 的 `while` 在 Go 中叫做 `for`。

```go
for sum < 1000 {
  sum += sum
}
```



* 同 `for` 一样， `if` 语句可以在条件表达式前执行一个简单的语句。该语句声明的变量作用域仅在 `if` 之内。

```go
if v := math.Pow(x, n); v < lim {
  return v
}
```



* Go 拥有指针。指针保存了值的内存地址。

  类型 `*T` 是指向 `T` 类型值的指针。其零值为 `nil`。

  但与 C 不同，Go 没有指针运算。



* Go 的数组声明：

类型 `[n]T` 表示拥有 `n` 个 `T` 类型的值的数组。

表达式

```go
var a [10]int
```

会将变量 `a` 声明为拥有 10 个整数的数组。



* 切片(有点类似于数组)

```go
a[low : high]
```

它会选择一个半开区间，包括第一个元素，但排除最后一个元素。

以下表达式创建了一个切片，它包含 `a` 中下标从 1 到 3 的元素：

```go
a[1:4]
```

切片并不存储任何数据，它只是描述了底层数组中的一段。

更改切片的元素会修改其底层数组中对应的元素。

与它共享底层数组的切片都会观测到这些修改。



* 数组切片的长度和容量

切片拥有 **长度** 和 **容量**。

切片的长度就是它所包含的元素个数。

切片的容量是从它的第一个元素开始数，到其底层数组元素末尾的个数。

切片 `s` 的长度和容量可通过表达式 `len(s)` 和 `cap(s)` 来获取。



* `for` 循环的 `range` 形式可遍历切片或映射。

  当使用 `for` 循环遍历切片时，每次迭代都会返回两个值。第一个值为当前元素的下标，第二个值为该下标所对应元素的一份副本。

```go
for i, v := range pow {
  fmt.Printf("2**%d = %d\n", i, v)
}
```



* 映射(有点类似于字典)

映射将键映射到值。

映射的零值为 `nil` 。`nil` 映射既没有键，也不能添加键。

`make` 函数会返回给定类型的映射，并将其初始化备用。

```go
type Vertex struct {
	Lat, Long float64
}

var m = make(map[string]Vertex)
m["Bell Labs"] = Vertex{
  40.68433, -74.39967,
}
```

映射的文法：

```go
var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}
```

若顶级类型只是一个类型名，你可以在文法的元素中省略它。

```go
var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}
```



* 映射的修改

在映射 `m` 中插入或修改元素：

```go
m[key] = elem
```

获取元素：

```go
elem = m[key]
```

删除元素：

```go
delete(m, key)
```

通过双赋值检测某个键是否存在：

```go
elem, ok = m[key]
```

若 `key` 在 `m` 中，`ok` 为 `true` ；否则，`ok` 为 `false`。

若 `key` 不在映射中，那么 `elem` 是该映射元素类型的零值。

同样的，当从映射中读取某个不存在的键时，结果是映射的元素类型的零值。

**注** ：若 `elem` 或 `ok` 还未声明，你可以使用短变量声明：

```go
elem, ok := m[key]
```



* 类型断言

**类型断言** 提供了访问接口值底层具体值的方式。

```go
t := i.(T)
```

该语句断言接口值 `i` 保存了具体类型 `T`，并将其底层类型为 `T` 的值赋予变量 `t`。

若 `i` 并未保存 `T` 类型的值，该语句就会触发一个恐慌。

为了 **判断** 一个接口值是否保存了一个特定的类型，类型断言可返回两个值：其底层值以及一个报告断言是否成功的布尔值。

```go
t, ok := i.(T)
```



* 类型选择

**类型选择** 是一种按顺序从几个类型断言中选择分支的结构。

类型选择与一般的 switch 语句相似，不过类型选择中的 case 为类型（而非值）， 它们针对给定接口值所存储的值的类型进行比较。

```go
switch v := i.(type) {
case T:
    // v 的类型为 T
case S:
    // v 的类型为 S
default:
    // 没有匹配，v 与 i 的类型相同
}
```

类型选择中的声明与类型断言 `i.(T)` 的语法相同，只是具体类型 `T` 被替换成了关键字 `type`。



* Stringer (有点类似于 Swift 类的 description 作用)

[`fmt`](https://go-zh.org/pkg/fmt/) 包中定义的 [`Stringer`](https://go-zh.org/pkg/fmt/#Stringer) 是最普遍的接口之一。

```go
type Stringer interface {
    String() string
}
```

`Stringer` 是一个可以用字符串描述自己的类型。`fmt` 包（还有很多包）都通过此接口来打印值。



* 错误

Go 程序使用 `error` 值来表示错误状态。

与 `fmt.Stringer` 类似，`error` 类型是一个内建接口：

```go
type error interface {
    Error() string
}
```

（与 `fmt.Stringer` 类似，`fmt` 包在打印值时也会满足 `error`。）

通常函数会返回一个 `error` 值，调用的它的代码应当判断这个错误是否等于 `nil` 来进行错误处理。

```go
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```



* Goroutine

Go 程（goroutine）是由 Go 运行时管理的轻量级线程。

```go
go f(x, y, z)
```

会启动一个新的 Go 程并执行

```go
f(x, y, z)
```

`x`, `y` 和 `z` 的求值发生在当前的 Go 程中，而 `f` 的执行发生在新的 Go 程中。



* 信道

信道是带有类型的管道，你可以通过它用信道操作符 `<-` 来发送或者接收值。

```go
ch <- v    // 将 v 发送至信道 ch。
v := <-ch  // 从 ch 接收值并赋予 v。
```

（“箭头”就是数据流的方向。）

和映射与切片一样，信道在使用前必须创建：

```go
ch := make(chan int)
```

默认情况下，发送和接收操作在另一端准备好之前都会阻塞。这使得 Go 程可以在没有显式的锁或竞态变量的情况下进行同步。



* 带缓冲的信道

信道可以是 *带缓冲的*。将缓冲长度作为第二个参数提供给 `make` 来初始化一个带缓冲的信道：

```go
ch := make(chan int, 100)
```

仅当信道的缓冲区填满后，向其发送数据时才会阻塞。当缓冲区为空时，接受方会阻塞。



* range 和 close

发送者可通过 `close` 关闭一个信道来表示没有需要发送的值了。接收者可以通过为接收表达式分配第二个参数来测试信道是否被关闭：若没有值可以接收且信道已被关闭，那么在执行完

```
v, ok := <-ch
```

之后 `ok` 会被设置为 `false`。

循环 `for i := range c` 会不断从信道接收值，直到它被关闭。

*注意：* 只有发送者才能关闭信道，而接收者不能。向一个已经关闭的信道发送数据会引发程序恐慌（panic）。

*还要注意：* 信道与文件不同，通常情况下无需关闭它们。只有在必须告诉接收者不再有需要发送的值时才有必要关闭，例如终止一个 `range` 循环。



* select 语句

`select` 语句使一个 Go 程可以等待多个通信操作。

`select` 会阻塞到某个分支(case)可以继续执行为止，这时就会执行该分支。当多个分支都准备好时会随机选择一个执行。

```go
select {
case c <- x:
  fmt.Println("a")
  x, y = y, x+y
case <-quit:
  fmt.Println("quit")
  return
}
```

当 `select` 中的其它分支都没有准备好时，`default` 分支就会执行。

为了在尝试发送或者接收时不发生阻塞，可使用 `default` 分支：

```go
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 中接收会阻塞时执行
}
```



* sync.Mutex

如果想要保证同时只有一个 Goroutine 访问一个共享变量时，不会发生冲突，一种方案就是使用 **互斥锁（Mutex）**这一数据结构来提供这种机制。

Go 标准库中提供了 [`sync.Mutex`](https://go-zh.org/pkg/sync/#Mutex) 互斥锁类型及其两个方法：

- `Lock`
- `Unlock`

我们可以通过在代码前调用 `Lock` 方法，在代码后调用 `Unlock` 方法来保证一段代码的互斥执行。

我们也可以用 `defer` 语句来保证互斥锁一定会被解锁。

