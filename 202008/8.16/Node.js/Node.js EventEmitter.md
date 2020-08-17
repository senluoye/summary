# Node.js EventEmitter

### 前言

Node.js 所有的异步 I/O 操作在完成时都会发送一个事件到事件队列。

> 关于node.js异步I/O的理解：
>
> https://www.cnblogs.com/liuchuanfeng/p/6703993.html
>
> 关于几种I/O模型的理解
>
> https://blog.csdn.net/ZWE7616175/article/details/80591587

Node.js 里面的许多对象都会分发事件：

比如一个 net.Server 对象会在每次有新连接时触发一个事件， 一个 fs.readStream  对象会在文件被打开的时候触发一个事件。 所有这些产生事件的对象都是 events.EventEmitter 的实例。 

### EventEmitter 类

events 模块只提供了一个对象： events.EventEmitter。

EventEmitter 的核心就是事件触发与事件监听器功能的封装。

比如，你可以通过require("events");来访问该模块：

```javascript
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
```

这是之前提到过的。

### 关于事件

> EventEmitter 对象如果在实例化时发生错误，会触发 error 事件。
>
> 当添加新的监听器时，newListener 事件会触发。
>
> 当监听器被移除时，removeListener 事件被触发。



整一个简单的例子说明EventEmitter的用法。

创建一个event.js文件，代码如下：

```javascript
var EventEmitter = require('events').EventEmitter; 
var event = new EventEmitter();
//这两步跟上面的引入再创建EventEmitter是一样的

event.on('some_event', function() { 
    console.log('some_event 事件触发'); 
}); 
setTimeout(function() { 
    event.emit('some_event'); 
}, 1000); 
```

结果我们都知道，一秒钟后输出 **'some-event 事件触发'**

> 原理解释：
>
> event 对象注册了事件 some_event 的一个监听器，然后我们通过 setTimeout 在 1000 毫秒以后向 event 对象发送事件 some_event，此时会调用some_event 的监听器



### 事件触发

当事件触发时，注册到这个事件的全部事件监听器被依次调用，事件参数会被作为回调函数的参数也传递进去。

整一个例子解释：

```javascript
//某一个js文件内
var events = require('events'); 
var emitter = new events.EventEmitter(); 
emitter.on('someEvent', function(arg1, arg2) { 
    console.log('listener1', arg1, arg2); 
}); 
emitter.on('someEvent', function(arg1, arg2) { 
    console.log('listener2', arg1, arg2); 
}); 
emitter.emit('someEvent', 'arg1 参数', 'arg2 参数'); 
```

结果如下：

```javascript
listener1 arg1 参数 arg2 参数
listener2 arg1 参数 arg2 参数
```

以上是EventEmitter最简单的用法。

### EventEmitter属性介绍

EventEmitter 提供了多个属性，如 **on**  和 **emit**。**on** 函数用于绑定事件函数，**emit** 属性用于触发一个事件等等。

#### 方法

listener代表回调函数。

> | 1    | **addListener(event, listener)** <br> 为指定事件添加一个监听器到监听器数组的尾部。 |
> | ---- | :----------------------------------------------------------- |
> | 2    | **on(event, listener)**<br>为指定事件注册一个监听器，接受一个字符串 event 和一个回调函数。(好像跟上面那个没啥区别) |
> | 3    | **once(event, listener)**<br/>为指定事件注册一个单次监听器，即 监听器最多只会触发一次，触发后立刻解除该监听器。 |
> | 4    | **removeListener(event, listener)**<br>移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。<br>它接受两个参数，第一个是事件名称，第二个是回调函数名称。 |
> | 5    | **removeAllListeners([event])**<br>移除所有事件的所有监听器， 如果指定事件，则移除指定事件的所有监听器。 |
> | 6    | **setMaxListeners(n)**<br/>默认情况下， EventEmitters 如果你添加的监听器超过 10 个就会输出警告信息。 setMaxListeners 函数用于提高监听器的默认限制的数量。 |
> | 7    | **listeners(event)**<br/>返回指定事件的监听器数组。          |
> | 8    | **emit(event, [arg1], [arg2], [...])**<br/>按监听器的顺序执行执行每个监听器，如果事件有注册监听返回 true，否则返回 false。 |

