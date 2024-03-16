# 需要字符串转义时优先使用raw string

**反例**

```go
const prefix = "name=\"stonebird\""
```

**正例**

```go
const prefix = `name="stonebird"`
```