## XSS攻击

### 基础概念

XSS（**Cross Site Scripting**）攻击全称**跨站脚本攻击**，是为不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS，XSS是一种经常出现在web应用中的计算机安全漏洞，它允许恶意web用户将代码植入到提供给其它用户使用的页面中。比如这些代码包括**HTML代码**和**客户端脚本**。它可以通过对网页注入可执行代码且成功地被浏览器执行，达到攻击的目的，形成了一次有效XSS攻击，一旦攻击成功，它可以获取用户的联系人列表，然后向联系人发送虚假诈骗信息，可以删除用户的日志等等，有时候还和其他攻击方式同时实施比如SQL注入攻击服务器和数据库、Click劫持、相对链接劫持等实施钓鱼，它带来的危害是巨大的。



### 主要危害

- 盗取各类用户帐号，如机器登录帐号、用户网银帐号、各类管理员帐号
- 控制企业数据，包括读取、篡改、添加、删除企业敏感数据的能力
- 盗窃企业重要的具有商业价值的资料
- 非法转账
- 强制发送电子邮件
- 网站挂马
- 控制受害者机器向其它网站发起攻击



### XSS攻击类型

根据攻击的方式，XSS攻击可以分为三类：反射型XSS、存储型XSS、DOM Based XSS。



#### 反射型XSS

反射型XSS也被称为**非持久性XSS** （No-persistent XSS），这种攻击方式把XSS的 Payload[^①] 写在URL中，通过浏览器直接“反射”给用户。这种攻击方式通常需要诱使用户点击某个恶意链接，才能攻击成功。一般容易出现在搜索页面、留言板块。



**反射型 XSS 的攻击步骤**：

1.攻击者构造出特殊的 URL，其中包含恶意代码。

2.用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。

3.用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。

4.恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

反射型 XSS 跟存储型 XSS 的区别是：存储型 XSS 的恶意代码存在数据库里，反射型 XSS 的恶意代码存在 URL 里。

反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。

由于需要用户主动打开恶意的 URL 才能生效，攻击者往往会结合多种手段诱导用户点击。

POST 的内容也可以触发反射型 XSS，只不过其触发条件比较苛刻（需要构造表单提交页面，并引导用户点击），所以非常少见。

例如：

```js
<body>
  <div id="t"></div>
  <input id="s" type="button" value="获取数据" onclick="test()">
  <script>
    function test() {
      const arr = ['1', '2', '3', '<img src="11" onerror="console.log(window.localStorage)" />'] // 此处可以运行恶意js代码，盗取信息
      const t = document.querySelector('#t')
      arr.forEach(item => {
        const p = document.createElement('p')
        p.innerHTML = item
        t.append(p)
      })
    }
  </script>
</body>
```

假设这是一个留言板块，加载到这一页时，点击“获取数据”按钮，页面会输出：

![反射型XSS](../images/web%E5%AE%89%E5%85%A8.assets/1568422122195.png)

黑客可以轻易盗取存储在你本地浏览器的各种信息（此处为 Local Storage 的信息），进而模拟登陆信息，黑入账户，进行各种操作。



#### 存储型XSS

存储型XSS又被称为**持久性XSS**（**Persistent XSS**），会把黑客输入的恶意脚本存储在服务器的数据库中。当其他用户浏览页面包含这个恶意脚本的页面，用户将会受到黑客的攻击。一个常见的场景就是黑客写下一篇包含恶意JavaScript脚本的博客文章，当其他用户浏览这篇文章时，恶意的JavaScript代码将会执行。



**存储型 XSS 的攻击步骤**：

1.攻击者将恶意代码提交到目标网站的数据库中。

2.用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。

3.用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。

4.恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

例如：

```js
<body>
  <div id="t">
    这是我写的一篇文章
  </div>
  <script>
     console.log(navigator.userAgent) // 此处可以加入恶意代码，盗取信息
  </script>
</body>
```

