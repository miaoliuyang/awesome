

### 1. Pass props to a component rendered

if you need to pass a prop to a component being rendered by React Router, instead of using Routes component prop, use its render prop passing it an inline function then pass along the arguments to the element you’re creating.

```
     <Route
       path='/dashboard'
       render={(props) => <Dashboard {...props} isAuthed={true} />}
     />
```

### 2. Programmatically navigate

1) using a <Redireact /> componenet

What are the downsides to this approach? The most often heard criticism is that you need to create a new property on the component’s state in order to know when to render the Redirect. That’s valid, but again, that’s pretty much the whole point of React - state changes update the UI. “It’s more typing”, yes. Naturally by explicitly defining and modifying your state, you have to type more. My argument is explicit state leading to a declarative API is better than implicit state handled by an impertive API.

2) history

When a component is rendered by React Router, that component is passed three different props: **location, match, and history**

Now, what if the Register component wasn’t being rendered by React Router? (Meaning, we’re not passing Register as a component prop to a Route. Instead, we’re just rendering it ourselves like <Register />). If it’s not rendered by React Router, then we won’t have access to history.push. The team thought of this use case so React Router comes with a handy HOC called **withRouter**. 

### 3. Query Strings support

using **this.props.location.search** to get the  query string, such as "?filter=top&origin=im". and then can use the library  query-string to parse it 

### 4. Handling 404 pages (catch all routes) 

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

### 5. Ambiguous Matches

By placing all of your <Route>s inside of a Switch component, React Router guarantees that it’ll only render the first match. This means that by wrapping all the routes in a Switch and by placing the dynamic route last, React Router will only render the dynamic route if the other static routes don’t match.

```
     <Switch>
       <Route path='/' component={Home} />
       <Route path='/notifications' component={Notifications} />
       <Route path='/:handle' component={Profile} />
     </Switch>
```

### 6. Pass props to React Router's Link component

There are two different ways to pass data from a Link component through to the new route that’s being rendered. The first is through URL Parameters and the second is through state.

```
     <Link to={{
       pathname: '/tylermcginnis',
       state: {
         fromNotifications: true
       }
     }}>Tyler McGinnis</Link>
```

Now, the component that’s being rendered for that route (in this case, Profile) would be able to access fromNotifications by accessing **props.location.state**.
