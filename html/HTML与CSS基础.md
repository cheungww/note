## HTML（Hypertext Markup Language）的发展

- 1993年6月：HTML第一个版本发布。
- 1995年11月：HTML2.0
-  1997年1月：HTML3.2（W3C推荐）
- 1999年12月：HTML4.01（W3C推荐）
- 2000年底：XHTML1.0（W3C推荐）
-  2014年10月：HTML5（W3C推荐）



## doctype

为了让浏览器知道我们使用的HTML版本我们还需要在网页的最上边添加一个doctype声明，来告诉浏览器网页的版本。

**html4**

- 过渡版

  ```html
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  ```

- 严格版

  ```html
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
  ```

- 框架集

  ```html
  <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN"
  "http://www.w3.org/TR/html4/frameset.dtd">
  ```



**xhtml1.0**

- 过渡版

  ```html
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "
  http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
  ```

- 严格版

  ```html
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
  ```

- 框架集

  ```html
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN"
  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
  ```



**html5**

```html
<!DOCTYPE html>
```

现在我们使用的都是 html5 的文档声明



## `<meta>`

- 作用：
  - 标签可提供有关页面的元信息，比如针对搜索引擎和更新频度的描述和关键词。
  - 标签位于文档的头部，不包含任何内容。 标签的属性定义了与文档相关联的名称/值对。

- meta的用法

  - 设置页面的字符集

    ```html
    <meta charset="utf-8">
    ```

  - 设置网页的描述

    ```html
    <meta name="description" content="">
    ```

  - 设置网页的关键字

    ```html
    <meta name="keywords" content="">
    ```

  - 请求的重定向

    ```html
    <meta http-equiv="refresh" content="5;url=地址"/>
    ```



## 实体

- 在HTML中预留了一些字符。
- 这些预留字符是不能在网页中直接使用的。
- 比如<和>,我们不能直接在页面中使用 `<` 和 `>` 号，因为浏览器会将它解析为html标签。
- 为了可以使用这些预留字符，我们必须在html中使用字符实体。
- 语法: &实体名;

**常见HTML字符实体**

| 显示结果 | 描述        | 实体名称             | 实体编号  |
| :------- | :---------- | :------------------- | :-------- |
|          | 空格        | `&nbsp;`             | `&#160;`  |
| <        | 小于号      | `&lt; `              | `&#60;`   |
| >        | 大于号      | `&gt;`               | `&#62;`   |
| &        | 和号        | `&amp;`              | `&#38;`   |
| "        | 引号        | `&quot;`             | `&#34;`   |
| '        | 撇号        | `&apos; ` (IE不支持) | `&#39;`   |
| ￠       | 分          | `&cent;`             | `&#162;`  |
| £        | 镑          | `&pound; `           | `&#163;`  |
| ¥        | 人民币/日元 | `&yen;`              | `&#165;`  |
| €        | 欧元        | `&euro;`             | `&#8364;` |
| §        | 小节        | `&sect;`             | `&#167;`  |
| ©        | 版权        | `&copy;`             | `&#169;`  |
| ®        | 注册商标    | `&reg;`              | `&#174;`  |
| ™        | 商标        | `&trade;`            | `&#8482;` |
| ×        | 乘号        | `&times;`            | `&#215;`  |
| ÷        | 除号        | `&divide;`           | `&#247;`  |



## 选择器

### 选择器（selector）

选择器会告诉浏览器：网页上的哪些元素需要设置什么样的样式。

1. **元素选择器**
   • 元素选择器（标签选择器），可以根据标签的名字来从页面中选取指定的元素。
   • 语法：**标签名** { }
   • 比如p则会选中页面中的所有p标签，h1会选中页面中的所有h1标签。
2. **类选择器**
   • 类选择器，可以根据元素的class属性值选取元素。
   • 语法：**.className** { }
   • 比如.hello会选中页面所有class属性为hello的元素。
3. **ID选择器**
   • ID选择器，可以根据元素的id属性值选取元素。
   • 语法：**#id** { }
   • 比如#box会选中页面中id属性值为box的元素，和class属性不同，id属性是不能重复的。
4. **复合选择器**
   • 复合选择器，可以同时使用多个选择器，这样可以选择同时满足多个选择器的元素。
   • 语法：  **选择器1选择器2** {}
   • 例如div.box1会选中页面中具有box1这个class的div元素。
5. **群组选择器**
   • 群组选择器，可以同时使用多个选择器，多个选择器将被同时应用指定的样式。
   • 语法：**选择器1, 选择器2, 选择器3** { }
   • 比如p,.hello,#box会同时选中页面中p元素，class为hello的元素，id为box的元素。
6. **通用选择器**
   • 通用选择器，可以同时选中页面中的所有
   元素。
   • 语法： ***** { }
7. **后代选择器**
   • 后代选择器可以根据标签的关系，为处在元素内部的代元素设置样式。
   • 语法：**祖先元素 后代元素 后代元素** { }
   • 比如p strong 会选中页面中所有的p元素内的strong元素。



### 伪类和伪元素

