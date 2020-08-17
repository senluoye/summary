# Node.js 路由

### 前言

Node.js也有路由，我们需要为路由提供请求的URL和其他需要的GET以及POST参数，随后路由需要根据这些数据执行相应的代码

> 路由就是URL到函数的映射
>
> route是一条路由
>
> 如：
>
> ​	/users  - - > 调用 getAllUsers()函数
>
> ​    /users/count/ - - > 调用 getUsersCount()函数
>
> 就是把路径与函数进行一一对应，通过路径信息调用特定的函数
>
> Routers负责管理route与函数之家的映射关系

所以我们需要查看HTTP请求，并从中提取出请求的URL以及GET/POST 参数。

我们需要的数据会包含在request对象中，这个对象作为 onRequest() 回调函数的第一个参数传递。为了解析这个数据，我们需要引入额外的模块——url和querystring 模块。

```javascript
                   url.parse(string).query
                                           |
           url.parse(string).pathname      |
                       |                   |
                       |                   |
                     ------ -------------------
http://localhost:8888/start?foo=bar&hello=world
                                ---       -----
                                 |          |
                                 |          |
   querystring.parse(queryString)["foo"]    |
                                            |
				 querystring.parse(queryString)["hello"]
```

> 这个onRequest函数指的就是http.createServer（）里面作为参数的回调函数

当然我们也可以用 querystring 模块来解析 POST 请求体中的参数。



### 开冲

整一个案例：我们现在来给onRequest()函数加上一些逻辑，用来找出浏览器请求的URL路径。

创建一个server.js文件：

```javascript

var http = require("http");
var url = require("url");
 
function start() {
  function onRequest(request, response) {
      //定义一个onrequest函数
    var pathname = url.parse(request.url).pathname;
      //url.parse()解析一个URL字符串并返回一个URL对象
      //pathname是路径
      
    console.log("Request for " + pathname + " received.");
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }
 
  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}
 
exports.start = start;
//封装成模块
```

这样，我们获得了URL路径，也就可以根据不同的路径区别不同的请求了，这也使得我们可以使用路由来将请求一URL路径为基准映射到处理程序上。

这意味着在我们构建的应用中，来自 /start 和 /upload 的请求可以使用不同的代码来处理。

现在来编写路由。

router.js文件：

```javascript

function route(pathname) {
  console.log("About to route a request for " + pathname);
}
 
exports.route = route;

```

我们并没有在这里表示我们做了什么，但是可以用来看到路由是否和服务器整合起来。

抛弃硬编码的方式，我们将使用依赖注入的方式较松散地添加路由模块。

首先，我们来扩展一下服务器（server.js里的）的 start() 函数，以便将路由函数作为参数传递过去

server.js 文件代码更新如下：

```javascript
var http = require("http");
var url = require("url");
 
function start(route) {//新传入一个route参数
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");
 
    route(pathname);//将url传进去
 
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }
 
  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}
 
exports.start = start;

```

接下来是主文件index.js：

```javascript
var server = require("./server");
var router = require("./router");
 
server.start(router.route);

```

下面是最终的输出：

```
Server has started
Request for /received
About to route a request for /
Request for /favicon.icoreceived
About to route a request for /favicon.ico
```

（以上输出包含了比较烦人的 /favicon.ico 请求相关的部分）

我们在浏览器访问 **http://127.0.0.1:8888/**，就可以看到输出结果，是一个由Hello World文字的网页。

