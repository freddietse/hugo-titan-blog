---
title: "Gin"
date: "2020-11-03"
toc: false
---

Gin 是一个基于 Golang 开发的 Web 框架。

## 快速开始

### 0. 下载和安装 Go

#### Windows

首先[下载](https://golang.org/dl/) `.msi` 后缀的安装包（如 `go1.15.3.windows-amd64.msi`）进行安装。默认情况下，安装程序的路径为 `C:\Go\` ，程序会自动将 `C:\Go\bin\` 目录添加到环境变量中。如果自定义路径，则需要手动添加环境变量。

安装完成后，可以使用命令行验证 Go 是否安装成功：

```bash
$ go version
```

### 1. 准备工作

#### 开启 Go modules

[Go Modules](https://github.com/golang/go/wiki/Modules) 是 Golang 官方团队推荐的项目依赖管理工具。

首先使用 `go env` 命令查看当前环境是否开启了 Go modules，`on` 代表开启，`off` 代表关闭。

```bash
$ go env

...
set GO111MODULE=on
...
```

如果没有开启，则需要运行如下命令开启：

```bash
$ go env -w GO111MODULE=on
```

#### 设置 GOPROXY 代理

由于网络问题，使用 `go get` 命令下载第三方模块特别慢，所以还需要设置七牛云的 [Go 模块代理](https://goproxy.cn/)。

```bash
$ go env -w GOPROXY=https://goproxy.cn,direct
```

### 3. 初始化项目

```bash
$ go mod init github.com/my/repo

go: creating new go.mod: module github.com/my/repo
```

- [Awesome Go](https://github.com/avelino/awesome-go)
- [《跟煎鱼学 Go》](https://eddycjy.com/go-categories/)
- [《Go 简易教程》](https://learnku.com/docs/the-little-go-book)
- [《Go 入门指南》](https://learnku.com/docs/the-way-to-go)
- [《Go Web 编程》](https://learnku.com/docs/build-web-application-with-golang)
