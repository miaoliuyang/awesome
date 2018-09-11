

### Pass props to a component rendered

### Programmatically navigate

1) using a <Redireact /> componenet

What are the downsides to this approach? The most often heard criticism is that you need to create a new property on the component’s state in order to know when to render the Redirect. That’s valid, but again, that’s pretty much the whole point of React - state changes update the UI. “It’s more typing”, yes. Naturally by explicitly defining and modifying your state, you have to type more. My argument is explicit state leading to a declarative API is better than implicit state handled by an impertive API.

2) history

When a component is rendered by React Router, that component is passed three different props: **location, match, and history**

Now, what if the Register component wasn’t being rendered by React Router? (Meaning, we’re not passing Register as a component prop to a Route. Instead, we’re just rendering it ourselves like <Register />). If it’s not rendered by React Router, then we won’t have access to history.push. The team thought of this use case so React Router comes with a handy HOC called **withRouter**. 

### Query Strings support

using **this.props.location.search** to get the  query string, such as "?filter=top&origin=im". and then can use the library  query-string to parse it 

### Handling 404 pages (catch all routes) 

by using Switch. All we need to do is wrap our Routes inside of a Switch then just as we wanted, only the first match will ever be rendered.

By using Redirect changing the route

```
     <Switch>
        <Route path="/" exact component={Home}/>
        <Route path="/will-match" component={WillMatch}/>
        <Redirect from='/old-match' to='/will-match' />
        <Route component={NoMatch} />
      </Switch>
```
