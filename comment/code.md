# 注释位于上边或右边

如果注释位于单独一行，则需要以大写开头（缩略词或变量名除外）并以`.`结尾。

**正例**

```go
if l.flag&(Lshortfile|Llongfile) != 0 {
    // Release lock while getting caller info - it's expensive.
    l.mu.Unlock()
    var ok bool
    _, file, line, ok = runtime.Caller(calldepth)
    if !ok {
        file = "???"
        line = 0
    }
    l.mu.Lock()
}
```

如果位于语句后方，则不要以大写开头，且结尾不需要加入标点符号

**正例**

```go
const Ldate = 1 << iota // the date in the local time zone: 2009/01/23
```

# 外部引用的标识符都应该有注释

所有导出符号的代码必须有注释

**正例**

```go
// FindByName find some info by enter the name.
func FindByName(name string) string{
    ...
}
```

# 避免无用的垃圾注释

避免简单的注释，重复、无内容的注释

**反例**

```go
var age = 18 // definition age eq 18

// WriteData 
// name:WriteData  	重复
// args：		   没内容
// return：		   没内容
func WriteData(buf []byte,length int)(int error){}
```

# 正式代码不应该包含TODO/FIXME注释

**反例**

```go
// TODO: todo somethings
// FIXME: fixed some bugs
```

