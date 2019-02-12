https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect

Element.getBoundingClientRect()方法返回元素的带下及其相对于视口的位置。

返回值是一个DOMRect对象,这个对象是由该元素的getClientRects() 方法返回的一组矩形的集合, 即：是与该元素相关的CSS 边框集合 。

DOMRect 对象包含了一组用于描述边框的只读属性——left、top、right和bottom，单位为像素。除了 width 和 height 外的属性都是相对于视口的左上角位置而言的。left、top可能是负值(在出现滚动条的元素没完全展示的情况下)。

当计算边界矩形时，会考虑视口区域（或其他可滚动元素）内的滚动操作，也就是说，当滚动位置发生了改变，top和left属性值就会随之立即发生变化（因此，它们的值是相对于视口的，而不是绝对的）。如果你需要获得相对于整个网页左上角定位的属性值，那么只要给top、left属性值加上当前的滚动位置（通过window.scrollX和window.scrollY），这样就可以获取与当前的滚动位置无关的值。

为了跨浏览器兼容，请使用 window.pageXOffset 和 window.pageYOffset 代替 window.scrollX 和 window.scrollY。



