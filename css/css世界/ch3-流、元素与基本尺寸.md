#### 3.1 块级元素 block-level element
1. "块级元素"和"diplay为block元素"不是一个概念, 例如```<li>```元素默认的display值是list-item,```<table>```元素默认的display值是table,但它们都是"块级元素",都复合块级元素的基本特征,即一个水平流上只能单独显示一个元素,多个块级元素则换行显示。
```
配合clear属性清除浮动影响 https://demo.cssworld.cn/3/1-1.php
.clear:after{
    content: '';
    display: table; //也可以是block或者list-item
    clear:both
}
```
2. 每个元素都有两个盒子,外在盒子

#### 3.2 width/height作用的具体细节
##### 3.2.1 深藏不露的width:auto

width的默认值是auto,它至少包含了以下4种不同的宽度表现:
- 充分利用可用空间。比方说```<div>```和```<p>```这些元素的宽度默认是100%于父级容器的。这种充分利用可用空间的行为还有个专有名字fill-available
- 收缩与包裹。典型代表就是浮动,绝对定位,inline-block元素或table元素,英文称为shrink-to-fit。CSS3中的fit-content指的就是这种宽度表现。
- 收缩到最小。这个最容易出现在table-layout为auto的表格中。https://demo.cssworld.cn/3/2-1.php  这种行为在规范中被描述为preferred minumum width或者minimum content width 后来还有一个更好听的名字min-content。
- 超出容器限制。除非有明确的width相关设置,否则上面3种情况尺寸都不会主动超过父级容器宽度的,但是存在一些特殊情况。例如内容很长的连续的英文和数字,或者内联元素被设置了white-space:nowrap。https://demo.cssworld.cn/3/2-2.php 这个现象后来有了专门的属性值描述max-content。

在CSS世界中,尺寸也分"内部尺寸"和"外部尺寸", 其中"内部尺寸"英文写作Intrinsic Sizing 表示尺寸由内部尺寸决定,"外部尺寸"英文写作Extrinsic Sizing,宽度由外部元素决定。上面4种尺寸表现中,第一个是"外部尺寸",其余的全部是"内部尺寸"。

1. 外部尺寸与流体特性
(1) 正常流宽度
所谓流动性，并不是看不去的宽度100%显示那么简单,而是一种margin/border/padding和content内容区域自动分配水平空间的机制。 https://demo.cssworld.cn/3/2-3.php  这个例子中上下两个导航均有margin和padding,前者无width设置,完全借助流特性,后者宽度width:100%。结果后面的尺寸超出了外部的容器,即所谓的"流动性丢失"。
(2) 格式化宽度
格式化宽度仅出现在"绝对定位模型"中,即position属性值为absolute或fixed的元素中。在默认情况下,绝对定位元素的宽度表现为"包裹性",宽度由内部尺寸决定。但有一种情况其宽度是由外部尺寸决定的。
对于非替换元素,当left/top或top/bottom对立方位的属性值同时存在的时候,元素的宽度表现为"格式化宽度",其宽度大小相对于最近的具有定位特性(position的属性值不是static)的祖先元素计算。
```
div{position:absolute; left:20px; width:20px}
```
假设该元素最近的具有定位特性的祖先元素的宽度是1000像素,则这个元素的宽度是1000-20-20=960像素。
"格式化宽度"具有完全的流动性,也就是margin,border,padding和content内容区域同样会自动分配水平(和垂直)空间。

2. 内部尺寸与流体特性
在CSS世界中,"内部尺寸"有下面3种表现形式:
(1) 包裹性(shrink-to-fit):
"包裹性"除了"包裹",还有"自适应性"。所谓"自适应性"指的是元素尺寸有内部元素决定,但永远小于"包含块"容器的尺寸(除非容器尺寸小于元素的"首选最小宽度")。
因此对于一个元素,如果其display属性值是inline-block

#### 3.3 内联元素 inline element/inline-level element