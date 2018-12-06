Redux Notes
===========
<!-- TOC -->

- [0.1. Main components of `redux`.](#01-main-components-of-redux)
- [0.2. Reducer](#02-reducer)
- [0.3. Store](#03-store)
- [0.4. combining reducers](#04-combining-reducers)
- [0.5. `react-redux` bindings](#05-react-redux-bindings)
    - [0.5.1. Pass store via  `Provider`](#051-pass-store-via--provider)
        - [0.5.1.1. App.jsx](#0511-appjsx)
    - [0.5.2. Using `connect` to create higher order wrapper components](#052-using-connect-to-create-higher-order-wrapper-components)
        - [0.5.2.1. TodoList.jsx](#0521-todolistjsx)
        - [0.5.2.2. TodoListRedux.jsx](#0522-todolistreduxjsx)

<!-- /TOC -->
Redux is useful for state management. More info can be found on the [Redux Website](https://redux.js.org/)

Here are some notes on redux. Hope they help, I wanted to refresh my memory on using it for the future.

## 0.1. Main components of `redux`. 

  - *Store* - State is stored as a single object and maintained via a object called a _store_. it has important 3 methods for dispatching actions, listening for state changes and doing a callback, and getting state object
  - *Reducer* - The reducer is a pure function / series of functions that take in state, action. An action is dispatched from your components

## 0.2. Reducer

Reducers must be pure and accept two arguments. 
  - `state` which represents the entire state of your application as an object.
  - `action` which is an object literal with a `type` property describing your action, generally a string is used for its serializability and easy to read. Additionally any other attributes can be added to the object


Typically switch statements are useful to determine actions. Another note is for unknown actions, which shouldn't occur, but can occur as a bug or be part of the intent, it is best to have a default action that return the current state unmodified.

``` js
//  here is a simple counter reducer with two actions + a default .
  const counterReducer = (state = 0, action) => {
    switch(action.type) {
      case 'INCREMENT':
        return state + 1;
      case 'DECREMENT':
        return state - 1;
      default:
        return state;
    }
  }

```

## 0.3. Store

A store is what we need to create the store with redux. you pass your reducer to the store on creating it

``` js

import {createStore} from 'Redux';

// note: we pass in our reducer at store creation
const store = createStore(counterReducer);

```
There are 3 important methods that can be called on the store object:


  - `store.subscribe(callback)` is used to listen for state changes and do a function like `rerender()` 
  - `store.getState()` is used to get the state as an object
  - `store.dispatch(action)` is used to dispactch an action 


``` js

// first we define a render method to the DOM
const render = () => { 
  //for our counter it would be a number..so we can just update this way
  document.innerText = store.getState(); 
}

// lets get our state changes to trigger our render function
store.subscribe(render);

//and lets set up some event handlers for a + and - button
document.querySelector('#increment').addEventListener('click', (e) => {
  store.dispatch({type: 'INCREMENT'});
});
document.querySelector('#decrement').addEventListener('click', (e) => {
  store.dispatch({type: 'DECREMENT'});
});
```

## 0.4. combining reducers

Most times for more complex apps you will not want a single reducer to rule them all, or at least, you want the ability to split up your logic thats inside your reducer into more modular related functions. 

For instance in a todo app, w lets say we have two features that we wanted to have two seperate functions. Our state in our store looks something like:

``` js
{
  'visibilityFilter': 'SHOW_ALL',
  'todos' : [...,{
    id: 25,
    descriptor: 'mow the lawn',
    completed: false
  },...]
}
```


  - Visibility (Show completed, Show all, Show Uncompleted)
    - Action 01:  SET_VISIBILITY_FILTER
  - Adding / Modifying todos
    - Action 01: ADD_TODO
    - Action 02: TOGGLE_TODO_COMPLETED

Well we can actually use another utility method of redux to handle mapping parts of the state to seperated reducer functions! This is called `combineRedccers` from Redux, unsurprisingly and what it does is take in an object where `keys` are the correspond keys on your state. and the value is the reducer function to handle that portion. Behind the scenes Redux will combine the reducers for you into one masterReducer funciton.

``` js

import {createStore, combineReducers} from 'Redux';

// pretend that these are our seperate reducers, they might even be imported from seperate files
const todoAddModifyReducer = (state=[], action) => {...}
const todoVisibilityReducer = (state='SHOW_ALL', action) => {...}

// specify mappings
const mainReducer = {
  todos: todoAddModifyReducer,
  visibilityFilter: todoVisibilityReducer
};

// comveniently combined!
const store = createStore(mainReducer);
```

## 0.5. `react-redux` bindings

So now we can manually pass our store down to our child components via props, but this gets very tedious. So we can use the `react-redux` library to help us do two things:

  - `provider` - Provider is a parent component wrapper that magically passes the store to all child components without actually explicitly specifying it as a prop. This is done behind the scenes via react's [Context](https://reactjs.org/docs/context.html).
  - `connect` - Connect essentially automatically creates a higher order component that gets your component the appropriate props. It basically takes in *two* mappings (one for `dispatch` event handlers (like `onClick`) and one for `state` mappings like `todos`).
    - `mapStateToProps(state, ownProps)` - takes redux store state keys and maps them to your components props. it should be a function that returns a object `{propsKey: stateValue}`
    - `mapDispatchToProps(dispatch, ownProps)` -  it should be a function that returns a object `{..., propsKey: eventThatDispatchesAnAction, ...}`


Imagine our file structure looks like
``` sh
APP/
│───index.js
│
├───components/
│       App.jsx
│       TodoItem.jsx
│       TodoList.jsx
│       TodoListRedux.jsx
│
└───reducers/
        myReducer.js
```


### 0.5.1. Pass store via  `Provider`

First we have our provider setup in our main app. Note: how we use provider here! See above for how to setup your reducer


#### 0.5.1.1. App.jsx
``` js

import React from 'react';
import TodoListRedux from './TodoListRedux';
import { Provider } from 'react-redux';
import {createStore} from 'Redux';
import {myReducer} from '../reducers/myReducer.js';

const App = () => (
  <Provider store={createStore(myReducer)}>
  <div>
    <TodoListRedux />
  </div>
  </Provider>
)

```

### 0.5.2. Using `connect` to create higher order wrapper components

For our todo list you can see this being done here. Our normal `todoList` is a presentation component. We will use `connect` from `react-redux` to automatically create `TodoItemRedux`, a wrapper component that automatically provides the `props` needed for dispatching actions and state.

#### 0.5.2.1. TodoList.jsx
``` js
import React from 'react';
import PropTypes from 'prop-types';
import TodoItem from './TodoItem';
​
const TodoList = ({ todos, toggleTodo }) => (
  <ul>
    {todos.map(todo => (
      <TodoItem key={todo.id} {...todo} onClick={() => toggleTodo(todo.id)} />
    ))}
  </ul>
)
​
TodoList.propTypes = {
  todos: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      completed: PropTypes.bool.isRequired,
      text: PropTypes.string.isRequired
    }).isRequired
  ).isRequired,
  toggleTodo: PropTypes.func.isRequired
}
​
export default TodoList

```

#### 0.5.2.2. TodoListRedux.jsx
``` js

import React from 'react';
import { connect } from 'react-redux';
import TodoList from './TodoList';


const toggleTodo = (id) => {
  // .. would literally return an action object 'TOGGLE_TODO'
}

const mapStateToProps = state => ({
  todos: state.todos
})
​
const mapDispatchToProps = dispatch => ({
  toggleTodo: id => dispatch(toggleTodo(id))
})
​
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

```
