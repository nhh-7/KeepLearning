

### 包

程序从main包开始运行。

导入：用圆括号将导入的包分成一组。

导出名：在 Go 中，如果一个名字以大写字母开头，那么它就是已导出的。

在导入一个包时，你只能引用其中已导出的名字。 任何「未导出」的名字在该包外均无法访问。

### 函数

```go
func add(x int, y int) int {
	return x + y
}
```

当连续两个或多个函数的已命名形参类型相同时，除最后一个类型以外，其它都可以省略。

```go
func add(x, y int) int {
	return x + y
}
```

函数可以返回任意数量的返回值。

```go
func swap(x, y string) (string, string) {
	return y, x
}
```

Go 的返回值可被命名，它们会被视作定义在函数顶部的变量。

### 变量

`var` 语句用于声明一系列变量。和函数的参数列表一样，类型在最后。

变量声明可以包含初始值，每个变量对应一个。如果提供了初始值，则类型可以省略；变量会从初始值中推断出类型。

在函数中，短赋值语句 `:=` 可以无需使用var而直接声明变量（需要提供初始值），只能在函数体中使用。

Go的基本类型有：

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名

float32 float64

complex64 complex128
```

没有明确初始化的变量声明会被赋予对应类型的 **零值**。

- 数值类型为 `0`，
- 布尔类型为 `false`，
- 字符串为 `""`（空字符串）。

表达式 `T(v)` 将值 `v` 转换为类型 `T`。Go 在不同类型的项之间赋值时需要显式转换。

常量的声明与变量类似，只不过使用 `const` 关键字。常量不能用 `:=` 语法声明。

### for循环

Go 只有一种循环结构：`for` 循环。

```go
sum := 0
for i := 0; i < 10; i++ {
	sum += i
}
```

初始化语句和后置语句是可选的。

去掉分号，只保留条件，就成为while循环，省略条件，就成为无限循环

```go
sum := 1
for sum < 1000 {
	sum += sum
}
```

使用range遍历切片或映射，每次迭代都会返回两个值。 第一个值为当前元素的下标，第二个值为该下标所对应元素的一份副本。

```go
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

for i, v := range pow {
    fmt.Printf("2**%d = %d\n", i, v)
}
// 可以将下标或值赋予 _ 来忽略它。
for i, _ := range pow
for _, value := range pow
```



### if语句

表达式外无需小括号 `( )`，而大括号 `{ }` 则是必须的

`if` 语句也可以在条件表达式前执行一个简短语句，定义一个只在该 if-else 作用域内使用的变量。

```go
if 条件1 {
    // 条件1为 true 时执行
} else if 条件2 {
    // 条件1为 false，条件2为 true 时执行
} else if 条件3 {
    // 条件1、2为 false，条件3为 true 时执行
} else {
    // 所有条件都为 false 时执行（可选）
}
```



### switch分支

Go 只会运行选定的 `case`，而非之后所有的 `case`。 在效果上，Go 的做法相当于这些语言中为每个 `case` 后面自动添加了所需的 `break` 语句。在 Go 中，除非以 `fallthrough` 语句结束，否则分支会自动终止。 Go 的另一点重要的不同在于 `switch` 的 `case` 无需为常量，且取值不限于整数。

无条件的switch

```go
t := time.Now()
switch {
    case t.Hour() < 12:
    	fmt.Println("早上好！")
    case t.Hour() < 17:
    	fmt.Println("下午好！")
    default:
    	fmt.Println("晚上好！")
}
```

### defer推迟

defer 语句会将函数推迟到外层函数返回之后执行。推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。

推迟调用的函数调用会被压入一个栈中。 当外层函数返回时，被推迟的调用会按照后进先出的顺序调用。

### 指针

go中不允许进行指针运算

### 结构体

```go
type Person struct {
    Name string
    Age  int
    City string
}
```

如果我们有一个指向结构体的指针 `p` 那么可以通过 `(*p).X` 来访问其字段 `X`。Go语言也允许我们使用隐式解引用，直接写 `p.X` 就可以

### 数组

数组不能改变大小，声明数组必须指定长度或者用`...`来自动推断

```go
var a [10]int 
arr := [...]int{1, 2, 3, 4, 5}  // 编译器自动计算长度为5
arr := [...]int{1: 10, 3: 30}  // 长度为4，结果：[0 10 0 30]
```

### 切片

类型 `[]T` 表示一个元素类型为 `T` 的切片。切片可以动态扩容，使用append

切片通过两个下标来界定，一个下界和一个上界，选出一个**左闭右开**的区间，二者以冒号分隔：

```go
primes := [6]int{2, 3, 5, 7, 11, 13}
var s []int = primes[1:4]
```

切片就像数组的引用， 切片并不存储任何数据，它只是描述了底层数组中的一段。更改切片的元素会修改其底层数组中对应的元素。

```go
q := []int{2, 3, 5, 7, 11, 13} // 这样实际会创建一个数组，然后再构建一个引用了它的切片
```

切片下界的默认值为 0，上界则是该切片的长度。

切片的长度就是它所包含的元素个数。切片的容量是从它的第一个元素开始数，到其底层数组元素末尾的个数。

切片可以用内置函数 `make` 来创建，这也是创建动态数组的方式。`make` 函数会分配一个元素为零值的数组并返回一个引用了它的切片：

```go
b := make([]int, 3, 5) // 第二参数表示len，第三参数表示cap
```

创建二维切片

```go
rows := 3
cols := 4
matrix := make([][]int, rows)  // 创建外层切片，长度为3

