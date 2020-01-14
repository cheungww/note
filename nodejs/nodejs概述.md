# Nodejs概述

## 简介

Node是JavaScript语言的服务器运行环境。

所谓“运行环境”有两层意思：首先，JavaScript语言通过Node在服务器运行，在这个意义上，Node有点像JavaScript虚拟机；其次，Node提供大量工具库，使得JavaScript语言与操作系统互动（比如读写文件、新建子进程），在这个意义上，Node又是JavaScript的工具库。

Node内部采用Google公司的V8引擎，作为JavaScript语言解释器；通过自行开发的libuv库，调用操作系统资源。

### 基本用法

安装完成后，运行node.js程序，就是使用node命令读取JavaScript脚本。

当前目录的`demo.js`脚本文件，可以这样执行。

```bash
$ node demo
# 或者
$ node demo.js
```

使用`-e`参数，可以执行代码字符串。

```bash
$ node -e 'console.log("Hello World")'
Hello World
```

### REPL环境(Read Eval Print Loop:交互式解释器)

在命令行键入node命令，后面没有文件名，就进入一个Node.js的REPL环境（Read–eval–print loop，”读取-求值-输出”循环），可以直接运行各种JavaScript命令。

```bash
$ node
> 1+1
2
>
```

如果使用参数 –use_strict，则REPL将在严格模式下运行。

```
$ node --use_strict
```

REPL是Node.js与用户互动的shell，各种基本的shell功能都可以在里面使用，比如使用上下方向键遍历曾经使用过的命令。

特殊变量下划线（_）表示上一个命令的返回结果。

```shell
> 1 + 1
2
> _ + 1
3
```

在REPL中，如果运行一个表达式，会直接在命令行返回结果。如果运行一条语句，就不会有任何输出，因为语句没有返回值。

```shell
> x = 1
1
> var x = 1
```

上面代码的第二条命令，没有显示任何结果。因为这是一条语句，不是表达式，所以没有返回值。



### 异步操作

Node采用V8引擎处理JavaScript脚本，最大特点就是单线程运行，一次只能运行一个任务。这导致Node大量采用异步操作（asynchronous operation），即任务不是马上执行，而是插在任务队列的尾部，等到前面的任务运行完后再执行。

由于这种特性，某一个任务的后续操作，往往采用回调函数（callback）的形式进行定义。

```js
var isTrue = function(value, callback) {
  if (value === true) {
    callback(null, "Value was true.");
  }
  else {
    callback(new Error("Value is not true!"));
  }
}
```

上面代码就把进一步的处理，交给回调函数callback。

Node约定，如果某个函数需要回调函数作为参数，则回调函数是最后一个参数。另外，回调函数本身的第一个参数，约定为上一步传入的错误对象。

```js
var callback = function (error, value) {
  if (error) {
    return console.log(error);
  }
  console.log(value);
}
```

上面代码中，callback的第一个参数是Error对象，第二个参数才是真正的数据参数。这是因为回调函数主要用于异步操作，当回调函数运行时，前期的操作早结束了，错误的执行栈早就不存在了，传统的错误捕捉机制try…catch对于异步操作行不通，所以只能把错误交给回调函数处理。

```
try {
  db.User.get(userId, function(err, user) {
    if(err) {
      throw err
    }
    // ...
  })
} catch(e) {
  console.log(‘Oh no!’);
}
```

上面代码中，db.User.get方法是一个异步操作，等到抛出错误时，可能它所在的try…catch代码块早就运行结束了，这会导致错误无法被捕捉。所以，Node统一规定，一旦异步操作发生错误，就把错误对象传递到回调函数。

如果没有发生错误，回调函数的第一个参数就传入null。这种写法有一个很大的好处，就是说只要判断回调函数的第一个参数，就知道有没有出错，如果不是null，就肯定出错了。另外，这样还可以层层传递错误。

```js
if(err) {
  // 除了放过No Permission错误意外，其他错误传给下一个回调函数
  if(!err.noPermission) {
    return next(err);
  }
}
```



