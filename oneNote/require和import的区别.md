## CommonJS 模块 和 ESMAScript6+ 模块的区别

import只能用于静态导入，就是必须在文件开始的时候，只能写在模块的顶层，不能在代码块之中（比如，在`if`代码块之中，或在函数之中）。而require就可以实现动态加载可以写在代码块之中。

| 加载方式   | 规范         | 命令    | 特点                                                         |
| ---------- | ------------ | ------- | ------------------------------------------------------------ |
| 运行时加载 | CommonJS/AMD | require | 社区方案，提供了服务器/浏览器的模块加载方案。非语言层面的标准。只能在运行时确定模块的依赖关系及输入/输出的变量，无法进行静态优化 |
| 编译时加载 | ESMAScript6+ | import  | 语言规格层面支持模块功能。支持编译时静态分析，便于JS引入宏和类型检验。动态绑定 |

require/exports 的用法只有以下三种简单的写法：

```js
const fs = require('fs')
exports.fs = fs
module.exports = fs
```


而 import/export 的写法就多种多样：

```js
import fs from 'fs'
import {default as fs} from 'fs'
import * as fs from 'fs'
import {readFile} from 'fs'
import {readFile as read} from 'fs'
import fs, {readFile} from 'fs'

export default fs
export const fs
export function readFile
export {readFile, read}
export * from 'fs'
```

两者重大差异：

1. CommonJS 模块输出的是一个**值的拷贝**，ES6 模块输出的是**值的引用**。

   `export`语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到**模块内部实时的值**。

   ```javascript
   export var foo = 'bar';
   setTimeout(() => foo = 'baz', 500);
   ```

   上面代码输出变量`foo`，值为`bar`，500 毫秒之后变成`baz`。

   这一点与 CommonJS 规范完全不同。CommonJS 模块输出的是**值的缓存**，**不存在动态更新**，详见[《Module 的加载实现》](http://es6.ruanyifeng.com/#docs/module-loader)一节。

   

   `export`命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错，下一节的`import`命令也是如此。这是因为处于条件代码块之中，就没法做静态优化了，违背了 ES6 模块的设计初衷。

   ```javascript
   function foo() {
     export default 'bar' // SyntaxError
   }
   foo()
   ```

   上面代码中，`export`语句放在函数之中，结果报错。

2. CommonJS 模块是**运行时加载**，ES6 模块是**编译时输出接口**（**静态加载**）。

   ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。

   ```javascript
   // CommonJS模块
   let { stat, exists, readFile } = require('fs');
   
   // 等同于
   let _fs = require('fs');
   let stat = _fs.stat;
   let exists = _fs.exists;
   let readfile = _fs.readfile;
   ```

   上面代码的实质是整体加载`fs`模块（即加载`fs`的所有方法），生成一个对象（`_fs`），然后再从这个对象上面读取 3 个方法。这种加载称为“**运行时加载**”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

   ES6 模块不是对象，而是通过`export`命令显式指定输出的代码，再通过`import`命令输入。

   ```javascript
   // ES6模块
   import { stat, exists, readFile } from 'fs';
   ```

   上面代码的实质是从`fs`模块加载 3 个方法，其他方法不加载。这种加载称为“**编译时加载**”或者**静态加载**，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

   由于 ES6 模块是编译时加载，使得静态分析成为可能。有了它，就能进一步拓宽 JavaScript 的语法，比如引入宏（macro）和类型检验（type system）这些只能靠静态分析实现的功能。



参考：

> [ES6 模块与 CommonJS 模块的差异]([http://es6.ruanyifeng.com/#docs/module-loader#ES6-%E6%A8%A1%E5%9D%97%E4%B8%8E-CommonJS-%E6%A8%A1%E5%9D%97%E7%9A%84%E5%B7%AE%E5%BC%82](http://es6.ruanyifeng.com/#docs/module-loader#ES6-模块与-CommonJS-模块的差异))

