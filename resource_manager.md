# 在性能敏感的情况下建议采用池化技术优化资源使用

一般而言，重用一个已经创建的对象比创建一个新对象快，除非确实需要重新创建。

go语音申请的变量内存需要gc。gc回收需要额外的开销。大量的内存回收可能会导致GC期间CPU消耗过多。业务处理能力下降。

其原则是尽可能少申明变量：

- 局部变量尽量利于
- 小变量合并，如果小变量过多，可以把这些变量放在一个结构体内。降低扫描和回收时间
- 高并发任务中通过goroutine池，避免调度和GC带来的冲击；goroutine虽然轻量级，但对于高并发的轻量级任务下，频繁创建groutine来执行，效率也非常低。
- 在合理的情况下使用struct{}空结构体。来避免数据值所占用的空间，如：配合map实现set集合。chan传递空消息等。

# 避免资源泄漏

在程序每个可能走到的流程中，应确保完整的释放文件句柄、DB连接等资源。特别是在异常发生时，若申请的资源（如文件句柄、数据库连接、内存资源）未得到释放，会引起资源异常占用。

实际操作中能够用defer释放的尽量用defer释放。不能强制使用defer

应确保：

- 主程序结束，所有的goroutine默认都会退出。要有安全的通知goroutine结束机制如（context包）
- 主程序没有结束，有些goroutine已经不在使用，要确保及时退出。

**正例**

```go
f,err := os.OpenFile("/test.txt",OS_RDONLY,0644)
if err != nil {
    return
}
defer func(){
    err = f.Close()
    if err != nil {
    	// 异常处理
	}
}()
```

# 合理使用defer

不要再defer中修改返回值（尤其是具名返回的情况下），使得代码不易维护。

避免在循环中使用defer

推荐使用defer的两种场景

- 资源释放
- 如果代码可能导致pannic，使用defer func recover

**反例**

```go
func tryChange() int {
    var result  int
    defer func() {
       result++ // 不符合：试图修改返回值（实际并不能） 
    }()
}
```

# 禁止重复释放资源

重复释放一般处于错误的流程判断中。

要求：

- 禁止重复关闭释放的文件、数据库连接、或网络连接等资源。
- 禁止重复释放chan等系统内置类型的对象资源

**反例**

```go
func foo(c chan int) {
    defer close(c)
    for {
        err := get()
        if err != nil {
            c <- 0
            close(c) // 不符合：重复释放channel
        }
    }
}
```

# make申请的slice、map时，根据预估或校验范围大小来申请合适的内存。

为避免扩容带来的性能消耗，在初始化slice和map时，根据预估范围来申请合适的容量。

```go
var mp = make(map[string]string 1000)
```

# 避免过多的time.After函数调用导致消耗大量的资源

如果在某个时间段频繁的多次调用，则可能导致很多未过期的Timer值，从而导致大量的内存和计算消耗。

反例

```go
func longRunning(message <-chan string){
    for {
        select{
            case <-time.After(time.Minute):
            	return // 会一直堆积，造成资源消耗
            case msg := <- message:
             	do()
        }
    }
}
```

**正例**

```go
func longRunning(message <-chan string){
    timer := time.NewTimer(time.Minute)
    for {
        select{
                case <-time.C:
                    return // 会一直堆积，造成资源消耗
                case msg := <- message:
                    do()
                    if !timer.stop(){
                        <-timer.C
                    }
         }
        // 必须重置以复用
        timer.Rest(time.Mintue)
     }
}
```