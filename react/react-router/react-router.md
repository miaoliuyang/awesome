

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

There are two different ways to pass data from a Link component through to the new route that’s being rendered. The first is through URL Parameters(**props.match.params**) and the second is through state(**props.location.state**).

```
     <Link to={{
       pathname: '/tylermcginnis',
       state: {
         fromNotifications: true
       }
     }}>Tyler McGinnis</Link>
```

Now, the component that’s being rendered for that route (in this case, Profile) would be able to access fromNotifications by accessing **props.location.state**.

### 7. Rendering a Sidebar or Breadcrumbs 

A common UI pattern is to have a sidebar or breadcrumb navbar in your app. Because React Router allows you to render and match more than one Route per page, implementing this pattern is pretty straight forward. The goal of this post is to show how you can, by rendering multiple Routes, render separate components at separate parts of your page based on the path (like a sidebar).

### 8. Customizing your own Link component

```
     const OldSchoolMenuLink = ({ children, to, exact }) => (
       <Route path={to} exact={exact} children={({ match }) => (
         <div className={match ? 'active' : ''}>
           {match ? '> ' : ''}
           <Link to={to}>
             {children}
           </Link>
         </div>
       )}/>
     )
     
     render() {
       return (
         <Router>
           <div>
             <OldSchoolMenuLink exact={true} to="/">
               Home
             </OldSchoolMenuLink>
             <OldSchoolMenuLink to="/about">
               About
             </OldSchoolMenuLink>

             <hr/>

             <Route exact path="/" component={Home}/>
             <Route path="/about" component={About}/>
           </div>
         </Router>
       )
     }
```

### 9. Animated Transitions

react-transition-group  TransitionGroup and CSSTranstion

TransitionGroup.Defined, it’s a “state machine for managing the mounting and unmounting of components over time”. In practice, the first thing it does is it keeps track of all of its children (props.children) inside of its local state. Then, whenever its props change and its componentWillReceiveProps is called, it loops over the next children and figures out which are new (entering), which have been deleted (exiting), and which children have stayed the same. Once its figured that out, it clones and merges all of its children together passing to each item a few props which represent its status (exiting, entering, etc). At this point it updates its local state with all of the merged children (which all individually know if they’re entering, exiting, or remaining the same). That causes a re-render and the new merged children are shown to the view.

That was a lot of words to say that TransitionGroup renders all its new and old children after passing certian props to each based on if they’re new, old, or the same.

You may be wondering why it renders all the old children, new children, and children that didn’t change. The reason for that is for animation purposes. For example, an “old” (exiting) child might receive an animation that takes its opacity from 1 to 0. A new (entering) child might pull a Peter Theil and receive an animation that takes its opacity from 0 to 1. Regardless, it’s important to understand that all the children are going to be shown for a specified time.

The last important item to mention about TransitionGroup is the way in which is keeps track of which children are which. If you’re familiar with React, you’re familiar with the idea of key props. When you’re mapping over an array to create a list UI, you need to give each item a unique key prop. This way, React can effectively manage that list. It’s the exact same thing with TransitionGroup's children. Each child needs its own unique key prop so TransitionGroup can more effectively figure out which children have changed (entered or exited).

Next we have the CSSTransition component. When you render a TransitionGroup, its direct children must either be a CSSTransition component or a Transition component (both come with the library).

What CSSTransition does is it takes the information it got from TransitionGroup, specifically if certain children are entering, leaving, or staying the same, and it applies a pair of class names to them during the ‘appear’, ‘enter’, and ‘exit’ stages of the transition based on their status. What this allows you to do is, based on those class names, have CSS in your app which will select the same class names that CSSTransition is applying and add some styles to those elements. For example, if we told CSSTransition to apply a fade class, our CSS might look like this

```
.fade-enter {
  opacity: 0;
  z-index: 1;
}

.fade-enter.fade-enter-active {
  opacity: 1;
  transition: opacity 250ms ease-in;
}
```

TransitionGroup only gets us part of the way there; we also need to tie in CSSTransition. Just like we did with TransitionGroup, let’s go ahead and wrap our Switch inside of a CSSTransition component so we can specify some properties for how Switch will be animated. We’ll pass it two props, timeout and className. timeout specifies how long TransitionGroup will display all of its children before removing the old children (aka how long the animation will take place). className is the className that is applied to the component as it enters or exits.

For example, if you provide a className of fade, then the following classes will be added to the component during its animation lifecycle - fade-enter, fade-enter-active, fade-exit, fade-exit-active, fade-appear, and fade-appear-active.

Remember earlier when we talked about how TransitionGroup keeps track of its children? Because we didn’t give CSSTransition a unique key, whenever we switch routes, TransitionGroup just assumes nothing happened since none of its children’s keys changed.