![存储型XSS](../images/web%E5%AE%89%E5%85%A8.assets/1568422764157.png)

黑客可以在 script 里加入恶意代码，盗取用户信息，此处直接输出了浏览器信息，黑客可以获取到这些信息后，发送到自己的服务器，随意操作。



#### DOM Based XSS

**DOM Based XSS** 是一种利用前端代码漏洞进行攻击的攻击方式。前面的反射型XSS与存储型XSS虽然恶意脚本的存放位置不同，但其本质都是利用后端代码的漏洞。简单来说，通过修改页面 DOM 节点形成的 XSS。

反射型和存储型xss是服务器端代码漏洞造成的，payload是在响应页面中；而 DOM Based中，payload不在服务器发出的HTTP响应页面中，当客户端脚本运行时（渲染页面时），payload才会加载到脚本中执行。



**DOM 型 XSS 的攻击步骤**：

1.攻击者构造出特殊的 URL，其中包含恶意代码。

2.用户打开带有恶意代码的 URL。

3.用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。

4.恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

DOM 型 XSS 跟前两种 XSS 的区别：DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。

例如：

```js
<body>
  <div id="t"></div>
  <input type="text" id="text" value="">
  <input type="button" id="s" value="search" onclick="test()">
  <script>
  function test() {
    const str = document.querySelector('#text').value
    document.querySelector('#t').innerHTML = '<a href="' + str + '" >查找结果</a>'
  }
  </script>
</body>
```

该页面的作用是，在输入框内输入一个内容，跳出查找结果的链接，点击查找结果后，页面会自动跳到百度（毒）页面效果如下：

![DOM Based XSS](../images/web%E5%AE%89%E5%85%A8.assets/1568426601048.png)



但是细心的我们会发现，这字符串拼接有可乘之机啊，输入`" onclick=alert(/XSS/) //`：

![DOM Based XSS](../images/web%E5%AE%89%E5%85%A8.assets/1568426813966.png)

果然，页面执行了我们输入的东西，上面的内容是，第一个双引号闭合掉`href`的第一个双引号，然后插入`onclick`事件，最后注释符 `//`注释掉第二个双引号，点击跳转链接，脚本就被执行了。



### XSS Payload

在黑客 XSS 攻击成功之后，攻击者能够对用户当前浏览的页面植入各种恶意脚本，通过恶意脚本来控制浏览器，这些脚本实质上就是 `JavaScript` 脚本（或者是其他浏览器可以运行的脚本），这种恶意植入且具有完成各种具体功能的恶意脚本就被称为 **XSS Payload**。

一个最常见的 `XSS Payload` ，就是通过浏览器读取 Cookie 对象，进而发起 `Cookie 劫持` 攻击。

一般一个网站为了防止用户无意间关闭页面，重新打开需要重新输入账号密码繁杂的情况下，一般都会把登录信息（登录凭证）加密存储在 CooKie 中，并且设置一个超时时间，在此时间段内，用户利用自己账号信息随意进出该网站。如果该网站遭到 `XSS Payload` ，黑客盗取了该用户的 Cookie 信息，往往意味着该用户的登录凭证丢失了，换句话说，攻击者不需要知道该用户的账号密码，直接利用盗取的 Cookie 模拟凭证，直接登录到该用户的账户。

如下所示，攻击者先在一个社区发表一篇文章：

![1568430196288](../images/web%E5%AE%89%E5%85%A8.assets/1568430196288.png)

你有意无意点了一下 `点我得大奖` 这个时候，`XSS Payload` 就生效了：

![1568430082470](../images/web%E5%AE%89%E5%85%A8.assets/1568430082470.png)

该`XSS Payload`会请求一个 img 图片，图片请求地址即为黑客的服务器地址， url 参数带上 Cookie ，我们在后台服务器接收到了这个请求：

![1568430325078](../images/web%E5%AE%89%E5%85%A8.assets/1568430325078.png)

