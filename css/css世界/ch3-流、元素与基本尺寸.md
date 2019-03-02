#### 3.1 块级元素 block-level element
1.常见的块级元素有`<div>, <li>和<table>`等。

2."块级元素"和"display为block的元素"不是一个概念。 例如`<li>`元素默认的display值是list-item,`<table>`元素默认的display值是table, 但它们都是"块级元素",都符合块级元素的基本特征：一个水平流上只能单独显示一个元素, 多个块级元素则换行显示。

3.正是由于"块级元素"具有换行特性, 因此理论上都可以配合clear属性来清除浮动带来的影响。https://demo.cssworld.cn/3/1-1.php
```
.clear:after{
    content: '';
    display: table; //也可以是block或者list-item
    clear:both
}
```
对于IE浏览器, 普通元素设置dispaly:list-item有效, 但是:before/:after等伪元素不行。

4.每个元素都有两个盒子,外在盒子和容器盒子。外在盒子负责元素是可以一行显示, 还是只能换行显示; 容器盒子负责宽高, 内容呈现等。于是, 值为block的元素的盒子实际由外在的"块级盒子"和内在的"块级容器盒子"组成, 值为inline-block的元素则由外在的"内联盒子"和内在的"块级容器盒子"组成, 值为inline的元素则内外均是"内联盒子"。inline-table示例 https://demo.cssworld.cn/3/1-2.php

5.list-item还会生成一个"标记盒子"(marker box)
#### 3.2 width/height作用的具体细节
##### 3.2.1 width:auto
1.width的默认值是auto, 它至少包含了以下4种不同的宽度表现:
- 充分利用可用空间。比方说`<div>`和`<p>`这些元素的宽度默认是100%于父级容器的。这种充分利用可用空间的行为还有个专有名字**fill-available**
- 收缩与包裹。典型代表就是浮动,绝对定位,inline-block元素或table元素,英文称为shrink-to-fit。CSS3中的**fit-content**指的就是这种宽度表现。
- 收缩到最小。这个最容易出现在table-layout为auto的表格中。
https://demo.cssworld.cn/3/2-1.php  当每一列空间都不够的时候, 文字能断就断, 但中文是随便断的, 英文单词不能断。于是第一列每个字都断掉，这种行为在规范中被描述为preferred minumum width或者minimum content width。后来还有一个更好听的名字**min-content**。
- 超出容器限制。除非有明确的width相关设置,否则上面3种情况尺寸都不会主动超过父级容器宽度的,但是存在一些特殊情况。例如内容很长的连续的英文和数字,或者内联元素被设置了white-space:nowrap。https://demo.cssworld.cn/3/2-2.php 子元素既保持了inline-block元素的收缩特性, 又同时让被内容宽度最大, 直接无视父级容器的宽度限制。这个现象后来有了专门的属性值描述**max-content**。

2.在CSS世界中,尺寸也分"内部尺寸"和"外部尺寸", 其中"内部尺寸"英文写作Intrinsic Sizing, 表示尺寸由内部尺寸决定, "外部尺寸"英文写作Extrinsic Sizing, 宽度由外部元素决定。上面4种尺寸表现中,第一个是"外部尺寸",其余的全部是"内部尺寸"。

3.外部尺寸与流体特性

(1) 正常流宽度
```
a{
    dispaly: block;
    width: 100%;  /*多余, 流特性*/
}
```
所谓流动性，并不是看不去的宽度100%显示那么简单,而是一种margin/border/padding和content内容区域自动分配水平空间的机制。 https://demo.cssworld.cn/3/2-3.php  这个例子中上下两个导航均有margin和padding, 前者无width设置, 完全借助流特性,后者宽度width:100%。结果后面的尺寸超出了外部的容器,即所谓的"流动性丢失"。

块级元素的流体特性, 这种特性体现在里面的"容器盒子上"。

(2) 格式化宽度

