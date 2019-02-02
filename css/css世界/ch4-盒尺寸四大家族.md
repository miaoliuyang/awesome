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