**有时候异步任务太多会出现回调地狱的现象:**

![timg](node.js概述.assets/callback_hell.jpeg)

所以通常会采用Promise来处理异步任务:

```js
var fs = require('fs')

function pReadFile(filePath) {
  return new Promise(function (resolve, reject) {
    fs.readFile(filePath, 'utf8', function (err, data) {
      if (err) {
        reject(err)
      } else {
        resolve(data)
      }
    })
  })
}

let p1 = pReadFile('./data/a.txt');
let p2 = pReadFile('./data/b.txt');
let p3 = pReadFile('./data/c.txt');

p1
  .then(function (data) {
    console.log(data)
    return p2;
  })
  .then(function (data) {
    console.log(data)
    return p3;
  })
  .then(function (data) {
    console.log(data)
  })
// 当 p1 读取成功的时候
// 当前函数中 return 的结果就可以在后面的 then 中 function 接收到
// 当你 return 123 后面就接收到 123
//      return 'hello' 后面就接收到 'hello'
//      没有 return 后面收到的就是 undefined
// 上面那些 return 的数据没什么卵用
// 真正有用的是：我们可以 return 一个 Promise 对象
// 当 return 一个 Promise 对象的时候，后续的 then 中的 方法的第一个参数会作为 p2 的 resolve
```

***



### 全局对象和全局变量

Node提供以下几个全局对象，它们是所有模块都可以调用的。

- **global**：表示Node所在的全局环境，类似于浏览器的window对象。需要注意的是，如果在浏览器中声明一个全局变量，实际上是声明了一个全局对象的属性，比如`var x = 1`等同于设置`window.x = 1`，但是Node不是这样，至少在模块中不是这样（REPL环境的行为与浏览器一致）。在模块文件中，声明`var x = 1`，该变量不是`global`对象的属性，`global.x`等于undefined。这是因为模块的全局变量都是该模块私有的，其他模块无法取到。
- **process**：该对象表示Node所处的当前进程，允许开发者与该进程互动。
- **console**：指向Node内置的console模块，提供命令行环境中的标准输入、标准输出功能。

Node还提供一些全局函数。

- **setTimeout()**：用于在指定毫秒之后，运行回调函数。实际的调用间隔，还取决于系统因素。间隔的毫秒数在1毫秒到2,147,483,647毫秒（约24.8天）之间。如果超过这个范围，会被自动改为1毫秒。**在浏览器环境下该方法返回一个整数，代表这个新建定时器的编号; 在node环境下, 该方法返回一个对象.**

  ```bash
  // 在浏览器的环境中
  let a = setTimeout(function(){}, 1)
  a // 1005
  typeof a // "number"
  ```
  
  ```bash
  // 在node环境中
iMac:bin web$ node
  > let a = setTimeout(function(){}, 1)
  undefined
  > a
  Timeout {
    _called: true,
    _idleTimeout: 1,
    _idlePrev: null,
    _idleNext: null,
    _idleStart: 22676,
    _onTimeout: [Function],
    _timerArgs: undefined,
    _repeat: null,
    _destroyed: true,
    domain: [Domain],
    [Symbol(unrefed)]: false,
    [Symbol(asyncId)]: 187,
    [Symbol(triggerId)]: 5 }
  > typeof a
  'object'
  ```
  
- **clearTimeout()**：用于终止一个setTimeout方法新建的定时器。

- **setInterval()**：用于每隔一定毫秒调用回调函数。由于系统因素，可能无法保证每次调用之间正好间隔指定的毫秒数，但只会多于这个间隔，而不会少于它。指定的毫秒数必须是1到2,147,483,647（大约24.8天）之间的整数，如果超过这个范围，会被自动改为1毫秒。**该方法在浏览器环境下返回一个整数，代表这个新建定时器的编号, 在node环境下, 该方法返回一个对象。**

- **clearInterval()**：终止一个用setInterval方法新建的定时器。

