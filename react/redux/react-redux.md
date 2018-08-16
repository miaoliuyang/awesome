
1. Make state more predictable
   
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