格式化宽度仅出现在"绝对定位模型"中,即position属性值为absolute或fixed的元素中。在默认情况下,绝对定位元素的宽度表现为"包裹性",宽度由内部尺寸决定。但有一种情况其宽度是由外部尺寸决定的。

对于非替换元素, 当left/top或top/bottom对立方位的属性值同时存在的时候,元素的宽度表现为"格式化宽度",其宽度大小相对于最近的具有定位特性(position的属性值不是static)的祖先元素计算。
```
div{position:absolute; left:20px; right:20px}
```
假设该元素最近的具有定位特性的祖先元素的宽度是1000像素,则这个元素的宽度是1000-20-20=960像素。

"格式化宽度"具有完全的流动性,也就是margin,border,padding和content内容区域同样会自动分配水平(和垂直)空间。

4.内部尺寸与流体特性

在CSS世界中,"内部尺寸"有下面3种表现形式:

(1) 包裹性(shrink-to-fit):

"包裹性"除了"包裹",还有"自适应性"。"自适应性"是区分后面两种尺寸表现很重点的一点, 所谓"自适应性"指的是元素尺寸有内部元素决定,但永远小于"包含块"容器的尺寸(除非容器尺寸小于元素的"首选最小宽度")。换句话说,"包裹性"元素类似有个max-width: 100%的限制(此说法只是便于大家理解, 实际上是有明显区别的。)

因此对于一个元素, 如果其display属性值是inline-block, 那么即使其里面内容再多,只要是正常文本, 宽度也不会超过容器。于是,图文混排的时候, 我们只要关心内容, 除非"首选最小宽度"比容器宽度还要大, 否则我们完全不需要担心某个元素内容太多而破坏了布局。

```
<button>按钮</button>
<input type="button" value="按钮">
```
按钮就是css世界中极具代表性的inline-block元素, 其"包裹性"表现为:按钮文字越多宽度越宽(内部尺寸特性), 但如果文字足够多, 则会在容器的宽度处自动换行(自适应特性) https://demo.cssworld.cn/3/2-4.php

`<button>`标签按钮才会自动换行,`<input>`标签按钮默认white-space:pre是不会换行的, 需要将pre值重置为默认的normal。

"包裹性"的应用场景: 页面某个模块的文字内容是动态的,希望文字少的时候居中显示,文字超过一行的时候居左显示。https://demo.cssworld.cn/3/2-5.php

除了inline-block元素, 浮动元素以及绝对定位元素都具有包裹性, 均有类似的智能宽度行为。

(2) 首选最小宽度

所谓"首选最小宽度"指的是元素最适合的最小宽度。具体表现如下:
- 东亚文字(如中文)最小宽度为每个汉字的宽度
- 西方文字最小宽度由特定的连续的英文字符单元决定。并不是所有的英文字符都会组成连续单元,一般会终止于空格(普通空格),短横线,问号以及其他非英文字符等。如display:inline-block这几个字符以连接符-作为分隔符,形成了display:inline和block两个连续单元,由于连接符-分隔位置在字符后面,因此最后的宽度就是display:inline-的宽度。如果想让英文字符和中文一样, 每一个字符都用最小宽度单元, 可以使用word-break:break-all
- 类似图片这样的替换元素的最小宽度就是该元素内容本身的宽度。

(3)最大宽度

"最大宽度"实际等同于"包裹性"元素设置white-space:nowrap声明后的宽度。如果内部没有块级元素或者块级元素没有设定宽度值, 则"最大宽度"实际上是最大的连续内联盒子的宽度。"连续内联盒子"指的是全部都是内联级别的一个或一堆元素, 中间没有任何的换行标签`<br>`或其他块级元素。

##### 3.2.2 width值作用的细节
"内在盒子"的4个盒子content box, padding box, border box, margin box 默认情况下width是作用在content box。

对于块状元素, 如果width:auto, 则元素会如水流般充满整个容器, 而一旦设置了width具体数值, 则元素的流动性就会被阻断。

