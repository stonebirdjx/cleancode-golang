# 命令执行检查

- 使用`exec.Command`、`exec.CommandContext`、`syscall.StartProcess`、`os.StartProcess`等函数时，第一个参数（path）直接取外部输入值时，应使用白名单限定可执行的命令范围，不允许传入`bash`、`cmd`、`sh`等命令；
- 使用`exec.Command`、`exec.CommandContext`等函数时，通过`bash`、`cmd`、`sh`等创建shell，-c后的参数（arg）拼接外部输入，应过滤\n $ & ; | ' " ( ) `等潜在恶意字符

**反例**

```go
func foo() {
	userInputedVal := "&& echo 'hello'" // 假设外部传入该变量值
	cmdName := "ping " + userInputedVal

	// 未判断外部输入是否存在命令注入字符，结合sh可造成命令注入
	cmd := exec.Command("sh", "-c", cmdName)
	output, _ := cmd.CombinedOutput()
	fmt.Println(string(output))

	cmdName := "ls"
	// 未判断外部输入是否是预期命令
	cmd := exec.Command(cmdName)
	output, _ := cmd.CombinedOutput()
	fmt.Println(string(output))
}
```

**正例**

```go
func checkIllegal(cmdName string) bool {
	if strings.Contains(cmdName, "&") || strings.Contains(cmdName, "|") || strings.Contains(cmdName, ";") ||
		strings.Contains(cmdName, "$") || strings.Contains(cmdName, "'") || strings.Contains(cmdName, "`") ||
		strings.Contains(cmdName, "(") || strings.Contains(cmdName, ")") || strings.Contains(cmdName, "\"") {
		return true
	}
	return false
}

func main() {
	userInputedVal := "&& echo 'hello'"
	cmdName := "ping " + userInputedVal

	if checkIllegal(cmdName) { // 检查传给sh的命令是否有特殊字符
		return // 存在特殊字符直接return
	}

	cmd := exec.Command("sh", "-c", cmdName)
	output, _ := cmd.CombinedOutput()
	fmt.Println(string(output))
}
```