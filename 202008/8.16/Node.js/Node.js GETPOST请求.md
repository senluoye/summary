# Node.js GET/POST请求

### 前言

在很多场景中，我们的服务器都需要跟用户的浏览器打交道，如表单提交。

表单提交到服务器一般都使用 GET/POST 请求。

### 关于GET和POST

##### GET

GET通常用于获取服务端数据。

常见发起GET请求的方式有：URL 、src/href、表单（form）。

参数名与参数值之间没有空格。

参数值不需要使用单双引号包括。

##### GET方式提交数据的特点

1、get方式在url后面拼接参数，只能以文本的形式传递参数。

2、传递的数据量小，4kb左右（不同浏览器会有差异）。

3、安全性低，会将信息显示在地址栏。

4、速度快，通常用于对安全性要求不高的请求。

##### POST

POST就是发送、提交。可以向指定的资源提交要被处理的数据。

>如果使用表单方式进行提交，表单的method必须设置为POST

##### POST方式提交的特点

1、post提交数据相对于get的安全性高一些。（注意：抓包软件也会抓到post的内容，安全性要求高可以进行加密）

2、传递数据量大，请求对数据长度没有要求。

3、请求不会被缓存，也不会保留在浏览器的历史记录中。

4、用于密码等安全性要求高的场合，提交数据量较大的场合，如上传文件，发布文章等。

5、POST方式提交数据上限默认为8M（可以在PHP的配置文件post_max_size选项中修改）



### 获取GET请求内容

 由于GET请求直接被嵌入在路径中，URL是完整的请求路径，包括了?后面的部分，因此你可以手动解析后面的内容作为GET请求的参数。

之前学过的url.parse函数可以解析URL中的参数：

```javascript
var http = require('http');
var url = require('url');
var util = require('util');
 
http.createServer(function(req, res){
    res.writeHead(200, {'Content-Type': 'text/plain; charset=utf-8'});
    res.end(util.inspect(url.parse(req.url, true)));
}).listen(3000);

```

>util.inspect方法可以将任意对象转换为字符串。
>
>url.parse解析一个URL字符串，第一个参数是要解析的字符串，第二个参数的布尔值如果为 `true`，则 `query` 属性(这就是url的参数)总会通过 [`querystring`](https://www.nodeapp.cn/querystring.html) 模块的 `parse()` 方法生成一个对象。 如果为 `false`，则返回的 URL 对象上的 `query` 属性会是一个未解析、未解码的字符串。 默认为 `false`。
>
>之前学的时候也说过response.end()也可以向前端返回数据。

这里引用菜鸟的案例：

在浏览器中访问 **http://localhost:3000/user?name=菜鸟教程&url=www.runoob.com** 然后查看返回结果:

![alt](https://www.runoob.com/wp-content/uploads/2014/06/4A1C02B2-2EB8-4976-9F35-F3760713D495.jpg)

这样我们就  可以拿到请求的参数了。

### 获取URl的参数

上一节已经展示了我们如何获取GET的请求内容，接下来演示怎么获得参数，同样也是用url.parse：

```javascript

var http = require('http');
var url = require('url');
var util = require('util');
 
http.createServer(function(req, res){
    res.writeHead(200, {
        'Content-Type': 'text/plain'；
        charset=UTF-8
        //当中文参数出现乱码的时候可以指定编码格式
    });
 
    // 解析 url 参数
    var params = url.parse(req.url, true).query;
    res.write("网站名：" + params.name);
    res.write("\n");
    res.write("网站 URL：" + params.url);
    res.end();
 
}).listen(3000);

```

### 获取POSt请求内容

POST 请求的内容全部的都在请求体中，http.ServerRequest 并没有一个属性内容为请求体，原因是等待请求体传输可能是一件耗时的工作。

比如上传文件，而很多时候我们可能并不需要理会请求体的内容，恶意的POST请求会大大消耗服务器的资源，所以 node.js 默认是不会解析请求体的，当你需要的时候，需要手动来做。

以下是基本语法的结构说明：

```javascript

var http = require('http');
var querystring = require('querystring');
var util = require('util');
 
http.createServer(function(req, res){
    // 定义了一个post变量，用于暂存请求体的信息
    var post = '';     
 
    // 通过req的data事件监听函数，每当接受到请求体的数据，就累加到post变量中
    req.on('data', function(chunk){    
        post += chunk;
    });
 
    // 在end事件触发后，通过querystring.parse将post解析为真正的POST请求格式，然后向客户端返回。
    req.on('end', function(){    
        post = querystring.parse(post);
        res.end(util.inspect(post));
    });
}).listen(3000);

```



以下实例表单通过 POST 提交并输出数据：

```javascript

var http = require('http');
var querystring = require('querystring');
 
var postHTML = 
  '<html><head><meta charset="utf-8"><title>菜鸟教程 Node.js 实例</title></head>' +
  '<body>' +
  '<form method="post">' +
  '网站名： <input name="name"><br>' +
  '网站 URL： <input name="url"><br>' +
  '<input type="submit">' +
  '</form>' +
  '</body></html>';
 
http.createServer(function (req, res) {
  var body = "";
   
  //定义data事件，当有数据可读时触发
  req.on('data', function (chunk) {
    body += chunk;
  });
    
  //定义end事件，当没有更多的数据可读时触发
  req.on('end', function () {
    // 解析参数
    body = querystring.parse(body);
    // 设置响应头部信息及编码
    res.writeHead(200, {'Content-Type': 'text/html; charset=utf8'});
 
    if(body.name && body.url) { // 输出提交的数据
        res.write("网站名：" + body.name);
        res.write("<br>");
        res.write("网站 URL：" + body.url);
    } else {  // 输出表单
        res.write(postHTML);
    }
    res.end();
  });
}).listen(3000);

```

结果就拿菜鸟的图做展示：

![alt](https://www.runoob.com/wp-content/uploads/2014/06/nodepost.gif)



