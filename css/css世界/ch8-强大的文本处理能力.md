#### 8.1 line-height的另外一个朋友font-size
##### 8.1.1 font-size和vertical-align
line-hegiht的部分类别属性值是相对于font-size计算的,vertical-align百分比值又是相当于line-height计算的。
```
p{
    font-size: 16px;
    line-height: 1.5;
}
p > img{
    vertical-align: -25%;
}
```
此时 p > img选择器对应元素的vertical-align计算值应该是`16px*1.5*-25%=-6px`

##### 8.1.2 理解font-size与ex,em和rem的关系
ex是字符x高度,显然和font-size关系密切,font-size值越大,自然ex对应的大小也就大。

em在传统排版中指一个字模的高度,是字模的高度,不是字符的高度。其一般由M的宽度决定(因为宽高相同),所以叫em。之所以em完全取决于M的字形,毕竟英文26个字母方方正正的不算多。但是汉字每个字都正好是一个em。尤其作为印刷体的宋体效果最为明显。这种表现在CSS中也有体现。

例如浏览器默认font-size大小是16px,假设一个div宽度是160px,则正好可以放下10个汉字不换行。

在CSS中，1em的计算值等同于当前元素所在的font-size计算值,可以将其想象成当前元素中(如果有)汉字的高度。

em是相对于当前元素,rem是相对于根元素。rem是CSS3单位,IE9以上浏览器才支持。

##### 8.1.3 理解font-size的关键字属性值
font-size支持长度值,如1em,也支持百分比值,如100%。font-size还支持关键字。

font-size的关键字属性值分一下两类:

(1)相对尺寸关键字,指相对于当前元素font-size计算。包括
- larger 是big元素的默认font-size属性值
- smaller 是small元素的默认font-size属性值

(2)绝对尺寸关键字,与当前元素font-size无关,仅受浏览器设置的字号影响。注意是"浏览器设置",而非"根元素"
- xx-large 和h1元素计算值一样
- x-large  和h2元素计算值一样
- large    和h3元素计算值近似,偏差在1像素以内
- medium   和h4元素计算值一样,是font-size的默认值
- small    和h5元素计算值近似
- x-small  和h6元素计算值近似
- xx-small 无对应的html元素

```
html{
    font-size: 14px;
}
html{
    font-size: 87.5%;
}
```
上述两个CSS代码再绝大多数场景下没有差异，全部计算为14px。但是如果用户对浏览器的字号进行了调整。例如把默认的"中"设置成了"大",那么此时font-size: 87.5%的计算值则大了一圈。

##### 8.1.4 font-size:0与文本隐藏
桌面chrome浏览器下有个12px的字号限制,就是文字的font-size计算值不能小于12px(0除外)。如果font-size:0,那么文字就会直接被隐藏掉。

