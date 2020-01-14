## CSS绘制三角形—border法

### 1. 实现一个简单的三角形

使用CSS盒模型中的border（边框）即可实现如下所示的三角形：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-67c0a1d5b9f40bc2.webp)

CSS实现简单三角形

### 实现原理：

首先来看在为元素添加border时，border的样子；假设有如下代码：

```xml
<div></div>

div {
    width: 50px;
    height: 50px;
    border: 2px solid orange;
}
```

效果图：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-255fb1efa9b0ca20.webp)

border的一般使用

这是我们平常使用border最普遍的情况——往往只给border一个较小的宽度（通常为1-2px）；然而这样的日常用法就会容易让大家对border的形成方式产生**误解**，即认为元素的border是由四个矩形边框拼接而成。

然而事实并不是这样。实际上，元素的border是由**三角形**组合而成，为了说明这个问题，我们可以增大border的宽度，并为各border边设置不同的颜色：

```css
div {
    width: 50px;
    height: 50px;
    border: 40px solid;
    border-color: orange blue red green;
}
```

效果图：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-9dc8deee9d6c1922.webp)

border的形成方式

既然如此，那么更进一步，把元素的内容尺寸设置为0会发生什么情况呢？

```css
div {
    width: 0;
    height: 0;
    border: 40px solid;
    border-color: orange blue red green;
}
```

效果图：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-d69e514577bcca5e.webp)

元素内容尺寸为0

我们将惊奇地发现，此时元素由上下左右4个三角形“拼接”而成；那么，为了实现最终的效果，即保留最下方的三角形，还应该怎么做？很简单，我们只需要把其它border边的颜色设置为**白色**或**透明色**：

```css
div {
    width: 0;
    height: 0;
    border: 40px solid;
    border-color: transparent transparent red;
}
```



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-f1cfc55454bb35ad.webp)

最终效果

Duang~ 最终的简单三角形就绘制出来了。同理，如果想要得到其它边上的三角形，只需要将剩余的border边颜色设置为白色或透明色即可。

不过，被“隐藏”的上border仍然占据着空间，要想使得绘制出的三角形尺寸最小化，还需要将上border的宽度设置为0（其它情况同理）：

```css
div {
    width: 0;
    height: 0;
    border-width: 0 40px 40px;
    border-style: solid;
    border-color: transparent transparent red;
}
```

### 2. 实现带边框的三角形

带边框的三角形是指为三角形添加其它颜色的边框，如同为元素添加border一样：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-37145a70108d01a2.webp)

带边框的三角形

由于不能继续通过为已有三角形设置border的方法来为其设置边框（因为三角形本身就是利用border实现的），所以只好另想办法。而能想到的一个最自然的方法就是**三角形叠放**，即把当前三角形叠放在更大的三角形上方，上图所示的实现方法就是把黄色三角形放在了尺寸更大的蓝色三角形上。

为了实现这样的效果，需要利用**绝对定位**方法：
首先定义出外面的蓝色三角形：

```objectivec
<div id="blue"><div>

#blue {
    position:relative;
    width: 0;
    height: 0;
    border-width: 0 40px 40px;
    border-style: solid;
    border-color: transparent transparent blue;
}
```

效果为：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-84caea6299357cfa.webp)

外围蓝色三角形

随后需要定义黄色三角形，由于黄色三角形的定位需要参考蓝色三角形的位置，所以需要用到绝对定位方法。为此还需要将黄色三角形作为蓝色三角形的子元素。一个可行的办法是在蓝色三角形内部定义一个额外的标签以表示黄色三角形，但为了节约标签起见，更好的办法是使用**伪元素**:

```css
#blue:after {
    content: "";
    width: 0;
    height: 0;
    position: absolute;
    top: 0px;
    left: 0px;
    border-width: 0 40px 40px;
    border-style: solid;
    border-color: transparent transparent yellow;
}
```

得到的效果为：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-d7ff237ed3ffc81c.webp)

定义黄色三角形

