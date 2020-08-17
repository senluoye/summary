# Node.js Web 模块

### 前言(关于web服务器)

Web服务器一般指网站服务器，是指驻留于因特网上某种类型计算机的程序，Web服务器的基本功能就是提供Web信息浏览服务。它只需支持HTTP协议、HTML文档格式及URL，与客户端的网络浏览器配合。

### web应用架构

我们来通过一张图片来进行理解：

![alt](https://www.runoob.com/wp-content/uploads/2015/09/web_architecture.jpg)

- **Client** - 客户端，一般指浏览器，浏览器可以通过 HTTP 协议向服务器请求数据。 
- **Server** - 服务端，一般指 Web 服务器，可以接收客户端请求，并向客户端发送响应数据。
- **Business** - 业务层， 通过 Web 服务器处理应用程序，如与数据库交互，逻辑运算，调用外部程序等。
- **Data** - 数据层，一般由数据库组成。

### 使用 Node 创建 Web 服务器

我们之前使用过Node.js提供的http模块，http 模块主要用于搭建 HTTP 服务端和客户端，使用 HTTP 服务器或客户端功能必须调用 http 模块。

关于引用我们也使用过了：

```javascript
var http = require('http');
```

### 整一个实例

接下来我们演示最基本的HTTP服务器架构。

创建server.js文件：

```javascript

var http = require('http');
var fs = require('fs');
var url = require('url');
 
 
// 创建服务器
http.createServer( function (request, response) {  
   // 解析请求，包括文件名
   var pathname = url.parse(request.url).pathname;
   
   // 输出请求的文件名
   console.log("Request for " + pathname + " received.");
   
   // 从文件系统中读取请求的文件内容
   fs.readFile(pathname.substr(1), function (err, data) {
      if (err) {
         console.log(err);
         // HTTP 状态码: 404 : NOT FOUND
         // Content Type: text/html
         response.writeHead(404, {'Content-Type': 'text/html'});
      }else{             
         // HTTP 状态码: 200 : OK
         // Content Type: text/html
         response.writeHead(200, {'Content-Type': 'text/html'});    
         
         // 响应文件内容
         response.write(data.toString());        
      }
      //  发送响应数据
      response.end();
   });   
}).listen(8080);
 
// 控制台会输出以下信息
console.log('Server running at http://127.0.0.1:8080/');

```

接下来创建一个index.js文件：

```javascript

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>helloworld</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>

```

然后我们就可以执行server.js文件了：

```javascript
$ node server.js
Server running at http://127.0.0.1:8080/
```

浏览器打开：**http://127.0.0.1:8080/index.html**，就能看到我们的结果。

> 关于上面的例子，我的理解是我们在输入那一串网址后，server.js里面的代码监听到8080端口，然后获取了url进行解析。