- **require()**：用于加载模块。

- **Buffer()**：用于操作二进制数据。

Node提供两个全局变量，都以两个下划线开头。

- `__filename`：指向当前运行的脚本文件名。
- `__dirname`：指向当前运行的脚本所在的目录。

除此之外，还有一些对象实际上是模块内部的局部变量，指向的对象根据模块不同而不同，但是所有模块都适用，可以看作是伪全局变量，主要为module, module.exports, exports等。





## NPM(Node Package Manager)

• CommonJS包规范是理论，NPM是其中一种实践。
• 对于Node而言，NPM帮助其完成了第三方模块的发布、安装和依赖等。借助NPM，Node与第三方模块之间形成了很好的一个生态系统。

### NPM命令

```shell
# 查看版本
npm –v

# 帮助说明
npm

# 搜索模块包
npm search 包名

# 在当前目录安装包
npm install 包名

# 全局模式安装包
npm install 包名 –g

# 删除一个模块
npm remove 包名

# 从本地安装
npm install 文件路径

# 从镜像源安装
npm install 包名 –registry=地址

# 设置镜像源
npm config set registry 地址
```



## 模块化结构

### 概述

Node.js采用模块化结构，按照[CommonJS规范](http://wiki.commonjs.org/wiki/CommonJS)定义和使用模块。模块与文件是一一对应关系，即加载一个模块，实际上就是加载对应的一个模块文件。

require命令用于指定加载模块，加载时可以省略脚本文件的后缀名。

```js
var circle = require('./circle.js');
// 或者
var circle = require('./circle');
```

require方法的参数是模块文件的名字。它分成两种情况，第一种情况是参数中含有文件路径（比如上例），这时路径是相对于当前脚本所在的目录，第二种情况是参数中不含有文件路径，这时Node到模块的安装目录，去寻找已安装的模块（比如下例）。

```
var bar = require('bar');
```

有时候，一个模块本身就是一个目录，目录中包含多个文件。这时候，Node在package.json文件中，寻找main属性所指明的模块入口文件。

```json
{
  "name" : "bar",
  "main" : "./lib/bar.js"
}
```

上面代码中，模块的启动文件为lib子目录下的bar.js。当使用`require('bar')`命令加载该模块时，实际上加载的是`./node_modules/bar/lib/bar.js`文件。下面写法会起到同样效果。

```
var bar = require('bar/lib/bar.js')
```

如果模块目录中没有package.json文件，node.js会尝试在模块目录中寻找index.js或index.node文件进行加载。

模块一旦被加载以后，就会被系统缓存。如果第二次还加载该模块，则会返回缓存中的版本，这意味着模块实际上只会执行一次。如果希望模块执行多次，则可以让模块返回一个函数，然后多次调用该函数

***

**路径形式的模块**:

```txt
./   当前目录,不可省略
../  上一级目录, 不可省略
/xxx 几乎不用. 首位的 / 在这里表示是当前文件模块所属磁盘根路径
d:/a/foo.js 几乎不用; .js 后缀名可以省略
```

**核心模块**的本质也是文件, 核心模块文件已经被编译到了二进制文件中了，我们只需要按照名字来加载就可以了eq: `require('fs')`、`require('http')`.

**第三方模块**: 凡是第三方模块都必须通过 npm 来下载, 使用的时候就可以通过 require('包名') 的方式来进行加载才可以使用, 不可能有任何一个第三方包和核心模块的名字是一样的

既不是核心模块、也不是路径形式的模块, 查找规则如下:

```txt
以 let tmp = require('art-template') 为例：
先找到当前文件所处目录中的 node_modules 目录
node_modules/art-template
node_modules/art-template/package.json 文件
node_modules/art-template/package.json 文件中的 main 属性
main 属性中就记录了 art-template 的入口模块
然后加载使用这个第三方包
实际上最终加载的还是文件

如果 package.json 文件不存在或者 main 指定的入口模块是也没有
则 node 会自动找该目录下的 index.js
也就是说 index.js 会作为一个默认备选项

如果以上所有任何一个条件都不成立，则会进入上一级目录中的 node_modules 目录查找
如果上一级还没有，则继续往上上一级查找
。。。
如果直到当前磁盘根目录还找不到，最后报错：
can not find module xxx
var template = require('art-template')
```

***注意*** : 我们一个项目有且只有一个 node_modules，放在项目根目录中，这样的话项目中所有的子目录中的代码都可以加载到第三方包, 不会出现有多个 node_modules

我们一个项目有且只有一个 node_modules，放在项目根目录中，这样的话项目中所有的子目录中的代码都可以加载到第三方包, 不会出现有多个 node_modules

```txt
模块查找机制

   优先从缓存加载

   核心模块

   路径形式的文件模块

   第三方模块

   node_modules/art-template/

   node_modules/art-template/package.json

   node_modules/art-template/package.json main

   index.js 备选项

   进入上一级目录找 node_modules

   按照这个规则依次往上找，直到磁盘根目录还找不到，最后报错：Can not find moudle xxx

   一个项目有且仅有一个 node_modules 而且是存放到项目的根目录
```

***

### 核心模块

如果只是在服务器运行JavaScript代码，用处并不大，因为服务器脚本语言已经有很多种了。Node.js的用处在于，它本身还提供了一系列功能模块，与操作系统互动。这些核心的功能模块，不用安装就可以使用，下面是它们的清单。

- **http**：提供HTTP服务器功能。
- **url**：解析URL。
- **fs**：与文件系统交互。
- **querystring**：解析URL的查询字符串。
- **child_process**：新建子进程。
- **util**：提供一系列实用小工具。
- **path**：处理文件路径。
- **crypto**：提供加密和解密功能，基本上是对OpenSSL的包装。
- **os**: 提供与操作系统相关的实用方法.

上面这些核心模块，源码都在Node的lib子目录中。为了提高运行速度，它们安装时都会被编译成二进制文件。

核心模块总是最优先加载的。如果你自己写了一个HTTP模块，`require('http')`加载的还是核心模块。

### 自定义模块

Node模块采用CommonJS规范。只要符合这个规范，就可以自定义模块。

下面是一个最简单的模块，假定新建一个foo.js文件，写入以下内容。

```js
// foo.js

module.exports = function(x) {
    console.log(x);
};
```

上面代码就是一个模块，它通过module.exports变量，对外输出一个方法。

这个模块的使用方法如下。

```js
// index.js

var m = require('./foo');

m("这是自定义模块");
```

上面代码通过require命令加载模块文件foo.js（后缀名省略），将模块的对外接口输出到变量m，然后调用m。这时，在命令行下运行index.js，屏幕上就会输出“这是自定义模块”。

```
$ node index
这是自定义模块
```

module变量是整个模块文件的顶层变量，它的exports属性就是模块向外输出的接口。如果直接输出一个函数（就像上面的foo.js），那么调用模块就是调用一个函数。但是，模块也可以输出一个对象。下面对foo.js进行改写。

```js
// foo.js

var out = new Object();

function p(string) {
  console.log(string);
}

out.print = p;

module.exports = out;
```

上面的代码表示模块输出out对象，该对象有一个print属性，指向一个函数。下面是这个模块的使用方法。

```js
// index.js

var m = require('./foo');

m.print("这是自定义模块");
```

上面代码表示，由于具体的方法定义在模块的print属性上，所以必须显式调用print属性。





## Buffer(缓冲区)

• 从结构上看Buffer非常像一个数组，它的元素为16进制的两位数。

• 数组中不能存储二进制的文件，而 buffer 就是专门用来存储二进制数据

• 实际上一个元素就表示内存中的一个字节。
• 实际上Buffer中的内存不是通过JavaScript分配的，而是在底层通过C++申请的。
• 也就是我们可以直接通过Buffer来创建内存中的空间。

### Buffer的操作

• 使用Buffer保存字符串

```js
let str = "你好 atguigu";
let buf = Buffer.from(str , "utf-8");
```



• 创建指定大小的Buffer对象

```js
let buf3 = Buffer.alloc(1024*8)
```



• 创建一个指定大小的buffer对象，可以包含敏感数据(即含有内存之前保存的值)

```js
let buf3 = Buffer.allocUnsafe(1024*8)
```



• 将buffer转换为一个字符串

```js
let str = "你好 atguigu";
let buf = Buffer.from(str , "utf-8");
console.log(buf.toString())
```





### Buffer的转换

- Buffer与字符串间的转换
  - 支持的编码:
    - ASCII、UTF-8、UTF-16LE/UCS-2、Base64、Binary、Hex
  - 字符串转Buffer
    - Buffer.from(str , [encoding]);
  - Buffer转字符串
    - buf.toString([encoding] , [start] , [end]);

### 写入操作

• 向缓冲区中写入字符串
– buf.write(string[, offset[, length]][, encoding])
• 替换指定索引位置的数据
– buf[index]
• 将指定值填入到缓冲区的指定位置
– buf.fill(value[, offset[, end]][, encoding])

### 读取操作

• 将缓冲区中的内容，转换为一个字符串返回
– buf.toString([encoding[, start[, end]]])
• 读取缓冲区指定索引的内容
– buf[index]

### 其他操作

• 复制缓冲区
– buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])
• 对缓冲区切片
– buf.slice([start[, end]])
• 拼接缓冲区
– Buffer.concat(list[, totalLength])



