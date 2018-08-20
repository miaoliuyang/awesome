
1. Store

   **Make state more predictable**
   
   Benefits of State Tree?
   * Shared cache
   * Predictable State Changes
   * Improved developer tooling
   * Pure Functions
   * Server Rendering
   
   Store includes a state tree and the methods: Get Listen Update
   ```
   function createStore(){
      // The store should have four parts
      // 1. The state
      // 2. Get the state
      // 3. Listen to changes on the state.
      // 4. Update the state
      
      let state
      let listeners = []
      
      const getState = () => state
      
      const subscribe = (listener) => {
         listeners.push(listener)
         return () => {
            listeners = listeners.fiter((l) => l !== listener)
         }
      }
      
      return {
         getState
      }
   }
   
   const store = createStore()
   var subscribe = store.subscribe(() => {

   })
   subscribe() // will subscribe
   ```
   
 2. Actions
 
    an action is an object which describes what sort of transformation you want to make to your state
   
    One thing to note is that actions must have a type property to specify, well, the “type” of action which is occurring. Other than ‘type’, the structure of the action is up to you.
   
    ```
     const action = {
        type: 'LIKE_TWEET',
        id: 950788310443724800,
        uid: 'tylermcginnis'
      }
    ```
 3. Pure Functions
 
    The whole concept of a pure function is consistency and predictability.The reason for the consistency and predictability is because pure functions have the following characteristics.
     
    *  Pure functions always return the same result given the same arguments. 
    *  Pure function's execution doesn't depend on the state of the application. They depend only on the arguments passed into them
    *  Pure functions don't modify the variables outside of their scope. Never produce any side effects
    
 4. Reducer Function
 
    ```
      function todso(state = [], action){
          if(action.type === 'ADD_TODO'){
              return state.concat([action.todo])
          }
          
          return state
      }
    ```
 5. Combining Reducers
 
 6. Action Creators