> 详见：[前端面试题-伪类和伪元素](https://segmentfault.com/a/1190000013737796) 和 [详解 CSS 属性 - 伪类和伪元素的区别](https://segmentfault.com/a/1190000000484493) 

• 有时候，你需要选择本身没有标签，但是仍然易于识别的网页部位，比如段落首行或鼠标滑过的连接。CSS为他们提供一些选择器：伪类和伪元素。

- **给链接定义样式**
  有四个伪类可以让你根据访问者与该链接的交互方式，将链接设置成4种不同的状态。

  - 正常链接:

    a:link

  - 访问过的链接

    a:visited（只能定义字体颜色）

  - 鼠标滑过的链接

     `a:hover`

  -  正在点击的链接

     a:active

- **其他**

  - 获取焦点

     :focus

  - 指定元素前
    :before

  - 指定元素后
     :after

  - 选中的元素
    ::selection

- **给段落定义样式**

  - 首字母
    :first-letter
  - 首行
    :first-line

- **属性选择器**
  • 属性选择器可以挑选带有特殊属性的标签。
  • 语法:
  [属性名]
  [属性名="属性值"]
  [属性名~="属性值"]
  [属性名|="属性值"]
  [属性名^="属性值"]
  [属性名$="属性值"]
  [属性名*="属性值"]

- **子元素选择器**
  • 子元素选择器可以给另一个元素的子元素设置样式。
  • 语法：**父元素 > 子元素** {}
  • 比如body > h1将选择body子标签中的所
  有h1标签。

- **其他子元素选择器**

  - 选择第一个子标签

    :first-child

  - 选择最后一个子标签
    :last-child

  - 选择指定位置的子元素
    :nth-child

  - 选择指定类型的子元素
    :first-of-type
    :last-of-type
    :nth-of-type

- **兄弟选择器**
  • 除了根据祖先父子关系，还可以根据兄弟关系查找元素。
  • 语法:

  - 查找后边一个兄弟元素
    **兄弟元素 + 兄弟元素** {}
  - 查找后边所有的兄弟元素
    **兄弟元素 ~ 兄弟元素** {}

- **否定伪类**
  • 否定伪类可以帮助我们选择不是其他东西的某件东西。
  • 语法: 比如p:not(.hello)表示选择所有的p元素但是class为hello的除外。

### 继承

• 就像父亲的财产会遗传给儿子一样，在CSS中祖先元素的样式同样也会被子元素继承。
• 继承是指应用在一个标签上的那些CSS样式会同时被应用到其内嵌标签上。
• 比如为父元素设置了字体颜色，子元素也会应用上相同的颜色。
• 当然并不是所有的样式都会被继承

### 选择器的权重

在页面中使用CSS选择器选中元素时，经常都是一个元素同时被多个选择器选中。
• 比如：
– body h1
– h1
• 上边的两个选择器都会选择h1元素，如果两个选择器设置的样式不一致那还好不会产生冲突，但是如果两个选择器设置的是同一个样式，这样h1到底要应用那个样式呢？CSS中会默认使用权重较大的样式，权重又是如何计算的呢？



#### 权重的计算

不同的选择器有不同的权重值：（权重：高 --> 低）

| 选择器 | !important | [内联样式](#css的内联、外联、嵌套写法) | id选择器 | 类、属性、伪类选择器 | 元素选择器 | 通配符、子选择器、相邻选择器 | **继承**的样式 |
| ------ | :--------: | :------------------------------------: | :------: | :------------------: | :--------: | :--------------------------: | :------------: |
| 权重   |    最高    |                  1000                  |   100    |          10          |     1      |              0               |    没有权重    |

> 属性选择器指的是:根据元素的属性及属性值来选择元素，比如button的type属性等。伪类选择器: :active :focus等选项。
>
> 伪元素选择器： :before :after 等

计算权重需要将一个样式的全部选择器相加，比如上边的body h1的权重是20，h1的权重是10，所以第一个选择器设置的样式会优先显示。



#### 权重规则总结:

> 1. **!**important 优先级最高，但也会被权重高的 **!**important 所覆盖
> 2. **内联**样式总会**覆盖**外部样式表的任何样式(除了!important)
> 3. 单独使用一个选择器的时候，不能跨等级使css规则生效
> 4. 如果两个权重不同的选择器作用在同一元素上，权重值高的css规则生效
> 5. 如果两个相同权重的选择器作用在同一元素上：以后面出现的选择器为最后规则.
> 6. 权重相同时，与元素距离近的选择器生效
> 7. 继承的样式是没有权重的，父类的 !important是对父类样式优先级最高，但对子类是没有权重的



#### 权重规则

1. **不推荐使用!important**

   不推荐使用`!important`，因为`!important`根本没有结构与上下文可言，并且很多时候权重的问题，就是因为不知道在哪里定义了一个`!important`而导致的。

   **覆盖 !important:**

   虽然我们应该尽量避免使用 !important ，但你应该知道如何覆盖 !important，加点权重就可以实现，codepen的[demo](https://codepen.io/OBKoro1/pen/ZoVxgQ)

   ```html
   //!important 优先级最高，但也会被权重高的 !important所覆盖
   <button id="a" class="a">aaa</button>
   #a{
   background: blue  !important;  /* id的 !important 覆盖class的 !important*/
   }
   .a{
   background: red  !important;
   }
   ```

   

2. **内联样式总会覆盖外部样式表的任何样式,会被`!important`覆盖**

   

3. **单独使用一个选择器的时候，不能跨等级使css规则生效**

   **无论多少个class组成的选择器，都没有一个ID选择器权重高**。类似的，无论多少个元素组成的选择器，都没有一个class选择器权重高、无论多少个ID组成的选择器，都没有行内样式权重高。

   codepen的[demo](https://codepen.io/OBKoro1/pen/ZoVxgQ)

   ```txt
   在demo中使用了11个class组成一个选择器，最后还是一个ID选择器，设置的样式生效。
   ```

   所以权重是在双方处于同一等级的情况下，才开始对比。

   

4. **如果两个权重不同的选择器作用在同一元素上，权重值高的css规则生效**

   选择器可能会包含一个或者多个与权重相关的计算点，若经过权重值计算得到的权重值越大，则认为这个选择器的权重高。举一个简单的栗子:

   ```css
   .test #test{ } // id 100+class 10=110;
   .test #test span{} // id 100+class 10+span 1=111;
   .test #test .sonClass{} // id 100+class 10+class 10=120; //生效
   ```

5. **如果两个相同权重的选择器作用在同一元素上：以后面出现的选择器为最后规则.**

   ```html
   <div id="app">
     <div class="test" id="test">
                         <span >啦啦啦</span>
     </div>
   </div>
   #test span{
   color:blue;
   }
   #app span{ // 生效 因为后面出现
   color: red;
   }
   ```

   

6. **权重相同时，与元素距离近的选择器生效**

   比如不同的style表，head头部等,来看下面的栗子：

   ```html
   #content h1 { // css样式表中
   padding: 5px;
   }
   <style type="text/css">
   #content h1 { // html头部 因为html头部离元素更近一点，所以生效
   padding: 10px;
   }
   </style>
   ```

7. **继承的样式是没有权重的**，父类的 !important是对父类样式优先级最高，但对子类是没有权重的

   ```html
   <style>
     p { color: black !important; }
   
     span { color: blue; }
   
     p span { color: yellow; }
   
     #s1 { color: orange; }
   
     p .c1 { color: red; }
   </style>
   <p><span id="s1" class="c1">文字</span></p>
   <!-- “文字” 的颜色是 橙色（orange） -->
   ```

   

参考

> [你对CSS权重真的足够了解吗？](https://segmentfault.com/a/1190000014947296) 
>
> [CSS中选择器的权重值](https://segmentfault.com/a/1190000012941923) 



## css的内联、外联、嵌套写法

1，内联样式写法

```html
<p style="color:red;"></p>
```

2，外联样式写法

```html
<link href="你的css文件地址" rel="stylesheet" type="text/css"></link>
```

3，嵌套样式写法

在html界面 `<head>` 里面写一下代码

```html
<style type="text/css">
.p {
	color:red;
}
</style>
```



## 文本标签

### `<em>` 和 `<strong>`

em标签用于表示一段内容中的着重点。
• strong标签用于表示一个内容的重要性。
• 这两个标签可以单独使用，也可以一起使用。
• 通常em显示为斜体，而strong显示为粗体。

### `<i>` 和 `<b>` 

• i标签会使文字变成斜体。
• b标签会使文字变成粗体。
• 这两个标签和em和strong类似，但是这两个标签没有语义。
• 所以根据html5标准，当我们只想设置文本特殊显示，而不需要强调内容时就可以使用i和b标签

### `<small>` 

• small标签表示细则一类的旁注，通常包括免责声明、注意事项、法律限制、版权信息等。
• 浏览器在显示small标签时会显示一个比父元素小的字号。

```html
<p><small>&copy;2016 尚硅谷. 保留所有权利.</small></p>
```

### `<cite>` 

• 使用cite标签可以指明对某内容的引用或参考。例如，戏剧、文章或图书的标题，歌曲、电影、照片或雕塑的名称等。

```html
<p>
<cite>《七龙珠》</cite>讲的是召唤神龙的故事。
</p>
```

### `<blockquote>` 和 `<q>` 

• blockquote和q表示标记引用的文本。
• blockquote用于长引用，q用于短引用。
• 在两个标签中还可以使用cite属性来表示引用的地址。

```html
孟子曾经说过：
<blockquote>天将降大任于是人也...</blockquote>
他说的真对啊！
<p>孔子曾经说过：<q>学而时习之不亦说乎</q></p>
```

### `<sup>`  和 `<sub>` 

• sup和sub用于定义上标和下标。
• 上标主要用于表示类似于103中的3。
• 下标则用于表示类似余H2O中的2。

### `<ins>` 和 `<del>` 

• ins表示插入的内容，显示时通常会加上 下划线。
• del表示删除的内容，显示时通常会加上 删除线。

### `<code>` 和 `<pre>` 

• 如果你的内容包含代码示例或文件名，就 可以使用code元素。

• pre元素表示的是预格式化文本，可以使用 pre包住code来表示一段代码。 

```html
<pre>
  <code>
  function fun(){
  alert("hello");
  }
  </code>
</pre>
```



### 有序列表

 • 使用ol和li来创建一个有序列表。 

```html
<ol>
<li>列表项1</li>
<li>列表项2</li>
<li>列表项3</li>
</ol>
```



### 无序列表

• 使用ul和li来创建一个无序列表。

```html
<ul>
<li>列表项1</li>
<li>列表项2</li>
<li>列表项3</li>
</ul>
```



### 定义列表

• 使用dl、dd、dt来创建一个定义列表。

```html
<dl>
<dt>定义项1</dt>
<dd>定义描述1</dd>
<dt>定义项2</dt>
<dd>定义描述2</dd>
<dt>定义项3</dt>
<dd>定义描述3</dd>
</dl>
```



## 文本格式化

### 单位

• **px**
– 如果我们将一个图片放大的话，我们会发现一个图片是有一个一个的小色块构成的，这一个小色块就是一个像素，也就是1px，对于不同的显示器来说一个像素的大小是不同的。
• **百分比**
– 也可以使用一个百分数来表示一个大小，百分比是相对于父元素来说的，如果父元素使用的大小是16px，则100%就是16px，200%就是32px。
• **em**
– em和百分比类似，也是相对于父元素说的，1em就相当于100%，2em相当于200%，1.5em相当于150%。

### 颜色

• 在CSS中可以直接使用颜色的关键字来代表一种颜色。
• 17中颜色
– aqua、black、blue、fuchsia、gray、green、lime、maroon、navy、olive、orange、purple、red、silver、teal、white、yellow。
• 还有147种svg颜色，这里就不一 一列举了

### 十六进制颜色

• 用的最多的颜色是十六进制符号。一个颜色值，比如：#6600FF实际上包含了三组十六进制的数字。
• 上边的例子中66代表红色的浓度，00代表绿色的浓度，FF代表了蓝色的浓度。最后的颜色是由这些指定浓度的红绿蓝混合而成的。
• 如果每一组数中的两个数字都相同，就可以把十六进制的数字缩短为只有3个字符，如将**#6600FF**缩短为**#60F**。

### RGB值

• 也可以使用计算机中常用的RGB值来表示颜色。可以使用0~255的数值，也可以使用0%~100%的百分比数。
– RGB(100%,0%,0%)
– RGB(0,255,0)
• 第一个数表示红色的浓度，第二个数表示绿色浓度，第三个数表示蓝色的浓度。

### RGBA

• RGBA表示一个颜色和RGB类似，只不过比RGB多了一个A（alpha）来表示透明度，透明度需要一个0-1的值。0表示完全透明，1表示完全不透明。
– RGBA(255,100,5,0.5)

### 文字大小

• font-size用来指定文字的大小。

### 字体(一)

• 通过font-family可以指定标签中文字使用的字体。
• 例如：`p{font-family:Arial}` 

​	– 上边这行代码指定了p标签中使用名为arial作为字体
• 一般来说只有用户计算机中安装了我们指定的字体时，它才会显示，否则这行代码是没有意义的。

### 字体(二)

• 通过font-family可以同时指定多个字体。
• 例如：`p{font-family:Arial , Helvetica , sans-serif}` 
• 如上我实际上指定了三种字体，那么到底使用的是哪个呢？浏览器会优先使用第一个，如果没有找到则使用第二个，以此类推。
• 这里面sans-serif并不是指的具体某一个字体。而是一类字体。

### 字体分类

• serif（衬线字体）
• sans-serif（非衬线字体）
• monospace （等宽字体）
• cursive （草书字体）
• fantasy （虚幻字体）
• 以上这些分类都是一些大的分类，并没有涉及具体的类型，如果将字体指定为这些格式，浏览器会自己选择指定类型的字体。

### 斜体和粗体

• font-style用来指定文本的斜体。
– 指定斜体：font-style:italic
– 指定非斜体：font-style:normal
• font-weight用来指定文本的粗体。
– 指定粗体：font-weight:bold
– 指定非粗体：font-weight:normal

### 小型大写字母

• font-variant属性可以将字母类型设置为小型大写。在该样式中，字母看起来像是稍微缩小了尺寸的大写字母。
– **font-variant: small-caps**

### 字体属性的简写

• font可以一次性同时设置多个字体的样式。
• 语法：
– font:加粗 斜体 小型大写 大小/行高 字体
• 这里前边几个加粗、斜体和小型大写的顺序无所谓，也可以不写，但是大小和字体必须写且必须写到后两个。

### 行间距

• line-height用于设置行高，行高越大则行间距越大。
• 行间距 = line-height – font-size

### 大写化

• text-transform样式用于将元素中的字母全都变成大小写。
– 大写：text-transform:uppercase
– 小写：text-tansform:lowercase
– 首字母大写：text-transform:capitalize
– 正常：text-transform:none

### 文本的修饰

• text-decoration属性，用来给文本添加各种修饰。通过它可以为文本的上方、下方或者中间添加线条。
• 可选值：
– underline
– overline
– line-through
– none

### 字母间距和单词间距

• letter-spacing用来设置字符之间的间距。
• word-spacing用来设置单词之间的间距。
• 这两个属性都可以直接指定一个长度或百分数作为值。正数代表的是增加距离，而负数代表减少距离。

### 对齐文本

• text-align用于设置文本的对齐方式。
• 可选值：
– left：左对齐
– right：右对齐
– justify：两边对齐
– center：居中对齐

### 首行缩进

• text-indent用来设置首行缩进。
• 该样式需要指定一个长度，并且只对第一行生效。



## 盒子模型

盒子模型
• 一个盒子我们会分成几个部分：
– 内容区(content)
– 内边距(padding)
– 边框(border)
– 外边距(margin)

![盒子模型](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/1568647071698.png)



### 内容区

• 内容区指的是盒子中放置内容的区域，也就是元素中的文本内容，子元素都是存在于内容区中的。
• 如果没有为元素设置内边距和边框，则内容区大小默认和盒子大小是一致的。
• 通过width和height两个属性可以设置内容区的大小。
• width和height属性**只适用于块元素**。

### 内边距

• 顾名思义，内边距指的就是元素内容区与边框以内的空间。
• **默认情况下width和height不包含padding的大小**。
• 使用padding属性来设置元素的内边距。
• 例如：
	– padding:10px 20px 30px 40px
	– 这样会设置元素的上、右、下、左四个方向的内边距。
• padding:10px 20px 30px;
	– 分别指定上、左右、下四个方向的内边距
• padding:10px 20px;
	– 分别指定上下、左右四个方向的内边距
• padding:10px;
	– 同时指定上左右下四个方向的内边距
• 同时在css中还提供了padding-top、padding-right、paddingright、padding-bottom分别用来指定四个方向的内边距。

### 边框

• 可以在元素周围创建边框，边框是元素可见框的最外部。
• 可以使用border属性来设置盒子的边框：
	– border:1px red solid;
	– 上边的样式分别指定了边框的宽度、颜色和样式。
• 也可以使用border-top/left/right/bottom分别指定上右下左四个方向的边框。
• 和padding一样，默认width和height并包括边框的宽度。

### 边框的样式

• 边框可以设置多种样式：
– none（没有边框）
– dotted（点线）
– dashed（虚线）
– solid（实线）
– double（双线）
– groove（槽线）
– ridge（脊线）
– inset（凹边）
– outset（凸边）

### 外边距

• 外边距是元素边框与周围元素相距的空间。
• 使用margin属性可以设置外边距。
• 用法和padding类似，同样也提供了四个方向的margin-top/right/bottom/left。
• 当将左右外边距设置为auto时，浏览器会将左右外边距设置为相等，所以这行代码**margin:0 auto可以使元素居中**。

### display

• 我们不能为行内元素设置width、height、margin-top和margin-bottom。
• 我们可以通过修改display来修改元素的性质。
• 可选值：
– block：设置元素为块元素
– inline：设置元素为行内元素
– inline-block：设置元素为行内块元素
– none：隐藏元素（元素将在页面中完全消失）

### visibility

• visibility属性主要用于元素是否可见。
• 和display不同，使用visibility隐藏一个元素，隐藏后其在文档中所占的位置会依然保持，不会被其他元素覆盖。
• 可选值：
– visible：可见的
– hidden：隐藏的

### overflow

• 当相关标签里面的内容超出了样式的宽度和高度是，就会发生一些奇怪的事情，浏览器会让内容溢出盒子。
• 可以通过overflow来控制内容溢出的情况。
• 可选值：
– visible：默认值
– scroll：添加滚动条
– auto：根据需要添加滚动条
– hidden：隐藏超出盒子的内容

### 文档流

• 文档流指的是文档中可现实的对象在排列时所占用的位置。
• 将窗体自上而下分成一行行，并在每行中按从左至右的顺序排放元素，即为文档流。
• 也就是说在文档流中元素默认会紧贴到上一个元素的右边，如果右边不足以放下元素，元素则会另起一行，在新的一行中继续从左至右摆放。
• 这样一来每一个块元素都会另起一行，那么我们如果想在文档流中进行布局就会变得比较麻烦。

### 浮动

• 所谓浮动指的是使元素脱离原来的文本流，在父元素中浮动起来。
• 浮动使用float属性。
• 可选值：
– none：不浮动
– left：向左浮动
– right：向右浮动
• 块级元素和行内元素都可以浮动，当一个**行内元素浮动**以后将会自动**变为一个块级元素**。
• 当一个块级元素浮动以后，宽度会默认被内容撑开，所以当漂浮一个块级元素时我们都会为其指定一个宽度。

• 当一个元素浮动以后，其下方的元素会上移。元素中的内容将会围绕在元素的周围。
• 浮动会使元素完全脱离文本流，也就是不再在文档中在占用位置。
• 元素设置浮动以后，会一直向上漂浮直到遇到父元素的边界或者其他浮动元素。
• 元素浮动以后即完全脱离文档流，这时不会再影响父元素的高度。也就是浮动元素不会撑开父元素。
• 浮动元素默认会变为块元素，即使设置display:inline以后其依然是个块元素。

**浮动会造成高度塌陷问题**，如下：

红色 div 是蓝色 div 的父元素，由于蓝色 div设置了左浮动，脱离了文档流，导致父元素高度塌陷

![高度塌陷](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E9%AB%98%E5%BA%A6%E5%A1%8C%E9%99%B7.png)

```html
<div class="box1">
  <div class="box2"></div>
</div>
<div class="box3"></div>
<style type="text/css">
.box1{
	border: 10px red solid;
	overflow: hidden;
}

.box2{
	width: 100px;
	height: 100px;
	background-color: blue;
	float: left;
}

.box3{
	 height: 100px;
	 background-color: yellow;
}
</style>
```

解决：

根据W3C的标准，在页面中元素都一个隐含的属性叫做Block Formatting Context，简称BFC（详见[块格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)），该属性可以设置打开或者关闭，默认是关闭的。
当开启元素的BFC以后，元素将会具有如下的特性：
	1.父元素的垂直外边距不会和子元素重叠	
	2.开启BFC的元素不会被浮动元素所覆盖
	3.开启BFC的元素可以包含浮动的子元素
如何开启元素的BFC
	1.设置元素浮动

​       使用这种方式开启，虽然可以撑开父元素，但是会导致父元素的宽度丢失而且使用这种方式也会导致下边的     元素上移，不能解决问题
​	2.设置元素绝对定位
​	3.设置元素为inline-block

		- 可以解决问题，但是会导致宽度丢失，不推荐使用这种方式
	4.将元素的overflow设置为一个非visible的值


推荐方式：将overflow设置为hidden是副作用最小的开启BFC的方式。

```css
.box1{
	overflow: hidden;
}
```

​	![解决高度塌陷](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E8%A7%A3%E5%86%B3%E9%AB%98%E5%BA%A6%E5%A1%8C%E9%99%B7.png)



### 清除浮动

• clear属性可以用于清除元素周围的浮动对元素的影响。
• 也就是元素不会因为上方出现了浮动元素而改变位置。
• 可选值：
– left：忽略左侧浮动
– right：忽略右侧浮动
– both：忽略全部浮动
– none：不忽略浮动，默认值

### 定位

• position属性可以控制Web浏览器如何以及在何处显示特定的元素。
• 可以使用position属性把一个元素放置到网页中的任何位置。
• 可选值：
– static
– relative
– absolute
– fixed

#### 相对定位(relative)

• 每个元素在页面的文档流中都有一个自然位置。相对于这个位置对元素进行移动就称为相对定位。**周围的元素完全不受此影响**。
• 当将position属性设置为relative时，则开启了元素的相对定位。
• 当开启了相对定位以后，可以使用top、right、bottom、left四个属性对元素进行定位。

**相对定位的特点**
• 如果不设置元素的偏移量，元素位置不会发生改变。
• 相对定位**不会**使元素**脱离文本流**。元素在文本流中的位置不会改变，在文档流原先的位置遗留空白区域。
• 相对定位不会改变元素原来的特性。
• 相对定位会使元素的层级提升，使元素可以覆盖文本流中的元素。

• **定位的起始位置为此元素原先在文档流的位置**。



#### 绝对定位(absolute)

• 绝对定位指使元素相对于**html元素**或**离他最近的祖先定位元素进行定位**。如果他**没有给left、right、top和bottom设置值**，**并且他的所有祖先元素都没有设置定位**，则他保持在**自然位置**
• 当将position属性设置为absolute时，则开启了元素的绝对定位。
• 当开启了绝对定位以后，可以使用top、right、bottom、left四个属性对元素进行定位。

**绝对定位的特点**
• 绝对定位会使元素完全脱离文本流。
• 绝对定位的块元素的宽度会被其内容撑开。
• 绝对定位会使行内元素变成块元素。
• **一般**使用绝对定位时会同时为**其父元素**指定一个**相对定位**，以确保元素可以**相对于父元素进行定位**。

#### 固定定位(fixed)

• 固定定位的元素会被锁定在屏幕的某个位置上，当访问者滚动网页时，固定元素会在屏幕上保持不动。
• 当将position属性设置为fixed时，则开启了元素的固定定位。
• 当开启了固定定位以后，可以使用top、right、bottom、left四个属性对元素进行定位。
• 固定定位的其他特性和绝对定位类似。

• **元素的位置相对于浏览器窗口是固定位置**。

#### 静态定位(static)

HTML元素的默认值，不受top、bottom、left、right属性影响，元素出现在正常的文档流中

#### 粘性定位(sticky)

MDN描述：盒位置根据正常流计算(这称为正常流动中的位置)，然后相对于该元素在流中的 flow root（BFC）和 containing block（最近的块级祖先元素）定位。在所有情况下（即便被定位元素为 `table 时`），该元素定位均不对后续元素造成影响。当元素 B 被粘性定位时，后续元素的位置仍按照 B 未定位时的位置来确定。`position: sticky `对 `table` 元素的效果与 `position: relative `相同。

该定位依赖于自身的top、left、bottom、right阀值，所以必须设置其中之一的值sticky才会有效，否则，如果该元素**不为 table 元素**时，表现为**静态定位**（static），如果该元素为 **table 元素**时，表现为**相对定位**（relative）。（此处的表现效果的运行环境：Chrome 76.0.3809.100）

效果：元素在视口没有滚动到设定的阀值时，它会表现为static定位（如果为 **table** 元素则表现为 **relative** 定位），当元素被滚动到阀值时，它不会越过阀值，而是表现为fixed定位。
举个🌰，假设设定了position:sticky; top:20px; 那么该元素初始布局时会按static定位，如果初始的定位在视口的top值小于20px，则它立马就会表现为top：20px的fixed定位；或者初始布局的视口top值大于20px，它正常布局，当滚动窗口时，一旦使它top值到达或者小于20px时，它表现为top：20px的fixed定位，直到它的父元素的底部和它自生底部重合，它就会黏在父元素底部被滚动走

### z-index

• 当元素开启定位以后就可以设置z-index这个属性。
• 这个属性可以提升定位元素所在的层级。
• z-index可以指定一个整数作为参数，值越大元素显示的优先级越高，也就是z-index值较大的元素会显示在网页的最上层。

## 设置元素的背景

### background-color

• background-color属性用来为元素设置背景颜色。
• 需要指定一个颜色值，当指定了一个颜色以后，整个元素的可见区域都会使用这个颜色作为背景色。
• 如果不设置背景颜色，元素默认背景颜色为透明，实际上会显示父元素的背景颜色。

### background-image

• background-image可以为元素指定背景图片。
• 和background-color类似，这不过这里使用的是一个图片作为背景。
• 需要一个url地址作为参数，url地址需要指向一个外部图片的路径
• 例如：
background-image: url(1.jpg)

### background-repeat

• background-repeat用于控制背景图片的重复方式。
• 如果只设置背景图片默认背景图片将会使用平铺的方式，可以通过该属性进行修改。
• 可选值：
– repeat：默认值，图片左右上下平铺
– no-repeat：只显示图片一次，不会平铺
– repeat-x：沿x轴水平平铺一张图片
– repeat-y：沿y轴水平平铺一张图片

### background-position

• background-position用来精确控制背景图片在元素中的位置。
• 可以通过三种方式来确定图片在水平方向和垂直方向的起点。
– 关键字：top right bottom left center
– 百分比
– 数值

### background-attachment

• background-attachment用来设置背景图片是否随页面滚动。
• 可选值：
– scroll：随页面滚动
– fixed：不随页面滚动

### background

• background是背景的简写属性，通过这个属性可以一次性设置多个样式，而且样式的顺序没有要求。
• 例如：
background: green url(1.jpg) no-repeat center center fixed;

### CSS Sprite

• CSS Sprites是一种网页图片应用处理方式。
• 通过这种方式我们可以将网页中的零星图片集中放到一张大图上。
• 这样一来，一次请求便可以同时加载多张图片，大大提高了图片的加载效率。



## 表格

• 在Web的历史中，HTML的表格发挥了极大的作用。最初创建表格就是为了以表格的形式显示数据，后来表格变成了一个极受欢迎的布局工具。
• 但是有了CSS以后，CSS在布局网页方面实际上会更出色，所以现在我们使用表格的作用只有一个，就是用来表示格式化的数据。
• HTML中的表格可以很复杂，但是通常情况下我们不需要创建过于复杂的表格。

### table、tr、th、td

• 使用table标签创建一个表格。
• tr表示表格中的一行。
• tr中可以编写一个或多个th或td。
• th表示表头。
• td表示表格中的一个单元格。

### caption、thead、tbody、tfoot

• caption表示表格的标题。
• thead表示表格的头部。
• tbody表示表格的主体。
• tfoot表示表格的底部。

### 合并单元格

• 合并单元格指将两个或两个以上的单元格合并为一个单元格。
• 合并单元格可以通过在th或td中设置属性来完成。
• 横向合并
	– colspan
• 纵向合并
	– rowspan

### 表格的样式

• 之前学习的很多属性都可以用来设置表格的样式，比如color可以用来设置文本的颜色。padding可以设置内容和表格边框的距离。
• text-align：设置文本的水平对齐。
• vertical-align：设置文本的垂直对齐。
	– 可选值：top、baseline、middle、bottom
• border-spacing：边框间距
• border-collapse：合并边框
	– collapse：合并边框
	– separate：不合并边框

## 表单

表单可以将用户填写的信息提交的服务器
• 例子：

![1568707605387](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/1568707605387.png)

### 表单项

文本框

```html
<input type="text" name="name">
```

密码框

```html
<input type="password" name="pwd">
```

多选框

```html
<input type="checkbox" name="sports">
```

单选框

```html
<input type="radio" name="gender">
```

提交按钮

```html
<input type="submit" value="提交">
```

下拉列表

```html
<select>
	<option>北京</option>
</select>
```

![1568707815193](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/1568707815193.png)

#### input

• input是我们使用的最多的表单项，它可以根据不同的type属性呈现不同的状态。
• type属性可选值：
– text：文本框
– password：密码框
– submit：提交按钮
– radio：单选按钮
– checkbox：多选框
– reset ：重置按钮

#### select、option

• select用于创建一个下拉列表。
• option表示下拉列表中的列表项。
• optgroup用于为列表项分组。

#### textarea

• textarea用来创建一个文本域，实际效果和文本框类似，只是可以输入多行数据。
• 可用属性：
– cols：文本域的列数
– rows：文本域的行数

#### fieldset、legend、label

• fieldset用来为表单项进行分组。
• legend用于指定每组的名字。
• label标签用来为表单项定义描述文字。



## CSS 布局

### 圣杯布局（三栏中间自适应两边固定）

![圣杯布局（float）](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E5%9C%A3%E6%9D%AF%E5%B8%83%E5%B1%80%EF%BC%88float%EF%BC%89.png)

**实现原理(float 实现)**

html代码中，middle部分首先要放在container的最前部分，然后是left，right

1. 将三者都设置 `float:left`, `position:relative` (因为相对定位后面会用到）

2. middle设置 `width:100%` 占满一行

3. 此时middle占满一行，所以要把 left 标签拉到 middle 标签所在行的最左边，使用 `margin-left:-100%`

   （[margin-left](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-left) 百分比的值，是指最近的块容器的宽度 *width* 的百分比，关于 margin-left 的负值与 float: left 搭配使用，为什么会使元素换行，详见 [探究负边距（negative margin）原理](https://www.cnblogs.com/LiveWithIt/p/6024864.html) ）；

4. 这时 left 标签拉回到 middle 标签所在行的最左边，但会覆盖 middle 标签内容的左端，要把middle 标签内容拉出来，所以在**外围container加上 `padding:0 210px`**

5. middle 标签内容拉出来了，但 left 标签也跟着出来了，所以要还原，就对 left 标签使用相对定位 `left:-210px`

6. 同理，right 标签要拉到 middle 标签所在行的最右边，使用 `margin-left:-210px`，`right:-210px`

**不足：** 「圣杯布局」存在宽度过小导致布局混乱的缺陷。



**实现代码(float 实现)**

```html
// 运行环境 Chrome 75.0.3770.100， Windows 10.0
<!DOCTYPE HTML>
<html lang="en-US">
<head>
    <meta charset="UTF-8">
    <title>圣杯布局</title>
    <style type="text/css">
        body {
            /* 2*left(这里为container的padding)+right：210*2+200 */
            min-width: 620px;
            text-align: center;
        }
        #demo {
            margin: auto;
        }
        #header, #footer {
            height: 50px;
            background-color: #aaa;
        }
        #container {
            overflow: hidden; /*利用bfc特性避免了高度塌陷，并且帮助实现左右栏等高布局*/
            margin: 10px 0;
            padding:0 210px;
        }
        #middle, #left, #right {
            float:left;
				    padding-bottom: 10000px; /*实现左右栏等高布局*/
				    margin-bottom: -10000px; /*与padding-bottom抵消，实现左右栏等高布局*/
        }
        #left {
            position:relative;
            left:-210px;
            width:200px;
            margin-left:-100%;
            background-color: red;
        }
        #right {
            position:relative;
            background-color: green;
            width: 200px;
            margin-left: -200px;
            right: -210px;
        }
        #middle {
            background-color: blue;
            width:100%;
        }
    </style>

</head>
<body>
    <div id="demo">
        <header id="header">头部</header>
        <div id="container">
            <!-- 一般主内容区域为重要部分，所以把 middle 放在最前面，优先加载 -->
            <div id="middle"> 
                middle 主内容区域<br>middle 主内容区域<br>middle 主内容区域<br>
                middle 主内容区域<br>middle 主内容区域<br>middle 主内容区域<br>
            </div>
            <div id="left">
                left<br>左侧边栏区域
            </div>
            <div id="right">
                right<br>右侧边栏区域
            </div>
        </div>
        <footer id="footer">底部</footer>
    </div>
</body>
</html>
```



**[flex](https://www.runoob.com/w3cnote/flex-grammar.html) 实现**：

![圣杯布局（flex）](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E5%9C%A3%E6%9D%AF%E5%B8%83%E5%B1%80%EF%BC%88flex%EF%BC%89-1568601492476.png)



```html
<html>

<head>
  <title>Flex 实现圣杯布局</title>
  <style type="text/css">
    html,
    body {
    display: flex;
    flex-direction: column;
    min-height: 600px;
    height: 100%;
    font-size: 28px;
    font-weight: bolder;
    }

    header,
    footer {
    height: 150px;
    background-color: #666;
    display: flex;
    justify-content: center;
    align-items: center;
    flex: none;
    }

    .content {
    flex: 1; /* 高度自适应 */
    display: flex;
    }

    nav,
    aside {
    background-color: #eb6f43;
    flex: 0 1 200px;
    display: flex;
    justify-content: center;
    align-items: center;
    }

    main {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1; /* 宽度自适应 */
    background-color: #d6d6d6;
    }

    nav {
    order: -1; /* 调整顺序在main前面 */
    }
  </style>
</head>

<body>
  <header>
    header
  </header>
  <div class="content">
    <main>main</main>
    <nav>nav</nav>
    <aside>aside</aside>
  </div>
  <footer>
    footer
  </footer>
</body>

</html>
```



**absolute实现**：

![圣杯布局（absolute）](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E5%9C%A3%E6%9D%AF%E5%B8%83%E5%B1%80%EF%BC%88absolute%EF%BC%89.png)



```html
<!DOCTYPE HTML>
<html lang="en-US">
<head>
    <meta charset="UTF-8">
    <title>双飞翼布局</title>
    <style type="text/css">
    
        .header, .footer {
            height: 50px;
            background-color: #aaa;
        }
        .wrapper {
            position: relative; 
        }
        .main {
            margin:0 100px;
            background-color: blue;
        }
        .left {
            position: absolute;
            left: 0;
            top: 0;
            background-color: red;
        }
        .right {
            position: absolute;
            right: 0;
            top: 0;
            background-color: green;
        }
        .left, .right {
            width: 80px;
            height: 100%;
        }
    </style>
</head>
<body>
    <div class="header">header</div>
    <div class="wrapper">
        <div class="main">
            main<br />
            绝对定位实现圣杯布局
        </div>
        <div class="left">
            left
        </div>
        <div class="right">
            right
        </div>
    </div>
    <div class="footer">footer</div>
</body>
</html>
```





### 双飞翼布局（三栏中间自适应两边固定）

![双飞翼布局](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E5%8F%8C%E9%A3%9E%E7%BF%BC%E5%B8%83%E5%B1%80-1576421872112.png)



**实现原理**

html代码中，middle部分首先要放在container的最前部分，然后是left，right

1. 将三者都设置 `float:left`
2. middle设置 `width:100%` 占满一行
3. 此时middle占满一行，所以要把left拉到middle所在行的最左边，使用 `margin-left:-100%`，同理right使用 `margin-left:-200px`
4. 此时middle的内容被覆盖，要把middle的内容拉出来，除了使用外围container的padding，还可以考虑使用margin，给middle增加一个内层div -- middle_content, 然后设置 `margin:0 210px`

**不足** : 「双飞翼布局」增加了 DOM 树的层级，因此也会增加 浏览器渲染引擎构建布局树时的计算消耗.



**圣杯与双飞翼的区别是**：圣杯布局是**使用外围container的padding**，双飞翼布局是**给middle增加一个内层div -- middle_content, 然后设置 margin，并且没有使用相对定位（relative）**

```html

<!DOCTYPE HTML>
<html lang="en-US">
<head>
    <meta charset="UTF-8">
    <title>双飞翼布局</title>
    <style type="text/css">
        body {
            /* 2*left(这里为container的padding)+right：210*2+200 */
            min-width: 620px;
            text-align: center;;
        }
        #header, #footer {
            height: 50px;
            background-color: #aaa;
        }
        #container {
            overflow: hidden;
            margin: 10px 0;
        }
        #middle, #left, #right {
            float:left;
				    padding-bottom: 10000px; /*实现左右栏等高布局*/
				    margin-bottom: -10000px; /*与padding-bottom抵消，实现左右栏等高布局*/
        }
        #left {
            background-color: red;
            width:200px;
            margin-left: -100%;
        }
        #right {
            background-color: green;
            width: 200px;
            margin-left: -200px;
        }
        #middle {
            width: 100%;
        }
        #middle_content {
            background-color: blue;
            margin: 0 210px;
        }
    </style>
</head>
<body>
    <div id="demo">
        <header id="header">头部</header>
        <div id="container">
            <div id="middle">
                <div id="middle_content">
                    middle_content<br>middle_content<br>middle_content<br>
                    middle_content<br>middle_content<br>主内容区域
                </div>
            </div>
            <div id="left">
                left<br>左侧边栏区域
            </div>
            <div id="right">
                right<br>右侧边栏区域
            </div>
        </div>
        <footer id="footer">底部</footer>
    </div>
</body>
</html>
```



**实现代码**

参考

> [圣杯布局 vs 双飞翼布局](https://segmentfault.com/a/1190000012059648)
>
> [探究负边距（negative margin）原理](https://www.cnblogs.com/LiveWithIt/p/6024864.html)
>
> [CSS Flex 布局：用 Flex 来实现圣杯布局](https://segmentfault.com/a/1190000013631838)



### 两栏布局（左栏宽固定，右栏自适应）

![两栏布局（左栏固定右栏自适应）](../images/HTML%E4%B8%8ECSS%E5%9F%BA%E7%A1%80.assets/%E4%B8%A4%E6%A0%8F%E5%B8%83%E5%B1%80%EF%BC%88%E5%B7%A6%E6%A0%8F%E5%9B%BA%E5%AE%9A%E5%8F%B3%E6%A0%8F%E8%87%AA%E9%80%82%E5%BA%94%EF%BC%89.png)

```html
<div class="wrapper">
  <div class="left">
    左边固定宽度，高度不固定 </br> </br></br></br>高度有可能会很小，也可能很大。
  </div>
  <div class="right">
    这里的内容可能比左侧高，也可能比左侧低。宽度需要自适应。</br>
    这里的内容可能比左侧高，也可能比左侧低。宽度需要自适应。</br>
    这里的内容可能比左侧高，也可能比左侧低。宽度需要自适应。</br>
  </div>
</div>
<style>
	.left {
  	background: lightgreen;
  	width: 120px;
	}

	.right {
 		 background-color: lightcoral;
	}
</style>
```

下面的布局样式都以上面的 html 为例。

1. **flex布局：**

`flex`可以说是最好的方案了，代码少，使用简单。 **需要注意**的是，`flex`容器的一个默认属性值:`align-items: stretch;`。这个属性导致了列等高的效果。 为了让两个盒子高度自动，需要设置: `align-items: flex-start;`

```css
.wrapper {
  display: flex;
  align-items: flex-start;
}

.left {
  flex: 0 0 auto;
  background: lightgreen;
  width: 120px;
}

.right {
  flex: 1;
  background-color: lightcoral;
}
```



2. **`float+margin-left`方案**

   `block` 级别的元素会认为浮动的元素不存在，但是`inline`级别的元素能识别到浮动的元素。这样，`block` 级别的元素就可以和浮动的元素同处一行了。 为了让右侧盒子和左侧盒子保持距离，需要为左侧盒子留出足够的距离。这个距离的大小为左侧盒子的宽度以及两个盒子之间的距离之和。然后将该值设置为右侧盒子的`margin-left`。

    **缺点：**

   - 需要清除浮动
   - 需要计算右侧盒子的`margin-left`

   ```css
   .wrapper {
       overflow: hidden;		/*清除浮动*/
   }
   
   .left {
       float: left;
   }
   
   .right {
       margin-left: 120px;
   }
   ```

   

3. **使用 `float+BFC` 方法**

   这个方案是利用了左侧浮动，但是右侧盒子通过`overflow: auto;`形成了BFC，因此右侧盒子不会与浮动的元素重叠。这种情况下，只需要为左侧的浮动盒子设置`margin-right`，就可以实现两个盒子的距离了。而右侧盒子是`block`级别的，所以宽度能实现自适应。 **缺点:** **父元素需要清除浮动**

   ```css
   .wrapper {
       overflow: auto;
   }
   
   .left {
       float: left;
   }
   
   .right {
       overflow: auto;
   }
   ```



4. **使用`absolute+margin-left`方法**

   让两个`block`排列到一起的方法是对左侧盒子使用`position: absolute`的绝对定位。这样，右侧盒子也能**无视**掉它。

   **缺点:**

   - 使用了绝对定位，若是用在某个div中，需要更改父容器的`position`。
   - 没有清除浮动的方法，若左侧盒子高于右侧盒子，就会超出父容器的高度。因此只能通过设置父容器的`min-height`来放置这种情况。

   ```css
   .wrapper {
     position: relative;
   }
   
   .left {
       position: absolute;
   }
   
   .right {
       margin-left: 120px;
   }
   ```

参考

> [七种实现左侧固定，右侧自适应两栏布局的方法](https://zhuqingguang.github.io/2017/08/16/adapting-two-layout/#) 