## fs（文件系统）

• 在Node中，与文件系统的交互是非常重要的，服务器的本质就将本地的文件发送给远程的客户端
• Node通过fs模块来和文件系统进行交互
• 该模块提供了一些标准文件访问API来打开、读取、写入文件，以及与其交互。
• 要使用fs模块，首先需要对其进行加载
– const fs = require("fs");

### 同步和异步调用

• fs模块中所有的操作都有两种形式可供选择同步和异步。
• 同步文件系统会阻塞程序的执行，也就是除非操作完毕，否则不会向下执行代码。
• 异步文件系统不会阻塞程序的执行，而是在操作完成时，通过回调函数将结果返回。

### 打开和关闭文件

• 打开文件
– fs.open(path, flags[, mode], callback)
– fs.openSync(path, flags[, mode])
• 关闭文件
– fs.close(fd, callback)
– fs.closeSync(fd)

### 打开状态

|模式 |说明|
|----|----|
|r |读取文件 , 文件不存在则出现异常|
|r+ |读写文件 , 文件不存在则出现异常|
|rs |在同步模式下打开文件用于读取|
|rs+ |在同步模式下打开文件用于读写|
|w |打开文件用于写操作 , 如果不存在则创建，如果存在则截断|
|wx |打开文件用于写操作, 如果存在则打开失败|
|w+ |打开文件用于读写, 如果不存在则创建, 如果存在则截断|
|wx+ |打开文件用于读写, 如果存在则打开失败|
|a |打开文件用于追加, 如果不存在则创建|
|ax |打开文件用于追加, 如果路径存在则失败|
|a+ |打开文件进行读取和追加, 如果不存在则创建该文件|
|ax+ |打开文件进行读取和追加,如果路径存在则失败|



