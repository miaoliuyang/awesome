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

#### 6.3 CSS世界的结界---BFC
##### 6.3.1 BFC的定义
BFC全称为block formatting context,中文为"块级格式化上下文"。如果一个元素具有BFC,内部子元素再怎么翻江倒海,都不会影响外部的元素。所以BFC元素是不可能发生margin重叠的,因为margin重叠是会影响外面的元素的,BFC原来也可以用来清除浮动的影响,因为如果不清楚,子元素浮动则父元素高度坍塌,必然会影响后面元素布局和定位,这显然有违BFC元素的子元素不会影响外部元素的设定。

如下场景会触发BFC:
- html根元素
- float的值不为none
- overflow的值为auto scroll或hidden
- display的值为table-cell table-caption和inline-block中的任何一个
- position的值不为relative和static

只要元素符合上面任意一个条件,就无须使用clear:both属性来清除浮动的影响了。因此不要见到一个div元素就加个类似.clearfix的类名。

##### 6.3.2 BFC与流体布局
BFC的结界特性更重要的用途其实不是去margin重叠或者是清除float的影响,而是实现更健壮,更智能的自适应布局。
```
<div class="father">
    <img src="1.jpg">
    <p class="animal">小猫1
</div>
img {float: left;}
```
如果给.animal元素设置具有BFC特性的属性,如overflow:hidden。则根据BFC的表现原则,具有BFC特性的元素的子元素不会受外部元素影响,也不会影响外部元素。于是这里的.animal元素为了不和浮动元素产生任何交集,顺着浮动边缘形成自己的封闭上下文。

普通流体元素在设置了overflow:hidden之后会自动填满容器中除了浮动元素以外的的剩余空间,形成自适应布局效果,而且这种自适应布局比纯流体自适应更加智能。比如让图片尺寸变大或变小,右侧自适应内容无效更改任何样式代码,都可以自动填满剩余的空间。

实际项目开发的时候,图片和文字会保持合适的间距。如果元素是左浮动,则浮动元素可以设置margin-right或透明border-right或padding-right;或者右侧BFC元素设置成透明border-left或padding-left,但不包括margin-left。因为如果要使用margin-left,则其值必须是浮动元素的宽度间隙大小就变成动态不可控了,无法大规模复用。假设希望间隙是10px,则下面这几种写法都是可以的。
- img{margin-right: 10px;}
- img{border-right: 10px solid transparent;}
- img{padding-right: 10px;}
- .animal{border-left: 10px solid transparent;}
- .animal{padding-left: 10px;}

#### 6.4 最佳结界overflow
要想彻底清除浮动的影响,最合适的属性不是clear而是overflow。一般使用overflow:hidden利用BFC的"结界"特性彻底解决浮动对外部或兄弟元素的影响。有很多其他CSS声明也能清除浮动,但基本上都会让元素的宽度变现为"包裹性",而overflow:hidden声明不会影响元素原先的流体特性或宽度变现。

###### 6.4.1 overflow剪裁界线border box
一个设置了overflow:hidden声明的元素,假设同时存在border属性和padding属性,类似如下CSS代码
```
.box{
    width:200px;height:800px;
    padding:10px;
    border:10px solid;
    overflow:hidden;
}
```
则当子元素内容超出容器宽度高度限制的时候,裁剪的边界是border box的内边缘,而非padding box的内边缘。http://demo.cssworld.cn/6/4-1.php

如果想实现元素裁剪同时四周留有间隙的效果的话,可以试试使用透明边框,此时内间距padding属性是无能无力的。

overflow属性的一个不兼容问题: chrome浏览器下,如果容器可滚动(假设是垂直滚动),则padding-box也算在滚动尺寸之内,IE和firefox浏览器忽略padding-bottom。例如上面的例子,把overflow属性改为auto时,滚动到底部会发现,chrome浏览器下面有10像素的空白,firefox和ie则没有。
#### 6.6 absolute与overflow

绝对定位元素不总是被父级overflow属性裁剪,尤其是overflow在绝对定位元素及其包括块之间的时候。

如果overflow不是定位元素,同时绝对定位元素和overflow容器之前也没有定位元素,则overflow无法对absolute元素进行裁剪。

