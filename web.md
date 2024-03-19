# 跨域资源共享CORS限制请求来源

CORS请求保护不当可导致敏感信息泄漏，因此应当严格设置Access-Control-Allow-Origin使用同源策略进行保护。

**正例**

```go
c := cors.New(cors.Options{
	AllowedOrigins:   []string{"http://qq.com", "https://qq.com"},
	AllowCredentials: true,
	Debug:            false,
})

// 引入中间件
handler = c.Handler(handler)
```

# 设置正确的HTTP响应包类型

响应头Content-Type与实际响应内容，应保持一致。如：API响应数据类型是json，则响应头使用`application/json`；若为xml，则设置为`text/xml`。

# 添加安全响应头

- 所有接口、页面，添加响应头 `X-Content-Type-Options: nosniff`。
- 所有接口、页面，添加响应头`X-Frame-Options `。按需合理设置其允许范围，包括：`DENY`、`SAMEORIGIN`、`ALLOW-FROM origin`。用法参考：[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/X-Frame-Options)

# 外部输入拼接到HTTP响应头中需进行过滤

- 应尽量避免外部可控参数拼接到HTTP响应头中，如业务需要则需要过滤掉`\r`、`\n`等换行符，或者拒绝携带换行符号的外部输入。

# 外部输入拼接到response页面前进行编码处理

出html页面或使用模板生成html页面的，推荐使用`text/template`自动编码，或者使用`html.EscapeString`或`text/template`对`<, >, &, ',"`等字符进行编码。

```go
import (
	"html/template"
)

func outtemplate(w http.ResponseWriter, r *http.Request) {
	param1 := r.URL.Query().Get("param1")
	tmpl := template.New("hello")
	tmpl, _ = tmpl.Parse(`{{define "T"}}{{.}}{{end}}`)
	tmpl.ExecuteTemplate(w, "T", param1)
}
```

# 安全维护session信息

- 用户登录时应重新生成session，退出登录后应清理session。

```go
import (
	"github.com/gorilla/handlers"
	"github.com/gorilla/mux"
	"net/http"
)

// 创建cookie
func setToken(res http.ResponseWriter, req *http.Request) {
	expireToken := time.Now().Add(time.Minute * 30).Unix()
	expireCookie := time.Now().Add(time.Minute * 30)

	//...

	cookie := http.Cookie{
		Name:     "Auth",
		Value:    signedToken,
		Expires:  expireCookie, // 过期失效
		HttpOnly: true,
		Path:     "/",
		Domain:   "127.0.0.1",
		Secure:   true,
	}

	http.SetCookie(res, &cookie)
	http.Redirect(res, req, "/profile", 307)
}

// 删除cookie
func logout(res http.ResponseWriter, req *http.Request) {
	deleteCookie := http.Cookie{
		Name:    "Auth",
		Value:   "none",
		Expires: time.Now(),
	}
	http.SetCookie(res, &deleteCookie)
	return
}
```

# CSRF防护

- 涉及系统敏感操作或可读取敏感信息的接口应校验`Referer`或添加`csrf_token`。

```go
// good
import (
	"github.com/gorilla/csrf"
	"github.com/gorilla/mux"
	"net/http"
)

func main() {
	r := mux.NewRouter()
	r.HandleFunc("/signup", ShowSignupForm)
	r.HandleFunc("/signup/post", SubmitSignupForm)
	// 使用csrf_token验证
	http.ListenAndServe(":8000",
		csrf.Protect([]byte("32-byte-long-auth-key"))(r))
}
```

# 默认最小鉴权

- 除非资源完全可对外开放，否则系统默认进行身份认证，使用白名单的方式放开不需要认证的接口或页面。

- 根据资源的机密程度和用户角色，以最小权限原则，设置不同级别的权限，如完全公开、登录可读、登录可写、特定用户可读、特定用户可写等

- 涉及用户自身相关的数据的读写必须验证登录态用户身份及其权限，避免越权操作

  ```sql
  -- 伪代码
  select id from table where id=:id and userid=session.userid
  ```

- 没有独立账号体系的外网服务使用`QQ`或`微信`登录，内网服务使用`统一登录服务`登录，其他使用账号密码登录的服务需要增加验证码等二次验证