### 写入文件

• fs中提供了四种不同的方式将数据写入文件
– 简单文件写入
– 同步文件写入
– 异步文件写入
– 流式文件写入

#### 简单文件写入

• fs.writeFile(file, data[, options], callback)
• fs.writeFileSync(file, data[, options])
• 参数：
– file 文件路径
– data 被写入的内容，可以是String或Buffer
– options 对象，包含属性（encoding、mode、flag）
– callback 回调函数

#### 同步文件写入

• fs.writeSync(fd, buffer, offset, length[, position])
• fs.writeSync(fd, data[, position[, encoding]])
• 要完成同步写入文件，先需要通过openSync()打开文件来获取一个文件描述符，然后在通过writeSync()写入文件。
• 参数
– fd 文件描述符，通过openSync()获取
– data 要写入的数据（String 或 Buffer）
– offset buffer写入的偏移量
– length 写入的长度
– position 写入的起始位置
– encoding 写入编码

#### 异步文件写入

• fs.write(fd, buffer, offset, length[, position], callback)
• fs.write(fd, data[, position[, encoding]], callback)
• 要使用异步写入文件，先需要通过open()打开文件，然后在回调函数中通过write()写入。
• 参数：
– fd 文件描述符
– data 要写入的数据（String 或 Buffer）
– offset buffer写入的偏移量
– length 写入的长度
– position 写入的起始位置
– encoding 写入编码

