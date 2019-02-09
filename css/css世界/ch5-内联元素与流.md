#### 5.1 字母x

##### 5.1.1 字母x与CSS世界的基线
在各种内联相关模型中,凡是涉及垂直方向的排版或者对齐的,都离不开最基本的基线(baseline)。例如line-height行高的定义就是两基线的间距,vertical-align的默认值就是基线。

字母x的下边缘(线)就是基线。

##### 5.1.2 字母x与CSS中的x-height
通俗地讲x-height指的就是小写字母x的高度。术语描述就是基线和等分线(mean line)(也称作中线,midline)之前的距离。

CSS中有些属性的定义和这个x-height有关,最典型的就是vertical-align:middle。middle指的是基线往上1/2 x-height高度。可以近似理解为字母x交叉点那个位置。

由此可见，vertical-align:middle并不是绝对的垂直居中对齐,平常看到的middle效果只是一种近似效果。因为不同的字体在行内盒子中的位置是不一样的,比如 微软雅黑 就是一个字符下沉比较明显的字体，所有字符的位置都比其他字体要偏下一点儿。也就是说, 微软雅黑 字体的字母x的交叉点是在容器中分线的下面一点。不难理解为什么vertical-align：middle不是相对于容器中分线对齐的了,因为在毕竟CSS世界中文字内容是主体,所以对于内联元素垂直居中应该是对文字,而非居外部的块级容器所言。

##### 5.1.3 字母x与CSS中的ex
ex是CSS中的一个相对单位,指的就是小写字母x的高度。就是指x-height

由于字母x受字体等CSS属性影响大,不稳定,因此ex不太适合用来限定元素的尺寸。但可以用来实现不受字体和字号影响的内联元素的垂直居中对齐效果。

内联元素默认都是基线对齐的,而基线就是x的底部,而1ex就是一个x的高度。http://demo.cssworld.cn/5/1-1.php
```
.icon-arrow{
    display: inline-block;
    width: 20px;
    height: 1ex;
    background: url(arrow.png) no-repeat center;
}
```

##### 5.2 内联元素的基石line-height
##### 5.2.1 内联元素的高度之本line-height
```
<div class="test1"></div>
.test1{
    font-size: 16px;
    line-height: 0;
    border: 1px solid #ccc;
    background: #eee;
}

<div class="test2"></div>
.test2{
    font-size: 0;
    line-height: 16px;
    border: 1px solid #ccc;
    background: #eee;
}
```
div的高度是由行高决定的,而非文字。http://demo.cssworld.cn/5/2-1.php

对于非替换元素的纯内联元素,其可视高度完全由line-height决定。padding, border属性对可视高度是没有任何影响的,这也是我们平常口中的"盒模型"约定俗成说的是块级元素的原因。

对于文本这样的纯内联元素,line-height就是高度计算的基石,专业说法就是指定了用来计算行框盒子高度的基础高度。line-height设为16px,则一行文字高度就是16px,两行就是32px,三行就是48px。

内联元素的高度由固定高度和不固定高度组成,这个不固定的部分就是这里的"行距"。line-height之所以起作用,就是通过改变"行距"来实现的。"行距"分散在当前文字的上方和下方,也就是即使是第一行文字,其上方也是有"行距的",只不过这个"行距"的高度仅仅是完整"行距"高度的一半,因此也称为"半行距"。

行距 = 行高 - em-box。em-box是CSS世界中比较虚的一个概念,其高度正好就是1em。em是一个相对font-size大小的CSS单位。因此1em等用于当前一个font-size大小。所以 行距 = line-height - font-size

很多人把文字图形区域看成是em-box范围,实际上这是不正确的。比方说一些带尾巴的英文字符q或者g,其小尾巴是在em-box范围之外的,而对于汉字,很多字体图形高度实际上要小于em-box高度的。

内容区域(content area)可以近似理解成firefox/IE浏览器下文本选中带背景色的区域。在大多数场景下,内容区域和em-box是不一样的,内容区域高度受font-family和font-size双重影响,而em-box仅受font-size影响,通常内容区域高度要更高一些。除了下面这种情况。也就是"当我们的字体是宋体的时候,内容区域和em-box是等同的"
```
.test{
    font-family: simsun;
    font-size: 80px;
    line-height: 120px;
    background-color: yellow;
}

.test > span{
    background-color: white;
}

<div class="test">
    <span>sphinx</span>
</div>
```
http://demo.cssworld.cn/5/2-2.php