for i := range matrix {
    matrix[i] = make([]int, cols)  // 为每行创建内层切片，长度为4
}
```

### map映射

```go
var m map[string]int = make(map[string]int) // key为string，value为int
m := make(map[string]int)
```

#### 修改映射

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
elem, ok := m[key]
```

若 `key` 在 `m` 中，`ok` 为 `true` ；否则，`ok` 为 `false`。

若 `key` 不在映射中，则 `elem` 是该映射元素类型的零值。

### 函数闭包

Go 函数可以是一个闭包。它允许函数捕获并引用其外部作用域中的变量，即使该函数在外部作用域执行完毕后仍然可以访问这些变量。闭包本质上是一个**函数 + 该函数所需捕获的环境变量**的组合体。

```go
func outer() func() int {
    x := 10           // x 是外部变量
    return func() int {
        return x * 2  // 闭包捕获并使用 x
    }
}

// 使用闭包
f := outer()  // 即使 outer() 执行完毕，闭包仍保留对 x 的引用。
fmt.Println(f())  // 输出: 20
```

特点

- 闭包捕获的是变量的**引用**，而非创建闭包时变量的值
- 如果多次调用同一个闭包，它们共享同一组捕获的变量

### 方法

Go 没有类。不过你可以为类型定义方法。方法就是一类带特殊的 **接收者** 参数的函数。方法接收者在它自己的参数列表内，位于 `func` 关键字和方法名之间。

```go
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

v := Vertex{3, 4}
fmt.Println(v.Abs())
```

也可以为非结构体类型声明方法。但只能为在同一个包中定义的接收者类型声明方法，而不能为其它别的包中定义的类型 （包括 `int` 之类的内置类型）声明方法。就是接收者的类型定义和方法声明必须在同一包内。

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}
```

#### 指针类型的接收者

可以为指针类型的接收者声明方法。指针接收者的方法可以**修改接收者指向的值**，若使用值接收者，那么只会对原始值的副本进行操作。

#### 方法与指针重定向

当接收者参数为指针的的方法被调用时，接收者既能是值又能是指针。当接收者参数为普通值时，接受者也可以是值或者指针。
而若函数的参数为指针，那么只能传入指针，参数为普通值，也只能传入值。

#### 选择使用指针接收者

首先，方法能够修改其接收者指向的值。

其次，这样可以避免在每次调用方法时复制该值。若值的类型为大型结构体时，这样会更加高效。

### 接口

**接口类型** 的定义为一组方法签名。
接口类型的变量可以持有任何实现了全部这些方法的值。

```go
type Abser interface {
	Abs() float64
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
} // MyFloat实现了Abser接口中的方法。