这个时候，黑客就可以获取到此 Cookie，然后模拟 CooKie 登陆。

当然传输的内容可以是任何内容，只要能获取到的，全都可以传输给后台服务器。

如何利用窃取的 Cookie 登陆目标用户的账户呢？这和`利用自定义Cookie访问网站`的过程是一样的，参考如下：

当没有登陆的时候，Cookie 内容是空的：

![1568430696609](../images/web%E5%AE%89%E5%85%A8.assets/1568430696609.png)

当我们手动添加 Cookie 后，登陆的内容如下：

![1568430833409](../images/web%E5%AE%89%E5%85%A8.assets/1568430833409.png)

此时，我们就已经登陆上了该用户的账户。

所以，通过 XSS 攻击，可以完成 `Cookie 劫持` 攻击，直接登陆进用户的账户。

其实都不需要带上参数，黑客就能获取到所有数据，这是因为当前 Web 中，Cookie 一般是用户凭证，浏览器发起的所有请求都会自动带上 Cookie 。

![1568431640490](../images/web%E5%AE%89%E5%85%A8.assets/1568431640490.png)

![1568431658256](../images/web%E5%AE%89%E5%85%A8.assets/1568431658256.png)

Cookie 的 `HttpOnly` 标识可以有效防止 `Cookie 劫持`，我们会在下面具体介绍。



### 防范手段

