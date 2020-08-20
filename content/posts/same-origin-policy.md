---
title: "深入 Web 开发：同源策略"
date: "2020-08-20"
---

同源策略（Same-origin policy）是 Web 应用程序安全的基石，最早可追溯到 1995 年的 Netscape Navigator2 ，目前所有现代浏览器都遵循这一策略。

<!--more-->

根据该策略，两个网页必须「同源」才能访问对方的资源。所谓「同源」指的是：

- 协议相同
- 域名相同
- 端口相同

下表给出相对 `http://example.com.html` 的同源检测示例：

| URL                                | 结果 | 原因     |
| ---------------------------------- | ---- | -------- |
| http://example.com/about.html      | 成功 | 同源     |
| https://example.com/about.html     | 失败 | 协议不同 |
| http://blog.example.com/about.html | 失败 | 域名不同 |
| http://example.com:81/about.html   | 失败 | 端口不同 |

### 同源策略的必要性

同源策略的目的是保证用户信息的安全，防止恶意的网站窃取数据。

举个例子，Cookie 是当前识别用户，实现持久会话的最好方式，很多网站都将用户的隐私信息和登录状态存储在 Cookie 中。假设没有同源策略的限制，一个涉及银行支付类网站的 Cookie 可以被其他网站随意获取，后果的严重性大家可以想象得到。

### 如何规避同源策略

虽然同源策略是必不可少的，但开发者在遇到 iframe 多窗口通信、HTTP 请求跨域访问资源等场景时，也必须运用合理的方法绕开规则。这并不相互矛盾，恰是一种必要的补充。

#### iframe 和多窗口通信

`window.postMessage()` 方法可以安全地实现 iframe 和多窗口跨源通信。

```bash
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

- `otherWindow` ：其他窗口的一个引用，比如 `iframe` 的 `contentWindow` 属性、执行 `window.open` 返回的窗口对象、或者是命名过或数值索引的 `window.frames` 。
- `message`：将要发送到其他 `window` 的数据。
- `targetOrigin`：接收消息事件窗口的 `URI` 。
- `[transfer]`：可选。一串和 `message` 同时传递的 `Transferable` 对象。 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。

下面用一个案例来实现 iframe 父子窗口之间传递数据：

```HTML
a.com
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <div>
        <iframe src="http://b.com" id="iframe"></iframe>
    </div>
    <script>
        window.onload = () => {

            // 发送信息到 b.com
            const otherWindow = document.getElementById('iframe').contentWindow;
            otherWindow.postMessage("你好，b 。收到我的信息了吗？", "http://b.com");

            // 接收来自 b.com 的信息
            function receiveMessage(event) {
                if (event.origin !== "http://b.com") {
                    return;
                }
                console.log(`a.com 收到了来自 b.com 的信息：${event.data}`);
            }
            window.addEventListener("message", receiveMessage, false);
        }

    </script>
</body>

</html>
b.com
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <h1>b.html</h1>
    <script>
        // 接收来自 a.com 的信息
        function receiveMessage(event) {
            if (event.origin !== "http://a.com") {
                return;
            }
            console.log(`b.com 收到了来自 a.com 的信息：${event.data}`);

            // 发送信息到 a.com
            event.source.postMessage("你好，我是 b 。我已经收到你的信息了，谢谢！", event.origin);
        }
        window.addEventListener("message", receiveMessage, false);
    </script>
</body>

</html>
```

#### HTTP 请求

HTTP 请求的方式有很多种：XHR、AJAX 、HttpClient、Axios、Fetch …

当我们使用 Fetch 请求一个不同源的 API 时，浏览器会立即抛出一个错误，提示无法跨域请求资源：

```javascript
fetch(`http://api.tp5.com/houses`)
  .then(resp => resp.json())
  .then(resp => console.log(resp))

// ERROR
Access to fetch at 'http://api.tp5.com/houses' from origin 'null' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
```

我们可以使用 CORS 或 JSONP 实现跨域访问 API，推荐使用 CORS。

**跨域资源共享（ CORS ）**

CORS 是 W3C 标准，相比 JSONP 只能发送 GET 请求，CORS 支持所有的 HTTP 请求。

CORS 实现的关键在服务端，只要服务端实现了 CORS 接口，就可以跨域通信。

CORS 请求分为简单请求和预检请求两种。

a. 简单请求

服务端以 thinkphp 5.0 为例，只需在项目根目录的 `public/index.php` 文件设置 `Access-Control-Allow-Origin` 的值为 `*` ，表示接受任何源的请求。

```php
<?php

header ('Access-Control-Allow-Origin:*');

...
```

当我们再次使用 fetch 请求 API 时，控制器显示了 API 返回的数据。

```javascript
fetch(`http://api.tp5.com/houses`)
  .then(resp => resp.json())
  .then(resp => console.log(resp))

// Response Data
{
    "code": 200,
    "message": "ok",
    "data": [
        ...
    ]
}
```

b. 预检请求

与简单请求不同，预检请求在实际请求前，会先使用 OPTIONS 方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。预检请求的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

**JSONP**

JSONP（ JavaScript Object Notation with Padding ）是 JSON 的一种“使用模式”，可用于跨域请求。JSONP 的实现原理是利用 `` 标签可以载入不同源的 JavaScript 文件的特征。JSONP 的优点是简单易用，没有兼容性问题，缺点是仅适用于 GET 请求。

接下来，我们来实现一个简单的 JSONP 。

首先，在 `index.html` 中引入 `data.json` 文件。需要注意的是，请求的路径后面有一个 `callback` 参数，用来通知服务器，客户端的回调函数名称为 `dataJson` 。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <script>
        function dataJson(data) {
            console.log(data);
        }
    </script>
    <script type="text/javascript" src="data.json?callback=dataJson"></script>
</body>

</html>
```

然后，服务器收到请求，将 JSON 数据放在 `data.json` 中的函数名里面，作为字符串返回给客户端。

```json
dataJson({
  "id": 0,
  "name": "Tom",
  "age": 23
})
```

最后，只要客户端定义了 `dataJson()` 函数，该函数就会被立即调用，函数的参数就是服务器返回的 JSON 数据。Console 显示的信息为：

```json
{
  "id": 0,
  "name": "Tom",
  "age": 23
}
```

### 总结

同源策略保障了 Web 应用程序的安全，`window.postMessage()` 、JSONP 和 CORS 等方法可以规避同源策略。

### 参考资料

1. [Same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy). Wikipedia
2. [window.postMessage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage). Mozilla Developer Network.
3. [同源限制](https://wangdoc.com/javascript/bom/same-origin.html). 网道（WangDoc.com），互联网文档计划.
4. [CORS 通信](https://wangdoc.com/javascript/bom/cors.html). 网道（WangDoc.com），互联网文档计划.
5. [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS). Mozilla Developer Network.
6. [HTTP 访问控制（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS). Mozilla Developer Network.