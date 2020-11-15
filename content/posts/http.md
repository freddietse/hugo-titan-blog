---
title: "HTTP"
date: "2020-11-15"
toc: true
---

### HTTP 版本

- HTTP/0.9
- HTTP/1.0
- HTTP/1.1
- HTTP/2.0
- HTTP/3.0

### URL

服务器资源名被称为统一资源标识符（Uniform Resource Identifier，URI），它有两种形式，分别是统一资源定位符（URL）和统一资源名（URN）。其中，URL 是最常见的形式，几乎所有的 URI 都是 URL。

大多数 URL 的结构都是由方案（scheme）、主机（host）和路径（path）三个部分组成。

#### 方案

方案告诉负责解析 URL 的应用程序（通常是浏览器）应该使用什么协议（protocol），常见的协议有：

| 方案        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| data        | [Data URIs](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/data_URIs) |
| file        | 指定主机上文件的名称                                         |
| ftp         | [文件传输协议](https://developer.mozilla.org/en-US/docs/Glossary/FTP) |
| http/https  | [超文本传输协议／安全的超文本传输协议](https://developer.mozilla.org/en-US/docs/Glossary/HTTP) |
| mailto      | 电子邮件地址                                                 |
| ssh         | 安全 shell                                                   |
| tel         | 电话                                                         |
| urn         | 统一资源名称                                                 |
| view-source | 资源的源代码                                                 |
| ws/wss      | （加密的） [WebSocket](https://developer.mozilla.org/zh-CN/docs/WebSockets) 连接 |

以下面的 URL 为例：

```bash
https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png
```

方案是 `https` ，主机是 `www.google.com` ，端口是 `443` ，路径是 `/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png` 。

#### 主机

#### 路径