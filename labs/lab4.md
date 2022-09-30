In this lab, our goal is to get some experience with a popular toolkit for Redux development known as Redux Toolkit.  The goal of Redux Toolkit is to simplify Redux development by reducing the amount of boilerplate code required and to make it easier to configure a Redux store.

 We will follow the same steps starting out as we did in lab 2, where you will use the TodoList reference implementation from lab 1 for you to use as a starting point. 

This app will have the same functionality as the previous lab; although there are some stretch goals to give you a chance to flex your React and Redux muscles. Today's lab will focus on integrating Redux, so that our todos are no longer stored in component state, but rather in a centralized store. There are required milestones in this lab that should be feasible to get through in the time allotted as well as a bonus milestones you are encouraged to attempt in class if you finish early or later on your own. If you are stuck at any point don’t hesitate to ask for assistance. This lab may be challenging as we will be introducing new vocabulary and patterns.  



#### What you will learn:
* **React** - passing props, using map, further understanding of components.
* **Redux** - store, reducers, mapStateToProps, connect. We will refer to [Redux - Quickstart]({{ site.baseurl }}/guides/redux-quickstart/).

#### Why we are doing this lab:
To gain experience with a popular toolkit for Redux development known as Redux Toolkit. 

## Milestone 0: Setup

In this lab, we will refactor our Todo List app from Lab 1 to use Redux instead of React state. We have provided a reference implementation for you to use as a starting point. To use the reference implementation:

```bash
$ git clone --single-branch --branch lab1-ref https://github.com/varoonsahgal/rrf-todo.git
```

Let's start by installing both the redux toolkit and the react-redux bindings that will allow us to work with Redux from our React project.  Go ahead and run this command:

```bash
$ npm install @reduxjs/toolkit react-redux
```


## Milestone 1: Create a Redux Store

Create a new folder under src named app.  Then under the app folder create a file named store.js - so the path should be `src/app/store.js`.  This is where we will create our Redux store. Import the configureStore API from Redux Toolkit. We'll start by creating an empty Redux store, and exporting it:


```js
import { configureStore } from '@reduxjs/toolkit'

export default configureStore({
  reducer: {},
})
```


## Milestone 2: Provide the Redux Store to React

Once the store is created, we can make it available to our React components by putting a React-Redux Provider component around our application in the `src/index.js` file. Import the Redux store we just created, put a `Provider` component around your `TodoApp`, and pass the store as a prop:


```js
import store from './app/store'
import { Provider } from 'react-redux'

ReactDOM.render(
<Provider store={store}>
<TodoApp />
</Provider>, 
document.getElementById('root')
);
```

Now, we will be able to access the Redux store from any of our React components.  Note that even though we can now access the Redux store in any component, we are still using component level state to initialize our list of todos as you can see in the `TodoApp` component.  We will refactor that component to use Redux state in milestone 4.


## Milestone 3: Create a Redux State Slice

A state slice is basically a piece - a "slice" - of the Redux state.  With Redux Toolkit, we have access to a function called `createSlice` which allows us to create that slice of state.  The great thing about this function is that it  automatically generates action creators and action types that correspond to the reducers and state which we provide as parameters. 

Let's go ahead and create a slice of Redux state so that we can work with our list of todos.

Add a new file named `src/features/todos/todosSlice.js`. In that file, import the `createSlice` API from Redux Toolkit.

We will provide an initial state, the reducers to handle state updates, and we will also export the action creators that are automatically created by Redux Toolkit.  

```js
import { createSlice} from '@reduxjs/toolkit'

export const todosSlice = createSlice({
  name: 'todos',
  initialState: [
    { id: 1, text: 'This is a simple todo list app using Redux state!' },
    { id: 2, text: 'Hover over todos and click on the `XX` to delete them!' },
    { id: 3, text: 'Add new todos if you like!' },
  ],
  reducers: {
    addTodo: (state, action) => {
      //fill in the code here
    },

    deleteTodo: (state, action) => {
      //fill in the code here
    }

  },
})

// Action creators are generated for each reducer function
export const { addTodo, deleteTodo} = todosSlice.actions

export default todosSlice.reducer
```

