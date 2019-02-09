#### 4.1 深入理解content
##### 4.1.1 content与替换元素
1.什么是替换元素
替换元素(replaced element)可以说是CSS世界中的另外一个派系。根据"外在盒子"是内联还是块级我们可以把元素分为内联元素和块级元素,而根据是否具有替换元素,可以把元素分为替换元素和非替换元素。

通过修改某个属性值呈现的内容就可以被替换的元素就称为"替换元素".```<img> <object> <video> <iframe>或者表单元素<textarea> <input>```都是典型的替换元素。

替换元素除了内容可替换这一特性以外,还有以下一些特性:
- 内容的外观不受页面上的CSS的影响。用专业的话讲就是样式表现在CSS作用域以外。如何更改替换元素本身的外观?需要类似appearance属性,或者浏览器自身暴露的一些接口样式,例如::-ms-check{}可以更改高版本IE浏览器下单复选框的内间距,背景色等样式,但是直接input[type='checkbox']{ }却无法更改内间距,背景色等样式。只能通过 input[type=checkbox]::-ms-check{}进行修改。
- 有自己的尺寸。在web中,很多替换元素在没有明确尺寸设定的情况下,其默认的尺寸(不包括边框)是300像素*150像素,如```<video> <iframe>或者<canvas>```等,也有少部分替换元素为0像素,如```<img>```图片,而表单元素的替换元素的尺寸和浏览器有关,没有明显的规律。
- 在很多CSS属性上有自己的一套表现规则。比较具有代表性的就是vertical-align属性。对于替换元素和非替换元素,vertical-align属性值的解释是不一样的。比方说vertical-align的默认值baseline被定义为字符x的下边缘。但是替换元素的内容往往不可能含有字符x,于是替换元素的基线就被硬生生定义成了元素的下边缘。

2.替换元素的默认display值
所有的替换元素都是内联水平元素,也就是替换元素和替换元素,替换元素和文字都是可以一行显示的。但是替换元素默认的display值却是不一样的。
|元素    | chrome | firefox |  IE   |
| ------ | ------ | ------ | ------ |
|```<img>``` |inline   |inline   |inline   |
|```<iframe>``` |inline   |inline   |inline   |
|```<video>``` |inline   |inline   |inline   |
|```<select>``` |inline-block   |inline-block   |inline-block   |
|```<input>``` |inline-block   |inline   |inline-block   |
|```range | file<input>``` |inline-block   |inline-block   |inline-block   |
|```hidden <input>``` |none   |none   |none   |
|```<button>``` |inline-block   |inline-block   |inline-block   |
|```<textarea>``` |inline-block   |inline   |inline-block   |

```<input type="button">和<button>```按钮的区别:两种按钮默认的white-space值不一样,前者是pre,后者是normal,所表现出现的现象差异就是当按钮文字足够多的时候,```<input>```按钮不会自动换行,```<button>```按钮则会。

3.替换元素的尺寸计算规则
将替换元素的尺寸从内而外分为3类: 固有尺寸,HTML尺寸和CSS尺寸
(1)固有尺寸
固有尺寸指的是替换内容原有的尺寸。例如图片,视频作为一个独立文件存在的时候都有自己的宽度和高度的。这个宽度和高度的大小就是这里的"固有尺寸"。对于表单类替换元素,"固有尺寸"可以理解为"不加修饰的默认尺寸"。比如在空白页面写上```<input>```,此时的尺寸就可以看成是```<input>```元素的固有尺寸。这就是输入框,下拉框这些变单元素默认的font-size/padding/margin等属性全部使用px作为单位的原因,因为这样可以保证这些元素的固有尺寸是固定大小,不会受外界CSS的影响。

(2)HTML尺寸
HTML尺寸只能通过HTML原生属性改变,这些HTML原生属性包括```<img>```的width和height属性,```<input>```的size属性,```<textarea>```的cols和rows属性等。

```<img width="300" height="100"><input type="file" size="30"> <textarea cols="20" rows="5"></textarea>```

(3)CSS尺寸
CSS尺寸指可以通过CSS的width和height或者max-width/min-width和max-height/min-height设置的尺寸,对应盒模型的content box。

