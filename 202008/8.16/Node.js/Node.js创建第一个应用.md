# Node.js创建第一个应用

### 前言

使用 Node.js 时，我们不仅仅 在实现一个应用，同时还实现了整个 HTTP 服务器。事实上，我们的 Web 应用以及对应的 Web 服务器基本上是一样的。

### Node.js应用组成

1. **引入 required 模块：**我们可以使用 **require** 指令来载入 Node.js 模块。
2. **创建服务器：**服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。
3. **接收请求与响应请求** 服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

### 创建Node.js应用

##### 引入 required 模块

```javascript
var http = require("http");
```

##### 创建服务器

使用 http.createServer() 方法创建服务器，并使用 listen 方法绑定 8888 端口。 函数通过 request, response 参数来接收和响应数据。比如我们先创建一个HelloWolrd.js，写入以下代码：

```javascript
var http = require("http");
//请求（require）Mode.js自带的http模块，并赋值给http

const { listeners } = require("process");

http.createServer(function(request, response) {
    //request、response参数来接收和响应数据

    response.writeHead(200, { 'Content-Type': 'text/plain' });
    // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain

    response.end('hello world\n');
    // 发送响应数据 "Hello World"
}).listen(8888); //用listen方法绑定8888端口

// 终端打印如下信息
console.log('server running at http://127.0.0.1:8888/')

//以上代码就是一个可以工作的HTTP服务器
```

然后使用node命令执行该文件就可以看到结果。

> 关于node命令：运行一个文件
>
> ```javascript
> node HelloWolrd.js
> ```

该文件运行结果是：

```javascript
server running at http://127.0.0.1:8888/
```

打开该地址就可以看到网页显示的内容。

