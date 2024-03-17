# 避免接口过大

golang推荐使用组合的方式来写程序。go开发者一般会嵌入其他已有接口类型的方式来构建新的接口。

小结构体的主要优势：

- 接口越小，抽象程度越高，越彻底，被人接受的程度越好，其实接口的实现和现实中情况是一样的，最小的接口就是`interface{}`
- 小接口有较少的方法，一般仅仅一个方法。要实现这个接口，开发者仅仅实现一个或少数几个方法就可以了。单元测试尤为重要，可以付出较少的成本来验证你的程序是否有bug。
- 职责单一，容易组合。

可以参考io标准库的设计

# 从使用者角度设计接口

应该从使用者而不是实现者的角度设计接口，实现者应返回具体类型。

一般来说 interface 不能在实现的地方（同一个文件里）定义，应该单独的定义在某个位置

**正例**

```go
type Producer interface {DoSomething()} 

// 其他位置实现
package impl

type MyProducer struct{...}

func (mp MyProducer)DoSomething(){...}
func NewProducer() MyProducer {
    return MyProducer{}
} 
```

# 避免类型可能不为空的接口变量和nil直接进行比较

在go语言底层，interface定义的变量有两个成员变量，一个类型和一个值，只有当两个都为nil的情况下的这个接口变量才是nil。

如果某个具体的对象赋值给接口变量，无论赋值的对象是不是nil，这个接口变量都不可能是nil，因此不存在在nil值赋值给接口变量。

- 确保赋值给接口变量的对象的指针有效，否则应赋予无类型的nil值
- 必须使用error作为函数的错误返回值类型，对非错误必须返回无类型的nil。

**正例**

```go
if err := do() ;err != nil {
    return err
}
return nil
```