下面HTML中的图片不会被裁剪
```
<div style="overflow:hidden;">
    <img src="1.jpg" style="position:absolute;">
</div>
```

overflow元素父级是定位元素也不会被裁剪
```
<div style="position:relative">
    <div style="overflow:hidden">  
        <img src="1.jpg" style="position:absolute;">
    </div>
</div>
```

但是如果overflow属性所在的元素同时也是定位元素,里面的绝对定位元素会被裁剪
```
<div style="overflow:hidden;position: relatvie;">
    <img src="1.jpg" style="position:absolute;"> <!-- 裁剪-->
</div>
```

如果overflow元素和绝对定位元素之间有定位元素,也会被裁剪
```
<div style="overflow:hidden;">
    <div style="position:relative;">  
        <img src="1.jpg" style="position:absolute;"><!-- 裁剪-->
    </div>
</div>
```

如果overflow的属性值不是hidden而是auto或者scroll,即使绝对定位元素高度比overflow元素高度还要大,也都不会出现滚动条。
```
<div class="box">
    <img src="1.jpg">
</div>

.box{
    width: 300px; height: 100px;
    background-color: #f0f3f9;
    overflow: auto;
}

.box > img{
    width: 256px; height: 193px;
    position: absolute;
}
```
实际开发的时候,绝对定位元素和非绝对定位元素往往看可能混在在一起,虽然绝对定位元素不能让滚动条出现,但是非绝对定位元素可以。于是就可能出现另外一种很有特色的现象。即当容器滚动的时候,绝对定位元素纹丝不动,不跟着滚动,表现类似fixed固定定位。
http://demo.cssworld.cn/6/5-11.php

由于position:fixed固定定位元素的包含块是根元素,因此除非是窗体滚动,否则上面讨论的所有overflow剪裁规则对固定定位都不适用。

#### 6.7 absolute与clip
clip属性要想起作用,元素必须是绝对定位或者固定定位,也就是position属性必须是absolute或者fixed.
```
clip: rect(top right bottom left)
```
这里的4个值不能缩写,且和border-width类似,不支持百分比值。
```
clip: rect(30px 200px 200px 200px)
```
可以想象手中有一把剪刀,面前有一块画布,上述css代码表示的含义是: 距离画布上边缘30px的地方剪一刀,距离画布右边缘200px的地方剪一刀,距离画布下边缘200px的地方剪一刀,距离画布左边缘20px的地方剪一刀。

##### 6.7.1 重新认识clip属性
1. fixed固定定位的裁剪

对于普通元素或者绝对定位元素,想要对其进行裁剪,可以利用语义更明显的overflow属性,但是对于position:fixed元素,因为fixed固定定位元素的包含块是根元素,除非是根元素滚动条,普通元素的overflow是根本无法对其进行裁剪的。此时就要用到clip属性
```
.fixed-clip{
    position: fixed;
    clip: rect(30px 200px 200px 200px);
}
```

2. 最佳可访问性隐藏

举个例子,很多网站左上角都有包含自己网站名称的标识(logo) 而这些标识一般都是图片,为了更好地SEO以及无障碍识别, 一般会使用h1标签写上网站的名称。
```
<a href="/" class="logo">
    <h1>CSS世界</h1>
</a>
```

如何隐藏h1标签中的"CSS世界"这几个文字,通常有以下一些技术选型
- 下策是display: none或者visibility: hidden隐藏, 因为屏幕阅读设备会忽略这里的文字
- text-indent缩进是中策,但文字如果缩进过大,大到屏幕之外,屏幕阅读设备也是不会读取的
- color: transparent是移动端上策,但却是桌面端中策,因为原生IE8浏览器并不支持它。color:transparent声明,很难用简单的方式阻止文本被框选中。
- clip剪裁隐藏式上策,既满足视觉上的隐藏,屏幕阅读设备等辅助设备也支持得很好
```
.logo h1{
    position: absolute;
    clip: rect(0 0 0 0);
}
```

