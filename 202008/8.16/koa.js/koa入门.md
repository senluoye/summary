# koa入门

### 介绍

Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API 开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa 帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。

### 安装（导入）koa.js

注意，vue项目的node模块中并没有安装koa.js，所以我们需要另外用命令本地安装。

##### 方法一：

```
$ npm i koa
```

##### 方法2：

在项目目录下创建一个`package.json`，这个文件描述了我们的`hello-koa`工程会用到哪些包。完整的文件内容如下：

```
{
    "name": "hello-koa2",
    "version": "1.0.0",
    "description": "Hello Koa 2 example with async",
    "main": "app.js",
    "scripts": {
        "start": "node app.js"
    },
    "keywords": [
        "koa",
        "async"
    ],
    "author": "Michael Liao",
    "license": "Apache-2.0",
    "repository": {
        "type": "git",
        "url": "https://github.com/michaelliao/learn-javascript.git"
    },
    "dependencies": {
        "koa": "2.0.0"
    }
}
```

>`dependencies`描述了我们的工程依赖的包以及版本号。其他字段均用来描述项目信息，可任意填写。

然后，我们在项目目录下执行`npm install`就可以把所需包以及依赖包一次性全部装好：

```
npm install
```



### 第一个应用程序

 首先我们创建一个 app.js：

```javascript
// 导入koa，和koa 1.x不同，在koa2中，我们导入的是一个class，因此用大写的Koa表示:
const Koa = require('koa');

// 创建一个Koa对象表示web app本身:
const app = new Koa();

// 对于任何请求，app将调用该异步函数处理请求：
app.use(async (ctx, next) => {
    await next();
    // 设置response的Content-Type:
    ctx.response.type = 'text/html';
    // 设置response的内容:
    ctx.response.body = '<h1>Hello, koa2!</h1>';
}
});

// 在端口3000监听:
app.listen(3000);
console.log('app started at port 3000...');
```

运行可以看到结果：

```
Server running at http://127.0.0.1:3000/
```

> 关于上面的案例：
>
> 首先是异步函数那里，对于每一个http请求，koa将调用我们传入的异步函数来处理。其中，参数ctx是由koa传入的封装了request和response的变量，我们可以通过它访问request和response。next是koa传入的将要处理的下一个异步函数。

### koa middleware（中间件）

让我们再仔细看看koa的执行逻辑。核心代码是：

```javascript
app.use(async (ctx, next) => {
    await next();
    ctx.response.type = 'text/html';
    ctx.response.body = '<h1>Hello, koa2!</h1>';
});
```

每收到一个http请求，koa就会调用通过`app.use()`注册的async函数，并传入`ctx`和`next`参数。

我们可以对`ctx`操作，并设置返回内容。但是为什么要调用`await next()`？

原因是koa把很多async函数组成一个处理链，每个async函数都可以做一些自己的事情，然后用`await next()`来调用下一个async函数。我们把每个async函数称为middleware，这些middleware可以组合起来，完成很多有用的功能。

例如，可以用以下3个middleware组成处理链，依次打印日志，记录处理时间，输出HTML：

```javascript
app.use(async (ctx, next) => {
    console.log(`${ctx.request.method} ${ctx.request.url}`); // 打印URL
    await next(); // 调用下一个middleware
});

app.use(async (ctx, next) => {
    const start = new Date().getTime(); // 当前时间
    await next(); // 调用下一个middleware
    const ms = new Date().getTime() - start; // 耗费时间
    console.log(`Time: ${ms}ms`); // 打印耗费时间
});

app.use(async (ctx, next) => {
    await next();
    ctx.response.type = 'text/html';
    ctx.response.body = '<h1>Hello, koa2!</h1>';
});
```

> ${表达式}表示输出或者计算给定表达式的内容

middleware的顺序很重要，也就是调用`app.use()`的顺序决定了middleware的顺序。

此外，如果一个middleware没有调用`await next()`，会怎么办？答案是后续的middleware将不再执行了。这种情况也很常见，例如，一个检测用户权限的middleware可以决定是否继续处理请求，还是直接返回403错误：

```
app.use(async (ctx, next) => {
    if (await checkUserPermission(ctx)) {
        await next();
    } else {
        ctx.response.status = 403;
    }
});
```

理解了middleware，我们就已经会用koa了！

> 最后注意`ctx`对象有一些简写的方法，例如`ctx.url`相当于`ctx.request.url`，`ctx.type`相当于`ctx.response.type`。

### 整一个案例

接下来是一个运用await next()的案例：

```js
const Koa = require('koa');
const app = new Koa();

// logger

app.use(async (ctx, next) => {
  await next();
  const rt = ctx.response.get('X-Response-Time');
    //获取响应头字段值
  console.log(`${ctx.method} ${ctx.url} - ${rt}`);
});

// x-response-time

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
  // 设置响应头 field - value
});

// response

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```

看看终端打印的内容

```js
GET / - 1ms
GET /favicon.ico - 0ms
GET / - 0ms
GET /favicon.ico - 0ms
GET / - 0ms
GET /favicon.ico - 0ms
```

上面以 “Hello World” 的响应作为示例，当请求开始时首先请求流通过 `x-response-time` 和 `logging` 中间件，然后继续移交控制给 `response` 中间件。

官方文档告诉我们，当一个中间件调用 `next()` 则该函数暂停并将控制传递给定义的下一个中间件。当在下游没有更多的中间件执行后，堆栈将展开并且每个中间件恢复执行其上游行为。

准确的来说，当碰到await时，整个函数停止，同时先执行await后面的''代码''，直到有某个函数不再设置await，同时往回一层层执行之前的函数。