假设line-height是1.5,font-size大小是14px,那么我们的半行距大小就是(14px`*`1.5 - 14px)/2 = 3.5px border以及line-height等传统CSS属性并没有小数像素的概念。因此这里的3.5px需要取整处理,如果标注的是文字上边距,则向下取整;如果是文字下边距,则向上取整。

当line-height设为2的时候,半行距是一半的文字大小,两行文字中间的间隙差不多一个文字尺寸大小;如果line-height大小是1倍文字大小,根据计算,半行距是0,也就是两行文字会紧密依偎在一起;如果line-height值是0.5 则此时的行距就是负值,虽然line-height不支持负值,但是行距可以为负值,此时两行文字就重叠纠缠在一起。http://demo.cssworld.cn/5/2-3.php

line-height不可以影响替换元素(如图片的高度)
```
.box{
    line-height: 256px;
}
<div class="box">
    <img src="1.jpg" height="128">
</div>
```
此时.box元素高度却是256px,但不是line-height把图片占据高度变高了,而是把"幽灵空白节点"的高度变高了。图片为内联元素,会构成一个"行框盒子",而在HTML5文档模式下,每一个"行框盒子"的前面都有一个宽度为0的"幽灵空白节点",其内联特性变现和普通字符一模一样,所以这里的容器高度会等于line-height设置的属性值256px。

实际开发的时候,图文和文字混在一起是很常见的,那这种内联替换元素和内联非替换元素在一起时的高度表现又是怎样的呢?

由于同属内联元素,因此会共同形成一个"行框盒子",line-height在这个混合元素的"行框盒子"中扮演的角色是决定这个行盒的最小高度。对于纯文本元素,line-height直接决定了最终的高度。但是如果同时有替换元素,则line-height的变现就只能决定最小高度,对最终的高度表现有望尘莫及之感。这是由于替换元素的高度不受line-height影响,另外就是vertical-align属性在背后作祟。

对于这种混合替换元素的场景,line-height要想一统江山,需要值足够大才行。但是实际开发的时候,line-height设置的值总是中规中矩,于是就会出现类似下面的场景,明明文字设置了line-height为20px,但是如果文字后面有小图标,最后"行框盒子"高度却是21px或者22px。这种现象背后最大的黑手其实就是vertical-align属性。

对于块级元素,line-height对其本身是没有任何作用的,我们平时改变line-height块级元素的高度随着变化实际上是通过改变块级元素里面内联级别元素占据的高度实现的。

比如一个p元素有10行图文内容,则这个p元素的高度就是由这10行内容产生的10个"行框盒子"高度累加而成的.一个article元素中可能会有20个p元素,则这个articel元素又是由这20个p元素高度累加而成的,同时块级元素还可以通过height和min-height以及盒模型中的margin,padding和border等属性改变占据的高度。所有这一切就构成了CSS世界完整的高度体系。

因为line-height几乎无所不在的继承特性,并且CSS世界是为了更好地图文展示,所以line-height不仅是内联元素高度的基石,而且还是整个CSS世界高度体系的基石。

#### 5.2.2 为什么line-height可以让内联元素"垂直居中"
要想让单行文字垂直居中,只要设置line-height大小和height高度一样就可以了。类似下面这样的代码
```
.title{
    height: 24px;
    line-height: 24px;
}
```
从效果上看,似乎验证了这种说法的正确性。但是实际上上面的说法会产生两个误区。

误区之一:要让单行文字垂直居中,只需要line-height这一属性就可以,与height一点关系也没有。直接如下设置就可以。
```
.title{
    line-height: 24px;
}
```

误区之二:行高控制文字垂直居中,不仅适用于单行,多行也是可以的。准确的说法应该是line-height可以让单行或多行元素近似垂直居中。

行高可以实现"垂直居中"原因在于CSS中"行距的上下等分机制"。如果行距的添加规则是在文字的上方或者下方,则行高是无法让文字垂直居中的。

说"近似"是因为文字字形的垂直中线位置普遍要比真正的"行框盒子"的垂直中线位置低。

多行文本或者替换元素的垂直居中实现原理和单行文本就不一样了,需要vertical-align属性帮助才可以。http://demo.cssworld.cn/5/2-4.php
```
.box{
    line-height: 120px;
    background-color: #f0f3f9;
}
.content{
    display: inline-block;
    line-height: 20px;
    margin: 0 20px;
    vertical-align: middle;
}
<div class="box">
    <div class="content">基于行高实现的...</div>
</div>
```