- [**网页安全政策（CSP）**](#CSP (网页安全政策))

  网页安全政策（Content Security Policy，缩写 CSP）可以防止 XSS 攻击。

- **入参字符过滤**

　　在源头控制，把输入的一些不合法的东西都过滤掉，从而保证安全性。如移除用户提交的的DOM属性如onerror，移除用户上传的Style节点，`<iframe>` , `<script>` ，`<a>` 节点等

- **出参进行编码**

　　如果源头没控制好，就得后期补救了：像一些常见的符号，如<>在输出的时候要对其进行转换编码，这样做浏览器是不会对该标签进行解释执行的，同时也不影响显示效果。例如：对<>做编码如：`<` 用: `&lt;`, `>` 用: `&gt; ` 来代替。

- **入参长度限制**

　　通过以上的案例我们不难发现xss攻击要能达成往往需要较长的字符串，因此对于一些可以预期的输入可以通过限制长度强制截断来进行防御。

- **设置cookie httponly为true**

　　Cookie由服务端来写并将httpOnly设置成为“true”，Cookie中设置了"HttpOnly"属性，那么通过程序(JS脚本、Applet等)将无法读取到Cookie信息，这样能有效的防止XSS攻击。

![httponly](../images/web%E5%AE%89%E5%85%A8.assets/1568428869168.png)



![httponly](../images/web%E5%AE%89%E5%85%A8.assets/1568428771070.png)



## CSP (网页安全政策)

### 简介

CSP 的实质就是白名单制度，开发者明确告诉客户端，哪些外部资源可以加载和执行，等同于提供白名单。它的实现和执行全部由浏览器完成，开发者只需提供配置。

CSP 大大增强了网页的安全性。攻击者即使发现了漏洞，也没法注入脚本，除非还控制了一台列入了白名单的可信主机。

两种方法可以启用 CSP。

- 通过 HTTP 头信息的`Content-Security-Policy`的字段。

  ```http
  Content-Security-Policy: script-src 'self'; object-src 'none';
  style-src cdn.example.org third-party.org; child-src https:
  ```

  ![Content-Security-Policy](../images/web%E5%AE%89%E5%85%A8.assets/1568433337870.png)

- 通过网页的`<meta>`标签

  ```html
  <meta http-equiv="Content-Security-Policy" content="script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:">
  ```



上面代码中，CSP 做了如下配置。

> - 脚本：只信任当前域名
> - `<object>`标签：不信任任何URL，即不加载任何资源
> - 样式表：只信任`cdn.example.org`和`third-party.org`
> - 框架（frame）：必须使用HTTPS协议加载
> - 其他资源：没有限制

启用后，不符合 CSP 的外部资源就会被阻止加载。

Chrome 的报错信息。

![1568433591189](../images/web%E5%AE%89%E5%85%A8.assets/1568433591189.png)



Firefox 的报错信息。

![1568433650612](../images/web%E5%AE%89%E5%85%A8.assets/1568433650612.png)



### 限制选项

CSP 提供了很多限制选项，涉及安全的各个方面。

#### 2.1 资源加载限制

以下选项限制各类资源的加载。

> - **script-src**：外部脚本
> - **style-src**：样式表
> - **img-src**：图像
> - **media-src**：媒体文件（音频和视频）
> - **font-src**：字体文件
> - **object-src**：插件（比如 Flash）
> - **child-src**：框架
> - **frame-ancestors**：嵌入的外部资源（比如<frame>、<iframe>、<embed>和<applet>）
> - **connect-src**：HTTP 连接（通过 XHR、WebSockets、EventSource等）
> - **worker-src**：`worker`脚本
> - **manifest-src**：manifest 文件

#### 2.2 default-src

`default-src`用来设置上面各个选项的默认值。

> ```http
> Content-Security-Policy: default-src 'self'
> ```

上面代码限制**所有的**外部资源，都只能从当前域名加载。

如果同时设置某个单项限制（比如`font-src`）和`default-src`，前者会覆盖后者，即字体文件会采用`font-src`的值，其他资源依然采用`default-src`的值。

#### 2.3 URL 限制

有时，网页会跟其他 URL 发生联系，这时也可以加以限制。

> - **frame-ancestors**：限制嵌入框架的网页
> - **base-uri**：限制`<base#href>`
> - **form-action**：限制`<form#action>`

#### 2.4 其他限制

其他一些安全相关的功能，也放在了 CSP 里面。

> - **block-all-mixed-content**：HTTPS 网页不得加载 HTTP 资源（浏览器已经默认开启）
> - **upgrade-insecure-requests**：自动将网页上所有加载外部资源的 HTTP 链接换成 HTTPS 协议
> - **plugin-types**：限制可以使用的插件格式
> - **sandbox**：浏览器行为的限制，比如不能有弹出窗口等。

#### 2.5 report-uri

有时，我们不仅希望防止 XSS，还希望记录此类行为。`report-uri` 就用来告诉浏览器，应该把注入行为报告给哪个网址。

> ```http
> Content-Security-Policy: default-src 'self'; ...; report-uri /my_amazing_csp_report_parser;
> ```

上面代码指定，将注入行为报告给`/my_amazing_csp_report_parser`这个 URL。

浏览器会使用`POST`方法，发送一个JSON对象，下面是一个例子。

> ```json
> {
>   "csp-report": {
>     "document-uri": "http://example.org/page.html",
>     "referrer": "http://evil.example.com/",
>     "blocked-uri": "http://evil.example.com/evil.js",
>     "violated-directive": "script-src 'self' https://apis.google.com",
>     "original-policy": "script-src 'self' https://apis.google.com; report-uri http://example.org/my_amazing_csp_report_parser"
>   }
> }
> ```



### Content-Security-Policy-Report-Only

除了`Content-Security-Policy`，还有一个`Content-Security-Policy-Report-Only`字段，表示不执行限制选项，只是记录违反限制的行为。

它必须与`report-uri`选项配合使用。

> ```http
> Content-Security-Policy-Report-Only: default-src 'self'; ...; report-uri /my_amazing_csp_report_parser;
> ```



### 选项值

每个限制选项可以设置以下几种值，这些值就构成了白名单。

> - 主机名：`example.org`，`https://example.com:443`
> - 路径名：`example.org/resources/js/`
> - 通配符：`*.example.org`，`*://*.example.com:*`（表示任意协议、任意子域名、任意端口）
> - 协议名：`https:`、`data:`
> - 关键字`'self'`：当前域名，需要加引号
> - 关键字`'none'`：禁止加载任何外部资源，需要加引号

多个值也可以并列，用空格分隔。

> ```http
> Content-Security-Policy: script-src 'self' https://apis.google.com
> ```

如果同一个限制选项使用多次，只有第一次会生效。

> ```http
> # 错误的写法
> script-src https://host1.com; script-src https://host2.com
> 
> # 正确的写法
> script-src https://host1.com https://host2.com
> ```

如果不设置某个限制选项，就是默认允许任何值。



### script-src 的特殊值

除了常规值，`script-src`还可以设置一些特殊值。注意，下面这些值都必须放在单引号里面。

> - **'unsafe-inline'**：允许执行页面内嵌的`&lt;script>`标签和事件监听函数
> - **unsafe-eval**：允许将字符串当作代码执行，比如使用`eval`、`setTimeout`、`setInterval`和`Function`等函数。
> - **nonce值**：每次HTTP回应给出一个授权token，页面内嵌脚本必须有这个token，才会执行
> - **hash值**：列出允许执行的脚本代码的Hash值，页面内嵌脚本的哈希值只有吻合的情况下，才能执行。

nonce值的例子如下，服务器发送网页的时候，告诉浏览器一个随机生成的token。

> ```http
> Content-Security-Policy: script-src 'nonce-EDNnf03nceIOfn39fn3e9h3sdfa'
> ```

页面内嵌脚本，必须有这个token才能执行。

> ```javascript
> <script nonce=EDNnf03nceIOfn39fn3e9h3sdfa>
>   // some code
> </script>
> ```

hash值的例子如下，服务器给出一个允许执行的代码的hash值。

> ```http
> Content-Security-Policy: script-src 'sha256-qznLcsROx4GACP2dm0UCKCzCG-HiZ1guq6ZZDob_Tng='
> ```

下面的代码就会允许执行，因为hash值相符。

> ```markup
> <script>alert('Hello, world.');</script>
> ```

注意，计算hash值的时候，`<script>` 标签不算在内。

除了`script-src`选项，nonce值和hash值还可以用在`style-src`选项，控制页面内嵌的样式表。



### 注意点

（1）`script-src`和`object-src`是必设的，除非设置了`default-src`。

因为攻击者只要能注入脚本，其他限制都可以规避。而`object-src`必设是因为 Flash 里面可以执行外部脚本。

（2）`script-src`不能使用`unsafe-inline`关键字（除非伴随一个nonce值），也不能允许设置`data:`URL。

下面是两个恶意攻击的例子。

> ```js
> <img src="x" onerror="evil()">
> <script src="data:text/javascript,evil()"></script>
> ```

（3）必须特别注意 JSONP 的回调函数。

> ```js
> <script src="/path/jsonp?callback=alert(document.domain)//">
> </script>
> ```

上面的代码中，虽然加载的脚本来自当前域名，但是通过改写回调函数，攻击者依然可以执行恶意代码。



## CSRF (跨站请求伪造)

**CSRF**（**Cross-site request forgery**）**跨站请求伪造**，也被称为“One Click Attack”或者Session Riding，通常缩写为**CSRF**或者**XSRF**，是一种对网站的恶意利用。尽管听起来像跨站脚本（[XSS](https://baike.baidu.com/item/XSS)），但它与XSS非常不同，XSS利用站点内的信任用户，而CSRF则通过伪装成受信任用户的请求来利用受信任的网站。Cookie 往往用来存储用户的身份信息，恶意网站可以设法伪造带有正确 Cookie 的 HTTP 请求，这就是 CSRF 攻击。



### CSRF产生的过程

用户需要获取操作的权限，目标网站服务器会要求一次验证，通常都是一次账号密码登录。服务器验证通过之后，会在浏览器写一个会话ID，来标识用户的身份。这是一种隐式验证的方法，用户只要验证一次获取到会话ID之后，在会话ID未过期的一段时间内的请求操作是不需要再次验证的。但服务器只能判断请求是来源于这个授权过的浏览器，而不能判断请求是否是用户发起或者是用户授权的。所以当用户浏览了恶意网站之后，恶意网站就可以给服务器发起带有命令的请求，尝试改变服务器中的数据值。

### CSRF的攻击对象

CSRF的攻击对象也就是我们要保护的对象。CSRF利用的是授权浏览器向目标网站服务器发送任何请求时，都会自动带上会话ID或者cookie进行身份验证。但是由于浏览器同源策略的限制，恶意网站无法看到目标网站的cookie或者回话ID，也没有办法解析返回的内容，所以恶意所能做的就是给服务器发送请求，以执行请求中所描述的命令，在服务器端直接改变数据的值，而非窃取服务器中的数据

### CSRF的攻击手段

1. 利用天生具有跨域能力的标签-- `<script>`,`<a>`,`<img>`等，发起get请求

```html
<img src="http://www.target.com/order/pay?money=10000&to=34j34">
```

1. 利用iframe和javascipt发起post请求

```html
<iframe name="steal" style="display: none;"></iframe>
<form method="post" action="http:www.xxxx.com/trading/product/addCartProduct" name="transfer" target="steal">
　　　　　　　　<input type="hidden" name="id" value="b21a59eaa7e604b44fe8a3bf">
　　　　　　　　<input type="hidden" name="money" value="1000">
    <input type="submit" value="提交">
    　　　　　　
</form>
window.onload=function(){
    function steal()
    {
      var iframe = window.frames['steal'].document;
      var form = document.getElementsByTagName('form')[0];
      form.submit();
    }
    steal();
}
```

### CSRF的防御措施

**1. 涉及到数据更改的操作服务器严格使用post请求而不是get请求**
**2. 验证HTTP Referer 字段**
根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址。恶意网站只能在他自己的网站伪造请求，所以验证referer应该是可以过滤掉一部分CSRF攻击的。但是使用Rerferer值，就相当于把安全性交给了第三方，浏览器实现referer的方式不能保证没有漏洞。据说IE6是可以修改referer的值的。另外referer会记录下用户的访问来源，有些用户会觉得是侵犯了其隐私，而且用户是可以设置使用浏览器时不再提供referer的。（所以说这种方法虽然简单但是不可靠）
**3. 在请求地址中添加token**
CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求。服务器验证地址中加密的token，可以比较有效地避免攻击
**4. HTTP头自定义属性验证**
这个是token验证的变种，可以利用 XMLHttpRequest 这个类，一次性给ajax请求加上 特定的 HTTP 头属性，并把 token 值放入其中。
**5 显式验证，添加验证码验证，密码验证等。**
CSRF 攻击之所以能够成功，也是由于隐式验证，服务器无法判断请求真实地由用户发起和用户授权的。



------

[^①]: payload：有效载荷，有效负荷，有效载重。举个货运行业的例子。比如有一位客户需要支付一笔费用委托货车司机运送一车石油，石油本身的重量、车子的重量、司机的重量等等，这些都属于`载重(load)`。但是对于该客户来说，他关心的只有石油的重量，所以石油的重量是`有效载重(pay-load，也就是付费的重量)`。**所以抽象一下，payload 可以理解为一系列信息中最为关键的信息。**



## 参考

https://www.cnblogs.com/stefanieszx11/p/8602138.html

https://www.cnblogs.com/liangyin/p/9632653.html

https://www.cnblogs.com/mao2080/p/9460397.html

https://www.cnblogs.com/liangyin/p/9638235.html

https://www.freebuf.com/articles/web/185654.html

[web安全二，CSRF攻击](https://segmentfault.com/a/1190000013440532)