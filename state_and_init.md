# 保证作用域尽量的小

作用域的变量、函数、类型等在程序内可引用的越大，引起错误的可能性就越高。

不能导出的常量、变量、函数、或闭包只在包内使用

最小化作用域的方法：

>  *1、尽量推迟变量定义，在变量使用时才能被初始化*
>
>  *2、把相关语句放在一起或单独提取到子函数，是函数尽量小而集中，功能单一*

**正例**

```go
for i := 0; i < 10; i++ {
    ...
}
```

**反例**

```go
// 不符合，循环之后不需要访问i。而且i容易被篡改 
i := 0 
for ; i < 10; i++ {
	...
}
```

# 禁止使用魔鬼值

不要使用难以理解的字面量，禁止使用魔鬼字符串、魔鬼数字等

**正例**

```go
const Deleted 1
const XyzType 100
if xxx == XyzType {
  ...
}
return Deleted,nil
```

**反例**

```go
if xxx == 100 {
  ...
}
return 1001

// 下面的数字字面量，不容易理解其具体含义
switch curType {
	case 1:
	case 2:
	case 3:
	default:		
}
```

# 避免直接使用全局变量

禁止将全局变量导出，使用全局变量会导致业务代码和全局变量间产生耦合，并且难以追踪数据的变化。应尽量避免使用全局变量。

包内使用全局变量，应该对其进行封装，并注意避免因并发而出现的数据竞争

**反例**

```go
// 不符合，导出了全局变量,容易被他出修改
var SumValue = 0 
```

**正例**

```go
// 当前模块确保数据不存在竞争
var total = 0 

func GetTotal() int {
	return total
}
```

# 变量使用时才声明并初始化

遵循变量作用域最小原则与就近声明原则，在变量使用时才声明并初始化，使得代码更容易阅读。

**正例**

```go
var name = "stone bird"
```

**反例**

```go
var name string
...
name = "stone bird"
```

# 相关申明放在一个组内

相同用途的常量或变量应该放在一个常量组或变量组。相关性高的放置在同一个组里面，以提升代码的可读性。

```go
type Duration int

const (
	Nanosecond Duration = 1
	MicroSecond  = 1000 * Nanosecond
	...
)
```

# 初始化结构体变量时，尽可能采用复合字面量的方式

初始化结构体变量时，优先采用复合字面量的方式，并指定字段名称，以提升代码可读性。应该尽量用每一行一个`字段名：初始化值`的形式。

> *数组和slice的成员是结构体的情况除外。*

**反例**

```go
type stoneBird struct {
	name string
	age int
	language string
}

sb := new(stoneBird)
sb.name = "stone"
sb.age = 18
sb.language = "go"
```

**正例**

```go
func New() stoneBird {
	return stoneBird {
		name:     "stone",
		age:      18,
		language: "go",
	}
}
```

**例外**

```go
sbs := []stoneBird {
    {"stone",18,"go"},
    {"stone",19,"go"},
    {"stone",20,"go"},
}
```