clip裁剪被称为"最佳可访问性隐藏"。任何元素 任何场景都可以无障碍使用。哪里需要"可访问性隐藏"就加一个类名.clip即可。
```
.clip{
    position: absolute;
    clip: rect(0 0 0 0 )
}
```

##### 6.7.2 深入了解clip的渲染
```
.box{
    width: 300px; height: 100px;
    background-color: #f0f3f9;
    position: relative;
    overflow: auto;
}
.box > img {
    width: 256px; height: 192px;
    position: absolute;
    clip: rect(0 0 0 0);
}
```
图片显然看不见了,但是在chrome浏览器下,.box元素的滚动条依旧存在。在IE和Firefox浏览器下是没有滚动条的。这又是"未定义行为"的表现。

使用clip进行裁剪的元素其clientWidth和clientHeight包括样式计算的宽高都还是原来的大小。clip仅仅是决定了哪一部分是可见的,对于原来占据的空间并无影响。非可见部分无法影响点击事件等; 然后,虽然视觉上隐藏,但是元素的尺寸依然是原来的尺寸。
#### 6.8 absolute的流体特性
##### 6.8.1 当absolute遇到left/top/right/bottom属性
当absolute遇到left/top/right/bottom属性的时候,absolute元素才真正变成绝对定位元素。
```
.box{
    position: absolute;
    left: 0; right:; 0;
}
```
表示相对于绝对定位元素包含块的左上角对齐,此时原本的相对特性丢失.但是如果仅仅设置了一个方向的绝对定位,另一个方向依然保持相对特性。

##### 6.8.2 absolute的流体特性
绝对定位元素也就有流体特性,当然不是默认就有的,而是在"对立方向同时发生定位的时候"这一特定条件下。

当一个绝对定位元素,其对立定位方向属性同时有具体定位数值的时候,流体特性就发生了。
```
<div class="box"></div>
.box{
    position: absolute;
    left: 0; right: 0;
}
```
如果只有left属性或者只有right属性,则由于包裹性,此时.box宽度是0。但是left和right同时存在,所以宽度就不是0,而是表现为"格式化宽度",宽度大小自适应于.box包含块的padding box,也就是如果包含块padding box宽度发生变化,box的宽度也会随着一起变。

因此假设.box元素的包含块石根元素,则下面的代码可以让.box元素正好完全覆盖浏览器的可视窗口,并且如果改变浏览器窗口大小, .box会自动跟着一起变化。
```
.box{
    position: absolute;
    left: 0; right: 0; top: 0; bottom: 0;
}
```

设置了对立定位属性的绝对定位元素的表现神似普通的div元素,无论设置padding还是margin,其占据的空间一直不变,变化的就是content box的尺寸,这就是典型的流体表现特性。

绝对定位元素这种流体特性比普通元素要更强大,普通元素流体特性只有一个方向,默认是水平方向,但是绝对定位元素可以让垂直方向和水平方向同时保持流动性。
##### 6.8.3 absolute的margin:auto剧中
当绝对定位元素处于流体状态的时候,各个盒模型相关属性的解析和普通流体元素都是一模一样的。margin负值可以让元素的尺寸变大,并且可以使用margin:auto让绝对定位元素保持居中。

绝对定位元素的margin:auto的填充规则和普通流体元素的一模一样:
- 如果一侧定值,一侧auto,auto为剩余空间大小
- 如果两侧均是auto,则平分剩余空间

唯一区别是绝对定位元素的margin:auto居中是从IE8浏览器开始支持的,而普通元素的margin:auto居中很早就支持了。

如果绝对定位元素的尺寸是已知的,利用绝对定位元素的流体特性和margin:auto的自动分配特性实现居中
```
.element{
    width: 300px; height: 200px;
    position: absolute;
    left:0; right:0; top:0; bottom:0;
    margin:auto;
}
```
没有必要使用如下方法
```
.elemenet{
    width: 300px; height: 200px;
    position: absolute;
    left: 50%;
    right: 50%;
    transform: translate(-50%, -50%); /* 50%为自身尺寸的一半 */
}

.element{
    width: 300px; height: 200px;
    position: absolute;
    left: 50%;
    right: 50%;
    margin-left: -150px;
    mergin-top: -100px;
}
```