#### 流式文件写入

• 往一个文件中写入大量数据时，最好的方法之一是使用流。
• 若要将数据异步传送到文件，首需要使用以下语法创建一个Writable对象：
– fs.createWriteStream(path[, options])
• path 文件路径
• options {encoding:"",mode:"",flag:""}
• 一旦你打开了Writable文件流，就可以使用write()方法来写入它，写入完成后，在调用end()方法来关闭流。

```js
/*
    同步、异步、简单文件写入都不适合大文件的写入，性能较差，容易导致内存泄露
*/

let fs = require('fs')

let ws = fs.createWriteStream('hello.txt')

// 通过 ws 向文件输出内容
ws.write('你好丫丫')
ws.write('我很好')

// 关闭流
ws.end();
// 可以通过监听留的 open 和close 事件来监听流的打开和关闭
/*
    on(事件字符串, 回调函数)
      - 可以为对象绑定一个事件
    once(事件字符串, 回调函数)
      - 可以为对象绑定一个一次性事件，该事件将会出发一次以后自动失效
*/

ws.once('open', function () {  
  console.log('流打开了~~');
})

ws.once('close', function () {  
  console.log('流关闭了~~');
})
```



### 读取文件

• fs中提供了四种读取文件的方式
– 简单文件读取
– 同步文件读取
– 异步文件读取
– 流式文件读取



#### 简单文件读取

• fs.readFile(file[, options], callback)
• fs.readFileSync(file[, options])
– 参数：
	• file 文件路径或文件描述符
	• options   `<Object>` | `<String>`
		– encoding   `<Object>` | `<Null>`  默认 = null
		– flag `<String>`  默认 = 'r'
	• callback 回调函数，有两个参数err 、data



#### 同步文件读取

• fs.readSync(fd, buffer, offset, length,position)
– 参数：
• fd 文件描述符
• buffer 读取文件的缓冲区
• offset buffer的开始写入的位置
• length 要读取的字节数
• position 开始读取文件的位置



#### 异步文件读取

• fs.read(fd, buffer, offset, length,position, callback)
– 参数：
	• fd 文件描述符
	• buffer 读取文件的缓冲区
	• offset buffer的开始写入的位置
	• length 要读取的字节数
	• position 开始读取文件的位置
	• callback 回调函数 参数err , bytesRead , buffer

#### 流式文件读取

• 从一个文件中读取大量的数据时，最好的方法之一就是流式读取，这样将把一个文件作为Readable流的形式打开。
• 要从异步从文件传输数据，首先需要通过以下语法创建一个Readable流对象：
– fs.createReadStream(path[, options])
• path 文件路径
• options {encoding:"",mode:"",flag:""}
• 当你打开Readable文件流以后，可以通过readable事件和read()请求，或通过data事件处理程序轻松地从它读出。

```js
let fs = require('fs')
let rs = fs.createReadStream('hello.txt')

rs.once('open', function () {  
  console.log('流打开了~~');
})

rs.once('close', function () {  
  console.log('流关闭了~~');
})

rs.on('data', function (data) {  
  console.log(data);
})
```



#### 流式文件的读和写