#### 类方法

| 1    | **listenerCount(emitter, event)** <br>返回指定事件的监听器数量。 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

#### 事件

| 1    | **newListener**  <br> - **event** - 字符串<br> - 事件名称 **listener** - 处理事件函数 <br>该事件在添加新监听器时被触发。 |
| ---- | ------------------------------------------------------------ |
| 2    | **removeListener**  <br> - **event** - 字符串，事件名称<br> - **listener** - 处理事件函数 <br>从指定监听器数组中删除一个监听器。需要注意的是，此操作将会改变处于被删监听器后面的那些监听器的索引。 |

#### 实例

以下实例通过自定义的 connection（连接）事件演示了 EventEmitter 类的应用。

创建 main.js 文件，代码如下：

```javascript
var events = require('events');
var eventEmitter = new events.EventEmitter();

// 监听器 #1
var listener1 = function listener1() {
   console.log('监听器 listener1 执行。');
}

// 监听器 #2
var listener2 = function listener2() {
  console.log('监听器 listener2 执行。');
}

// 绑定 connection 事件，处理函数为 listener1 
eventEmitter.addListener('connection', listener1);

// 绑定 connection 事件，处理函数为 listener2
eventEmitter.on('connection', listener2);

//获取此时监听器的个数
var eventListeners = eventEmitter.listenerCount('connection');
console.log(eventListeners + " 个监听器监听连接事件。");

// 处理 connection 事件 
eventEmitter.emit('connection');

// 移除监绑定的 listener1 函数
eventEmitter.removeListener('connection', listener1);
console.log("listener1 不再受监听。");

// 触发连接事件
eventEmitter.emit('connection');

//再次获取监听器个数
eventListeners = eventEmitter.listenerCount('connection');
console.log(eventListeners + " 个监听器监听连接事件。");

console.log("程序执行完毕。");
```

让我们来看一下结果：

```
2 个监听器监听连接事件。
监听器 listener1 执行。
监听器 listener2 执行。
listener1 不再受监听。
监听器 listener2 执行。
1 个监听器监听连接事件。
程序执行完毕。
```

很好，符合预期。

### error事件

EventEmitter除了定义以上的事件之外，还定义了一个特殊的事件——error事件，这个事件包含了错误的语义，并且通常在我们遇到异常的时候触发。

> tips：
>
> 如果error某个时刻被触发，但是此时没有响应的监听器，那么Node.js会把它当做异常，退出程序并输出错误信息

你可以触发一下error事件：

```javascript
var events = require('events'); 
var emitter = new events.EventEmitter(); 
emitter.emit('error'); 
```

结果是这样的：

```javascript
events.js:306
    throw err; // Unhandled 'error' event
    ^

Error [ERR_UNHANDLED_ERROR]: Unhandled error. (undefined)
    at EventEmitter.emit (events.js:304:17)
m
    at Object.<anonymous> (F:\Web\我的学习\node.js学
习\error.js:3:9)
    at Module._compile (internal/modules/cjs/lo
ader.js:1138:30)
    at Object.Module._extensions..js (internal/
modules/cjs/loader.js:1158:10)
    at Module.load (internal/modules/cjs/loader
.js:986:32)
    at Function.Module._load (internal/modules/
cjs/loader.js:879:14)
    at Function.executeUserEntryPoint [as runMa
in] (internal/modules/run_main.js:71:12)       
    at internal/main/run_main_module.js:17:47
 {
  code: 'ERR_UNHANDLED_ERROR',
  context: undefined
}
```

### 继承EventEmitter

大多数时候我们不会直接使用 EventEmitter，而是在对象中继承它。包括 fs、net、 http 在内的，只要是支持事件响应的核心模块都是 EventEmitter 的子类。

这样做的原因有两点：

1.首先，具有某个实体功能的对象实现事件符合语义， 事件的监听和发生应该是一个对象的方法。

2.JavaScript 的对象机制是基于原型的，支持部分多重继承，继承 EventEmitter 不会打乱对象原有的继承关系。