实现的原理大致如下:
(1)多行文字使用一个标签包裹,然后设置display为inline-block。好处在于既能重置外部的line-height为正常的大小,又能保持内联元素特性,从而可以设置vertical-align属性,以及产生一个非常关键的"行框盒子"。我们需要的其实并不是这个""行框盒子",而是每个"行框盒子"都会附带的一个产物--幽灵空白节点。有了这个幽灵空白节点,line-height就有了作用的对象,从而相当于在.content元素前面撑起了一个高度为120px的宽度为0的内联元素。
(2)因为内联元素默认都是基线对齐的,所以通过对.content元素设置vertial-align:middle来调整多行文本的垂直位置,从而实现"垂直居中"效果。如果是要借助line-height来实现图片垂直居中效果,也是类似的原理和做法。

#####5.2.3 深入line-height的各类属性值
line-height的默认值是normal,还支持数值,百分比值以及长度值。

本质上normal实际上是一个变量,是一个和font-family有着密切关系的变量值。
|字体    | chrome | firefox |  IE   |
| ------ | ------ | ------ | ------ |
|微软雅黑 |1.32   |1.321   |1.32   |
|宋体 |1.141   |1.142   |1.141   |

只要字体确定,各个浏览器下的默认line-height解析值基本上都是一样的。然而不同的浏览器使用的默认中英文字体并不是一样的,并且不同操作系统的默认字体也不一样。换句话说,就是不同系统不同浏览器的默认line-height都是有差异的。因此实际开发的时候,对line-height的默认值进行重置是势在必行的。

- 数值.如line-height:1.5,其最终的计算值是和当前font-size相乘后的值。
- 百分比值.如line-height:150%,其最终的计算值是和当前font-size相乘后的值。
- 长度值.也就是带单位的值,如line-height:21px或者line-height:1.5em等。此处em是一个相对于font-size的相对单位,因此line-height:1.5em最终的计算值也是和当前font-size相乘后的值。

三者的继承细节有所差别。如果使用数值作为line-height的属性值,那么所有的子元素继承的都是这个值。但是如果使用百分比值或者长度值作为属性值,那么所有的子元素继承的是最终的计算值。

实际上line-height:150%,line-height:1.5em要想有line-height:1.5的继承效果,也是可以实现的。类似下面的CSS代码
```
*{
    line-height: 150%;
}
```
就是使用通配符匹配所有的元素。既然line-height数值可以让元素天然继承相对计算特性,那这里的通配符岂不完全没必要?

其实不然,两者还是有区别的。HTML中的很多替换元素,尤其表单类的替换元素,如输入框,按钮之类的,很多具有继承特性的CSS属性其自己也有一套。如font-family font-size以及line-height。由于继承是属于最弱的权重,因此body中设置的line-height是无法影响到这些替换元素的。但是`*`作为一个选择器就不一样了,会直接重置这些替换元素默认的line-height,这其实是我们需要的。但考虑到`*`的性能,可以折中使用下面的方法
```
body{
    line-height: 1.5;
}
input,button{
    line-height: inherit;
}
```

##### 5.2.4 内联元素line-height的"大值特性"
```
<div class="box">
    <span>内容...</span>
</div>

.box{
    line-height: 96px;
}
.box span{
    line-height: 20px;
}

和

.box{
    line-height: 20px;
}
.box span{
    line-height: 96px;
}
```
这两者的.box元素的高度都是96px。http://demo.cssworld.cn/5/2-6.php

无论内联元素line-height如何设置,最终父级元素的高度都是由数值大的那个line-height决定的。称之为"内联元素line-height的大值特性"。

这里的span是一个内联元素,因此自身是一个内联盒子。本例就一个内联盒子,只要有内联盒子在,就一定会有行框盒子,就是每一行内联元素外面包裹的一层看不见的盒子。在每个行框盒子前面有一个宽度为0的看不见的幽灵空白节点。当.box元素设置line-height:96px时,"字符"高度96px;当设置line-height:20px时,span元素的高度则变成了96px,而行框盒子的高度是由高度最高的那个内联盒子决定的,也就是.box元素高度永远都是最大的那个line-height的原因。

要避免幽灵空白节点的干扰,设置span元素display:inline-block,创建一个独立的行框盒子,这样span元素设置为line-height:20px就可以生效了,这也是多行文字垂直居中示例中这么设置的原因。

