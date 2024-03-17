# 使用internal目录避免内部公开的Api对外暴露

在设计项目或模块业务代码时，应该合理的使用internal目录，把内部公开的代码放在internal中，以提供对外合理的api

比如 example/a/internal只能被example/a开始的包导入，不能被example/e、 example/f等其他包导入。

# 禁止使用 `.` 来简化导入包

可以直接使用导入包里东西，这种写法不利于阅读

**反例**

```go
import . "example/a"  // 不符合：使用了 . 来简化导入包
```

**例外** 在测试文件_test.go中，为了避免循环依赖时方可使用

# 禁止使用相对路径导包

**反例**

```go
import "../net" 
```

# 导入包的顺序按稳定度排序

建议导入的顺序是 ：标准库、第三方库、本项目的库，不同类型之间用空行隔开

# 导入包时名称未冲突的情况下应避免使用别名

**反例**

```go
import （
	trace "runtime/trace" // 不符合：未冲突，尽量避免使用别名
）
```

**正例**

```go
import （
	trace "runtime/trace" 
	xtrace "golang.net/x/trace"
）
```