##### 3.2.3 CSS流体布局下的宽度分离原则
所谓"宽度分离原则", 就是css中的width属性不与影响宽度的padding/border(有时候包括margin)属性并存,也就是不能出现以下的组合:
```
.box{ width: 100px; border: 1px soild;}
.box{ width: 100px; padding: 20px; }
```

width独立占用一层标签, 而padding, border, margin利用流动性在内部自适应呈现。
```
.father{ width: 180px;}
.son{ margin:0 20px; padding:20px; border: 1px solid;}
```

##### 3.2.4 改变width/height作用细节的box-sizing

box-sizing:border-box让宽度直接作用在border box上, content box从宽度中释放, 形成了局部的流动性, 和padding一起自动分配width值。 但是box-sizing并不支持margin-box。

在css世界中,唯一离不开box-sizing:border-box的就是原生普通文本框`<input>`和文本域`<textarea>`的100%自适应父容器宽度。

`<textarea>`为替换元素, 替换元素的特性之一就是尺寸由内部元素决定, 且无论其display属性是inline还是block。对于非替换元素, 如果其display属性值为block, 则会具有流动性, 宽度由外部尺寸决定, 但是替换元素的宽度却不受display水平影响。因此通过css修改`<textarea>`的display水平是无法让尺寸100%自使用父容器的。所以只能通过width设定让`<textarea>`尺寸100%自适应父容器。但是`<textarea>`是有border的, 而且需要有一定的padding大小,否则输入的时候光标会顶到边框,体验不好。于是width/border和padding注定要共存,同时还要整体宽度100%自适应容器。使用box-sizing:border-box是解决之道。

```
textarea{
    width: 100%;
    -ms-box-sizing: border-box;
    box-sizing: border-box;
}
```
box-sizing:border-box可用来解决替换元素宽度自适应问题。
```
input, textarea, img, video, object{
    box-sizing: border-box;
}
```

##### 3.2.5 height:auto
width和height共用一套盒尺寸模型, box-sizing的解释也是类似的。但是height:auto要比width:auto简单而单纯得多。

height:auto也有外部尺寸特性,但其仅存在于绝对定位模型中,也就是"格式化高度",与"格式化宽度"类似。

##### 3.2.6 关于height:100%
对于width属性,就算父元素width为auto,其百分比值也是支持的;但是height属性,如果父元素height为auto,只要子元素在文档流中,其百分比值完全就被忽略了。

对于普通文档流中的元素,百分比高度值要想起作用,其父级必须有一个可以生效的高度值。

让元素支持height:100%效果

(1)设定显示的高度值
```
html,body{ height: 100%; }
```
(2)使用绝对定位
```
div{ height: 100%; position: absolute;}
```
此时height:100%就会有计算值,即使祖先元素的height计算为auto也是如此。绝对定位元素的百分比计算和非绝对定位元素的百分比计算是有区别的：绝对定位的宽高百分比计算是相对于padding box的,也就是说会把padding大小值计算在内,但是非绝对定位元素则是相对于content box计算的。https://demo.cssworld.cn/3/2-11.php 

#### 3.3 min-width/max-width和min-height/max-height
##### 3.3.1 为流体而生的min-width/max-width
```
img{ max-width: 100%; height:auto!important;}
```
height:auto是必需的,否则,如果原始图片有设定height,max-width生效的时候图片就会被水平压缩.强制height:auto可以确定宽度不超出的同时使图片保持原来的比例。但这样也会有体验上的问题,那就是在加载时图片占据高度会从0变成计算高度,图文会有明显的瀑布式下落。

##### 3.3.2 与众不同的初始值
max-width和max-height的初始值是none,min-width和min-height的初始值是auto

##### 3.3.3 超越！important,超越最大
1.max-width会覆盖width,而且这种覆盖不是普通的覆盖,是超级覆盖
```
<img src="1.jpg" style="width: 480px!important">
img{ max-width: 256px; }
```
上面的设置后图片最后呈现的宽度是256px

2.min-width会覆盖max-width,此规则发生在min-width和max-width冲突的时候
```
.container{ min-width: 1400px; max-width: 1200px}
```
上面的设置后.container元素表现为至少1400像素宽。

