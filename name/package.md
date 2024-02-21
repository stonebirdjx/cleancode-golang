# 包名

原则如下

- 只由小写字母组成。不包含大写字母和下划线等字符（测试包除外）。
- 简短并包含一定的上下文信息。例如`time`、`list`、`http`等。
- 不能是含义模糊的常用名或者与标准库同名。例如不能使用`util`或者`strings`。
- 包名能够作为路径的 base name，在一些必要的时候，需要把不同功能拆分为子包。例如应该使用`encoding/base64`而不是`encoding_base64`或者`encodingbase64`。

**正例**

```go
package sha256
package sha256_test
```

**反例**

```go
package stone_bird
```

# 包名要和所在的目录名一致

如果不一致，会给阅读带来困难，难以理解。

**正例**

```go
// 目录名：context
package context

// 目录名：port-obj
package portobj
```

**反例**

```go
// 目录名：context
package port_obj
```

