# 最小化使用cgo范围

cgo调用开销非常大，需要谨慎使用，如果能用纯go解决的就不要用cgo

# 使用C虚拟包辅助函数C.CString和C.CBytes返回的类型转换参数使用完后需要手动释放

**正例**

```go
// #include "stdio.h"
import "C"
import "unsafe"

func main() {
    cs := C.CString("hello")
    defer C.free(unsafe.Pointer(cs)) //需要手动释放
    C.puts(sc)
}
```

# 避免将Go内存指针直接传入C函数使用

CGO时go语音和c语音实现互通。在需要将go的字符串传入C语音时，先通过C.CString将go语音的字符串对应的数据复制到C语音的内存空间上。确保安全的使用，但会导致效率下降

**正例**

```go
package main
/*
#include "stdio.h"
#include "stdlib.h"

void printString(const char* s){
	printf("%s",s)
}
*/
import "C"
import "unsafe"

func main() {
    cs := C.CString("hello")
    defer C.free(unsafe.Pointer(cs)) //需要手动释放
    C.printString(sc)
}
```

# 避免使用C代码接管系统的信号

应尽量避免C代码接管系统的信号。signal应该go的信号处理来接管

# 避免直接将C代码嵌入在Go文件中

直接经C代码嵌套在go中，可读性差。应该将C代码打包程静态库/动态库的方式调用。

# 避免在CGO中使用C代码调用Go函数

防止下面情形

- 被C函数调用的go函数中再调用C函数
- 被Go函数调用的C函数中再调用Go函数