```js
let fs = require('fs')

let rs = fs.createReadStream('hello.txt')
let ws = fs.createWriteStream('hello1.txt')

// 用 pipe 可以将可读流中的内容直接输出可写流中
rs.pipe(ws)
```



#### 其他操作

• 验证路径是否存在
– ~~fs.exists(path，callback)~~
– fs.existsSync(path)
• 获取文件信息
– fs.stat(path, callback)
– fs.statSync(path)
• 删除文件
– fs.unlink(path, callback)
– fs.unlinkSync(path)

 • 列出文件
– fs.readdir(path[, options], callback)
– fs.readdirSync(path[, options])
• 截断文件
– fs.truncate(path, len, callback)
– fs.truncateSync(path, len)
• 建立目录
– fs.mkdir(path[, mode], callback)
– fs.mkdirSync(path[, mode])

• 删除目录
– fs.rmdir(path, callback)
– fs.rmdirSync(path)
• 重命名文件和目录
– fs.rename(oldPath, newPath, callback)
– fs.renameSync(oldPath, newPath)
• 监视文件更改写入
– fs.watchFile(filename[, options], listener)





## 异常处理

Node是单线程运行环境，一旦抛出的异常没有被捕获，就会引起整个进程的崩溃。所以，Node的异常处理对于保证系统的稳定运行非常重要。

一般来说，Node有三种方法，传播一个错误。

- 使用throw语句抛出一个错误对象，即抛出异常。
- 将错误对象传递给回调函数，由回调函数负责发出错误。
- 通过EventEmitter接口，发出一个error事件。



### try…catch结构

最常用的捕获异常的方式，就是使用try…catch结构。但是，这个结构无法捕获异步运行的代码抛出的异常。

```js
try {
  process.nextTick(function () {
    throw new Error("error");
  });
} catch (err) {
  //can not catch it
  console.log(err);
}

try {
  setTimeout(function(){
    throw new Error("error");
  },1)
} catch (err) {
  //can not catch it
  console.log(err);
}
```

上面代码分别用process.nextTick和setTimeout方法，在下一轮事件循环抛出两个异常，代表异步操作抛出的错误。它们都无法被catch代码块捕获，因为catch代码块所在的那部分已经运行结束了。

一种解决方法是将错误捕获代码，也放到异步执行。

```js
function async(cb, err) {
  setTimeout(function() {
    try {
      if (true)
        throw new Error("woops!");
      else
        cb("done");
    } catch(e) {
      err(e);
    }
  }, 2000)
}

async(function(res) {
  console.log("received:", res);
}, function(err) {
  console.log("Error: async threw an exception:", err);
});
// Error: async threw an exception: Error: woops!
```

上面代码中，async函数异步抛出的错误，可以同样部署在异步的catch代码块捕获。

这两种处理方法都不太理想。一般来说，Node只在很少场合才用try/catch语句，比如使用`JSON.parse`解析JSON文本。



### 回调函数

Node采用的方法，是将错误对象作为第一个参数，传入回调函数。这样就避免了捕获代码与发生错误的代码不在同一个时间段的问题。

```
fs.readFile('/foo.txt', function(err, data) {
  if (err !== null) throw err;
  console.log(data);
});
```

上面代码表示，读取文件`foo.txt`是一个异步操作，它的回调函数有两个参数，第一个是错误对象，第二个是读取到的文件数据。如果第一个参数不是null，就意味着发生错误，后面代码也就不再执行了。

下面是一个完整的例子。

```js
function async2(continuation) {
  setTimeout(function() {
    try {
      var res = 42;
      if (true)
        throw new Error("woops!");
      else
        continuation(null, res); // pass 'null' for error
    } catch(e) {
      continuation(e, null);
    }
  }, 2000);
}

async2(function(err, res) {
  if (err)
    console.log("Error: (cps) failed:", err);
  else
    console.log("(cps) received:", res);
});
// Error: (cps) failed: woops!
```

