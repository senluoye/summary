# Node.js 函数

### 前言

我们知道，在 JavaScript中，一个函数可以作为另一个函数的参数。我们可以先定义一个函数，然后传递，也可以在传递参数的地方直接定义函数。

而Node.js允许我们这样做：

```javascript
function one(x) {
  console.log(x);
}

function two(someFunction, value) {
  someFunction(value);
}

two(one, "something");
```

与原生js类似的用法。

### 传递匿名函数

Node.js当然也允许我们传递匿名函数，拿上面的例子再举个例子：

```javascript
function two(someFunction, value) {
  someFunction(value);
}

two(function(word){ 
    console.log(word) 
}, "something");
```

这时候函数没有具体的名称，但是也作为参数传进另一个函数内部。

### 函数传递与HTTP服务器工作

我们在创建http服务器的时候会用到匿名函数：

```javascript
var http = require("http");
//使用前引入http模块

http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("Hello World");
  response.end();
}).listen(8888);
```

> tips：
>
> http.createServer([requestListener])用于创建一个HTTP服务器，并将括号内的函数作为request事件的监听函数。返回一个server对象。
>
> response.writeHead(statusCode, [reasonPhrase], [headers])像请求的客户端发送响应头，第一个参数是状态码，后面参数是类似关联数组的对象，表示响应头的每个属性。（一个请求内最多执行一次）
>
> response.write(chunk, [encoding])，向请求的客户端发送响应内容（就是返回数据）。chunk是一个buffer或者字符串，如果是字符串，需要指定encoding说明编码方式，默认utf-8（可多次使用，返回数据会拼接在一起）
>
> response.end()结束请求，当然，该方法也可以用来像前端返回数据。
>
> 
> http.createServe返回的server对象有一个listen方法，用于监听参数表示的端口
> 

