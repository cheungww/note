## 为什么要使用href=”javascript:void(0);”

```
href=”javascript:void(0);”这个的含义是，让超链接去执行一个js函数，而不是去跳转到一个地址，而void(0)表示一个空的方法，也就是不执行js函数。
```

**为什么要使用href=”javascript:void(0);”**

javascript:是伪协议，表示url的内容通过javascript执行。void(0)表示不作任何操作，这样会防止链接跳转到其他页面。这么做往往是为了保留链接的样式，但不让链接执行实际操作，

`<a href="javascript：void(0)" onClick="window.open()"> `点击链接后，页面不动，只打开链接

`<a href="#" onclick="javascript:return false;">` 作用一样，但不同浏览器会有差异。



**href=”javascript:void(0);”与href=”#"的区别**

`<a href="javascript:void(0)">点击</a>`点击链接后不会回到网页顶部 `<a href="#">点击</a>` 点击后会回到网面顶部

"＃"其实是包含了位置信息，例如默认的锚点是＃top 也就是网页的上端

而javascript:void(0) 仅仅表示一个死链接这就是为什么有的时候页面很长浏览链接明明是＃可是跳动到了页首

而javascript:void(0) 则不是如此所以调用脚本的时候最好用void(0)

 

使用javascript的方法

`<a href="#" onclick="javascript:方法;return false;">文字</a>`

`<a href="javascript:void(0)" onclick="javascript:方法;return false;">文字</a>`

**补充 <a href="javascript:func();"这样点击a标签就可以执行func()函数了。**



## 事件循环

[深入理解javascript中的事件循环event-loop](https://www.cnblogs.com/xiaohuochai/p/8527618.html)



## [Cookie、session和localStorage、以及sessionStorage之间的区别](https://www.cnblogs.com/zr123/p/8086525.html)

**一、Cookie、session和localStorage的区别** 
cookie的内容主要包括：名字、值、过期时间、路径和域。路径与域一起构成cookie的作用范围。若不设置时间，则表示这个cookie的生命期为浏览器会话期间，关闭浏览器窗口，cookie就会消失。这种生命期为浏览器会话期的cookie被称为会话cookie。 
会话cookie一般不存储在硬盘而是保存在内存里，当然这个行为并不是规范规定的。若设置了过期时间，浏览器就会把cookie保存到硬盘上，关闭后再打开浏览器这些cookie仍然有效直到超过设定的过期时间。对于保存在内存里的cookie，不同的浏览器有不同的处理方式session机制。 
当程序需要为某个客户端的请求创建一个session时，服务器首先检查这个客户端的请求里是否已包含了一个session标识（称为session id），如果已包含则说明以前已经为此客户端创建过session，服务器就按照session id把这个session检索出来使用（检索不到，会新建一个），如果客户端请求不包含session id，则为客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，这个session id将被在本次响应中返回给客户端保存。保存这个session id的方式可以采用cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。

**二、cookie和session的区别：** 
*1、cookie数据存放在客户的浏览器上，session数据放在服务器上 
2、cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑*到安全应当使用session 
3、session会在一定时间内保存在服务器上，当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用cookie 
4、单个cookie保存的数*据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie 
5、建议将登录信息等重要信息存放为session，其他信息如果需要保留，可以放在cookie中 
6、session保存在服务器，客户端不知道其中的信心；cookie保存在客户端，服务器能够知道其中的信息 
7、session中保存的是对象，cookie中保存的是字符串 
8、session不能区分路径，同一个用户在访问一个网站期间，所有的session在任何一个地方都可以访问到，而cookie中如果设置了路径参数，那么同一个网站中不同路径下的cookie互相是访问不到的*

**三、web Storage和Cookie的区别** 
Web Storage的概念和cookie相似，区别是它是为了更大容量存储设计的，cookie的大小是受限的，并且每次请求一个新的页面的时候cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可跨域调用。 
除此之外，web storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。 
但是cookie也是不可或缺的，cookie的作用是与服务器进行交互，作为http规范的一部分而存在的，而web Storage仅仅是为了在本地“存储”数据而生 
sessionStorage、localStorage、cookie都是在浏览器端存储的数据，其中sessionStorage的概念很特别，引入了一个“浏览器窗口”的概念，sessionStorage是在同源的同窗口中，始终存在的数据，也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一个页面，数据仍然存在，关闭窗口后，sessionStorage就会被销毁，同时“独立”打开的不同窗口，即使是同一页面，sessionStorage对象也是不同的

Web Storage带来的好处： 
1、减少网络流量：一旦数据保存在本地之后，就可以避免再向服务器请求数据，因此减少不必要的数据请求，减少数

据在浏览器和服务器间不必要的来回传递 
2、快速显示数据：性能好，从本地读数据比通过网络从服务器上获得数据快得多，本地数据可以及时获得，再加上网

页本身也可以有缓存，因此整个页面和数据都在本地的话，可以立即显示 
3、临时存储：很多时候数据只需要在用户浏览一组页面期间使用，关闭窗口后数据就可以丢弃了，这种情况使用sessionStorage非常方便

**四、浏览器本地存储与服务器端存储的区别** 
其实数据既可以在浏览器本地存储，也可以在服务器端存储 
浏览器可以保存一些数据，需要的时候直接从本地存取，sessionStorage、localStorage和cookie都是由浏览器存储在本地的数据 
服务器端也可以保存所有用户的所有数据，但需要的时候浏览器要向服务器请求数据。 
1、服务器端可以保存用户的持久数据，如数据库和云存储将用户的大量数据保存在服务器端 
2、服务器端也可以保存用户的临时会话数据，服务器端的session机制，如jsp的session对象，数据保存在服务器上，

实际上，服务器和浏览器之间仅需传递session id即可，服务器根据session id找到对应用户的session对象，会话数据仅在一段时间内有效，这个时间就是server端设置的session有效期

服务器端保存所有的用户的数据，所以服务器端的开销较大，而浏览器端保存则把不同用户需要的数据分别保存在用户各自的浏览器中，浏览器端一般只用来存储小数据，而非服务可以存储大数据或小数据服务器存储数据安全一些，浏览器只适合存储一般数据

**五、sessionStorage、localStorage和cookie的区别** 
共同点：都是保存在浏览器端、且同源的 
区别： 
1、cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递，而sessionStorage和localStorage不会自动把数据发送给服务器，仅在本地保存。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下 
2、存储大小限制也不同，cookie数据不能超过4K，同时因为每次http请求都会携带cookie、所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大 
3、数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭之前有效；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie：只在设置的cookie过期时间之前有效，即使窗口关闭或浏览器关闭 
4、作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localstorage在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的 
5、web Storage支持事件通知机制，可以将数据更新的通知发送给监听者 
6、web Storage的api接口使用更方便

**六、sessionStorage与页面js数据对象的区别** 
页面中一般的js对象的生存期仅在当前页面有效，因此刷新页面或转到另一页面这样的重新加载页面的情况，数据就不存在了 
而sessionStorage只要同源的同窗口中，刷新页面或进入同源的不同页面，数据始终存在，也就是说只要浏览器不关闭，数据仍然存在



## js的编译

JS的“编译”可能指三种流程：**transform**、**bundle**、**uglify**。

transform：把ES6+的JS转译成es5或以下引擎能运行的代码。
bundle：合并依赖的模块。
uglify：压缩丑化，减小体积。

ES6模块是编译时加载：模块之间的依赖关系，在运行之前（即编译时）就通过静态分析来确定好了的。