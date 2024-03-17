# 使用恰当的错误处理机制

错误处理机制有:

- `in-band`错误提示：通过返回的内容，区别是正常值还是错误值
- 使用异常panic机制
- 使用error接口的方式返回错误值 （推荐使用）

# 确保正确处理函数的错误返回值

任何时候都不要忽略返回的error类型

主函数需要关闭文件之类的除外

**反例**

```go
b, _ : = get() // v ,err
b.bar() // 未正常处理错误，b可能为nil
```

**正例**

```go
if b,err := get() ;err != nil {
    b.bar()
}
```

# 错误信息不应该大写，或者以标点结尾。

错误字符串要求：除专有名词或者缩写词外，避免字符串以大写字面开头，或以标点结尾

**反例**

```go
fmt.Errorf("Something bad.") // 不符合，上下文拼接后，字符串中间会有大写字面开头的场景
```

**正例**

```go
fmt.Errorf("something good")
```

# 包外可见的函数禁止向外抛出panic

包外可见的函数禁止向外抛出panic，如果导出函数本身或者调用其他函数可能抛出panic，需要内部recover并转换成error返回给外包调用者。

**正例**

```go
func justDo() (err any) {
	defer func() {
		err = recover()
	}()
    panic("err")
	return err
}
```

# 确保发生异常时程序尝试恢复到合理的状态并记录日志

当函数/方法发生异常时，一般对象需要恢复到原来状态，安全关键的对象必须恢复到原来的状态。保持对象一致性常用手段包括：

- *输入校验，如校验方法的参数*
- *调整逻辑顺序，使可能发生异常的代码在对象修改之前执行。*
- *对业务操作失败时回滚*
- *对临时副本对象操作，直到操作完成，才把更新提交到原始对象上。*
- *编码修改对象状态*