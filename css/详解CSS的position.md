position 可以取值为：static（默认）、relative、absolute、fixed、sticky（实验性 API）、inherit。

> 实例环境：Chrome 77.0.3865.90

## static

> MDN: 该关键字指定元素使用正常的布局行为，即元素在文档常规流中当前的布局位置。此时 `top`, `right`, `bottom`, `left` 和 `z-index `属性无效。



## relative

> MDN：该关键字下，元素先放置在未添加定位时的位置，再在不改变页面布局的前提下调整元素位置（因此会**在此元素未添加定位时所在位置留下空白**）。**position:relative 对 table-*-group, table-row, table-column, ~~table-cell, table-caption~~（实际测试中，table-cell，table-caption元素是有效的，可能MDN有误） 元素无效**。

- 不会脱离文档流
- 调整了 relative 元素的位置，那么会在它原有的位置留下空白并且不影响其他元素的布局
- 相对于元素原有的位置进行定位的偏移
- position:relative 对 table-*-group, table-row, table-column 元素无效



**实例**

1. 相对定位的元素是在文档中的正常位置偏移给定的值，但是不影响其他元素的偏移。下面的例子中，注意未应用定位的其它元素是按照 "Two" 在正常位置的情况下进行布局的

   ![1571319982659](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571319982659.png)

   ```html
   <div class="wrap">
     <div class="father">
       <div class="box" id="one">One</div>
       <div class="box" id="two">Two</div>
       <div class="box" id="three">Three</div>
       <div class="box" id="four">Four</div>
     </div>
   </div>
   ```

   如无特殊说明，则以下实例都使用上面的 html 代码

   

   ```css
   .father {
   	border: 2px solid blueviolet;
   }
   .box {
     display: inline-block;
     width: 100px;
     height: 100px;
     background: red;
     color: white;
   }
   
   #two {
     position: relative;
     top: 20px;
     left: 20px;
     background: blue;
   }
   ```

   

2. position:relative 对 table-*-group, table-row, table-column 元素无效

   ![1571320064079](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571320064079.png)

   ```css
   #two {
     display: table-row;
     position: relative;
     top: 20px;
     left: 20px;
     background: blue;
   }
   ```

   

   但对于table-cell，table-caption元素表现还是有效的，不知道是 MDN 有误，还是因为浏览器环境问题

   ![1571320096593](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571320096593.png)

   ```css
   #two {
     display: table-cell; // 或者 table-caption
     position: relative;
     top: 20px;
     left: 20px;
     background: blue;
   }
   ```

   

## absolute

> MDN：不为元素预留空间，通过指定元素相对于最近的**非 static 定位祖先元素**的偏移，来确定元素位置。绝对定位的元素可以设置外边距（margins），且不会与其他边距合并。

- 会脱离文档流
- 相对于最近的**非 static 定位祖先元素（::question::）**的偏移，当这样的祖先元素不存在时，则相对于ICB[^1]（inital container block, 初始包含块）
- 可以设置外边距（margins），且不会与其他边距合并

1. 父元素设置为 relative，子元素设置为 absolute，则子元素根据父元素进行定位偏移（这是经常用的实例）

   ![1571320648360](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571320648360.png)

   ```html
   <div class="wrap">
     <div class="father">
       <div class="box" id="one">One</div>
       <div class="box" id="two">Two</div>
       <div class="box" id="three">Three</div>
       <div class="box" id="four">Four</div>
     </div>
   </div>
   <style>
   .father {
     position: relative;
     border: 2px solid blueviolet;
   }
   
   .box {
     display: inline-block;
     width: 100px;
     height: 100px;
     background: red;
     color: white;
   }
   
   #two {
     position: absolute;
     top: 20px;
     left: 20px;
     background: blue;
   }
   </style>
   ```

   如上图可知，`#two` 元素是相对于 `.father` 进行定位偏移

   

   **为了验证相对于最近的非 static 定位祖先元素的偏移这句话，例举了以下实例**

