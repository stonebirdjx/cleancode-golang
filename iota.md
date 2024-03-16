`iota`只用于定义系统内部常量，**不建议**在业务代码中使用。

枚举类型需要在`const`组中声明，被赋值的变量应该使用自定义类型。

每个`iota`只在某个`const`组中自动枚举，如果定义在新的`const`组中，每次都会从`0`重新开始。

**正例**

```go
const (
   signaturePKCS1v15 uint8 = iota + 225
   signatureRSAPSS
   signatureECDSA
   signatureEd25519
)
//...
type ClientAuthType int

const (
   NoClientCert ClientAuthType = iota
   RequestClientCert
   RequireAnyClientCert
   VerifyClientCertIfGiven
   RequireAndVerifyClientCert
)
```