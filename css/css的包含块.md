有时，一个元素的盒子的位置和尺寸根据一个确定的矩形计算，这个确定的矩形叫这个元素的包含块。一个元素的包含块根据以下规则确定：

1、根元素所在的包含块叫初始包含块 initial containing block。对于连续媒体设备（continuous media），初始包含块的大小等于视口viewpor的大小，基点在画布的原点（视口左上角）；对于分页媒体（paged media），初始包含块是页面区域（page area）。初始包含块的direction属性与根元素的相同。

2、对于其他元素，如果元素的position属性是relative或static，他的包含块是由最近的祖先块容器盒（block container ancestor box）的内容区域（content edge：width属性和height属性确定的区域）创建的。

3、如果一个元素的position属性为fixed，他的包含块由视口创建（连续媒体）或者由页面区域创建（paged media）。

4、如果元素的position为absolute，他的包含块由最近的position不为static的祖先元素创建，具体创建方式如下：

​           A.如果创建包含块的祖先元素是行内元素（inline element），包含块的范围是这个祖先元素中的第一个和最后一个行内盒的padding box围起来的区域。

​           B.如果这个祖先元素不是行内元素，包含块的范围是这个祖先元素的内边距+width区域（padding edge）。

如果没有找到这样的祖先元素，这个绝对定位的元素的包含块为初始包含块。

 

下面的文档中没有定位的元素：

```html
<HTML>
   <HEAD>
      <TITLE>Illustration of containing blocks</TITLE>
   </HEAD>
   <BODY id="body">
      <DIV id="div1">
      <P id="p1">This is text in the first paragraph...</P>
      <P id="p2">This is text <EM id="em1"> in the 
      <STRONG id="strong1">second</STRONG> paragraph.</EM></P>
      </DIV>
   </BODY>
</HTML>
```

包含块的创建如下表：

| 元素    | 创建其包含块的元素 |
| ------- | ------------------ |
| html    | 视口（初始包含块） |
| body    | html               |
| div1    | body               |
| P1      | div1               |
| P2      | div1               |
| em1     | P2                 |
| strong1 | P2                 |

如果添加以下CSS规则：

```css
#div1  { position: absolute; left: 50px; top: 50px }
#em1  { position: absolute; left: 100px; top: 100px }
```

 

包含块的创建如下表：

| 元素    | 创建其包含块的元素        |
| ------- | ------------------------- |
| html    | initial C.B. (初始包含块) |
| body    | html                      |
| div1    | initial C.B. (初始包含块) |
| p1      | div1                      |
| p2      | div1                      |
| em1     | div1                      |
| strong1 | em1                       |



参考

> [CSS学习笔记——包含块 containing block](https://www.cnblogs.com/fogwind/p/6017375.html) 
>
> [Definition of "containing block"](https://www.w3.org/TR/CSS2/visudet.html#containing-block-details) 