2. 父元素设置 absolute，子元素设置为 absolute，则子元素也是根据父元素进行定位偏移。这里只是**验证**当父元素为 relative、absolute、fixed、sticky时，子元素是否还是相对于父元素进行定位偏移，经本人测试过后，确实如此，所以就不再给父元素设置为 fixed、sticky的例子了

   ![1571321264237](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571321264237.png)

   ```html
   <style>
   .father {
     position: relative;
     border: 2px solid blueviolet;
   }
   
   .box {
     display: inline-block;
     width: 100px;
     height: 100px;
     background: red;
     color: white;
   }
   
   #two {
     position: absolute;
     top: 20px;
     left: 20px;
     background: blue;
   }
   
   #two-child {
     position: absolute;
     top: 20px;
     left: 20px;
     background: rgb(255, 47, 238);
   }
   </style>
   <body>
   <div class="wrap">
     <div class="father">
       <div class="box" id="one">One</div>
       <div class="box" id="two">Two
         <div class="box" id="two-child">Two-child</div>
       </div>
       <div class="box" id="three">Three</div>
       <div class="box" id="four">Four</div>
     </div>
   </div>
   </body>
   ```

   由上图可知，`#Two-child` 元素相对于 `#Two` 元素进行定位偏移，所以子元素确实是相对于非 static 定位父元素的偏移，如果要验证**子元素相对于最近的非 static 定位祖先元素的偏移**这句话，继续看下面的例子



3. 给祖父元素设置为 relative，不给父元素设置 position 的值（即默认的 static），子元素设置为 absolute

   ![1571322359623](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571322359623.png)

   ```html
   <style>
   .father {
     position: relative;
     border: 2px solid blueviolet;
   }
   
   .box {
     display: inline-block;
     width: 100px;
     height: 100px;
     background: red;
     color: white;
   }
   
   #two {
     /* position: absolute; */
     top: 20px;
     left: 20px;
     background: blue;
   }
   
   #two-child {
     position: absolute;
     top: 20px;
     left: 20px;
     background: rgb(255, 47, 238);
   }
   
   </style>
   <body>
   <div class="wrap">
     <div class="father">
       <div class="box" id="one">One</div>
       <div class="box" id="two">Two
         <div class="box" id="two-child">Two-child</div>
       </div>
       <div class="box" id="three">Three</div>
       <div class="box" id="four">Four</div>
     </div>
   </div>
   </body>
   ```

   由上图可知，因为离`#two-child` 最近的**非 static** 祖先元素是`.father`，所以`#two-child`是相对于 `.father` 元素进行定位偏移。

   到这里就成功验证了**设置为 relative的子元素相对于最近的非 static 定位祖先元素的偏移**这句话。



4. 当 relative 的子元素没有**非 static** 祖先元素时，则该子元素相对于根元素（准确来说是初始包含块）进行定位偏移

   ![1571323794499](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/1571323794499.png)

   ```html
   <style>
   * {
     margin: 0;
   }
   html {
     border: 1px solid greenyellow;
   }
   
   body {
     margin: 30px;
     border: 1px solid indigo;
   }
   
   .wrap {
     margin: 30px;
   }
   
   .father {
     border: 2px solid blueviolet;
   }
   
   .box {
     display: inline-block;
     width: 100px;
     height: 100px;
     background: red;
     color: white;
   }
   
   #two {
     top: 20px;
     left: 20px;
     background: blue;
   }
   
   #two-child {
     position: absolute;
     top: 20px;
     left: 20px;
     background: rgb(255, 47, 238);
   }
   
   </style>
   <body>
   <div class="wrap">
     <div class="father">
       <div class="box" id="one">One</div>
       <div class="box" id="two">Two
         <div class="box" id="two-child">Two-child</div>
       </div>
       <div class="box" id="three">Three</div>
       <div class="box" id="four">Four</div>
     </div>
   </div>
   </body>
   ```

   

## fixed