- 如果没有CSS尺寸和HTML尺寸,则使用固有尺寸最为最终的宽高。```<img src="1.jpg">```假设1.jpg这张图片原尺寸是256像素`*`192像素,则在页面中此图所呈现的宽高就是256像素`*`192像素。
- 如果没有CSS尺寸,则使用HTML尺寸作为最终的宽高。`<img src="i.jpg" width="128" height="96">`最终图片所呈现的宽高就是128像素`*`96像素。
- 如果有CSS尺寸,则最终尺寸由CSS属性决定。
```
img{width: 200px; height: 150px;}
<img src="1.jpg" width="128" height="96">
```
最终图片所呈现的宽高就是200像素`*`150像素。
- 如果"固有尺寸"含有固有的宽高比例,同事仅设置了宽度或者仅设置了高度,则元素依然按照固有的宽高比例显示
```
img{width: 200px;}
<img src="1.jpg" >
```
图片这种替换元素的资源本身具有特定的宽高比例,因此height也会等比例计算。
- 如果上面的条件都不符合,则最终宽度表现为300像素,高度为150像素,宽高为2:1。`<video></video>`在所有现代浏览器下的尺寸表现都是300像素`*`150像素。

内联替换元素和块级替换元素使用上面同一套尺寸计算规则。
```
img{display:block}
<img src="1.jpg">
```
虽然图片此时变成了块级,但是尺寸规则还还是和内联状态下一致。因为图片呈现的宽高还是图片原始尺寸的大小。也就是为何图片以及其他表单类替换元素设置display:block宽度却没有100%容器的原因。

需要注意`<img>`这个表示一个没有替换内容也没有尺寸设定的裸露的img元素。按照上述规则应该是300像素`*`150像素。结果不仅不是这个尺寸,而且各个浏览器下的尺寸还不一样。IE浏览器下是28`*`30,chrome浏览器下是0`*`0,firefox浏览器下显示的是0`*`22

WEB开发的时候为了提高加载性能以及节约宽度费用,首屏以下的图片就会通过滚屏加载的方式异步加载。这个即将被异步加载的图片为了布局稳健,体验良好,往往会使用一张透明的图片占位.例如`<img src="transparent.png">`实际上这个透明的占位图片也是多余的资源。可以直接使用如下方式实现一样的效果。
```
<img>

img{visibility:hidden;}
img[src]{visibility:visible;}
```
这里的img是没有src属性,而不是src=""。src=""在很多浏览器下依然会有请求,而且请求的是当前页面数据。只有没有src属性的时候,图片才不会有任何请求。

对于firefox浏览器,src默认的img不是替换元素,而是一个普通的内联元素,所以使用的就不试替换元素的尺寸规则,而是类似span的内联元素的尺寸规则.宽高会无效。
```
img{width:200px;height:150px;}
<img>
```
在IE和chrome浏览器下都按照预期图片尺寸200像素`*`150像素,但是firefox浏览器依然是默认图片尺寸。要修复此兼容性问题,只需设置`img{display: inline-block;}`

替换元素的固有尺寸有一个很重要的特性,那就是无法改变这个替换元素内容的固有尺寸。

```
div:before{
    content: url(1.jpg);
    display: block;
    width: 200px; height:200px;
}
```
此时::before伪元素呈现的图片的宽高并不是200像素`*`200像素。而是这张图片的原始尺寸大小。width和height属性都被直接无视了。在css世界中,图片资源的固有尺寸是无法改变的。http://demo.cssworld.cn/4/1-1.php

回到img元素,既然图片的固定尺寸不受CSS宽高限制,为何设定width和height会影响图片的尺寸? 这是因为图片中的content替换内容默认的适配方式是填充(fill),也就是外部设定的尺寸多大,我就填满跟着一样大。所以尺寸变化的本质并不是改变固有尺寸,而是采用了填充作为适配HTML尺寸和CSS尺寸的方式。在CSS3新世界中,img和其他一些替换的替换内容的适配方式可以通过object-fit属性修改。例如img元素的默认声明是object-fit:fill,如果设置object-fit:none,那么图片的尺寸就完全不受控制。变现会和非替换元素::before生成的图片尺寸类似;如果设置object-fit:contain则图片会以保持比例图片,尽可能利用HTML尺寸但又不会超出的方式显示。

4.替换元素和非替换元素的距离有多远
(1)替换元素和非替换元素直接只隔了一个src属性
如果把img的src属性去掉,img其实就和一个和span类似的普通内联标签,成了一个非替换元素。firefox浏览器证实了这一点。
```
img{display:block; outline:1px solid;}
<img>
```
按照替换元素的尺寸规则,宽度应该是0.但是firefox浏览器下最终的宽度是100%自适应父容器的可用宽度的。

chrome浏览器也有类似的表现,只是需要有不为空的alt属性值。`<img alt="任意值">`

在IE浏览器下没有src属性时还是完全的替换元素表现,原因在于IE浏览器中有个默认的占位替换内容,当src属性缺失的时候,会使用这个默认的占位内容,这也是IE浏览器下默认img尺寸是28`*`30而不像chrome浏览器那样为0`*`0的原因。在高版本的IE浏览器下,这个占位的替换内容作了透明处理。
http://demo.cssworld.cn/4/1-2.php

