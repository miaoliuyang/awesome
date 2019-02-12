##### 6.1 魔鬼属性float
##### 6.1.1 float的本质与特性
浮动的本质就是为了实现文字环绕效果。

float的特性:
- 包裹性

所谓"包裹性",由"包裹"和"自适应性"两部分组成。

(1)包裹。假设浮动元素父元素宽度200px,浮动元素子元素是一个128px宽度的图片,则此时浮动元素宽度表现为"包裹",就是里面图片的宽度128px
```
.father{ width: 200px; }
.float{ float: left;}
.float img { width: 128px;}
<div class="father">
    <div class="float">
        <img src="1.jpg">
    </div>
</div>
```
(2)自适应性。如果浮动元素的子元素不只是一张128px宽度的图片,则此时浮动元素宽度就自适应父元素的200px宽度,最终的宽度表现也是200px。
```
.father{ width: 200px; }
.float{ float: left;}
.float img { width: 128px;}
<div class="father">
    <div class="float">
        <img src="1.jpg">文字内容文字内容文字内容文字内容文字内容文字内容文字内容文字内容文字内容文字内容
    </div>
</div>
```
当然要想最大宽度自适应父元素宽度,一定是在浮动元素的"首选最小宽度"比父元素的宽度要小的前提下。比如上面例子中的文字全是一连串超长的英文字母,则浮动元素的宽度显然就不是200px了。

- 块状并格式化上下文

块状化的意思是元素一旦float的属性值不为none,则其display计算值就是block或者table。(inline-table计算为table外,inline inline-block等都计算为block)
```
var span = document.createElement('span');
document.body.appendChild(span);
span.style.cssFloat = 'left';
console.log(window.getComputedStyle(span).display);
```

因此没有理由出现下面的样式组合。
```
span{
    display: block; /* 多余 */
    float: left;
}
span{
    float: left;
    vertical-align: middle; /* 多余 */
}
```
也不要指望使用text-align属性控制浮动元素的左右对齐,因为text-align对块级元素是无效的。

- 破坏文档流
- 没有任何margin合并
##### 6.1.2 float的作用机制
float属性有个著名的特性表现,就是会让父元素的高度塌陷。
```
<div class="father">
    <img class="float" src="1.jpg">
</div>
<p class="animal">小猫</p>
```
float属性让父元素高度塌陷的原因就是为了实现文字环绕效果。然而"高度塌陷"只是让跟随的内容可以和浮动元素在一个水平线上,但这只是实现"环绕效果"的条件之一,要想实现真正的"环绕效果",就需要另外一个特性,那就是"行框盒子和浮动元素的不可重叠性",也就是"行框盒如果和浮动元素的垂直高度由重叠,则行框盒子在正常定位状态下只会跟随浮动元素,而不会发生重叠"。注意这里说的是"行框盒子",也就是每行内联元素所在的那个盒子,而非外部的块状盒子。实际上,由于浮动元素的塌陷,块状盒子是和图片完全重叠的。

但是,块状盒子中的"行框盒子"却被浮动元素限制,没有任何的重叠,可以借助::first-line伪元素暴露第一行的"行框盒子"区域
```
.animal:first-line{
    background: red;
    color: white;
}
```
这种限制是根深蒂固的,也就是"行框盒子"的区域永远都那么大,只要不改变当前布局方式,是无法通过其他CSS属性改变这个区域大小的。这就是之前提到的浮动后面元素margin负无穷大依然无效的原因。例如这里新增如下CSS代码。
```
.animal{
    margin-left: -100px;
}
```
会发现只有外部的块状容器盒子尺寸变大,而和浮动元素垂直方向有重叠的"行框盒子"依然被限死在那里。

很多人会有这样的想法,就是认为一个元素只要设置了具体了高度值,就不需要担心float属性造成的高度塌陷的问题了,但是其中隐含了陷阱,因为"文字环绕效果"是由两个特性("父级高度塌陷"和"行框盒子区域限制")共同作用的结果。定高只能解决"父级高度塌陷"带来的影响,但是对"行框盒子区域限制"却没有任何效果,结果导致的问题是浮动元素垂直区域一旦超出高度范围,或者下面元素的margin-top负值上偏移,就很容易使后面的元素发生"环绕效果"。
```
<div class="father">
    <div class="float">
        <img src="1.jpg">
    </div>
    文字
</div>
<div>文字</div>
.father{
    height: 64px;
    border: 1px solid #444;
}
.float{
    float: left;
}
.float img{
    width: 60px; height: 64px;
}
```
由于内联状态下的图片底部是有间隙的,也就是.float这个浮动元素的实际高度并不是64px,而是要比64px高几像素,带来的问题就是浮动元素的高度超出.father几像素。于是下面的文字"文字"和浮动元素垂直位置有了重叠。http://demo.cssworld.cn/6/1-1.php

