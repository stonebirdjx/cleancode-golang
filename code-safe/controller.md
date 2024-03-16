# 删除不必要的else

如果两个分支中都包含`return`语句，则可以去除冗余的`else`。能不要else的时候就不用else

**反例**

```go
if foo {
   return x
} else {
   return nil
}
```

**正例**

```go
if foo {
   return x
}
return nil
```

# 尽量保持正常代码路径为最小缩进

优先处理并缩进错误分支，增加可读性。

**反例**

```go
func aFunc() error {
    err := doSomething()
    if err == nil {
        err := doAnotherThing()
        if err == nil {
            return nil // 正常代码
        }
        return err
    }
    return err
}
```

**正例**

```go
func aFunc() error {
    if err := doSomething(); err != nil {
        return err    
    }
    if err := doAnotherThing(); err != nil {
        return err    
    }
    return nil // 正常代码
}
```

# 如果只用到`range`中的索引部分，则去除冗余的元素

**反例**

```go
for i, _ := range s {
   print(i)
}
```

**正例**

```go
for i := range s {
   print(i)
}
```

# 如果不使用`range`中的任何部分，则不要赋值给匿名变量

**反例**

```go
for _ = range s {
   ...
}
```

**正例**

```go
for range s {
   // ...
}
```

# 循环必须有显示的退出条件

程序无法正常结束是一种缺陷，特别是它无法响应外部对他的控制。如果循环没有退出条件，循环在任何或预计时间内将完不成执行，导致死循环。

应尽量避免使用空for，如果无法避免，需要在循环过程中设置能退出的条件，并使用break退出。

**反例**

```go
for {
    ... // 不符合，没有显示的退出条件
}
```

**正例**

```go
for {
    if condition {
        break
    }
}
```

# 避免在循环体中修改循环控制变量

如果在循环体内修改循环控制参数的数值，可能导致死循环，或者循环次数达不到预期

**反例**

```go
for i := 0 ; i < 10 ; i++ {
    i -= 1
}
```

**正例**

```go
for i := 0 ; i < 10 ; i++ {
    ...
}
```

# 慎用goto,goto只能向下跳转

goto语句会破坏程序的结构性，非必要不使用goto，使用goto时，也只允许跳转到本函数内的goto语句之后的标签（向下跳转）。

**反例**

```go
unsafe:
if done {
    goto unsafe // 不符合：goto往上跳转标签
}
```

**正例**

```go
if done{
    goto label
}
label:
```

# Switch要有default分支

每个switch，都应该包含一个default分支，即使default分支，没有业务逻辑代码。

> 枚举类型除外

**正例**

```go
switch str {
case "a":
default:
}
```

**例外**

```go
switch color {
case red:
case green:   
case blue:
}
```

# 禁止使用浮点数作为循环计数器

存储浮点数的精度有限，精度问题可能导致条件判断不准确，导致循环达不到预期。

**反例**

```go
for i := float32(2000000001);i < float32(2000000002);i++ {
    ... // 2e+9 不会进入循环
}
```

# 不要在迭代集合数据结构的过程中修改或者删除元素

使用 `for range`或 `for` 循环可以便捷的访问map和slice中的元素。

- 对slice是修改不会影响循环次数，但会影响结果。
- 对map的key的增加或删除会影响循环次数。

**反例**

```go
func main() {
	a := []int{0, 2, 1, 3, 5}
	for _, val := range a { // 不符合，在迭代原有的slice时，同事删除了索引为2的元素。
		fmt.Println(val)
		if len(a) > 4{
			a = append(a[0:2],a[3:]...)
		}
	}
}
// output 0 2 3 5 5

for k , v := range mp {
	mp["answer"] = 10 // 不符合：增加了key会影响对mp的变量
}
```