(2)替换元素和非替换元素之间只隔了一个CSS content属性
替换元素之所以是替换元素,就是因为其内容可替换,而这个内容就是margin,border,padding和content这4个盒子中的content box,对应的CSS属性是content,content属性决定了是替换元素还是非替换元素。

chrome浏览器下所有的元素都支持content属性,而其他浏览器仅在::before/::after伪元素中才有支持。
```
img:not([src]){content:url(1.png);}
<img>
```
结果和`<img src="1.png">`视觉效果一模一样。并且各种变现都复合替换元素,如尺寸规则,或者不支持::before/::after伪元素等。http://demo.cssworld.cn/4/1-3.php

另外如果图片原来有src地址,也可以使用content属性把图片内容给置换掉。http://demo.cssworld.cn/4/1-4.php
```
<img src="1.png">
img:hover{
    content:url("2.png")
}
```
content属性改变的仅仅是视觉呈现,当我们以右键或其他形式保存这张图片的时候,所保存的还是原来src对应的图片。

使用content属性还可以让普通标签元素变成替换元素。http://demo.cssworld.cn/4/1-5.php

5.content与替换元素关系剖析
(1)使用content生成的文本是无法选中,无法复制的,好像设置了user-select:none声明一样,但是普通元素的文本可以轻松选中。同时content生成的文本无法被屏幕阅读设备读取,也无法被搜索引擎抓取。
(2)不能左右:empty伪类。:empty是一个css选择器,当元素里面无内容的时候进行匹配。http://demo.cssworld.cn/4/1-6.php。
(3)content动态生成值无法获取。content是一个非常强大的CSS属性,其中一个强大之处就是计数器效果,可以自动累加数值.
```
.total:after{
    content:counter(icecream);
}
```
getComputedStyle方法可以获得伪元素的计算样式。但是得到的只是纯粹的content在css文件中的属性值.
```
var dom = document.querySelector('.total');
window.getComputedStyle(dom, "::after").content //结果是counter(icecream)
```
##### 4.1.2 content内容生成技术
content属性几乎都用在::before/::after这两个伪元素中,因此content内容生成技术有时候也称为::before/::after伪元素技术。IE8浏览器仅支持单冒号的伪元素。
1.content辅助元素生成

#### 4.2 温和的padding属性
##### 4.2.1 padding与元素的尺寸
CSS默认的box-sizing是content-box,所以使用padding会增加元素的尺寸。
```
.box{
    width: 80px;
    padding: 20px;
}
```
如果不考虑其他CSS干扰,此时.box元素所占据的宽度应该是120px。很多人会设置box-sizing为border-box。局部使用,尽量采用无宽度以及宽度分离准则。

很多人可能有这样的误区,如果设置了box-sizing：border-box,元素尺寸就不会变化。大多数情况下是这样的,但是如果padding值足够大,那么width也无能为力了。例如
```
.box{
    width: 80px;
    padding: 20px 60px;
    box-sizing: border-box;
}
```
此时的width会无效,最终宽度为120像素(60px`*`2),而里面的内容则表现为"首选最小宽度"。

上述尺寸表现是对具有块级特性的元素而言的,对于内联元素(不包括图片等替换元素)表现则有些许不同。很多人有这么一个错误的认知: 内联元素的padding只会影响水平方向,不会影响垂直方向。

这种认知是不准确的,内联元素的padding在垂直方向同样会影响布局,影响视觉表现。只是因为内联元素没有可视宽度和可视高度的说法(clientHeight和clientWidth永远是0)垂直方向的行为表现完全受line-height和vertical-align的影响,视觉上并没有改变和上一行下一行内容的间距,因此给我们的感觉就会是垂直padding没有起作用。

如果我们给内联元素加个背景色或者边框,就可以看出其尺寸空间确实受padding影响了。http://demo.cssworld.cn/4/2-1.php
```
a{
    padding: 50px;
    background-color: #cd0000;
}
```

尺寸虽有效,但是对上下元素的原本布局却没有任何影响,仅仅是垂直方向发生了层叠。

CSS中还有很多其他场景或属性会出现这种不影响其他元素布局二十出现层叠效果的现象。比如relative元素的定位,盒阴影box-shadow以及outline等。这些层叠现象虽然看似类似,但实际上是有区别的。其分为两类:一类是纯视觉效果,不影响外部尺寸;另一类则是会影响外部尺寸。box-shadow以及outline属于前者,而这里的inline元素的padding层叠属于后者。区分的方式很简单,如果父容器overflow:auto层叠区域超出父容器的时候,没有滚动条出现,则是纯视觉的;如果出现滚动条,则会影响尺寸,影响布局。

