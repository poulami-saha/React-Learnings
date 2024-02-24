# Welcome to React  Redux & Redux-Toolkit

Redux is a state management system for cross component or app-wide state. 

3 basic types of state are
1. Local State : Its scope is within a single component.
2. Cross Component State : Its scope is within more than one component Eg: open/closed state of a modal overlay. It requires prop drilling
3. App wide state: Its scope is across the application , Ex: User Authentication It requires prop drilling too

## Dont we have react context already?
1. Both Redux and context can be used at the same time in an app
2. React context have a complex setup and management for large applications with lot of components. Multiple providers will lead to deeply nested object and difficult to maintain
3. React context officially posts that the context is great for low frequency updates like theme or auth. For high frequency changes that its not good for Flux like state management, example of which is Redux.

### Core redux concepts
1. There always a central data store or state.
2. Components have a subscription that gets notified with a slice of the store data upon any change
3. Reducer functions can only update the store data and components are not directly allowed to change the store data. They must be pure, side-effect free and synchronous functions
4. Components dispatch/triggers actions, object with all details to the reducer that in turn updates the data in the store

### Implementation

First we need to install redux and its react counterpart react-redux
`npm install redux react-redux`
Next we need to craete the central data store and the reducer function that manipulates the store data
```
import {createStore} from "redux";

const counterReducer=(state={counter:0},action)=>{
	if(action==="increment){
	return{counter:state.counter+1)}
	}
	if(action==="decrease){
	return{counter:state.counter+action.amount)}
	}
	if(action==="decrement){
	return{
	counter:state.counter-1
	}
	}
	return state;
};

const store= createStore(counterReducer);
export default store;
```
Next the react App component needs to be provided with the store data
```
import {Provider} from "react-redux";

import store from "../../...."

return (
<Provider store={store}><App/></Provider?
```

Now components need to extract data from the store

```
import {useSelector, useDispatch} from "react-redux";

const Counter=()=>{
	const counter=useSelector(state=>state.counter.counter)//Extract the state that we need in the component.This manages the subscription behind the scene. the first counter is the name of the reducer
const dispatch=useDispatch();//To dispatch an action for the redux store

const incrementHandler=()=>{
	dispatch({type:"increment")
}
const decrementHandler=()=>{
	dispatch({type:"decrement")
}
const decreaseHandler=()=>{
	dispatch({type:"decrease",amount:5)
}
```

To handle the Redux store more efficiently we need to use **redux-toolkit**. This way we can create a slice of the global state, which has a unique name, its initial value and also a map of reducer functions. Inside those functions the state values can be directly changed .
```
import {createSlice,configureStore} from @reduxjs/toolkit"
const counterSlice=createSlice({
	name:"counter",
	initialState:{counter:0},
	reducers:{
	increment(state){
		state.counter++
	},
	decrement(state){...},
	increase(state,action){
		state.counter=state.counter+action.amount
	},
	}
})//Creates a slice of the global state

const store= createStore(counterSlice.reducer);
```
We can create multiple slice here based on the modules and then merge them together to add multiple reducers into 1 we can use **configureStore**

```
const store=configureStore({
	reducer:{
		counter:counterSlice.reducer,
		auth:authSlice.reducer,
		...
	})
export const counterActions=counterSlice.actions;
export const authActions=authSlice.actions;
```
Now the components can use this **counterActions** to dispatch an action
like 
```
const incrementHandler=()=>{
	dispatch(counterActions.increment()
}
```
Any payload that the function needs can be passed through this function

### Handle async code in Redux