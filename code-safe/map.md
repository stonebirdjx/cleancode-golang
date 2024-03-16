# 确保key读取到map的元素有效

key如果不存在，会返回零值。使用if ok ，判断key是否存在

**正例**

```go
if v, ok := mp["key"]; ok {
    ...
}
```