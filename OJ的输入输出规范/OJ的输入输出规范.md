## 牛客网

https://www.nowcoder.com/discuss/276

### **JavaScript（V8）**输入输出操作指南

一：单行输入，
    牛客网V8引擎是利用了readline()接收输入的每一行，该行字符数不能超过1024个，否则会报错。

```js
let line=readline();
```



二：多行输入，
    同理，好多同学对V8的多行输入示例不是很明白，其实它基于的readline()并不会像JS语言一样后面的会覆盖前面的语句，在这里readline是可以重载的，也就是说每一次调用readline()就会识别一行输入数据，多行输入其实就是写多个readline()即可。
    固定行数输入：

```js
let lineNum; // 获取第一行输入的数字，即行数
let lines = []
while(lineNum = parseInt(readline())){
  for(let i = 0; i < lineNum; i++) {
  	lines.push(readline()); // 遍历行数，依次获取每一行的字符
	}
}
print(lines) // 一次性全部输出所有结果

/*
eg：
输入：3
     1 2
     2 3
得到的是 line // 3
        lines // ['1 2', '3 4']
*/
```



任意行数输入:

```js
// 在单行的字符串中以空格作为分隔符，取出多个数据，并且数据转为整型：
let line;
while(line=readline()){ // 有多个输入样例，只要每一行有数据 ，就会继续循环读取单行数据
    var lines = line.trim().split(' '); // 去掉每一行的首尾空格，用空格分隔每一行的数据
    var a = parseInt(lines[0]);  // 将分隔出的字符串转为整型，并存储在变量 a 中
    var b = parseInt(lines[1]);
    print(a+b);
}
/*
输入：1 2
     3 4
     6 1
输出：3
     7
     7
*/

// ---------------------------------------------------------------------
// 直接将每一行的数据（字符串）存入数组 lines 中：
let lines = [], tmp;
while(tmp = readline()){
  lines.push(tmp);
}
print(lines);
/*
输入：1 2
     3 4
     6
输出：1 2,3 4,6
*/
```



三：输出，
    牛客网输出函数用 `print()` 和 `console.log()` 都可以，多行输入会根据你的print个数来自动折行；或者利用`print(lines + ‘\n’);` 来折行。



### JavaScript(Node) 输入输出操作指南

单行输入

```js
var readline = require('readline')
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
})
rl.on('line', function(line) {
  //line为输入的单行字符串，split函数--通过空格将该行数据转换为数组。
  var arr= line.split(' ')
 //数组arr的每一项都是字符串格式，如果我们需要整型，则需要parseInt将其转换为数字
  console.log(parseInt(arr[0]) + parseInt(arr[1]));
})
```



多行输入：

任意行输入：

```js
var readline = require('readline')
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
})
var inputs = []
rl.on('line', function(line){
  //trim()去除字符串两边的空白,line表示一行输入，最终得到的inputs数组的每一个元素表示一行输入。
  inputs.push(line.trim());
  console.log(inputs)
  //下面再根据要求对每一行数据进行处理，比如类似于单行输入将每一行数据按照空格转换为数组等
  //  ...
})
// console.log(inputs) // 不要在 rl.on 函数外面输出，否则会输出空串，因为 rl.on 是异步操作

/*
输入：
1 2
   3 4
     6 1
输出：
[ '1 2' ]
[ '1 2', '3 4' ]
[ '1 2', '3 4', '6 1' ]
*/
```



固定行输入：

输入第一个数据为接下来要输入的行数

```js
const readline = require('readline');
const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});
var inputArr = [];
rl.on('line', function (input) {
    inputArr.push(input);
    var nLine = +inputArr[0];
    if (inputArr.length == (nLine + 1)) {
        var arr = inputArr.slice(1);
        console.log(arr);
        inputArr = [];
    }
});
/*
输入：
4
32
12
34
23
输出：
[ '32', '12', '34', '23' ]
*/
```



输出

```
console.log("你的结果");
```

## 赛码

使用 JavaScript (Google V8 Engine(6.2.340))语言的输入输出：