#### 5.3 vertical-align
```
.box{ line-height: 32px; }
.box > span { font-size: 24px; }
<div class="box">
    <span>文字</span>
</div>
```
上例中.box元素的高度并不是32px,而是要大那么几像素(受不同字体影响,增加高度也不一样)比方说36px。http://demo.cssworld.cn/5/3-1.php

##### 5.3.1 vertical-align基本认识
vertical-align属性值分为以下4类:
- 线类,如baseline(默认值) top middle bottom
- 文本类,如text-top text-bottom
- 上标下标类,如sub super
- 数值百分比类,如20px 2em 20%

数值百分比类分为"数值类"和"百分比类",这两类具有相同的渲染规则。具体为:根据计算值的不同,相对于基线往上或往下偏移,到底是往上还是往下取决于vertical-align的计算值是正值还是负值,如果是负值,往下偏移,如果是正值,往上偏移。

vertical-align数值类属性值的表现查看http://demo.cssworld.cn/5/3-2.php

由于vertical-align的默认值是baseline,即基线对齐,而基线的定义是字母x的下边缘。因此内联元素默认都是沿着字母x的下边缘对齐的,对于图片等替换元素,往往使用元素本身的下边缘作为基线。

由于是相对字母x的下边缘对齐,而中文和部分英文字形的下边缘要低于字母x的下边缘,因此会给人感觉文字是明显偏下的,一般都会进行调整。比方说我们给文字内容设置vertical-align:10px,则文字内容就会在当前基线位置再往上精确偏移10px。

http://demo.cssworld.cn/5/3-3.php

vertical-align属性的百分比值是相对于line-height的计算值计算的。假设某元素的line-height是20px,那么此时vertical-align:-25%相当于设置vertical-align:-5px

###### 5.3.2 vertical-align作用的前提
vertical-align起作用是有前提条件的,这个前提条件就是:只能应用于内联元素以及display值为table-cell的元素。

换句话说,vertical-align属性只能作用于display计算值为inline inline-block inline-table或table-cell的元素上。因此默认情况下span strong em等内联元素和img button input等替换元素以及td单元格dou都是支持vertical-align属性的,其他块级元素则不支持。

有一些CSS属性值会在背后默默地改变元素display属性的计算值,从而导致vertical-align不起作用。比方说浮动和绝对定位会让元素块状化。因此下面的代码组合vertical-align是没有理由出现的
```
.example{
    float: left;
    vertical-align: middle; /* 没有作用 */
}

.example{
    position: absolute;
    vertical-align: middle; /* 没有作用 */
}
```

```
.box{ height: 128px; }
.box > img { height: 96px; vertical-align: middle; }
<div class="box">
    <img src="1.jpg">
</div>
```
此时图片顶着.box元素的上边缘显示,根本没垂直居中,完全没起作用。这种情况看上去是vertical-align:middle没起作用,实际上vertical-align是在努力地渲染的,只是行框盒子前面的"幽灵空白节点"高度太小,如果我们通过设置一个足够大的行高让"幽灵空白节点"高度足够,就会看到vertical-align:middle起作用了。
```
.box{
    height: 128px;
    line-height: 128px; /* 关键CSS属性 */
}
.box > img{
    height: 96px;
    vertical-align: middle;
}
```

```
.cell{
    height: 128px;
    display: table-cell;
}
.cell > img{
    height: 96px;
    vertical-align: middle;
}
<div class="cell">
    <img src="1.jpg">
</div>
```
结果图片并没有要垂直居中的迹象,还是紧贴着父元素的上边缘。但是如果vertical-align:middle是设置在table-cell元素上,图片就有了明显的变化。
```
.cell{
    height: 128px;
    display: table-cell;
    vertical-align: middle;
}
.cell > img{
    height: 96px;
}
```
虽然就效果而言,table-cell元素设置vertical-align垂直对齐的是子元素,但是其作用的并不是子元素,而是table-cell元素自身。就算table-cell元素的子元素是一个块级元素,也一样可以让其有各种垂直对齐表现。http://demo.cssworld.cn/5/3-4.php