f := MyFloat(-math.Sqrt2)
var a Abser = f  // a MyFloat 实现了 Abser
```

#### 接口与隐式实现

类型通过实现一个接口的**所有方法**来实现该接口。既然无需专门显式声明，也就没有“implements”关键字。
隐式接口从接口的实现中解耦了定义，这样接口的实现可以出现在任何包中，无需提前准备。

#### 接口值

接口也是值。它们可以像其它值一样传递。
接口值可以用作函数的参数或返回值。

在内部，接口值可以看做包含值和具体类型的元组：(value, type)

接口值保存了一个具体底层类型的具体值。
接口值调用方法时会执行其底层类型的同名方法。

#### 底层值为 nil 的接口值

即便接口内的具体值为 nil，方法仍然会被 nil 接收者调用。

```go
type I interface {
	M()
}
type T struct {
	S string
}
func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}
var i I
var t *T
i = t
i.M() // 仍然可以调用
```

#### nil 接口值

nil 接口值既不保存值也不保存具体类型。为 nil 接口调用方法会产生运行时错误，因为接口的元组内并未包含能够指明该调用哪个 **具体** 方法的类型。

```go
var i I
i.M() // 会出现错误
```

#### 空接口

指定了零个方法的接口值被称为 空接口；

```
type I interface {}
```

空接口可保存任何类型的值。（因为每个类型都至少实现了零个方法。）

#### 类型断言

**类型断言** 提供了访问接口值底层具体值的方式。

```go
t := i.(T)
```

该语句断言接口值 `i` 保存了具体类型 `T`，并将其底层类型为 `T` 的值赋予变量 `t`。
若 `i` 并未保存 `T` 类型的值，该语句就会触发一个 panic。

为了 **判断** 一个接口值是否保存了一个特定的类型，类型断言可返回两个值：其底层值以及一个报告断言是否成功的布尔值。

```go
t, ok := i.(T)
```

若 `i` 保存了一个 `T`类型，那么 `t` 将会是其底层值，而 `ok` 为 `true`。
否则，`ok` 将为 `false` 而 `t` 将为 `T` 类型的零值，程序并**不会产生 panic**。

#### 类型选择

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

#### Stringer

[`fmt`](https://go-zh.org/pkg/fmt/) 包中定义的 [`Stringer`](https://go-zh.org/pkg/fmt/#Stringer) 是最普遍的接口之一。`fmt` 包（还有很多包）都通过此接口来打印值。也就是说为类型实现此接口后，可以按照自己的规则来进行打印。

```go
type Stringer interface {
    String() string
}
```

#### 错误

Go 程序使用 `error` 值来表示错误状态。
与 `fmt.Stringer` 类似，`error` 类型是一个内建接口（`fmt` 包也会根据对 `error` 的实现来打印值）：

```go
type error interface {
    Error() string
}
```

用法：定义自己的错误类型，并为该类型实现这个Error函数，接着在需要返回错误的地方返回自定义的错误类型，使用 `fmt` 打印错误时，会根据实现函数进行打印。

#### Readers

`io` 包指定了 `io.Reader` 接口，它表示数据流的读取端。

`io.Reader` 接口有一个 `Read` 方法：

```go
func (T) Read(b []byte) (n int, err error)
```

`Read` 用数据填充给定的字节切片并返回填充的字节数和错误值。在遇到数据流的结尾时，它会返回一个 `io.EOF` 错误。

#### 图像

[`image`](https://go-zh.org/pkg/image/#Image) 包定义了 `Image` 接口：

```go
package image

