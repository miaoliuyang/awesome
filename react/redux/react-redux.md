
1. Store

   **Make state more predictable**
   
   Benefits of State Tree?
   * Shared cache
   * Predictable State Changes
   * Improved developer tooling
   * Pure Functions
   * Server Rendering
   
   Store includes a state tree and the methods: Get Listen Update
 
 2. Actions
 
    an action is an object which describes what sort of transformation you want to make to your state
   
    One thing to note is that actions must have a type property to specify, well, the “type” of action which is occurring. Other than ‘type’, the structure of the action is up to you.
   
 3. Pure Functions
 
    The whole concept of a pure function is consistency and predictability.The reason for the consistency and predictability is because pure functions have the following characteristics.
     
    *  Pure functions always return the same result given the same arguments. 
    *  Pure function's execution doesn't depend on the state of the application. They depend only on the arguments passed into them
    *  Pure functions don't modify the variables outside of their scope. Never produce any side effects
    
 4. Reducer Function
 
 5. Combining Reducers
 
 6. Action Creators
 
 ```
   // Library code
  function createStore (reducer) {
    // The store should have four parts
    // 1. The state
    // 2. Get the state. (getState)
    // 3. Listen to changes on the state. (subscribe)
    // 4. Update the state (dispatch)

    let state
    let listeners = []

    const getState = () => state

    const subscribe = (listener) => {
      listeners.push(listener)
      return () => {
        listeners = listeners.filter((l) => l !== listener)
      }
    }

    const dispatch = (action) => {
      state = reducer(state, action)
      listeners.forEach((listener) => listener())
    }

    return {
      getState,
      subscribe,
      dispatch,
    }
  }

  // App Code
  const ADD_TODO = 'ADD_TODO'
  const REMOVE_TODO = 'REMOVE_TODO'
  const TOGGLE_TODO = 'TOGGLE_TODO'
  const ADD_GOAL = 'ADD_GOAL'
  const REMOVE_GOAL = 'REMOVE_GOAL'

  function addTodoAction (todo) {
    return {
      type: ADD_TODO,
      todo,
    }
  }

  function removeTodoAction (id) {
    return {
      type: REMOVE_TODO,
      id,
    }
  }

  function toggleTodoAction (id) {
    return {
      type: TOGGLE_TODO,
      id,
    }
  }

  function addGoalAction (goal) {
    return {
      type: ADD_GOAL,
      goal,
    }
  }

  function removeGoalAction (id) {
    return {
      type: REMOVE_GOAL,
      id,
    }
  }

  // Reducer function
  function todos (state = [], action) {
    switch(action.type) {
      case ADD_TODO :
        return state.concat([action.todo])
      case REMOVE_TODO :
        return state.filter((todo) => todo.id !== action.id)
      case TOGGLE_TODO :
        return state.map((todo) => todo.id !== action.id ? todo :
          Object.assign({}, todo, {complete: !todo.complete})
        )
      default :
        return state
    }
  }

  function goals (state = [], action) {
    switch(action.type) {
      case ADD_GOAL :
        return state.concat([action.goal])
      case REMOVE_GOAL :
        return state.filter((goal) => goal.id !== action.id)
      default :
        return state
    }
  }

  function app (state = {}, action) {
    return {
      todos: todos(state.todos, action),
      goals: goals(state.goals, action)
    }
  }

  const store = createStore(app)

  store.subscribe(() => {
    console.log('The new state is: ', store.getState())
  })

  store.dispatch(addTodoAction({
    id: 0,
    name: 'Walk the dog',
    complete: false,
  }))

  store.dispatch(addTodoAction({
    id: 1,
    name: 'Wash the car',
    complete: false,
  }))

  store.dispatch(addTodoAction({
    id: 2,
    name: 'Go to the gym',
    complete: true,
  }))

  store.dispatch(removeTodoAction(1))

  store.dispatch(toggleTodoAction(0))

  store.dispatch(addGoalAction({
    id: 0,
    name: 'Learn Redux'
  }))

  store.dispatch(addGoalAction({
    id: 1,
    name: 'Lose 20 pounds'
  }))

  store.dispatch(removeGoalAction(0))
 ```
