1.  JSX 防注入攻击

    React DOM 在渲染之前默认会 过滤 所有传入的值。它可以确保你的应用不会被注入攻击。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 XSS(跨站脚本) 攻击

2.  JSX 代表 Objects

    Babel 转译器会把 JSX 转换为一个名为 React.createElement()的方法调用
