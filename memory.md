# 禁止解引用空指针

go中，对值类型，声明即默认初始化。对引用类型，申明后其值是空值（nil）。使用引用类型前，必须对其有效的构建并初始化好。

**反例**

```go
// 反例
func main() {
    var s *student
    if command {
        s = &student{}
    }
    s.Foo() // 不符合：s可能为nil
}
```

**正例**

```go
func main() {
    var s *student
    if command {
        s = &student{}
    }
    if s == nil { // nil 直接return
        return
    }
    s.Foo()
}
```

# 禁止解引用空的接口和内置引用类型变量

对chan、map内置引用类型变量，访问元素前，必须使用make显式的初始化。

对interface类型变量，在解引用或调用变量之前，应判断接口变量不为空。

**反例**

```go
// 不符合：未使用make显式初始化
var mp map[string]*student 
var ch chan int
```

# 使用[]byte而不是string存储敏感数据、敏感数据使用完后应立即清0

使用[]byte存储敏感数据，可以降低敏感数据泄漏风险

**正例**

```go
buf := make([]byte,1024)
_,err := f.read(buf)
```

# 避免使用unsafe包

uintptr是一个可容纳指针的指数，表示对象的地址。

使用时应确保：

- *uintptr 值转换unsafe.Pointer时地址有效*
- *解引用unsafe.Pointer时不存在内存越界访问*