实际上,对于非替换元素的内联元素,不仅padding不会加入盒高度的计算，margin和border也都是如此,都是不计算高度,但实际上在内联盒周围发生了渲染。

##### 4.2.2 padding的百分比值
padding属性值,和margin属性不同，padding属性值不支持负值;padding支持百分比值,但是和height等属性的百分比计算规则有些差异,差异在于:padding百分比值无论是水平方向还是垂直方向都是相对于宽度计算的。

实现横贯整个屏幕的头图效果,使用padding进行等比例控制,小屏幕下头图高度天然等比例缩小。http://demo.cssworld.cn/4/2-3.php
```
.box{
    padding: 10% 50%;
    position: relative;
}

.box > img{
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
}
```

上面的百分比值是应用在具有块状特性的元素上的,如果是内联元素,表现如下:
- 同样相对于宽度计算;
- 默认的高度和宽度细节有差异;
- padding会断行

```
.box{
    border: 2px dashed #cd0000;
}
span{
    padding: 50%;
    background-color: gray;
}
<div class="box">
    <span>内有文字若干</span>
</div>
```
表现诡异之处有: "内有"两个字不见了,"文字"两字居右显示了,背景区域非矩形,背景色宽带和外部容器宽度不一致等。

对于内联元素,其padding是会断行的,也就是padding区域是跟着内联盒模型中的行框盒子走的。上面的例子中由于文字比较多,一行显示不了，于是"若干"两字换到下一行,于是原本的padding区域也跟着一起掉下来了,根据后来居上的层叠规则,"内有"两字自然被正好覆盖,于是看不见了。同时规则的矩形区域由于换行,也变成了五边形。至于宽度和外部容器盒子不一样宽,那是自然的,如果没有任何文字内容,那自然宽带正好和容器一致,现在有"内有文字若干"这几个字,实际宽度就是容器宽带和这6给字宽带的总和.

假如是空的内联元素,里面没有任何文字,仅有一个span标签。此时会发现最终背景区域的宽度和高度是不相等的。padding:50%相对宽度计算,应该出来是个正方形。为何高度要高出一截。这是因为内联元素的垂直padding会让"幽灵空白节点"展现,也就是规范中的strut出现。要解决此问题,由于内联元素默认的高度完全受font-size大小控制,只要
```
span{
    padding: 50%;
    font-size: 0;
    background-color: gray;
}
```

##### 4.2.3 标签元素内置的padding
(1)ol/ul列表内置padding-left,但是单位是px不是em
(2)很多表单元素都内置padding

#### 4.3 激进的margin属性
padding负责内间距,margin负责外间距
##### 4.3.1 margin与元素尺寸以及相关布局
1.元素尺寸的相关概念
- 元素尺寸: 对应jQuery中的$().width()和$().height()方法,包括padding和border,也就是元素的border box的尺寸。在原生的DOM API中写作offsetWidth和offsetHeight。有时候也称为"元素偏移尺寸"
- 元素内部尺寸: 对应jQuery中的$().innerWidth()和$().innerHeight()方法,表示元素的内部区域尺寸,包括padding但不包括border,也就是元素的padding box的尺寸。在原生的DOM API中写作clientWidth和clientHeight,有时候也称为"元素可视尺寸"
- 元素外部尺寸: 对应jquery中的$().outerWidth(true)和$().outerHeight(true)方法,表示元素的外部尺寸,不仅包括padding和border,还包括margin。也就是元素的margin box的尺寸。没有相对应的原生的DOM API

"外部尺寸"有个不一样的特性,就是尺寸的大小有可能是负数。可以把"外部尺寸"理解成"元素占据的空间尺寸"。

2.margin与元素的内部尺寸
margin同样可以改变元素的可视尺寸,但是和padding几乎是互补态势。对于padding,元素设定了width或者保持"包裹性"的时候,会改变元素可视尺寸;但是对于margin则相反,元素设定了width或者保持"包裹性"的时候,margin对尺寸没有影响,只有元素是"充分利用可用空间"状态的时候,margin才可以改变元素的可视尺寸。
```
.father{
    width: 300px;
    margin: 0 -20px;
}
```
此时元素宽度还是300像素,尺寸无变化。因为只要宽度设定,margin就无法改变元素尺寸,这和padding是不一样的。

但是如果是下面这样的HTML和CSS
```
<div class="father">
    <div class="son"></div>
</div>

.father{ width: 300px; }
.son{ margin: 0 -20px; }
```
则.son元素的宽带就是340像素。尺寸通过负值设置变大了,因为此时的宽度表现是"充分利用可用空间"

