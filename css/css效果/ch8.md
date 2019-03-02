##### 8.6.4 word-break和word-wrap区别
1.word-break属性语法如下:
- normal: 使用默认的换行规则
- break-all: 允许任意非CJK(Chinese/Japanese/Korean)文本间的单词断行
- keep-all: 不允许CJK文本中的单词换行, 只能在半角空格或连字符处换行。非CJK文本的行文实际上和normal一致。

break-all这个值是所有浏览器都支持, 但是keep-all就不同了, 目前移动端还不适合使用keep-all。

另外Chrome, Safari以及其他WebKit或Blink浏览器还支持非官方标准的break-word。其表现就和word-wrap:break-word一样

2.word-wrap属性语法如下:
- normal: 正常的换行规则
- break-word: 一行单词中实在没有其他靠谱的换行点的时候换行。在CSS3规范中, 这个属性名称被修改为overflow-wrap, 但还不是所有浏览器支持。

word-break:break-all和word-wrap:break-word都能使连续英文字符换行, 但是区别可看 http://demo.cssworld.cn/8/6-5.php 效果。

word-break:break-all的作用是所有的都换行, 一点儿空隙都不放过,而word-wrap:break-word则带有怜悯之心, 如果这一行文字有可以换行的点, 如空格或CJK之类的, 就不打英文单词或字符的主意了, 在这些换行点换行, 至于对不对齐, 好不好看则不关心, 因此很容易出现一片一片空白区域的情况。

##### 8.6.5 white-space与换行和空格的控制
1.white-space的处理模型
white-space属性声明了如何处理元素内的空白字符, 这类空白字符包括space(空格)键, enter(回车)键, tab(制表符)键产生的空白。因为white-space可以决定图文内容是否在一行显示(回车空格是否生效),是否显示大段连续空白(空格是否生效)等。

- normal: 合并空白字符和换行符
- pre: 空白字符不合并, 并且内容只在有换行符的地方换行
- nowrap: 该值和normal也一样会合并空白字符, 但不允许文本环绕
- pre-wrap: 空白字符不合并,并且内容只会在换行符的地方换行, 同事允许文本环绕
- pre-line: 合并空白字符,但只在换行符的地方换行, 允许文本环绕

white-space的功能分三个维度,分别是: 是否合并空白字符, 是否合并换行符, 以及文本是否自动换行。

| 属性 | 换行 | 空格和制表 | 文本环绕 |
| ------ | ------ | ------ |------ |
| normal | 合并 | 合并 | 环绕 |
| nowrap | 合并 | 合并 | 不环绕 |
| pre | 保留 | 保留 | 不环绕 |
| pre-wrap | 保留 | 保留 | 环绕 |
| pre-line | 合并 | 合并 | 环绕 |

如果合并空格, 会让多个空格变成1个。如果合并换行, 会把多个连续换行合并成1个, 并当做1个普通空格处理,就是空格键敲出的那个空格。如果文本环绕,一行文字内容超出容器宽度时,会自动从下一行开始显示。

##### 8.6.7 解决text-decoration下划线和文本重叠问题

使用border-bottom模拟下划线
```
a{
    text-decoration: none;
    border-bottom: 1px solid;
}
```
对于纯内联元素, 垂直方向的padding属性和border属性对原来的布局定位等没有任何影响。也就是就算border-bottom宽度设为100px, 上下行文字的垂直位置依旧纹丝不动。

##### 8.6.8 text-transform字符大小写
text-transform要么全大写uppercase要么全小写lowercase

使用场景：不区分大小写的验证码输入
#### 8.7 了解:first-letter/:first-line伪元素
##### 8.7.1 :first-letter伪元素及其实例
1.::first-letter伪元素生效的前提

首先, 元素的display计算值必须是block, inline-block, list-item, table-cell或者table-caption, 其他所有display计算值都没有用, 包括display:table和display:flex等。

不是所有的字符都能单独作为::first-letter伪元素存在的。正常情况下可以直接作为伪元素的字符就是数字, 英文字母, 中文, $, 一些运算符以及非常容易被忽视的空格等。

