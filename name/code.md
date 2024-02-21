# 避免使用go内置标识符

**反例**

```go
var string string = "stone bird"
```

# 避免使用包名作为前缀

定义类型函数等，应避免包名的上下文信息

**反例**

```go
// stream.go
package stream

type StreamBuffer struct{ 
    //不符合，var buf = stream.StreamBuffer过于累赘
}
```

**正例**

```go
package stream

type Buffer struct{ 
    // 符合 var buf = stream.Buffer 层次清晰
}
```

# 方法接受者应该简洁

接受者名称应当简洁（1-2个字母即可）

**正例**

```go
func (c *Controller) send(c <-chan struct{})
```

不要使用me、self、this这种泛指的名称，这会让你看起来不像go程序员

**反例**

```go
func (self *Controller) send(c <-chan struct{})
```

# 导出错误的变量采用ErrName的格式

**正例**

```go
type ReadError struct {
    ...
}

var ErrReading = errors.New("read err")
```

- *错误类型：使用NameError*

- *错误变量：ErrName*

# 函数名应当是动词或动词短语

**正例**

```go
func postPayment(){}
func DeletePage(){}
func SaveOrder(){}
```

# 结构体名应该是名词或名词短语

**正例**

```go
type Customer struct {}
type WikiPage struct {}
type Account struct {}
```

# 专有名词的命名

应该全部大写或者全部小写

```bash
"ACL","API","ARPU","ASCII","BO","CPA","CPC","CPM","CPU","CSS","CPP","CPS","CPT","CQRS","CTR","CVR","DNS","DAU","DO","DSP","DTO","EOF","GUID","HTML","HTTP","HTTPS","ID","IO","IP","JSON","KPI","LBS","LHS","MAU","OKR","PC","PV","PO","POJO","QPS","RAM","RHS","RPC","SEM","SEO","SLA","SMTP","SNS","SPAM","SOHO","SQL","SSH","TCP","TLS","TTL","UDP","UGC","UED","UI","UID","UUID","URI","URL","UTF8","UV","VM","VO","VR","XML","XMPP","XSRF","XSS"
```

**正例**

```bash
urlArray, URLArray, userid  userID, UserID,  CPUInfo, cpuInfo
```

**反例**

```bash
UrlArray, UserId, CpuInfo
```

# 使用业界统一缩略语

和业界常用的缩略语保持一致，例如

```bash
temp => tmp
flag => flg
statistic => stat
increment => inc
message => msg
buffer => buf
error => err
argument => arg
parameters => params
initialize => init
index => idx
context => ctx
clear => clr
pointer => ptr
previous => prev
request => req
response => resp|rsp
maximum => max
minimum => min
result => ret/res
```