type Image interface {
    ColorModel() color.Model
    Bounds() Rectangle
    At(x, y int) color.Color
}
```

`Bounds` 方法的返回值 `Rectangle` 实际上是一个 [`image.Rectangle`](https://go-zh.org/pkg/image/#Rectangle)，它在 `image` 包中声明。

### 泛型

#### 类型参数

可以使用类型参数编写 Go 函数来**处理多种类型**。 函数的类型参数出现在函数参数之前的方括号之间。

```go
func Index[T comparable](s []T, x T) int
```

此声明意味着 `s` 是满足内置约束 `comparable` 的任何类型 `T` 的切片。 `x` 也是相同类型的值。

在 Go 语言中，`comparable` 是一个**内置的约束类型**，用于泛型函数和类型中限制类型参数必须支持相等性比较（即支持使用 `==` 和 `!=` 操作符）

除了`comparable`之外，Go的`constraints` 包中还内置了其他约束：

- **`constraints.Integer`** ：约定必须为整数类型。
- **`constraints.Float`**：表示所有浮点数类型
- **`constraints.Complex`**：表示所有复数类型
- **`constraints.Ordered`**：表示所有可排序的类型，包括整数，浮点数，字符串。
- **`constraints.Signed`**
- **`constraints.Unsigned`**

**任意类型约束：`interface{}` 或 `any`**

**自定义约束组合**

**底层类型约束（使用 `~` 符号）**

```go
type Stringer interface {
    ~string  // 表示所有底层类型是 string 的类型
}
```

- 

- 

- 

#### 泛型类型

除了泛型函数之外，Go 还支持泛型类型。 类型可以使用类型参数进行参数化，这对于实现通用数据结构非常有用。

```go
// List 表示一个可以保存任何类型的值的单链表。
type List[T any] struct {
	next *List[T]
	val  T
}
```

### 并发

#### Go 协程

Go 程（goroutine）是由 Go 运行时管理的轻量级线程。

```go
go f(x, y, z)
```

会启动一个新的 Go 协程并执行。`f`, `x`, `y` 和 `z` 的求值发生在当前的 Go 协程中，而 `f` 的执行发生在新的 Go 协程中。

#### 信道

信道是带有类型的管道，你可以通过它用信道操作符 `<-` 来发送或者接收值。

```go
ch <- v    // 将 v 发送至信道 ch。
v := <-ch  // 从 ch 接收值并赋予 v。
```

和映射与切片一样，信道在使用前必须创建：

```go
ch := make(chan int)
```

默认情况下，发送和接收操作在另一端准备好之前都会阻塞。这使得 Go 程可以在没有显式的锁或竞态变量的情况下进行同步。

#### 带缓冲的信道

信道可以是 **带缓冲的**。将缓冲长度作为第二个参数提供给 `make` 来初始化一个带缓冲的信道：

```go
ch := make(chan int, 100)
```

仅当信道的缓冲区填满后，向其发送数据时才会阻塞。当缓冲区为空时，接受方会阻塞。

#### range 和 close

发送者可通过 `close` 关闭一个信道来表示没有需要发送的值了。接收者可以通过为接收表达式分配第二个参数来测试信道是否被关闭：若没有值可以接收且信道已被关闭，那么在执行完

```go
v, ok := <-ch
```

此时 `ok` 会被设置为 `false`。

循环 `for i := range c` 会不断从信道接收值，直到它被关闭。

只应由发送者关闭信道，而不应由接收者关闭。向一个已经关闭的信道发送数据会引发程序 panic。

信道与文件不同，通常情况下无需关闭它们。只有在必须告诉接收者不再有需要发送的值时才有必要关闭，例如终止一个 `range` 循环。

#### select 语句

`select` 语句使一个 Go 程可以等待多个通信操作。

`select` 会阻塞到某个分支可以继续执行为止，这时就会执行该分支。当多个分支都准备好时会随机选择一个执行。

当 `select` 中的其它分支都没有准备好时，`default` 分支就会执行。

```go
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 中接收会阻塞时执行
}
```

#### `sync.Mutex`

 标准库中提供了 [`sync.Mutex`](https://go-zh.org/pkg/sync/#Mutex) 互斥锁类型及其两个方法：

- `Lock`
- `Unlock`

我们可以通过在代码前调用 `Lock` 方法，在代码后调用 `Unlock` 方法来保证一段代码的互斥执行。我们也可以用 `defer` 语句来保证互斥锁一定会被解锁。

### 算法

#### heap

```go
// 1. 定义一个类型（通常是切片）
type IntHeap []int

// 2. 实现 sort.Interface (Len, Less, Swap)
func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] } // 最小堆；若是最大堆则用 >
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

// 3. 实现 heap.Interface 要求的 Push
func (h *IntHeap) Push(x interface{}) {
	// Push 使用指针接收者，因为要修改切片的长度
	*h = append(*h, x.(int))
}

// 4. 实现 heap.Interface 要求的 Pop
func (h *IntHeap) Pop() interface{} {
	old := *h
	n := len(old)
	x := old[n-1]     // 取出最后一个元素
	*h = old[: n-1] // 缩减切片
	return x
}
```

```go
func main() {
	h := &IntHeap{2, 1, 5}
    
	// 1. 初始化堆，将切片中已有的数据转为一个合法堆的数据，如果切片没有数据，不进行init也可以！
	heap.Init(h)
    
	// 2. 使用heap包内的Push，传入指针
	heap.Push(h, 3)
    
	// 3. 获取并弹出堆顶元素（最小值）
	for h.Len() > 0 {
		fmt.Printf("%d ", heap.Pop(h)) // 输出: 1 2 3 5
	}
}
```

#### 快排模板

```go
func QuickSort(nums []int, left, right int) {
    if left >= right {
        return
    }
    
    // 使用这个双指针 partition
    p := Partition(nums, left, right)
    
    // 递归两边
    QuickSort(nums, left, p-1)
    QuickSort(nums, p+1, right)
}

func Partition(nums []int, left, right int) int {
    // 随机化基准值
    r := left + rand.Intn(right-left+1)
    nums[left], nums[r] = nums[r], nums[left]
    
    pivot := nums[left]
    i, j := left+1, right
    
    for {
        // 从左向右找第一个大于 pivot 的
        for i <= j && nums[i] < pivot {
            i++
        }
        // 从右向左找第一个小于 pivot 的
        for i <= j && nums[j] > pivot {
            j--
        }
        if i >= j {
            break
        }
        nums[i], nums[j] = nums[j], nums[i]
        i++
        j--
    }
    // 将 pivot 换到它最终的位置 j
    nums[left], nums[j] = nums[j], nums[left]
    return j
}
```

