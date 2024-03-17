# 合理使用匿名嵌套

使用结构体嵌套时应避免暴露内部方法

**反例**

```go
type MyStack struct {
    Mylist // 不符合：此匿名嵌入暴露了内部的实现
}
```

**正例**

```go
// 正例
type MyStack struct {
    list Mylist // 符合：不使用匿名嵌套，使得嵌入实现的方法不会被暴露出去
}
```