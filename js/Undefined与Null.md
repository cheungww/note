## Undefined

Undefined 是 js 的基本数据类型之一。undefined 的字面意思就是：未定义的值 。这个值的语义是，**希望表示一个变量最原始的状态，而非人为操作的结果** 。 这种原始状态会在以下 4 种场景中出现：

1. **声明一个变量，但是没有赋值**

   ```js
   var foo;
   console.log(foo); // undefined
   ```

   访问 foo，返回了 undefined，表示这个变量自从声明了以后，就从来没有定义过任何有效的值。

2. **访问对象上不存在的属性或者未定义的变量**

   ```js
   console.log(Object.foo); // undefined
   console.log(typeof demo); // undefined
   ```

   访问 Object 对象上的 foo 属性，返回 undefined ， 表示Object 上不存在或者没有定义名为 foo 的属性；对未声明的变量执行typeof操作符返回了undefined值。

3. **函数定义了形参，但没有传递实参**

   ```js
   //函数定义了形参 a
   function fn(a) {
       console.log(a); // undefined
   }
   fn(); //未传递实参
   ```

   函数 fn 定义了形参 a，但 fn 被调用时没有传递参数，因此，fn 运行时的参数 a 就是一个原始的、未被赋值的变量。

4. **使用void对表达式求值**

   ```
   void 0 ; // undefined
   void false; // undefined
   void []; // undefined
   void null; // undefined
   void function fn(){} ; // undefined
   ```

   ECMAScript 明确规定 void 操作符 对任何表达式求值都返回 undefined ，这和函数执行操作后没有返回值的作用是一样的，JavaScript 中的函数都有返回值，当**没有 return 操作**时，就**默认返回**一个原始的状态值，这个值就是 **undefined**，表明函数的返回值未被定义。



## NUll

null 的字面意思是：空值 。这个值的语义是，**希望表示一个对象被人为的重置为空对象，而非一个变量最原始的状态** 。 在内存里的表示就是，栈中的变量没有指向堆中的内存对象。

**一般在以下两种情况下我们会将变量赋值为null**

1. 如果定义的变量在**将来用于保存对象**，那么最好将该变量**初始化为null**，而不是其他值。换句话说，只要意在保存对象的变量还没有真正保存对象，就应该明确地让该变量保存null值，这样有助于进一步区分null和undefined。
2. 当一个**数据不再需要使用**时，我们最好通过将其**值设置为null来释放其引用**，这个做法叫做**解除引用**。不过解除一个值的引用并不意味着自动回收改值所占用的内存。解除引用的真正作用是让值脱离执行环境，以便垃圾收集器在下次运行时将其回收。解除引用还有助于消除有可能出现的循环引用的情况。这一做法适用于大多数全局变量和全局对象的属性，局部变量会在它们离开执行环境时(函数执行完时)自动被解除引用。



## Undefined 与 Null 的一些奇怪现象

- **undefined**

  undefined不是一个关键字，可以作为一个变量来使用，也就是说在一定条件下你可以随意更改它。

  - **全局属性 window.undefined**
    - **chrome 77.0.3865.75** 环境

    结果是undefined,也就是在新版的谷歌浏览器里面的**全局作用域**下面是无**法更改undefined**的

    ```js
    //这次改用const
    const undefined = 2; //Identifier 'undefined' has already been declared
    console.log(undefined); 
    ```

    用**const**则会**报错**,已经declared了,**let**也同样

    

    - ###### **IE浏览器（IE11）**

    ```js
    var undefined = 2;
    console.log(undefined); //undefined 
    ```

    这样的结果还是**undefined**,所以在**ie11**的**全局作用域**下面也**不能替换**

    

    - 直到**ie8版本**的浏览器发生了变化

    ```js
    var undefined = 2;
    console.log(undefined); //2
    ```

    竟然改变了,**ie8的全局作用域下是可以改变undefined**的

    

  - **函数作用域**

    在**谷歌浏览器77.0.3865.75** 版本下,我们试试在**函数作用域**下

    ```js
    (function () {
      var undefined = 2
      console.log(undefined);  //2 
    })()
    ```

    发现这样就**改变了undefined**

    ```js
    (function () {
      const undefined = 2
      console.log(undefined); //2
    })()
    ```

    **const同样可行**,并且在ie的11,10,9,8几个版本中,无一例外的,**undefined都被覆盖**了

    

    **原因**

    在全局作用域下,全局变量会变成 window上面的一个属性，相当于 window.undefined ，这时候我们查看这个属性

    ```js
    // chrome 77.0.3865.75
    console.log(Object.getOwnPropertyDescriptor(window, undefined)); 
    //{value: undefined, writable: false, enumerable: false, configurable: false}
    ```

    （chrome 77.0.3865.75环境）这时候拿到数据属性可以看到,**不可重写,不可枚举,不可改变特征值或者被删除**,所以在全局作用域下面是不可以被重写的.

    

    **ie8环境下**：

    ```js
    console.log(Object.getOwnPropertyDescriptor(window, undefined)); 
    //{configurable: false, enumerable: false, value: undefined, writable: true}
    ```

    **ie8**同样,**不可枚举,不可删除或修改特征值**,但是!!**可以被重写**!!所以ie8的全局作用域上面是可以被重写的!!

    

