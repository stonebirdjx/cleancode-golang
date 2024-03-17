# 避免数据竞争

map 、slice不是并发安全的，所以在并发下共享数据访问存在数据竞争（不单指map和slice）。并发时优先推荐消息传递数据，而不是共享内存。

go1.9 之后可以用sync.Map满足并发安全的map需求.

可以使用go 的race工具分析代码是否存在数据竞争。

```go
go test -race pkgname
go run -race src.go
go build -race cmds
go install -race pkgname
```

# 避免goroutine被永久阻塞

避免goroutine永久阻塞导致内存泄漏；goroutine永久阻塞一般是由于锁或者chan未正确使用造成的。

确保select的不会被长时间或者永久阻塞，可添加default分支：

需要遵循以下要求：

- 确保读取chan时，chan存在数据，否则会被阻塞。
- 不向值为nil（var ch chan int）的chan发送或接收数据，否则将导致goroutine永久阻塞
- 避免死锁导致goroutine阻塞。

# chan类型作函数参数时限定类型

chan类型作为函数参数传递时限定类型，根据函数创建最新权限得channel，以增加代码的可读性。

**正例**

```go
func readOnly (in <-chan int) {
    ...
}

func writeOnly(out chan<- int){
    ...
}

func readAndWrite(ch chan int){
    ...
}
```

# 使用chan时确保对chan的操作有效

保chan使用时已被初始化（使用make申请过空间），禁止对nil和closed的chan进行操作，否则可能导致以下问题

- *关闭closed的channel会产生panic*
- *关闭 nil 的 chan 会产生panic*
- *向closed的chan发送数据会panic*
- *当chan没数据时，取数据会返回零值。必须通过if ok判断是否正常取出数据。*

**正例**

```go
select {
case _, ok := <- ch:
    if !ok {
        return
    }
}
```

# 禁止拷贝锁和同步的对象

使用锁机制时，应避免死锁。复制锁或同步的对象本身没有同步保护，因此复制结果可能不完整（不是有原值得快照），即使锁是完整的它也会和已有锁存在冲突。

应确保：

- *正确的使用锁，程序中不存在死锁；注意使用相同的顺序请求和释放锁来避免死锁。*
- *确保加锁的代码范围合理；禁止在循环语句中使用defer语句释放锁。禁止多次释放同一个锁*
- *禁止拷贝包含锁或同步的数据结构（一般在sync包里定义了锁或同步的类型结构），只能通过指针或接口等引用类型来传递此类数据结构的引用。*

**反例**

```go
for i := 0 ; i < len(s) ; i++ {
    m.Lock()
    defer m.Unlock() // 不符合：不能在循环中使用defer来释放锁
}

type Counter struct{
   sync.Mutex
   n int64
}

func (c Counter) Value() int64 { 
    ... // 不符合：方法接受者为值类型，当它被调用时，Couner被复制。简介导致Counter中的Mutex被复制
}
```

**正例**

```go
for i := 0 ; i < len(s) ; i++ {
  	func() {
		 m.Lock()
    	 defer m.Unlock()
	}()
}

func (c *Counter) Value() int64 { // 有锁的对象，使用指针传递。 
    ...
}
```