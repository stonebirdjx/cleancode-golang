# 避免使用短声明定义一个空slice

使用var 关键字时定义空slice更清晰，并且不容易出错。

**反例**

```go
filter = []int{} // 不符合：避免短声明定义一个空的slice
```

**正例**

```go
var filter []int
```

# 始终使用len(s)==0检查slice是否为空

要检查slice是否为空（没有元素），必须始终检查其长度是否为0，

长度为0的slice不一定是nil 如 `filter := []int{}`,当如果slice ==nil长度度一定为0.

**反例**

```go
func isEmpty(s []string) bool {
    return  s == nil // 不符合：申请了空间的slice长度为0，不是nil 如[]int{}
}
```

**正例**

```go
func isEmpty(s []string) bool {
    return  len(s) == 0 
}
```

# 取值时长度校验

在对slice进行操作时，必须判断长度是否合法，防止程序panic

**反例**

```go
func foo() {
	var slice = []int{0, 1, 2, 3, 4, 5, 6}
	fmt.Println(slice[:10])
}
```

**正例**

```go
func decode(data []byte) bool {
	if len(data) == 6 {
		fmt.Println(data[5])
	}
	return false
}
```

# 不使用slice作为函数入参

slice在作为函数入参时，函数内对slice的修改可能会影响原始数据

**反例**

```go
func modify(array []int) {
    array[0] = 10 // 对入参slice的元素修改会影响原始数据
}
  
func main() {
	array := []int{1, 2, 3, 4, 5}
  
    modify(array)
    fmt.Println(array) // output：[10 2 3 4 5]
}
```

**正例**

```go
// 数组作为函数入参，而不是slice
func modify(array [5]int) {
    array[0] = 10
}

func main() {
    // 传入数组，注意数组与slice的区别
    array := [5]int{1, 2, 3, 4, 5}

    modify(array)
    fmt.Println(array)
}
```