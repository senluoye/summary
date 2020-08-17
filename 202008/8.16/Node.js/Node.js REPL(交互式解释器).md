# Node.js REPL(交互式解释器)

### 前言

Node.js REPL(Read Eval Print Loop:交互式解释器)  表示一个电脑的环境，类似 Window 系统的终端，我们可以在终端中输入命令，并接收系统的响应。

关于Node自带的交互式解释器，可以执行以下任务：

- **读取** - 读取用户输入，解析输入了Javascript 数据结构并存储在内存中。
- **执行** - 执行输入的数据结构
- **打印** - 输出结果
- **循环** - 循环操作以上步骤直到用户两次按下 **ctrl-c** 按钮退出。

启动Node终端只需要在终端输入node：

```javascript
node
```

### 关于运算

#### 简单的表达式运算

我们可以在 Node.js REPL 的命令行窗口中执行简单的数学运算：

```javascript
$ node
> 1 + 4
5
> 5 / 2
2.5
> 3 * 6
18
> 4 - 1
3
> 1 + ( 2 * 3 ) - 4
3
>
```

#### 使用变量

我们可以在终端中使用变量。

```javascript
$ node
> x = 10
10
> var y = 10
undefined
> x + y
20
> console.log("Hello World")
Hello World
undefined
> console.log(x)
10
undefined
```

可以看到，这其实跟浏览器的控制台差不多。

#### 多行表达式

Node REPL 支持输入多行表达式，这就有点类似 JavaScript。

```javascript
$ node
> var x = 0
undefined
> do {
... x++;                      	//那三个点是系统自动生成的
... console.log("x: " + x);
... } while ( x < 5 );
x: 1
x: 2
x: 3
x: 4
x: 5
undefined
>
```

#### 获取上一个表达式的结果

Node REPL允许你用下划线（ _ ）获取上一个表达式的结果

```javascript
$ node
> var x = 10
undefined
> var y = 20
undefined
> x + y
30
> var sum = _
undefined
> console.log(sum)
30
undefined
>
```

### 一些常用命令

- **ctrl + c** - 退出当前终端（终止批处理）。
- **ctrl + c 按下两次** - 退出 Node REPL。
- **ctrl + d** - 直接退出 Node REPL.
- **向上/向下 键** - 查看输入的历史命令
- **tab 键** - 列出当前命令
- **.help** - 列出使用命令
- **.break** - 退出多行表达式
- **.clear** - 退出多行表达式
- **.save \*filename\*** - 保存当前的 Node REPL 会话到指定文件
- **.load \*filename\*** - 载入当前 Node REPL 会话的文件内容。