只要元素的尺寸表现符合"充分利用可用空间",无论是垂直方向还是水平方向,都可以通过margin改变尺寸。

CSS世界默认的流方向是水平方向,因此对于普通流体元素,margin只能改变元素水平方向尺寸;但是对于具有拉伸特性的绝对定位元素,则水平或垂直方向都可以,因为此时的尺寸表现符合"充分利用可用空间"。

由于margin具有这种流体特性下的改变尺寸特性,因此margin可以很方便地实现很多流体布局效果。例如一侧定宽的两栏自适应布局效果,假设我们定宽的部分是128像素宽的图片,自适应的部分是文字。
(1)如果图片左侧定位
```
.box {overflow: hidden;}
.box > img { float: left; }
.box > p { margin-left: 140px; }
<div class="box">
    <img src="1.jpg">
    <p>文字内容....</p>
</div>
```
此时,文字内容就会根据.box盒子的宽带变化而自动排列,形成自适应布局效果,无论盒子是200像素还是400像素,布局依然良好,不会像纯浮动布局那样发生错位。

(2)如果图片右侧定位,只要图片的左浮动改成右浮动,文字内容的左margin改成右margin即可。
```
.box { overflow: hidden; }
.box > img { float: right; }
.box > p { margin-right: 140px; }
```

(3)如果图片右侧定位,同时顺序一致
```
.box{ overflow: hidden; }
.full{ width: 100%; float: left; }
.box > img { float: left; margin-left: -128px; }
.full > p { margin-right: 140px; }
<div class="box">
    <div class="full">
        <p>文字内容...</p>
    </di>
    <img src="1.jpg">
</div>
```
http://demo.cssworld.cn/4/3-1.php

3.margin与元素的外部尺寸
对于普通块状元素,在默认的水平流下,margin只能改变左右方向的内部尺寸,垂直方向则无法改变。如果我们使用writing-mode改变流向为垂直流,则水平方向内部尺寸无法改变,垂直方向可以改变。这是由margin:auto的计算规则决定的。

但是对于外部尺寸,margin属性的影响则更为广泛,只要元素具有块状特性,无论有没有设置width/height,无论是水平方向还是垂直方向,即使发生了margin合并,margin对外部尺寸都发生了影响。只是很多时候,抛开定位而言,我们似乎对外部尺寸的变化不是很敏感,实际上,CSS世界中不少棘手的问题都是需要借助margin的外部尺寸特性来实现的。

##### 4.3.2 margin的百分比值
和padding属性一样,margin的百分比值无论是水平方向还是垂直方向都是相对于宽度计算的。不过相对于padding,margin的百分比值得应用价值就低了一截。根本原因在于和padding不同,元素设置margin在垂直方向上无法改变元素自身的内部尺寸,往往需要父元素作为载体,此外由于margin合并的存在,垂直方向往往需要双倍尺寸才能和padding表现一致。
```
.box{
    background-color: olive;
    overflow: hidden;
}
.box > div {
    margin: 50%;
}
<div class="box"><div></div></div>
```
结果.box是一个宽高比为2：1的橄榄绿长方形

##### 4.3.3 正确看待CSS世界里的margin合并
1.什么是margin合并
块级元素的上外边距(margin-top)与下外边距(margin-bottom)有时会合并为单个外边距,这样的现象称为"margin合并"。从此定义上,可以捕获两点重要的信息
(1)块级元素,但不包括浮动和绝对定位元素,尽管浮动和绝对定位可以让元素块状化
(2)只发生在垂直方向,这种说法在不考虑writing-mode的情况下才是正确的,严格来讲,应该是只发生在当前文档流方向的相垂直的方向上。由于默认是水平流,因此发生margin的合并的就是垂直方向。

2.margin合并的3种场景
(1)相邻兄弟元素margin合并
```
p{ margin: 1em 0;}
<p>第一行</p>
<p>第二行</p>
```
则第一行和第二行之间的间距还是1em。因为第一行的margin-bottom和第二行的margin-top合并在一起了。

(2)父级和第一个/最后一个子元素
```
<div class="father">
    <div class="son" style="margin-top: 80px"></div>
</div>

<div class="father" style="margin-top: 80px">
    <div class="son"></div>
</div>

<div class="father" style="margin-top: 80px;">
    <div class="son" style="margin-top: 80px;"></div>
</div>
```
http://demo.cssworld.cn/4/3-3.php

如果阻止这里margin合并的发生：
对于margin-top合并,可以进行如下操作(满足一个条件即可)：
- 父元素设置为块状格式化上下文元素
- 父元素设置border-top值
- 父元素设置padding-top值
- 父元素和第一个子元素之前添加内联元素进行分隔。