Now the question is, what should we use for a unique key? Well, what is the thing that is changing in the app? It’s the app’s location. So ideally, we would use that as the key since we know if the location changed, we’d want TransitionGroup to be aware of that and let CSSTransition know. Now we have another problem 😣. How do we get the app’s location? We could reach out to window.location but that feels hacky since we’re using React Router. There are a few different ways to accomplish this with React Router. We could use the withRouter HOC would would give us access to location as well as the other router props (history and match). What we’ll do in this tutorial though is render a Route without a path and use a render prop. Now that may seem weird, but if you render a Route without a path, that route will always match and, like usual, the render prop will be passed location, match, and history.


Take a closer look at the Switch components in the images above. Notice that both of them have the exact same location prop. We don’t want that to happen. Remember the purpose of Switch is to render the component of the first Route that matches. If the location prop is the same on both Switch components, that means that the same Route is going to match in both Switches which means the same component is going to be rendered. This means that even if the animations are occurring, we’re not going to see them since both components being rendered are the same. What we need to do is figure out why the locations are the same, and how to fix them. The goal here is to make it so when TransitionGroup renders its children, the old child has the previous location while the new child has the new location. Right now, they both just have the new location.

Whenever you render a Switch component, under the hood Switch is going to get its location from context.location. You can think of it as Router keeping track of the app’s current location and Switch always has access to it. This gives us a huge insight into the current issue we’re having. When both Switches are rendered, they’re both grabbing the location from context. By the time they render, context.location has already been updated to the new location. This means that instead of getting one Switch with the old location and one with the new location, both have the new location since both got the location from context.location. Now, how do we fix this?

When you use Switch, it takes in an optional location prop. Basically it allows you to tell Switch “Hey, use this location instead of the location from context”. Exactly what we need. Since we already have access to location from earlier when we wrapped all our code in a Route, all we have to do is pass it so Switch.

```
import React, { Component } from 'react'
import { TransitionGroup, CSSTransition } from 'react-transition-group'
import {
  BrowserRouter as Router,
  Switch,
  Route,
  Link,
  Redirect
} from 'react-router-dom'

/* you'll need this CSS somewhere
.fade-enter {
  opacity: 0;
  z-index: 1;
}
.fade-enter.fade-enter-active {
  opacity: 1;
  transition: opacity 250ms ease-in;
}
*/

class App extends Component {
  render() {
    return (
      <Router>
        <Route render={({ location }) => (
          <div style={styles.fill}>
            <Route exact path="/" render={() => (
              <Redirect to="/hsl/10/90/50"/>
            )}/>

            <ul style={styles.nav}>
              <NavLink to="/hsl/10/90/50">Red</NavLink>
              <NavLink to="/hsl/120/100/40">Green</NavLink>
              <NavLink to="/rgb/33/150/243">Blue</NavLink>
              <NavLink to="/rgb/240/98/146">Pink</NavLink>
            </ul>

            <div style={styles.content}>
              <TransitionGroup>
                <CSSTransition
                  key={location.key}
                  classNames="fade"
                  timeout={300}
                >
                  <Switch location={location}>
                    <Route exact path="/hsl/:h/:s/:l" component={HSL} />
                    <Route exact path="/rgb/:r/:g/:b" component={RGB} />
                    <Route render={() => <div>Not Found</div>} />
                  </Switch>
                </CSSTransition>
              </TransitionGroup>
            </div>
          </div>
        )}/>
      </Router>
    )
  }
}

const NavLink = (props) => (
  <li style={styles.navItem}>
    <Link {...props} style={{ color: 'inherit' }}/>
  </li>
)

const HSL = ({ match }) => {
  const { params } = match

  return (
    <div style={{
      ...styles.fill,
      ...styles.hsl,
      background: `hsl(${params.h}, ${params.s}%, ${params.l}%)`
    }}>hsl({params.h}, {params.s}%, {params.l}%)</div>
  )
}

const RGB = ({ match }) => {
  const { params } = match

  return (
    <div style={{
      ...styles.fill,
      ...styles.rgb,
      background: `rgb(${params.r}, ${params.g}, ${params.b})`
    }}>rgb({params.r}, {params.g}, {params.b})</div>
  )
}

const styles = {}

styles.fill = {
  position: 'absolute',
  left: 0,
  right: 0,
  top: 0,
  bottom: 0
}

styles.content = {
  ...styles.fill,
  top: '40px',
  textAlign: 'center'
}

styles.nav = {
  padding: 0,
  margin: 0,
  position: 'absolute',
  top: 0,
  height: '40px',
  width: '100%',
  display: 'flex'
}

styles.navItem = {
  textAlign: 'center',
  flex: 1,
  listStyleType: 'none',
  padding: '10px'
}

styles.hsl  = {
  ...styles.fill,
  color: 'white',
  paddingTop: '20px',
  fontSize: '30px'
}

styles.rgb  = {
  ...styles.fill,
  color: 'white',
  paddingTop: '20px',
  fontSize: '30px'
}

export default App
```

Wrap your Switch component inside of both TransitionGroup and CSSTransition, pass the location’s key to CSSTransition and pass the location to Switch.



