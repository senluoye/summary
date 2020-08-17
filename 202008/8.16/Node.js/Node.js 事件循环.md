# Node.js 事件循环

### 前言

>Node.js 是单进程单线程应用程序，但是因为 V8 引擎提供的异步执行回调接口，通过这些接口可以处理大量的并发，所以性能非常高。
>
>Node.js 几乎每一个 API 都是支持回调函数的。
>
>Node.js 基本上所有的事件机制都是用设计模式中**观察者模式**实现。
>
>Node.js 单线程类似进入一个while(true)的事件循环，直到没有事件观察者退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数.

### 关于事件驱动程序

首先我们先了解一下Node.js的事件驱动模型，当webserver接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。当这个请求完成，它会被放到处理队列，当到达队列开头，这个结果被返会给用户。

> 这个模型非常高效可扩展性非常强，因为 webserver 一直接受请求但却不等待任何读写操作。（这也称之为非阻塞式IO或者事件驱动IO）

在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。下面是实现流程：

![alt](https://www.runoob.com/wp-content/uploads/2015/09/event_loop.jpg)

我们可以看到，这个流程有点类似于观察者模式。

> 事件相当于一个主题(Subject)，而所有注册到这个事件上的处理函数相当于观察者(Observer)。

### 关于事件

Node.js有多个内置的事件，我们可以引用events模块，并通过实例化EventEmitter 类来绑定和监听事件。

代码如下：

```javascript
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
```

以下程序绑定事件处理程序：

```javascript
//eventEmitter.on是绑定处理事件的处理器（事件处理）
eventEmitter.on('eventName', eventHandler);
//其实就相当于给函数弄一个事件名称
```

我们也可以通过程序触发事件：

```javascript
// eventEmitter.emit触发事件（事件请求）
eventEmitter.emit('eventName');
```

>事件的请求和处理是分开的，所以是异步
>
>两者关系案例（https://www.runoob.com/note/23603）

### 实例

下面来一个具体的实例。

先创建一个test.js文件，代码如下：

```javascript
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
 
// 创建事件处理程序
var connectHandler = function connected() {
   console.log('连接成功。');
  
   // 触发 data_received 事件 
   eventEmitter.emit('data_received');
}
 
// 绑定 connection 事件处理程序
eventEmitter.on('connection', connectHandler);
 
// 使用匿名函数绑定 data_received 事件
eventEmitter.on('data_received', function(){
   console.log('数据接收成功。');
});
 
// 触发 connection 事件 
eventEmitter.emit('connection');
 
console.log("程序执行完毕。");
```

让我们来看看程序结果：

```
连接成功。
数据接收成功。
程序执行完毕。
```

### Node 应用程序是如何工作的

在 Node 应用程序中，执行异步操作的函数将**回调函数**作为**最后一个参数**， **回调函数**接收**错误对象**作为**第一个参数**。

接下来整一个实例。

先创建一个input.txt文件，内容随意：

```
这是input文件
```

再创建一个main.js文件，代码如下：

```javascript
var fs = require("fs");

fs.readFile('input.txt', function (err, data) {
    //回调函数传入两个参数，分别为误和文件数据
   if (err){
      console.log(err.stack);//如果读取过程中发生错误，那么输出错误信息,比如没有input文件的时候会报错
      return;
   }
   console.log(data.toString());
});
console.log("程序执行完毕");
```

以下是输出结果：

```
程序执行完毕
这是input文件内的内容
```

用的函数是异步函数。而我们也可以根据结果看出这是异步执行的。