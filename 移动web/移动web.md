## Pixel 像素基础

https://www.imooc.com/video/9564 误人子弟

- ~~px（Pixels）：浏览器使用的抽象单位。（也称为 CSS pixels、逻辑像素）~~
- ~~dp：device independent pixels 设备无关像素~~
- ~~dpr：devicePixelRatio 设备像素缩放比~~

~~计算公式：~~

> ~~1px = (dpr) ^2^ * dp~~
>
> ~~平面上：1px = (2)^2^ * dp~~
>
> ~~维度上：1px = 2 * dp~~

~~平面上， 1 css pixel 相当于 4 * 1 devic pixel，维度上，1 css pixel 相当于 2 * 1 devic pixel~~

~~![1574324897311](../images/%E7%A7%BB%E5%8A%A8web.assets/1574324897311.png)~~

~~iphone5 的设备像素是 640 dp * 1136 dp，CSS 像素是 320px * 568px~~

~~因为 dpr = 2，640 dp * 1136 dp = 320px * 568px~~



| 单           位 | 名称                                                         | 描述                                                         |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| px              | Pixels、CSS Pixels（CSS 像素）                               | 对应于屏幕上的实际像素（[来自 MDN 的说法](https://developer.mozilla.org/zh-CN/docs/Glossary/CSS_pixel) ，它粗略相当于能够由人眼可舒适看出没有应变一个单个点的宽度或高度，但在其他方面尽可能小。根据定义，这是像素密度为96 DPI时单个像素的物理尺寸，其位置距观察者的眼睛一臂之遥） |
| dp              | 设备独立像素（又称设备无关像素 Device Independent Pixels 、密度独立性 Density Independent或设备独立像素)，简称 **DIP** 或 **DP** (参考： [DIP（设备独立像素）](https://baike.baidu.com/item/DIP/443947?fr=aladdin#ref_[1]_10545102) )。 | 与密度无关的像素——基于屏幕物理密度的抽象单位。（根据 [支持不同的像素密度](https://developer.android.com/training/multiscreen/screendensities.html#TaskUseDP) ）dp 是一个虚拟像素单位，1 dp 约等于中密度屏幕（160dpi；“基准”密度）上的 1 像素。对于其他每个密度，Android 会将此值转换为相应的实际像素数。 |

**DP** 或者说 **DiP** 是 Device independent Pixel 的缩写，而 **PT** 是 Point 的缩写，**DP 用于安卓系统开发，而 PT 用于 iOS 系统开发**。但从根本来讲它们都是一个意思。**SP** 是 Scale-independent pixels 的缩写，大意是可放大像素的意思，这个单位多用在安卓设备的字体大小上面。它跟 DP、 PT 的概念差不多，能够面对不同的屏幕尺寸渲染出大小适合文字。（其实 dp、pt、sp 是 UI 设计师关注的单位，详见[设备屏幕全解：设计师，你不该对你的屏幕一无所知](https://juejin.im/entry/59193fe88d6d81005880be37) ）

想对有关像素知识了解更详细的，推荐看下面链接

> https://developer.mozilla.org/zh-CN/docs/Glossary/CSS_pixel
>
> https://www.w3.org/TR/css-values-3/#absolute-lengths
>
> https://developer.android.com/guide/topics/resources/more-resources#Dimension
>
> https://developer.android.com/guide/practices/screens_support
>
> https://hacks.mozilla.org/2013/09/css-length-explained/
>
> [设备屏幕全解：设计师，你不该对你的屏幕一无所知](https://juejin.im/entry/59193fe88d6d81005880be37) 
>
> [Window.devicePixelRatio](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/devicePixelRatio) 
>
> [物理分辨率和逻辑分辨率的概念](https://zhidao.baidu.com/question/157935741.html) 



> **注意：** 
>
> 之前在看了一个讲解Pixel 像素基础的[视频](https://www.imooc.com/video/9564 ) ，其中视频里提到 px 与 dp 的关系：
>
> 1px = (dpr) ^2^ * dp （**错误公式**）
>
> 平面上：1px = (2)^2^ * dp（**错误公式**）
>
> 维度上：1px = 2 * dp（**错误公式**）
>
> 但是我在比较权威性的网站上查找上面的公式，
>
> 比如：
>
> MDN：[CSS pixel](https://developer.mozilla.org/zh-CN/docs/Glossary/CSS_pixel) 
>
> 安卓的开发者网站：[Dimension](https://developer.android.com/guide/topics/resources/more-resources#Dimension) 
>
> W3C： [Absolute Lengths](https://www.w3.org/TR/css-values-3/#absolute-lengths) 
>
> 但是都没有找到有关 px 与 dp 关系的公式，所以怀疑可能是视频作者自创的。因此**上面的公式是不可信的！！！**



- DPI（每英寸点数）：打印机每英寸可以喷的墨汁点（印刷行业）
- PPI（每英寸像素数）：屏幕每英寸的像素数量，即单位英寸内的像素密度

目前，在计算机显示设备参数描述上，二者意思表达的是一样的。

计算公式：

> PPI =  $\frac{\sqrt{(X^2 + Y^2)}}{Z}$ 
>
> X：长度像素数；Y：宽度像素数（单位：设备像素）；Z：屏幕大小(单位：英寸)
>
> 参考：[PPI（手机屏幕的PPI 和计算方法）](https://baike.baidu.com/item/PPI/9910558?fr=kg_qa) 



以 iPhone5 （分辨率：1136 * 640，屏幕大小：4 英寸）为例子

> ppi = $\frac{\sqrt{(1136^2 + 640^2)}}{4}$ = 326 ppi （ [视网膜 Retina 屏](https://baike.baidu.com/item/Retina/4616695?fr=aladdin) ）
>
> 注意：单位为设备的物理像素，非 CSS Pixels  



**PPI 越高，像素数越高，图像越清晰**

![1574326015827](../images/%E7%A7%BB%E5%8A%A8web.assets/1574326015827.png)

**但可视度越低（小），系统默认设置缩放比越大** 

例如：PC 设备分辨率

![1574326152234](../images/%E7%A7%BB%E5%8A%A8web.assets/1574326152234.png)





|                    | ~~ldpi~~ | ~~mdpi~~ | ~~hdpi~~ | ~~xhdpi~~ |
| ------------------ | -------- | -------- | -------- | --------- |
| ~~ppi~~            | ~~120~~  | ~~160~~  | ~~240~~  | ~~320~~   |
| ~~默认缩放比 dpr~~ | ~~0.75~~ | ~~1.0~~  | ~~1.5~~  | ~~2.0~~   |

~~Retina 屏（高清屏）：dpr 都是大等于 2.~~

~~以 iPhone5 为例子~~

~~![1574327870174](../images/%E7%A7%BB%E5%8A%A8web.assets/1574327870174.png)~~



## Viewport 视图

详见 [移动前端开发之viewport的深入理解](https://www.cnblogs.com/2050/p/3877280.html) 

ios 的 ViewPort：980px

手机浏览器默认做了两件事

1. 页面渲染在一个 980px（ios）的 viewport
2. 缩放

为什么渲染时要有 viewport

> 为了排版正确。
>
> 假如一个 300多像素的小屏幕来渲染1000多像素的页面，如果直接缩放页面，而没有viewport，那么排版可能就会混乱掉。所以为了排版正确，手机厂商就会伪造一个虚拟的 viewport（例如，980px），让页面先排版正确，然后再进行缩放，这样既排版正确，可视效果也较优



![1574329361110](../images/%E7%A7%BB%E5%8A%A8web.assets/1574329361110.png)

![1574329393356](../images/%E7%A7%BB%E5%8A%A8web.assets/1574329393356.png)

![1574329523361](../images/%E7%A7%BB%E5%8A%A8web.assets/1574329523361.png)

**补充一下知识：visual 、layout viewport**

visual viewport（度量 viewport）可以用 window.innerWidth 查询

layout viewport（布局 viewport）可以用 document.body.clientWidth 查询

为什么不是用默认的 980px 的布局 viewport

- 宽度不可控制，不同系统不同设备的默认值都可能不同
- 页面缩小版显示，交互不友好
- 链接不可点
- 有缩放，缩放后又有滚动

font-size 为 40px 等于 PC 上 12px 同等物理大小，不规范



## Meta 标签

```html
<meta content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no" name="viewport" id="viewport" />
```

- width：设置布局 viewport 的特定值（“device-width”）
- initial-scale：设置页面的初始缩放
- minimum-scale：最小缩放
- maximum-scale：最大缩放
- user-scalable：用户能否缩放

width=devie-width: 布局 viewport = 设备宽度

![1574343179111](../images/%E7%A7%BB%E5%8A%A8web.assets/1574343179111.png)

![1574343308449](../images/%E7%A7%BB%E5%8A%A8web.assets/1574343308449.png)



## 设计移动 web

![1574343659716](../images/%E7%A7%BB%E5%8A%A8web.assets/1574343659716.png)

方案二淘宝在用

方案一：

```html
<meta content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no" name="viewport" id="viewport" />
```



## Flexbox 弹性盒子布局

![1574344168705](../images/%E7%A7%BB%E5%8A%A8web.assets/1574344168705.png)

![1574344271417](../images/%E7%A7%BB%E5%8A%A8web.assets/1574344271417.png)

flex：1和flex：2是除了 width：100px ，剩余宽度三分之一和三分之二

![1574344409973](../images/%E7%A7%BB%E5%8A%A8web.assets/1574344409973.png)



![1574345183639](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345183639.png)

![1574345216117](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345216117.png)



## 响应式设计

![1574345393379](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345393379.png)

![1574345481501](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345481501.png)

![1574345634507](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345634507.png)

![1574345702786](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345702786.png)

![1574345741346](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345741346.png)

![1574345825117](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345825117.png)

![1574345887081](../images/%E7%A7%BB%E5%8A%A8web.assets/1574345887081.png)



## 移动web特别样式处理

![1574347602735](../images/%E7%A7%BB%E5%8A%A8web.assets/1574347602735.png)

![1574347915308](../images/%E7%A7%BB%E5%8A%A8web.assets/1574347915308.png)

![1574347970846](../images/%E7%A7%BB%E5%8A%A8web.assets/1574347970846.png)

![1574348334791](../images/%E7%A7%BB%E5%8A%A8web.assets/1574348334791.png)

![1574353106043](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353106043.png)

![1574353162941](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353162941.png)![1574353202870](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353202870.png)



## 终端交互优化

![1574348196569](../images/%E7%A7%BB%E5%8A%A8web.assets/1574348196569.png)

![1574353366690](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353366690.png)

![1574353504865](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353504865.png)

![1574353532819](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353532819.png)

![1574353667872](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353667872.png)

Tap 的 bug：点击了蒙层后，同时也触发了 红色的 click me

![1574353789815](../images/%E7%A7%BB%E5%8A%A8web.assets/1574353789815.png)

![1574356460594](../images/%E7%A7%BB%E5%8A%A8web.assets/1574356460594.png)

![1574356956537](../images/%E7%A7%BB%E5%8A%A8web.assets/1574356956537.png)

![1574357021010](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357021010.png)

![1574357397246](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357397246.png)

![1574357465884](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357465884.png)

![1574357569208](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357569208.png)

![1574357618111](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357618111.png)![1574357690175](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357690175.png)

![1574357756401](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357756401.png)

![1574357891613](../images/%E7%A7%BB%E5%8A%A8web.assets/1574357891613.png)