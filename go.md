## 提示
1. go语言中go.mod的require不能指定版本，还需要在replace里修改才行

## 设置国内代理
`go env -w GOPROXY=https://goproxy.cn,direct`

## 远程开发
在vscode的go插件setting.json里写入  
`{
    "go.goroot": "/usr/local/go",
    "go.gopath": "/home/liujx/gopath",
    "go.toolsEnvVars": {
        "GOPROXY": "https://goproxy.cn,direct"
    }
}
`

## 转json结构化打印结构体
```
"bytes"
"encoding/json"

bs, _ := json.Marshal(mynet)
var out bytes.Buffer
json.Indent(&out, bs, "", "\t")
fmt.Printf("spine-leaf=%v\n", out.String())
```