## 函数名的提升

JavaScript 引擎将函数名视同变量名，所以采用`function`命令声明函数时，整个函数会像变量声明一样，被提升到代码头部。所以，下面的代码不会报错。

```js
f();

function f() {}
```

表面上，上面代码好像在声明之前就调用了函数`f`。但是实际上，由于“变量提升”，函数`f`被提升到了代码头部，也就是在调用之前已经声明了。但是，如果采用赋值语句定义函数，JavaScript 就会报错。

```js
f();
var f = function (){};
// TypeError: undefined is not a function
```

上面的代码等同于下面的形式。

```js
var f;
f();
f = function () {};
```

上面代码第二行，调用`f`的时候，`f`只是被声明了，还没有被赋值，等于`undefined`，所以会报错。因此，如果同时采用`function`命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义。

```js
var f = function () {
  console.log('1');
}

function f() {
  console.log('2');
}

f() // 1
```



同时采用`function`命令和赋值语句声明同一个函数，执行该函数时，如果涉及函数名提升的，总是采用 `function` 定义的函数（也可以说是， `function` 声明的函数名的提升的优先级要比赋值语句声明的优先级要高）

```js
f()
var f = function () {
  console.log('1');
}

f()

function f() {
  console.log('2');
}

f() 
// 2 1 1
```

```js
f()
function f() {
  console.log('2');
}

f()

var f = function () {
  console.log('1');
}
f() // 2 2 1
```

> 参考：[函数名的提升](https://wangdoc.com/javascript/types/function.html#函数名的提升) 



## 类数组对象

> 定义（没有官方定义）：只包含使用从零开始，且自然递增的整数做键名，并且定义了length表示元素个数的对象，我们就认为它是类数组对象

```js
var arrayLike = {
    0: 'zhangsan',
    1: 'lisi',
    2: 'zhaoliu',
    length: 3
}
```



类数组对象转为数组的方法：

1. 使用 Array 原型链上的 slice 方法

   ```js
   var array = Array.prototype.slice.call(arrayLike);
   var array = [].slice.call(arrayLike);
   ```

2. 使用 for 循环

   ```js
   var len = arrayLike.length, array = new Array(len)
   for(var i = 0; i < len; i++) {
     array.push(arrayLike[i])
   }
   ```

3. 使用对象的扩展运算符

   ```js
   var array = [...arrayLike]
   ```

4. 使用 Array 的 from 方法

   ```js
   var array = Array.from(arrayLike)
   ```

   

## arguments对象

**arguments** 是一个对应于传递给函数的参数的类数组对象。

`arguments`对象是所有（**非箭头**）函数中都可用的**局部变量**。你可以使用`arguments`对象在函数中引用函数的参数。此对象包含传递给函数的每个参数，第一个参数在索引0处。

### 正常模式

在正常模式下，`arguments` 对象可以在运行时修改，并且 `arguments`对象与函数参数具有**绑定的关系**。

```js
function foo(a) { 
  arguments[0] = 99;   // 更新了arguments[0],同样更新了a
  console.log(a);
}
foo(10); // 99

function bar(a) { 
  a = 99;              // 更新了a,同样更新了arguments[0] 
  console.log(arguments[0]);
}
bar(10); // 99
```

上面代码中，arguments[0] 与 a 是绑定了一起的，更改了其中一个，另一个也会更改。

但是，如果在函数运行时给 arguments 对象**添加新的元素** （arguments 的 **length 属性的值不会改变**）,则此新添加的元素与函数参数是**没有绑定关系的**

（注意：arguments 对象是跟函数的**实参**绑定的）

```js
function test(a, b, c) {
  console.log(arguments);
  console.log('length:', arguments.length)
  arguments[2] = 10;
  console.log(arguments);
  console.log('length:', arguments.length)
  console.log('c: ', c)
}
test(1, 2)
/*
[Arguments] { '0': 1, '1': 2 }
length: 2
[Arguments] { '0': 1, '1': 2, '2': 10 }
length: 2
c:  undefined
*/
test(1, 2, 3)
/*
[Arguments] { '0': 1, '1': 2, '2': 3 }
length: 3
[Arguments] { '0': 1, '1': 2, '2': 10 }
length: 3
c:  10
*/
```



### 严格模式

严格模式下，`arguments`对象与函数参数不具有绑定关系。也就是说，修改`arguments`对象不会影响到实际的函数参数。

```js
function foo(a) { 
  'use strict'; // 开启严格模式
  arguments[0] = 99;   // 更新了arguments[0],但 a 没有更新
  console.log(a);
}
foo(10); // 10

function bar(a) { 
  'use strict'; // 开启严格模式
  a = 99;              // 更新了a,但 arguments[0] 没有更新 
  console.log(arguments[0]);
}
bar(10); // 10
```



### 剩余参数、默认参数和解构赋值参数

 当非严格模式中的函数**有**包含[剩余参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Rest_parameters)、[默认参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Default_parameters)和[解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)，那么`arguments`对象中的值**不会**跟踪参数的值（反之亦然）。相反, `arguments`反映了调用时提供的参数：

```js
// 剩余参数
function foo(...args) {
  console.log(args);
  console.log(arguments);
  args[1] = 100;
  console.log('----');
  console.log(args);
  console.log(arguments);
}
foo(1, 2)
/*
[ 1, 2 ]
[Arguments] { '0': 1, '1': 2 }
----
[ 1, 100 ]
[Arguments] { '0': 1, '1': 2 }
*/
function bar(...args) {
  console.log(args);
  console.log(arguments);
  arguments[1] = 100;
  console.log('----');
  console.log(args);
  console.log(arguments);
}
bar(1, 2)
/*
[ 1, 2 ]
[Arguments] { '0': 1, '1': 2 }
----
[ 1, 2 ]
[Arguments] { '0': 1, '1': 100 }
*/
```



```js
function foo(a = 55) { 
  arguments[0] = 99; // updating arguments[0] does not also update a
  console.log(a);
}
foo(10); // 10

function bar(a = 55) { 
  a = 99; // updating a does not also update arguments[0]
  console.log(arguments[0]);
}
bar(10); // 10
```

