> MDN：不为元素预留空间，而是通过指定元素**相对于屏幕视口（viewport）的位置来指定元素位置**。元素的位置在屏幕滚动时不会改变。**打印时，元素会出现在的每页的固定位置**。**`fixed` 属性会创建新的层叠上下文**。**当元素祖先的 `transform`  属性非 `none` 时，容器由视口改为该祖先**。

- 会脱离文档流
- 相对于屏幕视口（viewport）的位置来指定元素位置
- **打印**时，元素会出现在的**每页的固定位置**
- `fixed` 属性会创建新的层叠上下文
- 当元素祖先的 `transform`  属性非 `none` 时，容器由视口改为该祖先

1. 设置为 fixed 元素将会相对于屏幕视口（viewport）的位置来指定元素位置

   ![position为fixed](../images/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84position.assets/position%E4%B8%BAfixed.gif)

   ```html
   <style>
   .box {
     background: red;
     width: 60px;
     height: 60px;
     margin: 10px;
     color: white;
   }
   #one {
     position: fixed;
     top: 30px;
     left: 30px;
   }
   .outer {
     width: 300px;
     height: 200px;
     overflow: scroll;
     padding-left: 150px;
   }
   </style>
   <div class="outer">
     <p>
       Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam congue tortor eget pulvinar lobortis.
       Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nam ac dolor augue.
       Pellentesque mi mi, laoreet et dolor sit amet, ultrices varius risus. Nam vitae iaculis elit.
       Aliquam mollis interdum libero. Sed sodales placerat egestas. Vestibulum ut arcu aliquam purus viverra dictum vel
       sit amet mi.
       Duis nisl mauris, aliquam sit amet luctus eget, dapibus in enim. Sed velit augue, pretium a sem aliquam, congue
       porttitor tortor.
       Sed tempor nisl a lorem consequat, id maximus erat aliquet. Sed sagittis porta libero sed condimentum.
       Aliquam finibus lectus nec ante congue rutrum. Curabitur quam quam, accumsan id ultrices ultrices, tempor et tellus.
     </p>
     <div class="box" id="one">One</div>
   </div>
   ```

   设置为fixed 的 `#one`元素，相对于屏幕视口（viewport）的位置来指定元素位置，并且不会跟随滚动条移动



2. 当 fixed 的元素祖先的 `transform`  属性非 `none` 时，容器由视口改为该祖先，此时的 fixed 元素变成`absolute`一样的行为表现，会相对于 `transform` 不为 `none` 值的祖先元素进行定位偏移，并且**会跟随滚动条一起滚动**

   实现效果请见[transform使position:fixed元素absolute化Demo](http://www.zhangxinxu.com/study/201505/css3-transform-position-fixed-to-absolute.html) 



## sticky（实验性 API ，建议不要再生产环境中使用）

> MDN：盒位置根据正常流计算(这称为正常流动中的位置)，然后相对于该元素在流中的 flow root（**BFC**）和 containing block（**最近的块级祖先元素**）定位。在**所有情况下**（即便被定位元素为 `table` 时），**该元素定位均不对后续元素造成影响**。当元素 B 被粘性定位时，后续元素的位置仍按照 B 未定位时的位置来确定。`position: sticky `对 `table` 元素的效果与 `position: relative `相同。

- ::question: 会不会脱离文档流
- 相对于该元素在流中的 flow root（**BFC**）和 containing block（**最近的块级祖先元素**）定位
- 该元素定位均不对后续元素造成影响
- 当元素 B 被粘性定位时，后续元素的位置仍按照 B 未定位时的位置来确定
- `position: sticky `对 `table` 元素的效果与 `position: relative `相同







------

[^1]: 根元素所在的包含块叫初始包含块 initial containing block。对于连续媒体设备（continuous media），初始包含块的大小等于视口viewpor的大小，基点在画布的原点（视口左上角）；对于分页媒体（paged media），初始包含块是页面区域（page area）。初始包含块的direction属性与根元素的相同。有关包含块的内容，详见 [CSS的包含块（containing block）](https://www.vigor666.com/posts/b87a4295.html) 