#### 3.4 内联元素
##### 3.4.1 哪些元素是内联元素
1.从定义看
"内联元素"的"内联"特指"外在盒子",和"display为inline的元素"不是一个概念,inline-block和inline-table都是"内联元素",因为它们的"外在盒子"都是内联盒子。

2.从表现看
##### 3.4.2 内联盒模型
```
<p>这是一行普通的文字,这里有个<em>em</em>标签.</p>
```
上面的一段html包含了很多个盒子：
1.内容区域(content area)
内容区域指一种围绕文字看不见的盒子,其大小仅受字符本身特性控制,本质上是一个字符盒子(character box)但是有些元素,如图片这样的替换元素,其内容显然不是文字,不存在字符盒子之类的,因此对于这些元素,内容区域可以看成元素自身。可以把文本选中时的背景色区域作为内容区域。

文本选中区本质上就等同于基本盒尺寸中的content box。实际上,内容区域并没有明确的定义,所以将其理解为em盒(em-box,可看成是中文字符占据的1em高度区域)也是可以的。

在IE和Firefox浏览器下,文本的选中背景总能准确反映内容区域范围,但是Chrome浏览器下,::selection范围并不总是准确的,例如,和图文混排或者有垂直padding的时候，范围会明显过大。

2.内联盒子(inline box)
"内联盒子"不会让内容成块显示,而是排成一行,这里的"内联盒子"实际指的是元素的"外在盒子",用来决定元素是内联还是块级。该盒子又可以细分为"内联盒子"和"匿名内联盒子"两类。

如果外部包含内联标签```<span> <a>和<em>```则属于"内联盒子",如果是光秃秃的文字，则属于"匿名内联盒子"。需要注意的是,并不是所有光秃秃的文字都是"匿名内联盒子",其还有可能是"匿名块级盒子",关键要看前后的标签是内联还是块级。

3.行框盒子(line box)
每一行就是一个"行框盒子",每个"行框盒子"又由一个个"内联盒子"组成的。

4.包含盒子(containing box/containing block)
包含盒子由一行一行的"行框盒子"组成。在CSS规范中,并没有"包含盒子"的说法,更准确的称呼应该是"包含块"(containing block)。

##### 3.4.3 幽灵空白节点
"幽灵空白节点"是内联盒模型中非常重要的一个概念。具体指的是:在HTML5文档声明中,内联元素的所有解析和渲染表现就如同每个行框盒子的前面有一个"空白节点"一样。这个"空白节点"永远透明,不占据任何宽度,看不见也无法通过脚本获取,就好像幽灵一样,但又确确实实存在,表现如同文本节点一样。

注意,这里有一个前提,文档声明必须是HTML5文档声明```<!doctype html>```否则不存在"幽灵空白节点"

```
div{ background-color: #cd0000;}
span{ display: inline-block; }

<div><span></span></div>
```
结果,此```<div>```的高度并不是0。但内部的```<span>```元素的宽高明明是0。作祟的就是这里的"幽灵空白节点",再加上line-height和vertical-align的共同作用导致```<div>```的高度不是0。

"幽灵空白节点"实际上也是一个盒子,"strut",是一个存在于每个"行框盒子"前面,同时具有该元素的字体和行高属性的0宽度的内联盒。

Each line box starts with a zero-width inline box with the element's font and line height properties.We call that imaginary box a "strut"

Line boxes are created as needed to hold inline-level content within an inline formating context.Line boxes that contain no text,no preseved white space,no inline elements with none-zero margins,padding or borders,and no other in-flow content(such as images，inline blocks or inline-tables),and do not end with a preseved newline must be treated as zero-height line boxed for the purposes of determing the positions of any elements inside of them,and must be treated as not existing for any other purpose.
如果一个line box里没有文字,保留的空格,非0的margin或padding或border的inline元素,或其他的in-flow内容(比如文字,inline-block或inline-table元素)且不以保留的换行符结束的话。就会被视作高度为0的line box。
