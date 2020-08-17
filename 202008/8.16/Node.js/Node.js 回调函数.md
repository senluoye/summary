# Node.js 回调函数

### 前言

Node.js 异步编程的直接体现就是回调。

异步编程依托于回调来实现，但不能说使用了回调后程序就异步化了。

回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。

例如，我们可以一边读取文件，一边执行其他命令，在文件读取完成后，我们将文件内容作为回调函数的参数返回。这样在执行代码时就没有阻塞或等待文件 I/O 操作。这就大大提高了 Node.js 的性能，可以处理大量的并发请求。

回调函数一般作为函数的最后一个参数出现： 

```javascript
function foo1(name, age, callback) { }
function foo2(value, callback1, callback2) { }
```

### 案例

我们来看一个案例，分析阻塞和非阻塞的差别。

#### 阻塞代码实例

先创建一个input.txt，内容随意：

```
vbvbseivbe
```

然后创建一个main.js文件，代码如下：

```javascript
var fs = require("fs");
//fs 模块提供了用于与文件系统进行交互（以类似于标准 POSIX 函数的方式）的 API

var data = fs.readFileSync('input.txt')
//获取文件内容的函数

console.log(data.toString())
console.log('程序结束！')
```

可以看到结果：

```javascript
vbvbseivbe
程序执行结束!
```



>tips：读取文件内容
>
>`同步版本：fs.readFileSync(path[, options])` 
>
>接收两个参数，第一个是文件名/文件描述符，第二个表示文件的字符编码。
>
>异步版本：`fs.readFile(path[, options], callback)`
>
>多了一个对调函数
>
>详细说明请咨询nodeapi文档



#### 非阻塞代码实例

同样，开局创建一个input.txt，内容随意

```
asgciagsviubwa
```

接着创建main.js文件，代码如下：

```javascript
var fs = require("fs");

fs.readFile('input.txt', function (err, data) {
    //这里运用了阻塞案例中函数的异步版本
    if (err) return console.error(err);
    console.log(data.toString());
});

console.log("程序执行结束!");
```

然后来看看这次的结果：

```javascript
程序结束！
bvwbrberberbberb
```

首先可以很清晰的看到，上下的输出顺序不一样，阻塞案例文件内容输出在前，结束信号输出在后，很显然是同步执行代码。

而非阻塞案例是程序结束信号输出之后才输出问价内容，这是异步执行。

这两个执行步骤的区别，就是第一个实例在文件读取完后才执行程序。 第二个实例我们不需要等待文件读取完，这样就可以在读取文件时同时执行接下来的代码，大大提高了程序的性能。

>阻塞是按顺序执行的，非阻塞是不按顺序执行的。
>
>所以如果我们需要处理回调函数的参数，那么我们需要将这个处理写在回调函数内。