对于margin-bottom合并,可以进行如下操作(满足一个条件即可)：
- 父元素设置为块状格式化上下文元素
- 父元素设置border-bottom值
- 父元素设置padding-bottom值
- 父元素和最后一个子元素之间添加内联元素进行分隔
- 父元素设置height min-height或者max-height

所以上面因为margin合并导致头图掉下来的问题可以添加下面的CSS代码进行修复
```
.container {
    overflow: hidden;
}
```
其原理就是通过设置overflow属性让父级元素块状格式化上下文

(3)空块级元素的margin合并
```
.father{ overflow: hidden;}
.son { margin: 1em 0;}
<div class="father">
    <div class="son"></div>
</div>
```
此时.father所在的这个父级div元素高度仅仅是1em,因为.son这个空div元素的margin-top和margin-bottom合并在一起了。

如果不希望空div元素有margin合并,可以进行如下操作:
- 设置垂直方向的border
- 设置垂直方向的padding
- 里面添加内联元素(直接space键空格是没用的)
- 设置height或者min-height

3.margin合并的计算规则
margin合并的计算规则总结为"正正取大值" "正负值相加" "负负最负值"
(1)正正最大值。如果是相邻兄弟合并:
```
.a {margin-bottom: 50px; }
.b {margin-top: 20px;}
<div class="a"></div>
<div class="b"></div>
```
此时.a和.b两个div之间的间距是50px

如果是父子合并:
```
.father{ margin-top: 20px; }
.son{ margin-top: 50px;}
<div class="father">
    <div class="son"></div>
</div>
```
此时.father元素等同于设置了margin-top：50px

如果是自身合并:
```
.a { margin-top: 20px; margin-bottom: 50px;}
<div class="a"></div>
```
此时.a元素的外部尺寸是50px

(2)正负值相加。如果是相邻兄弟合并
```
.a{ margin-bottom: 50px;}
.b{ margin-top: -20px; }
<div class="a"></div>
<div class="b"></div>
```
此时.a和.b两个div之间的间距是30px

如果是父子合并
```
.father{ margin-top: -20px; }
.son { margin-top: 50px; }
<div class="father">
    <div class="son"></div>
</div>
```
此时.father元素等同于设置了margin-top:30px

如果是自身合并
```
.a{margin-top: -20px; margin-bottom: 50px;}
<div class="a"></div>
```
此时.a元素的外部尺寸是30px

(3)负负最负值。如果是相邻兄弟合并
```
.a{ margin-bottom: -50px; }
.b{ margin-top: -20px; }
<div class="a"></a>
<div class="b"></b>
```
此时 .a和.b两个div之间的间距是-50px

如果是父子合并
```
.father{margin-top: -20px;}
.son{ margin-top: -50px;}
<div class="father">
    <div class="son"></div>
</div>
```
此时 .father元素等同于设置了margin-top: -50px

如果是自身合并
```
.a { margin-top:-20px; margin-bottom: -50px;}
<div class="a"></div>
```
则此时.a的外部尺寸是-50px

##### 4.3.4 深入理解CSS中的margin: auto

(1)有时候元素就算没有设置width或height,也会自动填充.例如
`<div></div>`此div宽度就会自动填满容器。
(2)有时候元素就算没有设置width或height,也会自动填充对应的方位。例如
```
div{ position:absolute; left: 0; right: 0;}
```
此时div宽度会自动填满包含块容器。此时如果设置了width或height,自动填充特性就会被覆盖。例如
`div{width: 200px}` 此时div宽度就被限制成了200px，无法自动填充外部容器的可用宽度了。

假设外部的容器宽度是300px,则有100px的宽度被闲置,margin:auto就是为了填充这个闲置的尺寸而设计的。

margin:auto的填充规则如下:
(1)如果一侧定值,一侧auto,则auto为剩余空间大小
(2)如果两侧均是auto,则平分剩余空间

```
.father { width: 300px; }
.son{ width: 200px; margin-right: 80px; margin-left: auto}
```
http://demo.cssworld.cn/4/3-4.php

由于CSS世界中margin的初始值大小是0,因此上面的例子如果margin-right缺失,实现的效果正好是块级元素的右对齐效果
```
.son {
    width: 200px;
    margin-left: auto;
}
```

如果想让某个块状元素右对齐,不一定使用float:right。很多时候margin-left:auto才是最佳实践。margin属性的auto计算就是为块级元素左中右对其而设计的,和内联元素使用text-align控制左中右对齐正好呼应。

居中对齐左右同时auto计算即可
```
.son{
    width: 200px;
    margin-right: auto;
    margin-left: auto;
}
```

