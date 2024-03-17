# 必须处理类型断言的失败

类型断言可以判断接口变量封装的是否是期望类型；也可以判断接口变量封装的类型是否实现了目标接口。在对接口变量进行类型断言时，必须判断断言是否成功，如果失败仍执行会panic。

类型断言使用场景有两种：if ok 和 switch，switch分支使用default分支来处理其他情况

**正例**

```go
if v ,ok := a.(string) ; !ok {
	// 错误处理
}

switch t := t.(type) {
case int:
case bool: 
default: // 必须使用默认分支来处理失败的情况
}
```