最后说明一点, 字符前面不能有图片或者inline-block/inline-table之类的元素存在。

```
p:first-letter { color: silver;}
<p><i style="display:inline-block"></i>银色</p>
```
上述css代码"银色"两字的颜色还是黑色。因为多了一个display值是inline-block尺寸为0的i元素(chrome测试并无效果)

::before伪元素也参与::first-letter伪元素。
```
p:before{ content: '新闻 ';}
p:first-letter { color: silver;}
<p>这是新闻的标题</p>
```
结果"新"变成了银色。

2.::first-letter伪元素可以生效的CSS属性
如果字符被选作了::first-letter伪元素, 并不是像::before伪元素那样, 几乎所有CSS都有效, 只是一部分有效。
- 所有字体相关属性 font, font-style, font-variant, font-weight, font-size, line-height和font-famliy
- 所有背景相关属性 background-color, background-image, background-position, background-repeat, background-size和background-attachment
- 所有margin相关属性 margin, margin-top, margin-right, margin-bottom和margin-left
- 所有padding相关属性 padding, padding-top, padding-right, padding-bottom和padding-left
- 所有border相关属性 缩写的border, border-style, border-color, border-width和普通书写的属性
- color属性
- text-decoration, text-transform, letter-spacing, word-spacing, line-height, float和vertical-align(只有当float为none的时候)等属性。

因此。visibility:hidden或者display:none对::first-letter伪元素是无效的。

3.::first-letter伪元素的一些特点
(1)支持部分display属性值标签嵌套。::first-letter伪元素获取可以跨标签,也就是不仅能选择匿名内联盒子, 还能透过层层标签进行选择, 但是也有一些限制, 并不是所有标签嵌套都是有用的。

display值如果是inline, block, table, table-row, table-caption, table-cell, list-item都是可以的, 但是不能是inline-block和inline-table, 否则::first-letter伪元素会直接无效; 而display:flex则改变了规则, 直接选择了下一行的字符内容。

http://demo.cssworld.cn/8/7-1.php

```
p:first-letter{ color: silver;}
p>span{ display: table;}
<p><span>第一个</span>字符看会不会变色</p>
```

(2)颜色等权重总是多了一层。
```
p:first-letter{color: red;}
p>span{color: blue!important;}
<p><span>第一个</span>字符看会不会变红</p>
```
上述CSS代码"第"颜色是红色。:first-letter伪元素其实是作为子元素存在的,子元素的CSS设置一定比父元素的级别更高, 哪怕父级使用了!important。

##### 8.7.2 :first-line伪元素
- :first-line和:first-letter伪元素一样,IE9及以上版本浏览器支持双冒号::first-line{}写法, IE8浏览器只认识单冒号写法。
- :fisr-line和:first-letter伪元素一样,只能作用在块级元素上,也就是display为block, inline-block, list-item, table-cell或者table-caption的元素设置:first-line才有效, table, flex之类都是无效的。
- :first-line和:first-lette伪元素一样, 仅支持部分CSS属性。例如
  - 所以字体相关属性;
  - color属性;
  - 所有背景相关属性;
  - text-decoration, text-transform, letter-spacing, word-spacing, line-height和vertical-align等属性。
- :first-line和:first-lette伪元素一样, color等继承属性的权重总是多了一层。如果:first-line和:first-letter同时设置颜色, :first-letter级别比:first-line高,即使:first-line写在后面,甚至加!important也是如此。
- :first-line和:first-lette伪元素一样,  也支持标签嵌套, 但是具体细则和:first-letter出入较大, 例如它不支持table相关属性等。

http://demo.cssworld.cn/8/7-2.php

```
p:first-line{color: silver;}
p>span{display: inline-block;}
<p><span>第一行</span>字符看会不会变色</p>
```
结果在chrome浏览器下颜色居然断开了, IE和Firefox都没有这个问题, 文字颜色都是一样的。因此如果想使用:first-line, 首行内容不能混入inline-block/inline-table元素。