为什么明明容器定高,元素定高,margin: auto却无法垂直居中?
```
.father{
    height: 200px;
}

.son{
    height: 100px;
    margin: auto;
}
```
原因在于触发margin:auto计算有一个前提条件,就是width或height为auto时,元素是具有对应方向的自动填充特性的。比方说这里,假设说把.son元素的height: 100px去掉,.son的高度会自动和父元素等高变成200px吗?显然不会,因此无法触发margin:auto计算,故而无法垂直居中。

垂直方向margin实现居中的方法。第一种方法是使用writing-mode改变文档流的方向
```
.father{
    height: 200px;
    writing-mode: vertical-lr;
}
.son{
    height: 100px;
    margin: auto;
}
```
此时.son就是垂直居中对齐的。但是这样水平方向就无法auto剧中了。

让水平垂直同时居中的方法。就是这里要提的第二种方法,绝对定位元素的margin: auto居中。
```
.father{
    width: 300px;
    height: 150px;
    position: relative;
}
.son{
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
}
```
此时.son这个元素的尺寸表现为格式化宽带和格式化高度。和div的正常流宽度一样,同属于外部尺寸,也就是尺寸自动填充父级元素的可用尺寸,此时我们给.son设置尺寸
```
.son{
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 200px;
    height: 100px;
}
```
此时宽高被限制，原本应该填充的空间就被空余了出来,这多余的空间就是margin: auto计算的空间。因此,如果这时候再设置一个margin: auto
```
.son{
    position: absolute;
    top: 0; right: 0; bottom: 0; left: 0;
    width: 200px; height: 100px;
    margin: auto;
}
```
那么这个.son元素就水平方向和垂直方向同时居中了。因为auto正好把上下左右剩余空间全部等分了,自然就居中。http://demo.cssworld.cn/4/3-5.php

绝对定位下的margin:auto居中是用的最频繁的块级,元素垂直居中对齐方式,比top:50%然后margin负一半元高度的方法要好使得多。

假如说这里面的元素尺寸比外面的大,这个auto该怎么计算? 很简单,如果里面元素尺寸大,说明剩余可用空间都没有了,会被当作0来处理,也就是auto会被计算成0,其实就等于没有设置margin属性值,因为margin的初始值是0

另外对于替换元素,如果我们设置display: block,则margin:auto的计算规则同样适合。

##### 4.3.5 margin无效情形解析
(1)display计算值inline的非替换元素的垂直margin是无效的。虽然规范提到有渲染,但浏览器表现却未寻到一点踪迹,这和padding是有明显区别的。对于内联替换元素,垂直margin有效,并且没有margin合并的问题,所以图片永远不会发生margin合并。
(2)表格中的tr和td元素或者设置display计算值是table-cell或table-row的元素的margin都是无效的。但是如果计算值是table-caption,table或者inline-table则没有此问题
(3)margin合并的时候,更改margin值可能是没有效果的。以父子margin重叠为例,假设父元素设置有margin-top:50px;则此时子元素设置margin-top:30px就没有任何效果表现,除非大小比50px大,或者是负值。
(4)绝对定位元素非定位方位的margin值"无效"。很多时候,我们对元素进行绝对定位的时候,只会设置1-2个相邻方位。例如
```
img{ top: 10%; left: 30%;}
```
此时right和bottom值属于auto状态,也就是右侧和底部没有进行定位,此时这两个方向设置margin值我们在页面上是看不到定位变化的.例如
```
img{ top: 10%; left: 30%; margin-right:  30px;}
```
此时margin-right:30px几乎就是摆设.是margin没起作用吗?实际上不是的,绝对定位元素任意方位的margin值无论在什么场景上都一直有效。譬如这个例子,假设img宽度70%,同时父元素是具有定位属性,且overflow设置为auto的元素,则此时就会出现水平滚动条,因为margin-right:30px增加了图片的外部尺寸。

那为什么一般情况下没有效果呢?主要是因为绝对定位元素的渲染是独立的,普通元素和兄弟元素是心连心,你动我就动,但是绝对定位元素由于独立渲染无法和兄弟元素插科打诨,因此,margin无法影响兄弟元素定位,所以看上去无效。
(5)定高容器的子元素的margin-bottom或者宽度定死的子元素的margin-right的定位"失效"
```
<div class="box">
    <div class="child"></div>
</div>
.box{
    height: 100px;
}
.child{
    height: 80px;
    margin-bottom: 100px;
}
```
这里margin-bottom:100px是不会在容器底部形成100px的外间距的,看上去就像是"失效"一样。
```
.box{
    width: 100px;
}
.child{
    width: 80px;
    margin-right: 100px;
}
```
此时,margin-right：100px对元素的定位也没有任何影响。原因在于,若想使用margin属性改变自身的位置,必须是和当前元素定位方向一样的margin属性才可以,否则margin只能影响后面的元素或者父元素。

