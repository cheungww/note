## 预备知识

### 区别函数对象与实例对象

> 实例对象：new 函数产生的对象，称为实例对象，简称对象
>
> 函数对象：将函数作为对象使用时，简称函数对象

```js
function Fn() {} // Fn函数
const fn = new Fn() // Fn是构造函数，fn是实例对象
console.log(Fn.prototype) // Fn是函数对象
Fn.call({}) // Fn是函数对象
fn.call({}) // Uncaught TypeError: fff.call is not a function，实例对象是不能调用call的
$('#test') // jQuery 函数
$.get('/test') // jQuery 函数对象
```



### 两种类型的回调函数

#### 同步回调

> 立即执行，执行完后才结束，不会放入回调队列中

例如，数组遍历相关的回调函数、Promise 的 excutor 函数

```js
const arr = [0, 3, 4]
arr.forEach(item => console.log(item)) // 遍历回调，同步回调函数，不会放入任务队列中
console.log('forEach之后');
// 0 3 4 forEach之后
```

上面的运行结果表明，在数组的`forEach` 遍历完后，才执行 `console.log('forEach之后')` ，说明数组的 `forEach` 的回调函数是同步回调。



#### 异步回调

> 不会立即执行，会放入回调队列中将来执行

例如，定时器回调、ajax回调、Promise的成功/失败回调等

```js
setTimeout(() => {console.log('timeout callback')}, 0) // 异步回调函数，会放入任务队列
console.log('setTimeout 之后');
// setTimeout 之后
// timeout callback
```

上面的结果表明，先执行完同步代码后，再执行定时器的回调函数，说明定时器的回调函数是异步回调



### error

#### 常见内置错误

##### SyntaxError

> 语法错误

```js
// 变量名错误
var 1a;
// Uncaught SyntaxError: Invalid or unexpected token

// 缺少括号
console.log 'hello');
// Uncaught SyntaxError: Unexpected string

const c = ''''
// Uncaught SyntaxError: Unexpected string
```



##### ReferenceError

> 引用的变量不存在

```js
// 使用一个不存在的变量
console.log(a) // Uncaught ReferenceError: a is not defined
console.log('此代码不会执行') // 没有捕获 error ，此行代码不会执行
```

另一种触发场景是，将一个值分配给无法分配的对象，比如对函数的运行结果或者`this`赋值。

```js
// 等号左侧不是变量
console.log() = 1
// Uncaught ReferenceError: Invalid left-hand side in assignment

// this 对象不能手动赋值
this = 1
// ReferenceError: Invalid left-hand side in assignment
```

上面代码对函数`console.log`的运行结果和`this`赋值，结果都引发了`ReferenceError`错误。



##### RangeError

> 数据值不在其所允许的范围内

`RangeError`对象是一个值超出有效范围时发生的错误。主要有几种情况，一是数组长度为负数，二是`Number`对象的方法参数超出范围，以及函数堆栈超过最大值。

```js
// 数组长度不得为负数
new Array(-1)
// Uncaught RangeError: Invalid array length

// 函数的堆栈溢出，原因是无限递归
function fn() {
  fn()
}
fn()
// Uncaught RangeError: Maximum call stack size exceeded
```



##### TypeError

> 数据类型不正确

`TypeError`对象是变量或参数不是预期类型时发生的错误。比如，对字符串、布尔值、数值等原始类型的值使用`new`命令，就会抛出这种错误，因为`new`命令的参数应该是一个构造函数。

```js
new 123
// Uncaught TypeError: number is not a func

var obj = {};
obj.unknownMethod()
// Uncaught TypeError: obj.unknownMethod is not a function

var b
console.log(b.xx)
// Uncaught TypeError: Cannot read property 'xx' of undefined
```

上面代码的第二种情况，调用对象不存在的方法，也会抛出`TypeError`错误，因为`obj.unknownMethod`的值是`undefined`，而不是一个函数。第三种情况抛出 `TypeError` 错误，因为 `b` 的值是 `undefined` ，而 `undefined` 没有 `xx` 的属性。



#### 错误处理

##### 捕获错误 `try ... catch` 

```js
try {
	var b
	console.log(b.xx)
} catch(error) {
  console.log(error.message);
  console.log(error.stack);
}
// Cannot read property 'xx' of undefined
// TypeError: Cannot read property 'xx' of undefined at <anonymous>:3:16
```



##### 抛出错误 `throw error` 

```js
// 抛出错误
function something() {
  if (Date.now() % 2 === 1) {
    console.log('当前时间为奇数可以执行任务')
  } else { // 如果当前时间为偶数则抛出异常，有调用来处理
    throw new Error('当前时间为偶数无法执行任务')
  }
}

// 捕获处理异常
try {
	something()
} catch(error) {
  alert(error.message);
}
```



## Promise

### Promise 的基本使用

1. Promise 构造函数：Promise(excutor) {}

   - excutor 函数：执行器（resolve, reject）=> {}
   - resolve 函数：内部定义成功时我们调用的函数 value => {}
   - reject 函数：内部定义失败时我们调用的函数 reason => {}

   > 说明：excutor 会在 Promise 内部立即同步回调，异步操作在执行器中执行

   

2. Promise.prototype.then 方法：(onResolved, onRejected) => {}

   onResolved 函数：成功的回调  (value) => {}

   onRejected 函数：失败的回调函数  (reason) => {}

   > 指定用于得到成功 value 的成功回调和用于得到 reason 的失败回调返回一个新的 Promise 对象

   

3. Promise.prototype.catch 方法：(onRejected) => {}

   onRejected 函数：失败的回调函数 (reason) => {}

   > then() 的语法糖，相当于：then(undefined, onRejected)、then(null, onRejected)

   

4. Promise.resolve 方法：(value) => {}

   value: 成功的数据或 promise 对象

   > 返回一个成功 / 失败的 promise 对象

   

5. Promise.reject方法：(reason) => {}

   reason: 失败的原因

   > 返回一个 失败的 promise 对象
   
   
   
6. Promise.all 方法：(promisees) => {}

   promises：包含 n 个promise 的数组

   > 返回一个新的 promise，只要所有的 promise 都成功才成，只要有一个失败了就直接失败

   

7. Promise.race 方法：(promises) => {}

   promises：包含 n 个 promise 的数组

   > 返回一个新的 promise，第一个完成的 promise 的结果状态就是最终的结果状态

   