##### 5.3.3 vertical-align和line-height之间的关系
最明显的关系就是vertical-align的百分比值是相对于line-height计算的,但表面所见的这点关系只是冰山一角,实际是只要出现内联元素,这对好朋友一定会同时出现。
```
.box{ line-height: 32px; }
.box > span { font-size: 24px; }
<div class="box">
    <span>文字</span>
</div>
```
其中有一个很关键的点那就是24px的font-size大小是设置在span元素上的,这就导致了外部div元素的字体大小和span元素有较大出入。

span标签前面实际上有一个看不见的类似字符的幽灵空白节点。可以是用一个看得见的字符x占位,同时"文字"后面也添加一个x,便于看出基线位置。
```
<div class="box">
    x<span>文字x</span>
</div>
```
此时可以明显看到两处大小完全不同的文字。一处是字母x构成了一个匿名内联盒子,另一处是"文字x"所在的span元素,构成了一个"内联盒子"。由于都受line-height:32px影响。因此这两个"内联盒子"的高度都是32px。对字符而言,font-size越大字符的基线位置越往下,因为文字默认全部都是基线对齐,所以当字号大小不一样的两个文字在一起的时候,彼此就会发生上下位移,如果位移距离足够大,就会超过行高的限制,而导致出现意料之外的高度。

问题的解决方法,可以让幽灵空白节点和后面span元素字号一样大,也就是
```
.box{
    line-height: 32px;
    font-size: 24px;
}
```
或者改变垂直对齐方式,如顶部对齐,这样就不会有参差位移了
```
.box{ line-height: 32px; }
.box > span{
    font-size: 24px;
    vertical-align: top;
}
```

搞清楚了大小字号文字的高度问题,对更为常见的图片底部留有间隙的问题的理解就容易多了。现象如下:任意一个块级元素,里面若有图片,则块级元素高度基本上都要比图片的高度高
```
.box{
    width: 280px;
    outline: 1px solid #aaa;
    text-align: center;
}
.box > img {
    height: 96px;
}
<div class="box">
    <img src="1.jpg">
</div>
```
结果.box元素的高度底部平白无故多了5像素。为了直观演示原理,可以在图片前面辅助一个字符x代替"幽灵空白节点"。当前line-height计算值是20px,而font-size只有14px,因此字母x往下一定有至少3px的半行间距(具体大小与字体有关),而图片作为替换元素其基线是自身的下边缘。根据定义,默认和基线(也就是这里字母x的下边缘)对齐,字母x往下的行高产生的多余的间隙就嫁祸到图片下面。让人以为是图片产生的间隙,实际上是幽灵空白节点,line-height和vertical-align属性共同作用的结果。

要清楚该间隙,就知道如何对症下药了。
(1)图片块状化。可以一口气干掉幽灵空白节点 line-height和vertical-align
(2)容器line-height足够小。只要半行间距小到字母x的下边缘位置或再往上,自然就没有了撑开底部间隙高度空间了。比方说设置line-height:0
(3)容器font-size足够小。此方法要想生效,需要容器的line-height属性值和当前font-size相关,如line-height:1.5或者line-height:150%之类,否则只会让下面的间隙变得更大,因为极限位置因字符x变小而往上升了。
(4)图片设置其他vertical-align属性值。间隙的产生原因之一就是基线对齐,所以设置vertical-align的值为top middle bottom中的任意一个都是可以的。
http://demo.cssworld.cn/5/3-5.php

下面的"内联特性导致的margin无效"案例
```
<div class="box">
    <img src="1.jpg">
</div>
.box > img{
    height: 96px;
    margin-top: -200px;
}
```
按照理解-200px远远超过图片的高度,图片应该完全跑到容器的外面,但是图片依然有部分在.box元素中,而且就算margin-top设置为-99999px,图片也不会继续往上移动,完全失效。其原理和上面图片底部留有间隙实际上是一样的,图片的前面有一个幽灵空白节点,而在CSS世界中,非主动触发位移的内联元素是不可能跑到计算容器外面的.导致图片的位置被"幽灵空白节点"的vertical-align:baseline给限死了。可以使用字符x代替这个看不见的幽灵空白节点。因为字符x下边缘和图片下边缘对其,字符x非主动定位,不可能跑到容器外面,所以margin-top失效。