##### 6.1.3 float更深入的作用机制
```
<h3>我是一个非常长长到足以换行的标题文字内容<a href="#" style="float:right">更多</a></h3>
```
两个和float相关的术语。一个是浮动锚点(float anchor),二是浮动参考(float reference)
- 浮动锚点是float元素所在的"流"中的一个点,这个点本事不浮动,就表现而言更像一个没有margin border和padding的空的内联元素。
- 浮动参考指的是浮动元素对齐参考的实体。

在CSS世界中,float元素的"浮动参考"是"行框盒子",也就是float元素在当前"行框盒子"内定位。每一行内联元素都有一个"行框盒子",上面例子中标题文字比较多,两行显示了,因此有上下两个"行框盒子",而"更多"所在的a元素是在标题文字后面,位于第二行,因此这里设置了float:right的a元素是相对于第二行的"行框盒子"对齐的。

如果float元素前后全是块元素,那根本没有"行框盒子",此时就需要上面提到的"浮动锚点"出马了。其作用就是产生"行框盒子",因为"浮动锚点"表现如果一个空的内联元素,有内联元素自然就有"行框盒子",也就是float元素对齐的参考实体"行框盒子"对于块状元素也同样适用了,只不过这个"行框盒子"由于没有任何内容,所以无尺寸,看不见也摸不着。

##### 6.1.4 float与流体布局
```
<div class="fahter">
    <img src="1.jpg">
    <p class="animal">小猫</p>
</div>
.father{
    overflow:hidden;
}
.father > img{
    width: 60px; height:64px;
    float: left;
}

.animal {
    margin-left: 70px;
}
```
和文字环绕效果相比,区别就在于.animal多了一个margin-left:70px。.animal元素没有浮动,也没有设置宽度,因此流动性保持地保持地很好,设置margin-left border-left或者padding-left都可以自动改变content box的尺寸,继而实现了宽度自适应布局效果。http://demo.cssworld.cn/6/1-2.php

上面的技巧适用于一侧定宽一侧自适应,如果是宽度不固定,也有办法处理,如果是百分比宽度,则也是可以的。
```
.left{
    float: left;
    width: 50%;
}
.right{
    margin-left: 50%;
}
```

如果是多栏布局也同样适用。
```
<div class="box">
    <a href class="prev">&laquo;上一章</a>
    <a href class="next">下一章&raquo;</a>
    <h3 class="title">第112章 动物环绕</h3>
</div>
.prev{
    float: left;
}
.next{
    float: right;
}
.title{
    margin: 0 70px;
    text-align:center;
}
```
.title所在的h3标题元素直接左右margin,借助流体特性,保证不会和两个文字链接重叠。

#### 6.2 clear
##### 6.2.1 clear属性
```
clear: none | left | right | both
```
官方对clear属性的解释是: 元素盒子的边不能和前面的浮动元素相邻

设置了clear属性的元素自身如何如何,而不是float元素如何。clear属性值的理解如下
- none: 默认值
- left: 左侧抗浮动
- right: 右侧抗浮动
- both: 两侧抗浮动

凡是clear:left或者clear:right起作用的地方一定可以使用clear:both替换。

例如假设容器宽度足够宽,有10个li元素,设置了如下CSS代码
```
li{
    width: 20px; height: 20px;
    margin: 5px;
    float: left;
}
li: nth-of-type(3){
    clear: both;
}
```
最后列表是2行显示,而不是3行显示。原因在于clear属性是让自身不能和前面的浮动元素相邻,对后面的浮动元素是不闻不问的,因此才2行显示而非3行。

##### 6.2.2 clear缺点
clear属性只有块级元素才有效,而::aftre等伪元素默认都是内联水平,这就是借助伪元素清除浮动影响时需要设置display属性值的原因
```
.clear:after{
    content: '';
    display: block;
    clear: both;
}
```
http://demo.cssworld.cn/6/2-1.php

由于clear:both的作用本质是让自己不和float元素在一行显示,并不是真正意义上的清除浮动,因此float元素一些不好的特性依然存在,也是会有类似下面的现象。
(1)如果clear:both元素前面的元素就是float元素,则margin-top负值即使设成-9999px,也不见任何效果。
(2)clear:both后面的元素依旧可能会发生文字环绕的现象。