#### 6.9 position:relative
##### 6.9.1 relative对absolute的限制
虽然relative/absolute/fixed都能对absolute的"包裹性"以及"定位"产生限制,但只有relative可以让元素依然保持在正常的文档流中。
```
.box{
    position: absolute;
    left: 0; right: 0;top: 0;bottom: 0;
}
.icon{
    width: 20px; height: 20px;
    position: relative;
}
<div class="icon">
    <div class="box"></div>
</div>
```
此时.box尺寸被限制到20px`*`20px
##### 6.9.2 relative与定位
relative的定位有两大特性 一是相对自身 二是无侵入。
http://demo.cssworld.cn/6/6-1.php

relative的定位还有两点值得一提: 相对定位元素的left/top/right/bottom的百分比值是相对于包含块计算的,而不是自身,虽然定位位移是相对自身。

top和bottom这两个垂直方向的百分比值计算跟height的百分比值是一样的,都是相对于高度计算的。同时如果包含块的高度是auto,那么计算值是0,偏移无效,也就是说如果父元素没有设定高度或者不是格式化高度,那么relative类似top:20%的代码等同于top:0

当相对定位元素同时应用对立方向定位值的时候,也就是top/bottom和left/right同时使用的时候,其表现和绝对定位差异很大,绝对定位是尺寸拉伸,保持流体特性,但是相对定位只有一个方向的定位属性会起作用。哪个方向的属性起作用与文档流的顺序有关。默认的文档流是自上而下,从左往右,因此top/bottom同时使用的时候,top生效,left/right同时使用的时候,left生效。

##### 6.9.3 relative的最小化影响原则
relative的最小化影响原则:

(1)尽量不使用relative, 如果想定位某些元素,看看能否使用"无依赖的绝对定位"

(2)如果一定要使用relative,则该relatvie务必最小化。
#### 6.10 position:fixed
##### 6.10.1 position:fixed不一样的"包含块"
position:fixed固定定位元素的"包含块"是根元素,我们可以将其近似看成html元素。换句话说,唯一可以限制固定定位元素的就是html元素。

和"无依赖的绝对定位"类似,就是"无依赖的固定定位",利用absolute/fixed元素没有设置left/top/right/bottom的相对定位特性,可以将目标元素定位到想到的位置。
```
<div class="father">
    <div class="right">
        &nbsp;<div class="son"></div>
    </div>
</div>

.father{
    width: 300px; height: 200px;
    position: relative;
}
.right{
    height: 0;
    text-align: right;
    overflow: hidden;
}
.son{
    display: inline;
    width: 40px;
    height: 40px;
    position: fixed;
    margin-left: -40px;
}
```
##### 6.10.2 position:fixed的absolute模拟
```
<body>
    <div class="page">固定定位元素</div>
    <div class="fixed"></div>
</body>
html,body {
    height: 100%;
    overflow: hidden;
}

.page {
    height: 100%;
    overflow: auto;
}

.fixed {
    position: absolute;
}
```
整个页面的滚动条由.page元素产生,而非根元素,此时.fixed元素虽然是绝对定位,但是并不在滚动元素内部,自然滚动时不会跟随,如同固定定位效果,同时本身绝对定位。因此可以使用relative进行限制或者overflow进行裁剪等。

##### 6.10.3 position:fixed与背景锁定
背景锁定时,如果是移动端项目,阻止touchmove事件的默认行为可以防止滚动;如果是桌面端项目,可以让根元素直接overflow:hidden。但是windows操作系统下的浏览器的滚动条都是占据一定宽带的,滚动条的消失必然会导致页面的可用宽带变化,页面会产生晃动问题。可以使用border属性填补消失的滚动条。

蒙层显示的同时执行下面的代码
```
var widthBar = 17,root = document.documentElement;
if(typeof window.innerWidth = 'number'){
    widthBar = window.innerWidth - root.clientWidth;
}
root.style.overflow = 'hidden';
root.style.borderRight = widthBar + 'px solid transparent';
```
隐藏的时候执行下面的代码
```
var root = document.documentElement;
root.style.overflow = '';
root.style.borderRight = '';
```