以下场景必须严格进行长度限制：

- 作为数组索引
- 作为对象的长度或者大小
- 作为数组的边界（如作为循环计数器）

# 确保无符合整数运算时不会回绕

所谓回绕是指无法用无符号整数表示运算结果。这个结果会根据该类型可以表示的最大值加1执行求模操作。

> +、-、*、++、--、+=、-=、<<=、<<

**反例**

```go
func add(a, b uint64) {
	c := a + b // 如果 a + b > math.MaxUnit64 会存在无符号整数回绕（从0开始计算）
}
```

**正例**

```go
// 加法 判断是否大于 math.MaxUint64，其他类型类推
func add(a, b uint64) (uint64, error) {
	if a > math.MaxUint64-b {
		return 0,errors.New("lager num")
	}
	return a + b, nil
}

// 减法 判断前者是否小于后者
func sub(a, b uint64) (uint64, error) {
	if a < b {
		return 0, errors.New("回绕")
	}
	return a - b, nil
}
```

# 确保有符合整数运算时不会出现溢出

**整数溢出**是一种未定义的行为，意味着编译器处理器有符合整数溢出时具有很多选择，有符合整数溢出会发生在如下操作中

> +、-、*、/、++、--、+=、-=、/=、%=、<<=、<<

后续代码如以此作为分配内存的大小，将导致申请的内存比实际所需的过小、或者过大，从而导致内存不足的问题。

**反例**

```go
func add(a, b int32) {
	c := a + b // a + b > math.MaxInt32 时两者相加会产生有符合整数溢出
}
```

**正例**

```go
// 正例,需要先计算会不会溢出（从负值开始计算）
func doInt(a, b int32) {
	var c int32
	if (b > 0 && a > (math.MaxInt32-b)) ||
		(b < 0 && a < (math.MaxInt32-b)){
		// 错误处理
	}
	c = a + b
}
```

# 确保参与移位的操作数的位数足够

移位的位数应该是无符号整数，go对无类型的常数依照参与表达式运算数据类型进行推导

**反例**

```go
func foo(num uint16,bit uint8) bool {
	if num > (1 << bit) { // 不符合：这里（1 << bit）被推导成了uint16，可被移位操作回绕
		return true
	}
	return false
}
```

**正例**

```go
func foo(num uint16,bit uint8) bool {
	if uint32(num) > (uint32(1) << bit) { // 强制类型转换后，应满足函数设计要求
		return true
	}
	return false
}
```

# 确保除法运算和模运算中的除数不为0

> 运行时会报错

**正例**

```go
func div(total,a int) bool {
	if a == 0 {
		return false
	}
	avg = total / a
}
```

# 确保整数转换不会造成数据截断或者符号错误

当一个较大类型转换为较小类型时，并且该数原值超出较小整型的表示范围，就会发生截断错误。

有符号整型向无符号整型转换时，最高位会丧失作为符号位的功能

**反例**

```go
var a = math.MaxInt32
b := int16(a) // 不符合，不同类型强制转化数据会发生数据截断

var a = math.MinInt32
b := uint32(a) // 不符合，产生符号丢失
```

**正例**

```go
func int32216(a int32){
	if (a < math.MinInt16) || (a > math.MaxInt16) {
    	// 错误处理
	}
    int16(a)
}

func int2uint(a int32){
    if a < 0 {
    	// 错误处理
	}
    b := uint32(a)
}
```

- 带符号整数值为非负时，向无符号整型转换后，值不变
- *带符号整数值为负数时，向无符号整型转换后，结果通常是一个非常大的整数*