最后再看一个更为复杂的示例。text-align：justify声明可以帮忙我们实现兼容的列表两端对齐效果,但是text-align:justify两端对齐需要内容超过一行,同时为了让任意个数的列表最后一行也是左对齐排列,我们需要在列表最后辅助和列表宽度一样的空标签元素来占位
```
.box{
    text-align: justify;
}
.justify-fix{
    display: inline-block;
    width: 96px;
}
<div class="box">
    <img src="1.jpg" width="96">
    <img src="1.jpg" width="96">
    <img src="1.jpg" width="96">
    <img src="1.jpg" width="96">
    <i class="justify-fix"></i>
    <i class="justify-fix"></i>
    <i class="justify-fix"></i>
</div>
```
空的inline-block元素的高度是0,结果。box的下面有非常大的空隙存在。这个间隙的存在都是vertical-align和line-height共同作用的结果。按照之前解决问题的方法,可以直接给.box元素来个line-height:0 解决垂直间隙问题。结果这样设置之后图片和图片之间的间隙是没有了,但是图片和最后的占位元素之间依然有几像素的间距。这个是由于inline-block元素和基线baseline之间的关系引起的。

##### 5.3.4 深入理解vertical-align线性类属性值
1.inline-block与baseline
vertical-align属性的默认值是baseline。在文本之类的内联元素那里就是字符x的下边缘,对于替换元素则是替换元素的下边缘。但是如果是inline-block元素,则规则要复杂了。一个inline-bloc元素,如果里面没有内联元素,或者overflow不是visible,则该元素的基线就是其margin底边缘;否则其基线就是元素里面最后一行内联元素的基线。

两个同尺寸的inline-block水平元素,唯一区别就是一个是空的,一个里面有字符.
```
.dib-baseline{
    display: inline-block;
    width: 150px; height: 150px;
    border: 1px solid #cad5eb;
    background-color: #f0f3f9;
}
<span class="dib-baseline"></span>
<span class="dib-baseline">x-baseline</span>
```
明明尺寸,display水平都是一样的,结果两个却不再一个水平线上对齐。这是由于第一个框里没有内联元素,因此基线就是容器的margin下边缘,也就是下边框下面的位置。而第二个框里面有字符,纯正的内联元素,因此第二个框就是这些字符的基线,也就是字母x的下边缘了。

如果上面例子中line-height设置为0后。因为字符实际占据的高度是由line-height决定的,当line-height变成0的时候,字符占据的高度也是0,此时高度的起始位置就变成了字符内容区域的垂直中心位置,于是文字就有一半落在框的外面了。由于文字字符上移了,自然基线位置(字母x的底边缘)也往上移动了,也是两个框的垂直落差就更大了。

2.了解vertical-align: top/bottom
vertical-align:top就是垂直上边缘对齐。具体定义如下:
- 内联元素: 元素底部和当前行框盒子的顶部对齐。
- table-cell元素: 元素底padding边缘和表格行的顶部对齐。

用更通俗的话解释就是:如果是内联元素,则和这一行位置最高的内联元素的顶部对齐;如果display计算值是table-cell的元素,不放脑补成td元素,则和td元素上边缘对齐。

vertical-align:bottom声明与此类似,只是把"顶部"换成"底部",把"上边缘"换成"下边缘"。

需要注意的是内联元素的上下边缘对齐的这个"边缘"是当前"行框盒子"的上下边缘并不是块状容器的上下边缘。

3.vertical-align:middle与近似垂直居中
- 内联元素: 元素的垂直中心点和行框盒子基线往上1/2 x-height处对齐
- table-cell元素: 单元格填充盒子相对于外面的表格行居中对齐。

vertical-align:middle可以让内联元素的真正意义上的垂直中心位置和字符x的交叉点对齐。

基本上所有的字体中,字符x的位置都是偏下一点儿的,font-size越大偏移越明显。这导致默认状态下的vertical-align：middle实现的都是"近似垂直居中"。http://demo.cssworld.cn/5/3-8.php

如果想实现真正意义上的垂直居中状态,只要想办法让字符x的中心位置就是容器的垂直中心位置即可,通常的做法是设置font-size:0 整个字符x缩小成了一个看不见的点,根据line-height的半行间距上下等分规则,这个点就正好是整个容器的垂直中心位置,这样就可以实现真正意义上的垂直居中对齐了。

不过平常我们开发的时候,font-size可能就12px或14px,虽然最终的效果是"近似垂直居中",但偏差也就1px~2px的样子,普通用户其实是很难觉察到其中的差异的。

##### 5.3.5 深入理解vertical-align文本类属性值
##### 5.3.6 简单了解vertical-align上标下标类属性值
##### 5.3.7 无处不在的vertical-align
##### 5.3.8 基于vertical-align属性的水平垂直居中弹框