> 详见阮大大的 [Promise 对象](http://es6.ruanyifeng.com/#docs/promise) 或者 MDN 的 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 



### Promise 与纯回调函数的对比

1. 指定回调函数的方式更加灵活

   > 纯回调函数：必须在启动异步任务前指定回调函数
   >
   > Promise：启动异步任务 =》返回 Promise 对象 =》给Promise对象绑定回调函数（甚至可以在异步任务结束后指定）



```js
// 成功的回调函数
function successCallback(result) {
  console.log('文件创建成功：' + result)
}

// 失败的回调函数
function failureCallback(error) {
  console.log('文件创建失败：' + error)
}


// 1、使用纯回调函数
function createFileAsync(settings, successCallback, failureCallback) {
  let isSuccess = false;
  try {
    setTimeout(() => {
      console.log(`正在创建 ${settings} 文件`);
      isSuccess = true; // 这里假设成功地创建了文件
      successCallback(isSuccess);
    }, 200);
  } catch (error) {
    failureCallback(error)
  }
}

createFileAsync('test', successCallback, failureCallback)


// 2、使用 Promise
function createFileAsync(settings) {
  return new Promise((resolve, reject) => {
    resolve(settings); // 这里也假设成功地创建了文件，然后调用resolve
  });
}

const promise = createFileAsync('test')
setTimeout(() => {
  promise.then(successCallback)
  .catch(failureCallback);
  // 或者 promise.then(successCallback, failureCallback);
}, 3000)
```

由上面的例子可知，第一种是用纯回调函数的方法，在给 `createFileAsync` 异步任务绑定成功/失败的回调函数时，须通过传参的方式传给该异步任务，即 `必须在在启动异步任务前指定回调函数` ，而第二种的 Promise 的方法则不需要这样，他可以通过 `createFileAsync` 启动异步任务后返回一个 Promise 对象，然后根据 Promise 对象的状态，通过 `.then` 或者 `.catch` 的方式指定成功或者失败的回调函数



2. 支持链式调用，可以解决回调地狱的问题

   > 什么是回调地狱：回调地狱嵌套调用，外部回调函数异步执行的结果是嵌套的回调函数执行的条件
   >
   > 回调地狱的缺点：不便于阅读、不便于异常处理
   >
   > 解决方案：Promise 链式调用
   >
   > 终极解决方案：async / await



回调地狱

```js
doSomething(function(result) {
  doSomething(result, function (newResult) {
    doThirdThing(newResult, function (finalResult) {
      console.log('Got the final result:' + finalResult)                       
    }, failureCallback)
  }, failureCallback)
}, failureCallback)
```



使用 Promise 的链式调用解决回调地狱

```js
// 这里的 doSomething 函数返回的是 Promise 对象
doSomething().then(function (result) {
  return doSomethinElse(result)
})
.then(function (newResult) {
  return doThirdThing(newResult)
})
.then(function (finalResult) {
  console.log('Got the final result:' + finalResult)
})
.catch(failureCallback)
```



async / await

```js
async function request() {
  try {
    const result = await doSomething()
    const newResult = await doSomethingElse(result)
    const finalResult = await doThirdThing(newResult)
    console.log('Got the final result:' + finalResult)
  } catch (error) {
    failureCallback(error)
  }
}
```



### Promise 的特性

1. 如何改变 Promise 的状态

   - resolve(value)：如果当前是 pending 就会变成 fullfilled 状态（也可说是 resolved 状态）
   - reject(reason)：如果当前是 pending 就会变成 rejected 状态
   - 抛出异常：如果当前是 pending 就会变成 rejected

   ```js
   const p = new Promise((resolve, reject) => {
     // resolve(1) // promise 变为 resolved 成功状态
     // reject(2) // promise 变为 rejected 失败状态
     // throw new Error('出错了') // 抛出异常，promise 变为 rejected 失败状态,reason为抛出的error
     // throw 3 // 抛出异常，promise 变为 rejected 失败状态,reason为抛出的3
     a.xx = 4
   })
   
   p.then(
     value => {},
     reason => {console.log('reason', reason)}
   )
   // reason ReferenceError: a is not defined
   ```

   

2. 一个 promise 指定多个成功/失败回调函数，都会调用吗？

   - 当promise 改变为对应状态时都会调用

   ```js
   // 还是上面的Promise对象 p
   p.then(
     value => {},
     reason => {console.log('reason', reason)}
   )
   // reason ReferenceError: a is not defined
   
   p.then(
     value => {},
     reason => {console.log('reason2', reason)}
   )
   // reason2 ReferenceError: a is not defined
   ```

   上面例子中的 p 绑定了两次的回调函数，并且都调用了。

   

3. 改变  promise 状态和指定回调函数谁先谁后？

   - 都有可能，正常情况下是先指定回调函数再改变状态，但也可以先改变状态再指定回调
   - 如何先改变状态再指定回调？
     - 在执行器中直接调用 resolve() / reject()
     - 延迟更长的时间才调用 then()
   - 什么时候才能得到数据
     - 如果先指定的回调，那当状态发生改变时，回调函数就会调用，得到数据
     - 如果先改变的状态，那当指定回调时，回调函数就会调用，得到数据

   

   ```js
   // 1、常规：先指定回调函数，后改变的状态
   new Promise((resolve, reject) => {
     setTimeout(() => {
       resolve(1) // 后改变的状态（同时指定数据），异步执行回调函数
     }, 1000)
   }).then( // 先指定回调函数，保存当前指定的回调函数
   	value => {console.log('value', value)},
     reason => {console.log('reason', reason)}
   )
   
   // 2、如何先改变状态，后指定回调函数
   new Promise((resolve, reject) => {
     resolve(1) // 先改变的状态（同时指定数据）
   }).then( // 后指定回调函数，异步执行回调函数
   	value => {console.log('value2', value)},
     reason => {console.log('reason2', reason)}
   )
   
   console.log('此处证明上面的 value2 是异步执行的');
   
   const p = new Promise((resolve, reject) => {
     setTimeout(() => {
       resolve(1)
     }, 1000)
   })
   
   // 通过延迟更长的时间才调用 then() 来使状态先改变
   setTimeout(() => {
     p.then( // 后指定回调函数，异步执行回调函数
   	  value => {console.log('value3', value)},
       reason => {console.log('reason3', reason)}
     )
   }, 1100)
   ```

4. promise.then() 返回的新 promise 的结果状态有什么决定

   - 简单表达：有 then() 指定的回调函数执行的结果决定
   - 详细表达：
     - 如果抛出异常，新 promise 变成 rejected，reason 为抛出的异常
     - 如果返回的是非 promise 的任意值，新 promise 变成 resolved，value 为返回的值
     - 如果返回的是另一个新 promise，此 promise 的结果就会成为新的 promise 的结果

   ```js
   new Promise((resolve, reject) => {
     resolve(1)
     // reject(1)
   }).then(
   	value => {
       console.log('onResolved1()', value) // 没有 return 语句的话，默认是 return undefined
       // return 2 // (1)
       // return Promise.resolve(3) // (2)
       // return Promise.reject(4) // (3)
       // throw 5 // (4)
     },
     reason => {console.log('onRejected1()', reason)}
   ).then( 
   	value => {console.log('onResolved1()', value)},
     reason => {console.log('onRejected1()', reason)}
   )
   /* 
   onResolved1() 1
   onResolved1() undefined
   (1)
   onResolved1() 1
   onResolved1() 2
   (2)
   onResolved1() 1
   onResolved1() 3
   (3)
   onResolved1() 1
   onRejected1() 4
   (4)
   onResolved1() 1
   onRejected1() 5
    */
   ```

5. promise 如何串连多个操作任务

   - promise 的 then() 返回一个新的 promise，可以开成 then() 的链式调用
   - 通过 then 的链式调用串连多个同步 / 异步任务

   ```js
   new Promise((resolve, reject) => {
     setTimeout(() => {
       console.log('执行任务1（异步）')    
       resolve(1)
     }, 1000)
   }).then(
   	value => {
       console.log('任务1的结果：', value)
       console.log('执行任务2（同步）')
       return 2    
     }
   ).then(
   	value => {
       console.log('任务2的结果：', value)
       // 一般将 .then() 的回调函数里面的异步任务被封装到promise对象里执行，该异步任务执行完之后，
       // 才调用下一个 .then() 的回调函数
       return new Promise((resolve, reject) => {
         setTimeout(() => {
           console.log('执行任务3（异步）');
           resolve (3)
         }, 1000)
       })
     }
   ).then(
   	value => {
       console.log('任务3的结果：', value)
       // 因为此处的异步任务没有封装到promise对象中
       // 所以会进行异步调用该任务，先执行完所有的 .then(),再执行该异步任务，导致（1）比（2）、（3）后执行
       setTimeout(() => {
         console.log('执行任务4（异步）'); // (1)
         return 3
       }, 1000)
     }
   ).then(
   	value => {
       console.log('任务4的结果：', value) // (2)
     }
   ).then(
   	value => {
       console.log('任务5的结果：', value) // (3)
     }
   )
   /*
   执行任务1（异步）
   任务1的结果： 1
   执行任务2（同步）
   任务2的结果： 2
   执行任务3（异步）
   任务3的结果： 3
   任务4的结果： undefined
   任务5的结果： undefined
   执行任务4（异步）
   */
   ```

6. promise 异常传透

   - 当使用 promise 的 then 链式调用时，可以在最后指定失败的回调
   - 前面任何操作出了异常，都会传到最后失败的回调中处理

   ```js
   new Promise((resolve, reject) => {   
     reject(1)
   }).then(
   	value => {
       console.log('onResolved1()：', value)
       return 2
     },
     // reason => { throw reason }
   ).then(
   	value => {
       console.log('onResolved2():', value)
       return 3
     },
     // reason => Promise.reject(reason)
   ).catch(reason => {
     console.log('onRejected1():', reason)  
   })
   // onRejected1(): 1
   ```

   如上例可知，当 promise 的状态为 rejected 时，会将 reason 向后面 .then() 的**默认的 onRejected() 回调函**数（`reason => { throw reason }` 或者 `reason => Promise.reject(reason)` ，具体是函数，且看下文的源代码解析）一层一层的往后传递，直到被 .catch() 捕获 。

7. 中断 promise 链

   - 当使用 promise 的 then 链式调用时，在中间中断，不再调用后面的回调函数
   - 办法：在回调函数中返回一个 pending 状态的 promise 对象

   ```js
   new Promise((resolve, reject) => {   
     reject(1)
   }).then(
   	value => {
       console.log('onResolved1()：', value)
       return 2
     }
   ).catch(reason => {
     console.log('onRejected1():', reason)
     return new Promise(() => {}) // 返回一个pending的promise 中断promise链
   }).then(
   	value => {
       console.log('onResolved2():', value)
       return 3
     }
   )
   // onRejected1(): 1
   
   new Promise((resolve, reject) => {   
     resolve(1)
   }).then(
   	value => {
       console.log('onResolved1()：', value)
       return new Promise(() => {}) // 返回一个pending的promise 中断promise链
     }
   ).then(
   	value => {
       console.log('onResolved2():', value)
       return 3
     }
   )
   // onResolved1()： 1
   ```

   有上例可知，不管是在 .catch()，还是在 .then() 中，只要当回调函数返回的是一个 pending 状态的 promise 对象，就会中断 promise 链



## 自定义 Promise

### 定义整体结构

```js
/**
 * 自定义 Promise 函数模块：IIFE
 */
(function (window) {  
  /**
   * @description Promise构造函数
   * @param {*} excutor 执行器函数(同步执行)
   */
  function Promise(excutor) {
    
  }

  /**
   * @description Promise 原型对象的 then()，返回一个新的promise对象
   * @param {*} onResolved 成功的回调函数
   * @param {*} onRejected 失败的回调函数
   */
  Promise.prototype.then = function (onResolved, onRejected) {  

  }
  
  /**
   * @description Promise 原型对象的 catch()，返回一个新的promise对象
   * @param {*} onRejected 失败的回调函数
   */
  Promise.prototype.catch = function (onRejected) {  

  }

  /**
   * @description Promise 函数对象的 resolve 方法，
   * 返回一个指定结果的成功的promise
   * @param {*} value
   */
  Promise.resolve = function (value) {  

  }
  
  /**
   * @description Promise 函数对象的 reject 方法，
   * 返回一个指定结果的失败的promise
   * @param {*} reason
   */
  Promise.reject = function (reason) {  

  }
  
  /**
   * @description Promise 函数对象的 all 方法，返回一个promise，
   * 只有当所有promise都成功时才成功，否则只要有一个失败的就失败
   * @param {*} promises
   */
  Promise.all = function (promises) {  

  }
  
  /**
   * @description Promise 函数对象的 race 方法，
   * 返回一个promise，其结果有第一个完成的promise决定
   * @param {*} promises
   */
  Promise.race = function (promises) {  

  }

  // 向外暴露 Promise 函数 
  window.Promise = Promise
})(window)
```



### Promise 构造函数

- 先将Promise实例的this保存到self，否则this会根据执行环境而改变。

  给promise对象指定status属性，初始值为pending

  给promise对象指定一个拥有存储结果数据的属性

  每个元素的结构：`{ onResolved(){}, onRejected(){}}` 

- 定义 resolve 函数

  - 如果当前状态不是pending，直接 return
  - 将状态改为 resolved
  - 保存value数据
  - 如果有待执行callback函数，立即异步执行回调函数；放入队列中执行所有成功的回调

- 执行 reject 函数

  - 如果当前状态不是pending，直接 return
  - 将状态改为 rejected
  - 保存reason数据
  - 如果有待执行callback函数，立即异步执行回调函数；放入队列中执行所有失败的回调

- 立即同步执行excutor

  如果执行器抛出异常，使用 `try...catch` 捕获错误，promise对象变为rejected状态

```js
function Promise(excutor) {

  const self = this; // 先将Promise实例的this保存到self，否则this会根据执行环境而改变
  self.status = 'pending' // 给promise对象指定status属性，初始值为pending
  self.data = undefined // 给promise对象指定一个拥有存储结果数据的属性
  self.callbacks = [] // 每个元素的结构：{ onResolved(){}, onRejected(){}}

  function resolve(value) {
    // 如果当前状态不是pending，直接结束
    if (self.status !== 'pending') {
      return;
    }
    // 将状态改为resolved
    self.status = 'resolved'
    // 保存value数据
    self.data = value
    // 如果有待执行callback函数，立即异步执行回调函数
    if (self.callbacks.length > 0) {
      setTimeout(() => { // 放入队列中执行所有成功的回调
        self.callbacks.forEach(callbacksObj => {
          callbacksObj.onResolved(value)
        })
      });
    }
  }

  function reject(reason) {
    // 如果当前状态不是pending，直接结束
    if (self.status !== 'pending') {
      return;
    }
    // 将状态改为rejected
    self.status = 'rejected'
    // 保存reason数据
    self.data = reason
    // 如果有待执行callback函数，立即异步执行回调函数
    if (self.callbacks.length > 0) {
      setTimeout(() => { // 放入队列中执行所有失败的回调
        self.callbacks.forEach(callbacksObj => {
          callbacksObj.onRejected(reason)
        })
      });
    }
  }

  // 立即同步执行excutor
  try {
    excutor(resolve, reject)
  } catch (error) { // 如果执行器抛出异常，promise对象变为rejected状态
    reject(error)
  }
}

/**
 * @description Promise 原型对象的 then()，返回一个新的promise对象
 * @param {*} onResolved 成功的回调函数
 * @param {*} onRejected 失败的回调函数
 */
// 此 .then() 方法还没有完全实现，为了测试promise构造函数，暂时这样实现
Promise.prototype.then = function (onResolved, onRejected) {  
  const self = this;
  // 假设当前状态还是pending状态，将回调函数保存起来
  self.callbacks.push({
    onResolved,
    onRejected
  })
}
```

注意：先将Promise实例的this保存到self，否则this会根据执行环境而改变



```html
<!--测试-->
<script src="./lib/Promise.js"></script>
<script>
  const p = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(1)
      // reject(2)
      console.log('reject()改变状态之后')
    }, 100);
  })

  p.then(
    value => { console.log('onResolved1()', value) },
    reason => { console.log('onRejected1()', reason) }
  )
  
  p.then(
    value => { console.log('onResolved2()', value) },
    reason => { console.log('onRejected2()', reason) }
  )
</script>
<!--
reject()改变状态之后
onResolved1() 1
onResolved2() 1
-->
```



### then() 和 catch() 方法

- 定义一个处理执行 `onResolved` 或者 `onRejected` 函数执行结果的函数 `handle` 
  - 如果抛出异常，return的promise就会失败，reason就是error
  - 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
  - 如果回调函数返回是promise，return的promise结果就是这个promise的结果
- 当前状态还是pending状态，将回调函数保存起来
- 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
- 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
- 返回一个新的promise对象
- catch() 相当于 then(undefined, onRejected)

```js
/**
 * @description Promise 原型对象的 then()，返回一个新的promise对象
 * 返回的promise的结果由onResolved或onRejected执行的结果决定
 * @param {*} onResolved 成功的回调函数
 * @param {*} onRejected 失败的回调函数
 */
Promise.prototype.then = function (onResolved, onRejected) {
  // 向后传递成功的value
  onResolved = typeof onResolved === 'function' ? onResolved : value => value
  // 向后传递失败的reason，指定默认的失败的回调（实现错误/异常传透的关键点）
  onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }
  const self = this;

  // 返回一个新的promise对象
  return new Promise((resolve, reject) => {

    /**
     * @description 调用指定回调函数处理
     * @param {function} callback
     */
    function handle(callback) {
      /*
      1. 如果抛出异常，return的promise就会失败，reason就是error
      2. 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
      3. 如果回调函数返回是promise，return的promise结果就是这个promise的结果
      */

      try {
        const result = callback(self.data)
        // 3. 如果回调函数返回是promise，return的promise结果就是这个promise的结果
        if (result instanceof Promise) {
          // result.then(
          //   value => resolve(value), // 当result成功时，让return的promise也成功
          //   reason => reject(reason) // 当result失败时，让return的promise也失败
          // )
          result.then(resolve, reject) // 简洁的写法
        } else {
          // 2. 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
          resolve(result)
        }
      } catch (error) {
        // 1. 如果抛出异常，return的promise就会失败，reason就是error
        reject(error)
      }        
    }

    // 当前状态还是pending状态，将回调函数保存起来
    if (self.status === PENDING) {
      self.callbacks.push({
        onResolved (value) {
          handle(onResolved)
        },
        onRejected (reason) {
          handle(onRejected)
        }
      })
      // 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
    } else if (self.status === RESOLVED) {
      setTimeout(() => {
        handle(onResolved)
      });
      // 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
    } else { // rejected状态
      setTimeout(() => {
        handle(onRejected)
      });
    }
  });
}

/**
 * @description Promise 原型对象的 catch()，返回一个新的promise对象
 * @param {*} onRejected 失败的回调函数
 */
Promise.prototype.catch = function (onRejected) {  
  return this.then(undefined, onRejected)
}
```



```js
// 测试代码
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    // resolve(1)
    reject(2)
    console.log('reject()改变状态之后')
  }, 100);
}).then(
  value => { console.log('onResolved1()', value) },
  reason => { 
    console.log('onRejected1()', reason) 
    // return 3
    // throw 4
    return new Promise((res, rej) => rej(5))
  }
).then(
  value => { console.log('onResolved2()', value) },
  reason => { 
    console.log('onRejected2()', reason) 
    throw 6
  }
).catch(
  reason => {
    console.log('onRejected3()', reason);
    return new Promise(() => {}) // 中断 promise 链
  }
).then(
  value => { console.log('onResolved4()', value) },
  reason => { 
    console.log('onRejected4()', reason) 
    throw 6
  }
)
/*
reject()改变状态之后
onRejected1() 2
onRejected2() 5
onRejected3() 6
*/
```



### resolve 和 reject 方法

resolve 

- 如果value是promise，就使用value的结果作为promise的结果
- 如果value不是promise，则使用resolve 把 promise变为成功，数据是value
- 返回一个成功/失败的promise

reject

- 使用 reject， 返回一个失败的promise

```js
/**
 * @description Promise 函数对象的 resolve 方法，
 * 返回一个指定结果的成功的promise
 * @param {*} value
 */
Promise.resolve = function (value) {
  // 返回一个成功/失败的promise
  return new Promise((resolve, reject) =>{
    // value是promise
    if (value instanceof Promise) { // 使用value的结果作为promise的结果
      value.then(resolve, reject)
    } else { // value不是promise => promise变为成功，数据是value
      resolve(value)
    }
  })
}

/**
 * @description Promise 函数对象的 reject 方法，
 * 返回一个指定结果的失败的promise
 * @param {*} reason
 */
Promise.reject = function (reason) {
  // 返回一个失败的promise
  return new Promise((resolve, reject) => {
    reject(reason)
  })
}
```

测试：

```js
const p1 = Promise.resolve(2) // 如果是一般值，p1成功，value就是这个值
const p2 = Promise.resolve(Promise.resolve(3)) // 如果是成功的promise，p2成功，value是这个promise的value
const p3 = Promise.resolve(Promise.reject(4)) // 如果是失败的promise，p3失败，reason是这个promise的reason
p1.then(value => {console.log('p1', value)})
p2.then(value => {console.log('p2', value)})
p3.catch(reason => {console.log('p3', reason)})
```



### all 方法

- 定义一个用来保存所有成功value的数组
- 定义用来保存成功promise的数量
- 遍历promises获取每个promise的结果。（不直接用 p.then，而是用Promise.resolve()包裹，是因为promises可能包含不是promise对象的元素，例如，数字7）
- 如果 p 成功，将成功的value保存为values；成功的数量+1；并将value放入数组对应的位置；如果全部成功了，将return的promise改变成功
- 如果 p 失败，则直接 reject
- 返回一个promise

```js
/**
 * @description Promise 函数对象的 all 方法，返回一个promise，
 * 只有当所有promise都成功时才成功，否则只要有一个失败的就失败
 * @param {*} promises
 */
Promise.all = function (promises) {
  const values = new Array(promises.length) // 用来保存所有成功value的数组
  // 用来保存成功promise的数量
  let resolvedCount = 0
  return new Promise((resolve, reject) => {
    // 遍历promises获取每个promise的结果
    promises.forEach((p, index) => {
      // p.then( // 不直接用 p.then，是因为promises可能包含不是promise对象的元素，例如，数字7
      Promise.resolve(p).then(
        value => { // p成功，将成功的value保存为values
          resolvedCount++ // 成功的数量+1
          // values.push(value) 先成功先保存，顺序不对
          values[index] = value 
          // 如果全部成功了，将return的promise改变成功
          if (resolvedCount === promises.length) {
            resolve(values)
          }
        },
        reason => { 
          reject(reason)
        }
      )
    })
  })
}
```



```js
// 测试
const p1 = Promise.resolve(2) // 如果是一般值，p1成功，value就是这个值
const p2 = Promise.resolve(Promise.resolve(3)) // 如果是成功的promise，p2成功，value是这个promise的value
const p3 = Promise.resolve(Promise.reject(4)) // 如果是失败的promise，p3失败，reason是这个promise的reason
const p4 = new Promise(resolve => {
  setTimeout(() => {
    resolve(5)
  }, 100);
})
const pAll = Promise.all([p4, p1, 7, p2])
// const pAll = Promise.all([p4, p1, p2, p3])
pAll.then(
  values => {
    console.log('all onResovled()', values)
  },
  reason => {
    console.log('all onRejected()', reason)
  }
)
// all onResovled() [5, 2, 7, 3]
```



### race 方法

- 遍历promise获取每个promise的结果
- 一旦有成功的，将return变为成功
- 一旦有失败了，将return变为失败
- 返回一个promise

```js
/**
 * @description Promise 函数对象的 race 方法，
 * 返回一个promise，其结果有第一个完成的promise决定
 * @param {*} promises
 */
Promise.race = function (promises) {
  // 返回一个promise
  return new Promise((resolve, reject) => {
    // 遍历promise获取每个promise的结果
    promises.forEach((p, index) => {
      // p.then( // 不直接用 p.then，是因为promises可能包含不是promise对象的元素，例如，数字7
      Promise.resolve(p).then(
        value => { // 一旦有成功的，将return变为成功
          resolve(value)
        },
        reason => { // 一旦有失败了，将return变为失败
          reject(reason)
        }
      )
    })
  })
}
```



```js
// 测试
const p1 = Promise.resolve(1) // 如果是一般值，p1成功，value就是这个值
const p2 = Promise.resolve(Promise.resolve(2)) // 如果是成功的promise，p2成功，value是这个promise的value
const p3 = Promise.resolve(Promise.reject(3)) // 如果是失败的promise，p3失败，reason是这个promise的reason
const p4 = new Promise(resolve => {
  setTimeout(() => {
    resolve(4)
  }, 100);
})
const p5 = Promise.reject(5)
const pRace = Promise.race([p4, p5, p1, 7, p2])
pRace.then(
  values => {
    console.log('race onResovled()', values)
  },
  reason => {
    console.log('race onRejected()', reason)
  }
)
// race onResovled() 7
```



### resolveDelay()和rejectDelay() 方法

使用 setTimeout 来延迟调用 Promise.resolve 和  Promise.reject 的源代码

```js
/**
 * @description 返回一个promise对象，它在指定的时间后才确定结果
 * @param {*} value
 * @param {*} time
 */
Promise.resolveDelay = function (value, time) {
  // 返回一个成功/失败的promise
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      // value是promise
      if (value instanceof Promise) { // 使用value的结果作为promise的结果
        value.then(resolve, reject)
      } else { // value不是promise => promise变为成功，数据是value
        resolve(value)
      }
    }, time)
  })
}

/**
 * @description 返回一个promise对象，它在指定的时间后才确定结果
 * @param {*} value
 * @param {*} time
 */
Promise.rejectDelay = function (reason, time) {
  // 返回一个失败的promise
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(reason)
    }, time)
  })
}
```



```js
// 测试
const p6 = Promise.resolveDelay(66, 2000)
const p7 = Promise.resolveDelay(77, 2000)
p6.then(value => {console.log('p6', value)})
p7.then(reason => {console.log('p7', reason)})
// p6 66
// p7 77
```



### Promise最终版

```js
/**
 * 自定义 Promise 函数模块：IIFE
 */
(function (window) {  
  const PENDING = 'pending';
  const RESOLVED = 'resolved';
  const REJECTED = 'rejected';

  /**
   * @description Promise构造函数
   * @param {*} excutor 执行器函数(同步执行)
   */
  function Promise(excutor) {

    const self = this; // 先将Promise实例的this保存到self，否则this会根据执行环境而改变
    self.status = PENDING // 给promise对象指定status属性，初始值为pending
    self.data = undefined // 给promise对象指定一个拥有存储结果数据的属性
    self.callbacks = [] // 每个元素的结构：{ onResolved(){}, onRejected(){}}

    function resolve(value) {
      // 如果当前状态不是pending，直接结束
      if (self.status !== PENDING) {
        return;
      }
      // 将状态改为resolved
      self.status = RESOLVED
      // 保存value数据
      self.data = value
      // 如果有待执行callback函数，立即异步执行回调函数
      if (self.callbacks.length > 0) {
        setTimeout(() => { // 放入队列中执行所有成功的回调
          self.callbacks.forEach(callbacksObj => {
            callbacksObj.onResolved(value)
          })
        });
      }
    }

    function reject(reason) {
      // 如果当前状态不是pending，直接结束
      if (self.status !== PENDING) {
        return;
      }
      // 将状态改为rejected
      self.status = REJECTED
      // 保存reason数据
      self.data = reason
      // 如果有待执行callback函数，立即异步执行回调函数
      if (self.callbacks.length > 0) {
        setTimeout(() => { // 放入队列中执行所有失败的回调
          self.callbacks.forEach(callbacksObj => {
            callbacksObj.onRejected(reason)
          })
        });
      }
    }

    // 立即同步执行excutor
    try {
      excutor(resolve, reject)
    } catch (error) { // 如果执行器抛出异常，promise对象变为rejected状态
      reject(error)
    }
  }

  /**
   * @description Promise 原型对象的 then()，返回一个新的promise对象
   * 返回的promise的结果由onResolved或onRejected执行的结果决定
   * @param {*} onResolved 成功的回调函数
   * @param {*} onRejected 失败的回调函数
   */
  Promise.prototype.then = function (onResolved, onRejected) {
    // 向后传递成功的value
    onResolved = typeof onResolved === 'function' ? onResolved : value => value
    // 向后传递失败的reason，指定默认的失败的回调（实现错误/异常传透的关键点）
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }
    const self = this;

    // 返回一个新的promise对象
    return new Promise((resolve, reject) => {

      /**
       * @description 调用指定回调函数处理
       * @param {function} callback
       */
      function handle(callback) {
        /*
        1. 如果抛出异常，return的promise就会失败，reason就是error
        2. 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
        3. 如果回调函数返回是promise，return的promise结果就是这个promise的结果
        */

        try {
          const result = callback(self.data)
          // 3. 如果回调函数返回是promise，return的promise结果就是这个promise的结果
          if (result instanceof Promise) {
            // result.then(
            //   value => resolve(value), // 当result成功时，让return的promise也成功
            //   reason => reject(reason) // 当result失败时，让return的promise也失败
            // )
            result.then(resolve, reject) // 简洁的写法
          } else {
            // 2. 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
            resolve(result)
          }
        } catch (error) {
          // 1. 如果抛出异常，return的promise就会失败，reason就是error
          reject(error)
        }        
      }

      // 当前状态还是pending状态，将回调函数保存起来
      if (self.status === PENDING) {
        self.callbacks.push({
          onResolved (value) {
            handle(onResolved)
          },
          onRejected (reason) {
            handle(onRejected)
          }
        })
        // 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
      } else if (self.status === RESOLVED) {
        setTimeout(() => {
          handle(onResolved)
        });
        // 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
      } else { // rejected状态
        setTimeout(() => {
          handle(onRejected)
        });
      }
    });
  }
  
  /**
   * @description Promise 原型对象的 catch()，返回一个新的promise对象
   * @param {*} onRejected 失败的回调函数
   */
  Promise.prototype.catch = function (onRejected) {  
    return this.then(undefined, onRejected)
  }

  /**
   * @description Promise 函数对象的 resolve 方法，
   * 返回一个指定结果的成功的promise
   * @param {*} value
   */
  Promise.resolve = function (value) {
    // 返回一个成功/失败的promise
    return new Promise((resolve, reject) =>{
      // value是promise
      if (value instanceof Promise) { // 使用value的结果作为promise的结果
        value.then(resolve, reject)
      } else { // value不是promise => promise变为成功，数据是value
        resolve(value)
      }
    })
  }
  
  /**
   * @description Promise 函数对象的 reject 方法，
   * 返回一个指定结果的失败的promise
   * @param {*} reason
   */
  Promise.reject = function (reason) {
    // 返回一个失败的promise
    return new Promise((resolve, reject) => {
      reject(reason)
    })
  }
  
  /**
   * @description Promise 函数对象的 all 方法，返回一个promise，
   * 只有当所有promise都成功时才成功，否则只要有一个失败的就失败
   * @param {*} promises
   */
  Promise.all = function (promises) {
    const values = new Array(promises.length) // 用来保存所有成功value的数组
    // 用来保存成功promise的数量
    let resolvedCount = 0
    return new Promise((resolve, reject) => {
      // 遍历promises获取每个promise的结果
      promises.forEach((p, index) => {
        Promise.resolve(p).then(
          value => { // p成功，将成功的value保存为values
            resolvedCount++ // 成功的数量+1
            // values.push(value) 先成功先保存，顺序不对
            values[index] = value 
            // 如果全部成功了，将return的promise改变成功
            if (resolvedCount === promises.length) {
              resolve(values)
            }
          },
          reason => { 
            reject(reason)
          }
        )
      })
    })
  }
  
  /**
   * @description Promise 函数对象的 race 方法，
   * 返回一个promise，其结果有第一个完成的promise决定
   * @param {*} promises
   */
  Promise.race = function (promises) {
    // 返回一个promise
    return new Promise((resolve, reject) => {
      // 遍历promise获取每个promise的结果
      promises.forEach((p, index) => {
        Promise.resolve(p).then(
          value => { // 一旦有成功的，将return变为成功
            resolve(value)
          },
          reason => { // 一旦有失败了，将return变为失败
            reject(reason)
          }
        )
      })
    })
  }

  /**
   * @description 返回一个promise对象，它在指定的时间后才确定结果
   * @param {*} value
   * @param {*} time
   */
  Promise.resolveDelay = function (value, time) {
    // 返回一个成功/失败的promise
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        // value是promise
        if (value instanceof Promise) { // 使用value的结果作为promise的结果
          value.then(resolve, reject)
        } else { // value不是promise => promise变为成功，数据是value
          resolve(value)
        }
      }, time)
    })
  }

  /**
   * @description 返回一个promise对象，它在指定的时间后才确定结果
   * @param {*} value
   * @param {*} time
   */
  Promise.rejectDelay = function (reason, time) {
    // 返回一个失败的promise
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject(reason)
      }, time)
    })
  }

  // 向外暴露 Promise 函数 
  window.Promise = Promise
})(window)
```



### promise 的 class 版本

```js
/**
 * 自定义 Promise 函数模块：IIFE
 */
(function (window) {  
  const PENDING = 'pending';
  const RESOLVED = 'resolved';
  const REJECTED = 'rejected';

  class Promise {
    /**
     * @description Promise构造函数
     * @param {*} excutor 执行器函数(同步执行)
     */
    constructor (excutor) {

      const self = this; // 先将Promise实例的this保存到self，否则this会根据执行环境而改变
      self.status = PENDING // 给promise对象指定status属性，初始值为pending
      self.data = undefined // 给promise对象指定一个拥有存储结果数据的属性
      self.callbacks = [] // 每个元素的结构：{ onResolved(){}, onRejected(){}}

      function resolve(value) {
        // 如果当前状态不是pending，直接结束
        if (self.status !== PENDING) {
          return;
        }
        // 将状态改为resolved
        self.status = RESOLVED
        // 保存value数据
        self.data = value
        // 如果有待执行callback函数，立即异步执行回调函数
        if (self.callbacks.length > 0) {
          setTimeout(() => { // 放入队列中执行所有成功的回调
            self.callbacks.forEach(callbacksObj => {
              callbacksObj.onResolved(value)
            })
          });
        }
      }

      function reject(reason) {
        // 如果当前状态不是pending，直接结束
        if (self.status !== PENDING) {
          return;
        }
        // 将状态改为rejected
        self.status = REJECTED
        // 保存reason数据
        self.data = reason
        // 如果有待执行callback函数，立即异步执行回调函数
        if (self.callbacks.length > 0) {
          setTimeout(() => { // 放入队列中执行所有失败的回调
            self.callbacks.forEach(callbacksObj => {
              callbacksObj.onRejected(reason)
            })
          });
        }
      }

      // 立即同步执行excutor
      try {
        excutor(resolve, reject)
      } catch (error) { // 如果执行器抛出异常，promise对象变为rejected状态
        reject(error)
      }
    }

    /**
     * @description Promise 原型对象的 then()，返回一个新的promise对象
     * 返回的promise的结果由onResolved或onRejected执行的结果决定
     * @param {*} onResolved 成功的回调函数
     * @param {*} onRejected 失败的回调函数
     */
    then (onResolved, onRejected) {
      // 向后传递成功的value
      onResolved = typeof onResolved === 'function' ? onResolved : value => value
      // 向后传递失败的reason，指定默认的失败的回调（实现错误/异常传透的关键点）
      onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }
      const self = this;

      // 返回一个新的promise对象
      return new Promise((resolve, reject) => {

        /**
         * @description 调用指定回调函数处理
         * @param {function} callback
         */
        function handle(callback) {
          /*
          1. 如果抛出异常，return的promise就会失败，reason就是error
          2. 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
          3. 如果回调函数返回是promise，return的promise结果就是这个promise的结果
          */

          try {
            const result = callback(self.data)
            // 3. 如果回调函数返回是promise，return的promise结果就是这个promise的结果
            if (result instanceof Promise) {
              // result.then(
              //   value => resolve(value), // 当result成功时，让return的promise也成功
              //   reason => reject(reason) // 当result失败时，让return的promise也失败
              // )
              result.then(resolve, reject) // 简洁的写法
            } else {
              // 2. 如果回调函数返回不是promise，return的promise就会成功，value就是返回的值
              resolve(result)
            }
          } catch (error) {
            // 1. 如果抛出异常，return的promise就会失败，reason就是error
            reject(error)
          }        
        }

        // 当前状态还是pending状态，将回调函数保存起来
        if (self.status === PENDING) {
          self.callbacks.push({
            onResolved (value) {
              handle(onResolved)
            },
            onRejected (reason) {
              handle(onRejected)
            }
          })
          // 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
        } else if (self.status === RESOLVED) {
          setTimeout(() => {
            handle(onResolved)
          });
          // 如果当前是resolved状态，异步执行onResolved并改变return的promise状态
        } else { // rejected状态
          setTimeout(() => {
            handle(onRejected)
          });
        }
      });
    }
    
    /**
     * @description Promise 原型对象的 catch()，返回一个新的promise对象
     * @param {*} onRejected 失败的回调函数
     */
    catch (onRejected) {  
      return this.then(undefined, onRejected)
    }

    /**
     * @description Promise 函数对象的 resolve 方法，
     * 返回一个指定结果的成功的promise
     * @param {*} value
     */
    static resolve (value) {
      // 返回一个成功/失败的promise
      return new Promise((resolve, reject) =>{
        // value是promise
        if (value instanceof Promise) { // 使用value的结果作为promise的结果
          value.then(resolve, reject)
        } else { // value不是promise => promise变为成功，数据是value
          resolve(value)
        }
      })
    }
    
    /**
     * @description Promise 函数对象的 reject 方法，
     * 返回一个指定结果的失败的promise
     * @param {*} reason
     */
    static reject (reason) {
      // 返回一个失败的promise
      return new Promise((resolve, reject) => {
        reject(reason)
      })
    }
    
    /**
     * @description Promise 函数对象的 all 方法，返回一个promise，
     * 只有当所有promise都成功时才成功，否则只要有一个失败的就失败
     * @param {*} promises
     */
    static all (promises) {
      const values = new Array(promises.length) // 用来保存所有成功value的数组
      // 用来保存成功promise的数量
      let resolvedCount = 0
      return new Promise((resolve, reject) => {
        // 遍历promises获取每个promise的结果
        promises.forEach((p, index) => {
          Promise.resolve(p).then(
            value => { // p成功，将成功的value保存为values
              resolvedCount++ // 成功的数量+1
              // values.push(value) 先成功先保存，顺序不对
              values[index] = value 
              // 如果全部成功了，将return的promise改变成功
              if (resolvedCount === promises.length) {
                resolve(values)
              }
            },
            reason => { 
              reject(reason)
            }
          )
        })
      })
    }
    
    /**
     * @description Promise 函数对象的 race 方法，
     * 返回一个promise，其结果有第一个完成的promise决定
     * @param {*} promises
     */
    static race (promises) {
      // 返回一个promise
      return new Promise((resolve, reject) => {
        // 遍历promise获取每个promise的结果
        promises.forEach((p, index) => {
          Promise.resolve(p).then(
            value => { // 一旦有成功的，将return变为成功
              resolve(value)
            },
            reason => { // 一旦有失败了，将return变为失败
              reject(reason)
            }
          )
        })
      })
    }

    /**
     * @description 返回一个promise对象，它在指定的时间后才确定结果
     * @param {*} value
     * @param {*} time
     */
    static resolveDelay (value, time) {
      // 返回一个成功/失败的promise
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          // value是promise
          if (value instanceof Promise) { // 使用value的结果作为promise的结果
            value.then(resolve, reject)
          } else { // value不是promise => promise变为成功，数据是value
            resolve(value)
          }
        }, time)
      })
    }

    /**
     * @description 返回一个promise对象，它在指定的时间后才确定结果
     * @param {*} value
     * @param {*} time
     */
    static rejectDelay (reason, time) {
      // 返回一个失败的promise
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          reject(reason)
        }, time)
      })
    }
  }

  // 向外暴露 Promise 函数 
  window.Promise = Promise
})(window)
```



## async 和 await

### async 和 await 的基本使用

> MDN: [`async function`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function) 、 [`await`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await) 
>
> 阮一峰的《ECMAScript 6 入门》：[async 函数](http://es6.ruanyifeng.com/#docs/async) 



### async函数

1. 函数的返回值为promise对象
2. promise对象的结果由async函数执行的返回值决定

```js
async function fn1() {
  // 相当于包装了一层Promise
  // return 1
  // throw 2
  // return Promise.reject(3)
  // return Promise.resolve(4)
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(5)
    })
  })
}

const result = fn1()
// console.log(result)
result.then(
	value => {
        console.log('onResolved()', value)
    },
    reason => {
        console.log('onRejected()', reason)
    }
)
// onResolved() 5
```



### await表达式

1. await右侧的表达式一般为Promise对象，但也可以是其他的值
2. 如果await右侧表达式是Promise对象，await返回的是promise成功的值
3. 如果await右侧表达式是其他值，直接将此值作为await的返回值
   - 注意：
     - await必须写在async函数中，但async函数中可以没有await
     - 如果await的promise失败了，就会抛出异常，需要通过try...catch来捕获处理

```js
function fn2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(2)
    }, 1000)
  })
}

function fn3() {
  return 3
}

function fn4() {
  // throw 4
  return Promise.reject(4)
}

async function fn5() {
  try {
    // await右侧表达式是Promise对象，await返回的是promise成功的值
    const value = await fn2()
    console.log('value', value)
    // await右侧表达式是其他值，直接将此值作为await的返回值
    const value2 = await fn3()
    console.log('value2', value2);
    // 如果await的promise失败了，就会抛出异常
    const value3 = await fn4()
  } catch (error) {
    console.log('得到失败结果', error);
  }
}
fn5()
// value 2
// value2 3
// 得到失败结果 4
```



##  js异步之宏队列与微队列

### 原理图

![宏队列和微队列](../images/promise.assets/%E5%AE%8F%E9%98%9F%E5%88%97%E5%92%8C%E5%BE%AE%E9%98%9F%E5%88%97.png)



- JS中用来存储待执行回调函数的队列包含2个不同特定的队列
- 宏队列：用来保存待执行的宏任务（回调），比如：定时器回调/DOM事件回调/ajax回调
- 微队列：用来保存待执行的微任务（回调），比如：promise的回调/ [`MutationObserver`](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver) 的回调
- JS执行时会区别这2个队列
  - JS引擎首先必须执行所有的初始化同步任务代码
  - 每次准备取出第一个宏任务执行前，都要将所有的微任务一个一个取出来执行

```js
// (1)、(2)、(3)等注释代表任务
setTimeout(() => { // 会立即放入宏队列  // (1)
  console.log('timeout callback1()')
  Promise.resolve(3).then( // 会立即放入微队列  // (2)
    value => {
      console.log('Promise onResolved3()', value)
    }
  )
}, 0);

setTimeout(() => { // 会立即放入宏队列  // (3)
  console.log('timeout callback2()')
}, 0);

Promise.resolve(1).then( // 会立即放入微队列  // (4)
  value => {
    console.log('Promise onResolved1()', value)
  }
)

Promise.resolve(2).then( // 会立即放入微队列  // (5)
  value => {
    console.log('Promise onResolved2()', value)
  }
)
// 在浏览器（Chrome）运行的结果是：
/*
同步代码
Promise onResolved1() 1
Promise onResolved2() 2
timeout callback1()
Promise onResolved3() 3
timeout callback2()
*/
// 但是在 node（v10.15.1）环境下运行的结果是：
/*
同步代码
Promise onResolved1() 1
Promise onResolved2() 2
timeout callback1()
timeout callback2()
Promise onResolved3() 3
*/
```

执行过程（本文是在浏览器环境下讲解的）：

1. 执行同步代码（打印 ‘同步代码’），（1）、（3）加入宏队列，（4）、（5）加入微队列
2. 执行（4）、（5）（打印 Promise onResolved1() 1、Promise onResolved2() 2）
3. 执行（1）（打印 timeout callback1()），将（2）加入微队列
4. 执行（2）（打印 Promise onResolved3() 3）
5. 执行（3）（打印 timeout callback2()）



## 练习

```js
setTimeout(() => { // (1)
  console.log(1)
}, 0)

Promise.resolve().then(() => { // (2)
  console.log(2)
})

Promise.resolve().then(() => { // (3)
  console.log(4)
})

console.log(3) // （4）
// 3 2 4 1
```

执行过程：

1. （1）加入宏队列，（2）、（3）加入微队列，执行同步代码（4）（即打印 3）
2. 清空微队列（即执行微队列中的所有微任务，打印 2 4）
3. 执行宏队列的宏任务，每次执行宏任务前都要检查微队列是否为空，如果不为空，则先清空微队列，再执行宏任务（打印 1）
4. 结果是：3 2 4 1



```js
setTimeout(() => {  // (1)
    console.log(1)
}, 0)

new Promise((resolve) => {  // (2)
    console.log(2)
    resolve()
}).then(() => {  // (3)
    console.log(3)
}).then(() => {  // (4)
    console.log(4)
})
console.log(5)  // (5)
// 2 5 3 4 1
```

1. （1）加入宏队列，执行（2），（3）加入微队列，执行（5）
2. 执行（3），（4）加入微队列
3. 执行（4）
4. 执行（1）



```js
const first = () => (new Promise((resolve, reject) => {  // (1)
    console.log(3)
    let p = new Promise((resolve, reject) => {  // (2)
        console.log(7)
        setTimeout(() => {  // (3)
            console.log(5)
            resolve(6)  // 此处的 resolve 无作用
        }, 0)
        resolve(1)
    })
    resolve(2)  // (4)
    p.then((arg) => {  // (5)
        console.log(arg)
    })
}))
first().then((arg) => {  // (6)
    console.log(arg)
})
console.log(4)  // (7)
// 3 7 4 1 2 5
```

1. 执行（1） 、（2），将（3）加入宏队列，将（5）、（6）加入微队列，执行（7）

2. 执行（5）、（6），执行（3）



```js
setTimeout(() => {  // (1)
  console.log('0')
}, 0)

new Promise((resolve, reject) => {  // (2)
    console.log('1')
    resolve()
}).then(() => {  // (3)
  console.log('2')

  new Promise((resolve, reject) => {  // (4)
    console.log('3')
    resolve()
  }).then(() => {  // (5)
      console.log('4')
  }).then(() => {  // (6)
      console.log('5')
  })

}).then(() => {  // (7)
      console.log('6')
})

new Promise((resolve, reject) => {  // (8)
    console.log('7')
    resolve()
}).then(() => {  // (9)
    console.log('8')
})
// 1 7 2 3 8 4 6 5 0
```

1. 将（1）加入宏队列，执行（2），将（3）加入微队列，执行（8），将（9）加入微队列。（宏：【（1）】，微：【（3）、（9）】）（执行结果：1 7）
2. 执行（3）、（4），将（5）、（7）加入微队列，执行（9）。（宏：【（1）】，微：【（5）、（7）】）（执行结果：2 3 8）
3. 执行（5）、（7），将（6）加入微队列。（宏：【（1）】，微：【（6）】）（执行结果：4 6）
4. 执行（6）。（宏：【（1）】，微：【（7）】）（执行结果：5）
5. 执行（1）。（执行结果：0）



## 参考

> [错误处理机制](https://wangdoc.com/javascript/features/error.html) 
>
> [Promise 对象](http://es6.ruanyifeng.com/#docs/promise) 
>
> [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 





