# 禁止在闭包中直接使用循环控制变量

**反例**

```go
for i := 0 ;i < 5 ; i++ {
    wg.Add(1)
    go func (){ // 不符合：避免在闭包中直接使用i
        defer wg.Done()
        fmt.Println(i)
    }()
}
// output  55555
```

**正例**

```go
// 正例
for i := 0 ;i < 5 ; i++ {
    wg.Add(1)
    go func (j int) {
        defer wg.Done()
        fmt.Println(j)
    }(i)
}
```

> 1.22 版本之后使用没有问题，但仍不建议这样写