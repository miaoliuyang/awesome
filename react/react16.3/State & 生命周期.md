1.  setState()

a. 不能直接更新状态，应当使用 setState()。构造函数是唯一能够初始化 this.state 的地方

```
this.setState({comment: 'Hello'});
```

b. 状态更新可能是异步的。React 可以将多个 setState()调用合并成一个调用来提高性能

```
该函数接收先前的状态作为第一个参数，将此次更新被应用时的props作为第二个参数
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

c. 状态更新合并。状态可能包含一些独立的变量，可以调用 setState()独立地更新它们。

2.  数据自顶向下流动/单向数据流