例如,一个普通元素,在默认流下,其定位方向是左侧以及上方,此时只有margin-left和margin-top可以影响元素的定位。但是如果通过一些属性改变了定位方向,如float:right或者绝对定位元素的right右侧定位,则反过来margin-right可以影响元素的定位,margin-left只能影响兄弟元素。

在本例中,父容器只有一个子元素,因此没有影响兄弟元素的说法,加上要么定宽要么定高,右侧和底部无margin重叠,因此外部的元素也不会有任何布局上的影响,因此就给人"无效"的错觉,实际上是margin自身的特性导致,有渲染只是你看不到变化而已。

(6)鞭长莫及导致的margin无效
```
<div class="box">
    <img src="1.jpg">
    <p>内容</p>
</div>

.box > img {
    float: left;
    width: 256px;
}
.box > p {
    overflow: hidden;
    margin-left: 200px;
}
```
其中的margin-left：200px是无效的。

(7)内联特性导致的margin无效
```
<div class="box">
    <img src="1.jpg">
</div>
.box > img{
    height: 96px;
    margin-top: -200px;
}
```
一个容器里面有一个图片,然后这张图片设置margin-top负值让图片上偏移。但是随着我们的负值越来越负,结果达到某一个具体负值的时候,图片不再往上偏移了。

##### 4.4 border属性
###### 4.4.1 border-width不支持百分比值

outline,box-shadow,text-shadow等都不支持百分比值。

border-width还支持若干关键字,包括thin medium thick.对应的尺寸大小具体如下: thin:1px medium:3px thick:4px

border属性的默认宽带大小是medium

##### 4.4.2 border-style类型
1.border-style:none
border-style的默认值是none.有部分人可能会误以为是solid。这也是单纯设置border-width或border-color没有边框显示的原因。

2.border-style:solid

3.border-style:dashed

4.border-style:dotted

5.border-style:double
- 1px 0+1+0
- 2px 1+0+1
- 3px 1+1+1
- 4px 1+2+1
- 5px 2+1+2
- 6px 2+2+2
- 7px 2+3+2

从上表可以看到,当边框宽度是1px和2px的时候,其表现和border-style:solid是一模一样的;当边框是3px的时候,才开始有双线边框的表现。
```
.icon-menu{
    width: 120px;
    height: 20px;
    border-top: 60px double;
    border-bottom: 20px solid;
}
```
借助border-style:double实现一些等宽的图形效果。例如等比例"三道杠"图标效果。

6.其他border-style类型

##### 4.4.3 border-color和color
border-color有一个很重要也很实用的特性,就是border-color默认颜色就是color色彩。具体来讲,就是当没有指定border-color颜色值的时候,就会使用当前元素的color就算值作为边框色。
```
.box{
    border: 10px solid;
    color: red;
}
```
此时,.box元素的10px边框颜色就是红色。

具有类似特性的CSS属性还有outline,box-shadow和text-shadow等。
http://demo.cssworld.cn/4/4-1.php

##### 4.4.4 border与透明边框技巧

虽然color:transparent在IE9以上版本的浏览器才支持,但是border-color:transparent在IE7浏览器就开始支持。

1.右下方background定位的技巧
在CSS3新世界还没到来的时候,background定位有一个比较大的局限性,就是只能相对左上角数值定位,不能相对于右下角。假设现在有一个宽带不固定的元素,我们需要在距离右边缘50像素的位置设置一个背景图片,由于宽度不固定,所以无法通过设定具体数值来实现想要的效果,因为background是相对左上角定位的,我们的需求是右侧定位。要实现上面的需求,其中一种方法就是使用透明边框
```
.box{
    border-right: 50px solid transparent;
    background-position: 100% 50%;
}
```
此时,对50px的间距我们使用transparent边框表示,这样就可以使用百分比background-position定位到我们想要的位置了。因为background背景图片是相对于padding-box定位的,也就是说,backg-position:100%的位置计算默认是不会把border-width计算在内的。

2.优雅地增加点击区域大小
```
.icon-clear{
    width: 16px;
    height: 16px;
    border: 11px solid transparent;
    ...
}
```
此时点击区域大小从16`*`16一下子提升到38`*`38 http://demo.cssworld.cn/4/4-2.php

3.三角等图形绘制
```
div{
    width: 0;
    border: 10px solid;
    border-color:#f30 transparent transparent;
}
```
实现朝下的三角效果

##### 4.4.5 border与图形构建
http://demo.cssworld.cn/4/4-3.php

##### 4.4.6 border等高布局技术
http://demo.cssworld.cn/4/4-4.php