Note that action creators are automatically created for you by Redux Toolkit (RTK for short).  Also, your reducers live inside the same file - so using RTK we are simplifying our Redux development by not requiring separate folders for reducers, actions, etc.

You will also need to update the `src/app/store.js` file to import the reducer from `todosSlice` so that we can initialize the store with it.  Go ahead and change that file to this:

```js
import { configureStore } from '@reduxjs/toolkit'
import todosReducer from '../features/todos/todosSlice'


export default configureStore({
  reducer: {  
    todos: todosReducer,
  },  
})
```

## Milestone 4: Refactor to use Redux state
So far, we have set up and initialized our Redux store.  But, we are not actually using our Redux store yet.  Let's do that!

Let's make our updates to retrieve the Redux store inside the `TodoApp` component.  Go ahead and open up `todo-app.jsx`.  At the top of that file, add an import for the `useSelector` hook - React-Redux gives us access to this hook which will then allow us to retrieve our Redux state.

```js
import { useSelector} from 'react-redux'
```

Before we make use of `useSelector` to retrieve our state, let's get rid of the existing component level state.  Go ahead and remove the lines that reference `useState` and also remove the existing calls to `setTodoState` inside the functions.  

Now, add this line inside the top of the `TodoApp` component:

```js
const todoState = useSelector((state) => state.todos)
```

This will set the state that gets passed to `Todolist` as a prop, which you can see here:

```js
 <TodoList todos={todoState} handleRemove={removeTodo} />
```

You should now be able to load the app at `http://localhost:3000/` and see the Redux state reflected in the homepage!

Note that if you are running into an error saying something like "can't resolve React" you may have to delete a few things "rm -rf node_modules; rm -rf package-lock.json; rm -rf yarn.lock", and then run npm install again.  Then of course try starting the server again with npm start.


## Milestone 5: Add reducer implementations

If you take a look at the `src/features/todos/todosSlice.js` file, you'll see that we created earlier.

Redux requires that we write all state updates immutably, by making copies of data and updating the copies. However, Redux Toolkit's createSlice and createReducer APIs use Immer inside to allow us to write "mutating" update logic that becomes correct immutable updates.  

This means that you can write logic that looks like it's mutating the state, but behind the scenes it will be converted to the correct way of doing things within Redux which means immutable state updates.

Add the implementations for the `addTodo` and `deleteTodo` reducers inside the `src/features/todos/todosSlice.js` file. Then, once you have the implementations, call those reducer functions from the `todo-app.jsx` file.

You should utilize the [useDispatch](https://react-redux.js.org/api/hooks#usedispatch) hook in order to send data to the Redux store.


## Bonus: [Refactor] Form State in Redux

The `AddTodo` component currently manages its own state with respect to the input field. This is perfectly sensible since no other component is particularly interested in that data. However it may be the case that later you want to add a feature that requires consuming that state in a separate component, or maybe you just want to practice creating a state tree with multiple branches. Either way you’ll want to read up on [combineReducers](https://redux.js.org/api/combinereducers) to make it happen.

## Bonus: [Feature] “Complete” Todos

It would be neat if a user could add todos and mark them as completed in addition to deleting them. To toggle the state of the todo, the user should click on the todo text itself. You’ll have to modify our current data model to track this new information. You also have lots of creative freedom in developing the UI. A more straight-forward implementaion could display the existing todo list and add styling to identify completed todos – strikethrough is a reasonable choice. Feeling fancy? Provide a visibility toggle so the user can view pending, completed, or all todos. A more complex implementation might render two separate lists, one for pending and one for completed. This is a great way to leverage the reusability of React components. Don’t be limited by our suggestions!


