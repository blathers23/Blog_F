# Golang - Hello World!

> **⚠️ 这篇文章发布于以往的 Blog 网站，此处仅仅搬运收藏，不再对内容订正。**

**✍️ go verison 1.15.8**

使用 VSCODE 配置 go 开发环境时遇到了一些问题，主要是无法下载 Go modules，这里简要记一下配置方法（ Windows 系统）：

## 1.  GO111MODULE
### 环境变量方法：
打开环境变量，为用户新建环境变量，变量名为 `GO111MODULE`，变量值为 `on`。
### 命令方法：
```go
go env -w GO111MODULE='on'
```
*两种方法取其一即可。*

## 2.  GOPROXY
此处博主比较了 goproxy.io、阿里云镜像、goproxy.cn 三家镜像代理服务，博主自测 goproxy.cn 效果最佳。

### 环境变量方法：
打开环境变量，为用户新建环境变量，变量名为 `GOPROXY`，变量值为 `https://goproxy.cn,direct`。
### 命令方法：
```go
go env -w GOPROXY='https://goproxy.cn,direct'
```
*两种方法取其一即可。*

## 3.go.mod
在代码调试前，还需要在命令行中执行 `go mod init ****.go`，\*\*\*\*处为模块名称。