- **null**

  typeof null 的结果是object

  ```js
  var data = null;
  console.log(typeof data); // "object"
  ```

  typeof null 的结果为什么不是 null：

  在 javascript 的最初版本中，使用的 32 位系统，并且底层都表示为2进制,为了性能考虑使用低位存储了变量的类型信息：

  - 000：对象
  - 1：整数
  - 010：浮点数
  - 100：字符串
  - 110：布尔
  - 全部为0: null

  所以typeof就是利用这一机制去判断的,所以null全部为0就复合了对象的000,所以被判断为object

  **并且还有个注意点**

  ```js
  null == undefined 
  //true null和undefined用==比较结果是true,并且没有发生隐式转换,就是true
  ```

  

## Undefined、Null 类型判断

### 判断 Undefined

判断一个变量是否为 undefined 有两种方法。

1. 使用严格相等符 `===` 或不相等操作符 `!==` 来决定一个变量是否拥有值，这里**不使用标准相等操作符`==`,**是因为**标准相等符还会会检查变量是不是为null**，但是严格相等操作符不会检查。

   ```js
   var data;
   if (data === undefined) { // true
   	// do sth
   }
   undefined == null; //true
   undefined === null; // false
   ```

   

2. 使用typeof操作符，这种方式我们在上面已经使用过了，对未定义的变量检测时只能使用这种方式，要不然会出现报错。

   ```js
   var data;
   if (typeof data === "undefined") { // true
     // do sth
   }
   ```

   

### void 0

上面我们提到过了，undefined作为局部变量使用是可以被重写的，那么如果我们使用下面这种判断方式，是有风险的。

```js
if(data === undefined){
    //do something
}
```

官方文档对 void 运算符的解释如下：

> The void operator evaluates the given expression and then returns undefined.
>
> void 运算符 对给定的表达式进行求值，然后返回 undefined

使用void对后面的表达式求值，无论结果是多少，都会返回原始值undefined。因此我们可以**用`void 0`来代替undefined进行判断**，因为`void 0`始终返回的都是原始值undefined。

```js
var data;
console.log(data === void 0); //true
```



### 判断 Null

null的判断可以使用严格相等符`===`或不相等操作符`!==`判断，不使用标准相等符的原因是因为undefined会影响判断结果。和undefined不一样，不能使用typeof来判断一个值是否为null，原因上边已经讲了，使用typeof来检测null会返回"object",这样的话我们是没办法判断的。

```js
if(data === null){
	console.log("data中没有保存对象引用！");
}
```



## Undefined 与 Null 的区别

用一句话总结两者的区别就是：**undefined** 表示一个**变量自然的、最原始的状态值**，而 **null** 则表示**一个变量被人为的设置为空对象，而不是原始状态**。所以，在实际使用过程中，为了保证变量所代表的语义，不要对一个变量显式的赋值 undefined，当需要释放一个对象时，直接赋值为 null 即可。



参考

> [JavaScript深入理解之undefined与null](https://juejin.im/post/5aa4f7cc518825557e780256) 
>
> [JS中的null和undefined,undefined为啥用void 0代替?](https://juejin.im/post/5cc58a72e51d456e845b4289) 