上面代码中，async2函数的回调函数的第一个参数就是一个错误对象，这是为了处理异步操作抛出的错误。



### EventEmitter接口的error事件

发生错误的时候，也可以用EventEmitter接口抛出error事件。

```js
var EventEmitter = require('events').EventEmitter;
var emitter = new EventEmitter();

emitter.emit('error', new Error('something bad happened'));
```

使用上面的代码必须小心，因为如果没有对error事件部署监听函数，会导致整个应用程序崩溃。所以，一般总是必须同时部署下面的代码。

```
emitter.on('error', function(err) {
  console.error('出错：' + err.message);
});
```



### uncaughtException事件

当一个异常未被捕获，就会触发uncaughtException事件，可以对这个事件注册回调函数，从而捕获异常。

```js
var logger = require('tracer').console();
process.on('uncaughtException', function(err) {
  console.error('Error caught in uncaughtException event:', err);
});

try {
  setTimeout(function(){
    throw new Error("error");
  },1);
} catch (err) {
  //can not catch it
  console.log(err);
}
```

只要给uncaughtException配置了回调，Node进程不会异常退出，但异常发生的上下文已经丢失，无法给出异常发生的详细信息。而且，异常可能导致Node不能正常进行内存回收，出现内存泄露。所以，当uncaughtException触发后，最好记录错误日志，然后结束Node进程。

```js
process.on('uncaughtException', function(err) {
  logger.log(err);
  process.exit(1);
});
```



### unhandledRejection事件

iojs有一个unhandledRejection事件，用来监听没有捕获的Promise对象的rejected状态。

```js
var promise = new Promise(function(resolve, reject) {
  reject(new Error("Broken."));
});

promise.then(function(result) {
  console.log(result);
})
```

上面代码中，promise的状态变为rejected，并且抛出一个错误。但是，不会有任何反应，因为没有设置任何处理函数。

只要监听unhandledRejection事件，就能解决这个问题。

```
process.on('unhandledRejection', function (err, p) {
  console.error(err.stack);
})
```

需要注意的是，unhandledRejection事件的监听函数有两个参数，第一个是错误对象，第二个是产生错误的promise对象。这可以提供很多有用的信息。

```js
var http = require('http');

http.createServer(function (req, res) {
  var promise = new Promise(function(resolve, reject) {
    reject(new Error("Broken."))
  })

  promise.info = {url: req.url}
}).listen(8080)

process.on('unhandledRejection', function (err, p) {
  if (p.info && p.info.url) {
    console.log('Error in URL', p.info.url)
  }
  console.error(err.stack)
})
```

上面代码会在出错时，输出用户请求的网址。

```bash
Error in URL /testurl
Error: Broken.
  at /Users/mikeal/tmp/test.js:9:14
  at Server.<anonymous> (/Users/mikeal/tmp/test.js:4:17)
  at emitTwo (events.js:87:13)
  at Server.emit (events.js:169:7)
  at HTTPParser.parserOnIncoming [as onIncoming] (_http_server.js:471:12)
  at HTTPParser.parserOnHeadersComplete (_http_common.js:88:23)
  at Socket.socketOnData (_http_server.js:322:22)
  at emitOne (events.js:77:13)
  at Socket.emit (events.js:166:7)
  at readableAddChunk (_stream_readable.js:145:16)
```

## 命令行脚本

node脚本可以作为命令行脚本使用。

```
$ node foo.js
```

上面代码执行了foo.js脚本文件。

foo.js文件的第一行，如果加入了解释器的位置，就可以将其作为命令行工具直接调用。

```
#!/usr/bin/env node
```

调用前，需更改文件的执行权限。

```bash
$ chmod u+x foo.js
$ ./foo.js arg1 arg2 ...
```

作为命令行脚本时，`console.log`用于输出内容到标准输出，`process.stdin`用于读取标准输入，`child_process.exec()`用于执行一个shell命令。







## 摘自

http://javascript.ruanyifeng.com/nodejs/basic.html







