- 读取size个字符：gets(size)
  将读取至多size个字符，当还未达到size个时如果遇到回车或结束符，会提前结束。回车符可能会包含在返回值中。

  ```js
  let line;
  let arr = new Array()
  while (line = gets(5)) { // 读取5个字符，此处的 size 包含空格
  	arr.push(line)         // 如下结果可得出，如果第5个元素是换行符，也会被读取
    print(line)
  }
  /*
  输入：     
  1 3 5
  12345
  1 23
  3 4 0 3 5
  5 4    8
  输出：
  1 3 5
  12345
  1 23
  
  3 4 0
   3 5
  
  5 4  
    8
  
  */
  ```

  

- 输出信息：printsth(sth, ...)
  往控制台输出sth，当有多个参数时，用英文逗号分隔；最后**不**加回车。

  ```js
  printsth(1, 2, 46, 'a', 'b', '\n')
  printsth('x', 'u')
  printsth(1, 2, 46, 'a', 'b') // 不加 ‘\n’,则不换行
  printsth('x', 'u')
  /*
  输出：
  1 2 46 a b 
  x u
  1 2 46 a bx u
  */
  ```

  

- 输出一行：print(sth, ...) console.error(sth, ...) console.debug(sth, ...) console.info(sth, ...) console.log(sth, ...)
  往控制台输出sth，当有多个参数时，用英文逗号分隔；最后加回车。

  ```js
  print(1, 2, 46, 'a', 'b') // 每执行一个 print，就输出一行新增加的 print 会自动换行
  print('x', 'u')
  /*
  输出：
  1 2 46 a b 
  x u
  */
  ```

  

- 读取一个（长）整数：readInt()
  从控制台读取一个（长）整数。

  ```js
  let line;
  while (line = readInt()) { // 读取到小数点或者空行时，直接挑出
  	print(line)
  }
  /*
  输入：     
  188
  29.4
  203 9
  0.01
  输出：
  188
  29
  */
  ```

  

- 读取一个浮点型：readDouble()
  从控制台读取一个浮点型。

  ```js
  let line;
  while (line = readDouble()) {
  	print(line)
  }
  /*
  输入：     
  188
  29.4
  203 9
  
  0.01
  输出：
  188
  29.4
  203
  9
  0.01
  */
  ```

  

- 读取一行输入：read_line()
  将读取至多1024个字符，当还未达到1024个时如果遇到回车或结束符，提前结束。读取多行最简单的办法是while((line = read_line()) != '')。

  ```JS
  let line;
  while (line = read_line()) {
  	print(line)
  }
  /*
  输入：     
  188
  a b c
  29 4
  203 9
  0.01
  输出：
  188
  a b c
  29 4
  203 9
  0.01
  */
  ```

  

> 对于每组测试用例(即输入)：
>
> 第一行包括2个整数，N（1 <= N <= 1000），M(0 <= M <= N*(N-1)/2)，代表现有N个人（用1~N编号）和M组关系；
>
> 在接下来的M行里，每行包括3个整数，a，b, c，如果c为1，则代表a跟b是同乡；如果c为0，则代表a跟b不是同乡；
>
> 已知1表示小赛本人。

```js
var N, M;
// 每组第一行是2个整数，N和M，至于为啥用while，因为是多组。
while ((N=readInt()) != null && (M=readInt()) != null) {
  print (N + ' ' + M);
  // 循环读取“接下来的M行”
  for (let i=0; i<M; i++) {
    let a = readInt();
    let b = readInt();
    let c = readInt();
    // 每行是3个整数，a，b，c。
    print(a + ' ' + b + ' ' + c);
  }
  // M行读取完了，就又要开始下一组了，去while那里。
}
/*
输入：     
5 4
1 2 1
3 4 0
2 5 1
3 2 1
输出：
5 4
1 2 1
3 4 0
2 5 1
3 2 1
*/
```



## 参考

> [牛客网JS（nodeJS）单行、多行输入和输出](https://segmentfault.com/a/1190000014463637)
>
> [赛码编程考试须知](http://oj.acmcoder.com/ExamNotice.html)
>
> 