需要特别注意此时定义出的黄色三角形与蓝色三角形之间位置的偏移关系，该偏移将受到`top`、`left`（本例中）以及黄色三角形本身border宽度的共同影响。

可能会有这样的疑问：为什么黄色三角形会向左偏移一段距离呢，按道理不应该完全重合在蓝色三角形上吗，就像下面这样？



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-15761237d6d81ea2.webp)

黄色三角形与蓝色三角形完全重合

如果有这样的疑问，说明还没有对绝对定位产生足够的认识。绝对定位的区域是基于**绝对定位父元素的padding区域**，然后在此基础上运用`top`、`left`、`right`、`bottom`等一系列属性来约束绝对定位子元素的位置。在本例中，由于蓝色三角形作为绝对定位父元素，其内容的尺寸为0，则内容区域就是该三角形的上顶点：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-c35c52cedc36f7c9.webp)

绝对定位区域

对于黄色三角形，由于设置了`left: 0`和`top: 0`，所以黄色三角形的**所有内容**（包括border、margin）将根据蓝色三角形的上顶点进行定位。可以把此时`left: 0`和`top: 0`分别看作是两面“隔墙”——即上隔墙和左隔墙，黄色三角形的所有内容只能在上隔墙的下方和左隔墙的右方区域。

由于黄色三角形的内容区域也位于其顶点处，且对其设置了左右各40px的border，所以黄色三角形的内容区域将向右偏移40px，从而形成了之前的效果。

想想看将黄色三角形的位置设置为`left: 0`和`bottom: 0`,会得到怎样的定位效果？（下图所示）



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-34a258fa4ad4c91d.webp)

黄色三角形设置为left: 0和bottom: 0

搞懂了绝对定位后，只需要在原代码上稍作修改就可以将黄色三角形的顶点与蓝色三角形顶点相重合，同时还应该适当缩小黄色三角形的尺寸（按相似三角形等比例缩小）：

```css
#blue:after {
    content: "";
    position: absolute;
    top: 0px;
    left: -38px;
    border-width: 0 38px 38px;
    border-style: solid;
    border-color: transparent transparent yellow;
}
```

得到：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-d9cef23dd7f148dc.webp)

黄色三角形与蓝色三角形顶点重合

在上面的代码中，特意删除了之前对`width: 0`和`height: 0`的设置，因为子元素具有`position:absolute`设置，这会使得元素尺寸在不显式设置宽度和高度的情况下，收缩到元素内容的尺寸，由于内容设置的是`content: ""`，所以子元素的尺寸默认也就是0了。故设置`width: 0`和`height: 0`就变得多余了。

最后一步就是利用`top`将黄色三角形向下移动至合适的位置：

```css
#blue:after {
    content: "";
    position: absolute;
    top: 1px;
    left: -38px;
    border-width: 0 38px 38px;
    border-style: solid;
    border-color: transparent transparent yellow;
}
```

得到最终效果：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-967284b77f0737f5.webp)

最终效果

学会了带边框三角形的绘制，那么实现类似如下三角形箭头自然也是不在话下了：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-e096eaa26b1921c7.webp)

三角形箭头

实现代码：

```css
#blue:after {
    content: "";
    position: absolute;
    top: 2px;
    left: -38px;
    border-width: 0 38px 38px;
    border-style: solid;
    border-color: transparent transparent #fff;
}
```

### 3. 绘制其它角度的三角形

绘制其它角度的三角形，如：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-3f1ef0f5ffb572c4.webp)

右直角三角

或者：



![img](../images/css%E6%8A%80%E5%B7%A7.assets/9397803-2fd247c5c56a1ca5.webp)

左直角三角

就更简单了，其实它们都是基于之前绘制的三角形而来的。如果想绘制右直角三角，则将左border设置为0，上 border 和 右border设置为 transparent；如果想绘制左直角三角，将右border设置为0即可（其它情况同理）



参考

> [CSS绘制三角形—border法](https://www.jianshu.com/p/9a463d50e441) 