In this lab, we will refactor our Todo List app from Lab 1.  Go ahead and start from your finished version of lab 1.

This app will have the same functionality as the previous lab; although there are some stretch goals to give you a chance to flex your React and Redux muscles. Today's lab will focus on integrating Redux, so that our todos are no longer stored in component state, but rather in a centralized store. There are required milestones in this lab that should be feasible to get through in the time allotted as well as a bonus milestones you are encouraged to attempt in class if you finish early or later on your own. If you are stuck at any point don't hesitate to ask for assistance. This lab may be challenging as we will be introducing new vocabulary and patterns.


#### Required concepts before starting:
* Javascript - [ES6](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md)
* [React - components, classes, props, JSX](https://github.com/varoonsahgal/react-fundamentals/blob/main/reactstudyguide.md)

> If any of these concepts above need a refresher, please follow the links before getting started with the lab.

#### What you will learn:
* **React** - passing props, using map, further understanding of components.
* **Redux** - store, reducers, mapStateToProps, connect. We will refer to Redux QuickStart Guide

#### Why we are doing this lab:
Maintaining state on the front-end is a requirement for any apps that purport to do something of interest. Redux is a powerful new paradigm for managing state in a performant, functional, and maintainable way. The learning curve can be steep because it has it own vocabulary and design patterns. React and Redux are now essential parts of the front-end toolbelt for tech companies around the world. Notable ones being Facebook, Airbnb, Reddit, Netflix, Walmart, and of course Salesforce!


## Milestone 1: Create a Reducer
Redux's `createStore` function requires a root reducer to construct a store with. This reducer can be composed of many reducers, each managing their own branch of the state tree. However in our simple app, we only have one piece of state to track: todos. We will create a reducer to manage our collection of todos.

First, let's add Redux and the bindings for React to our project:

```bash
$ npm install redux react-redux
```
Create a file `reducer.js` in the `src` directory. A reducer must return the current state if it doesn't recognize an action type, and it must also provide an initial state if the current state is undefined. We'll begin with a reducer that does this absolute minimum before augmenting it with the ability to add and remove todos.

```js
export const todos = (state = [], action) => {
  switch (action.type) {
    default:
      return state;
  }
};
```


## Milestone 2: Create the Store
In `src/index.js` import the `createStore` function, the `Provider` higher order component as well as the root reducer you just created:

```jsx
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import { todos } from './reducer';
```

Now let's make our Redux store. Before the `ReactDOM.render` invocation, add:
```jsx
const store = createStore(todos);
```

Remember, this is all that's required to initalize a Redux store. However, since an empty todos array is not very interesting to look at, let's take advantage of `createStore`'s optional second argument – inital state. Right above our store initialization add these sample todos (or feel free to invent your own):

```jsx
const initialTodos = [
  { id: 1, text: 'use redux in todo app' },
  { id: 2, text: 'complete bonus milestone' },
]
```

And pass the array as the second argument to `createStore`

```jsx
const store = createStore(todos, initialTodos);
```

Now when we wire up our app with the redux store we'll get some visual feedback of our success! Ok, we need to make the store available to our component tree. Just after your `store` code, create an `App` component that wraps `TodoApp` with the `Provider` we importer earlier. Then pass your Redux store as a prop to the `Provider`, which will make any component in the hierarchy able to connect to the redux store using `connect`.

```jsx
const App = () => (
  <Provider store={store}>
    <TodoApp />
  </Provider>
);
```

Finally, let's render our provider-wrapped app:

```jsx
ReactDOM.render(<App />, document.getElementById('root'));
```

> It's common to inline the provider-wrapped app directly in the `render` method:
>
> ```javascript
> ReactDOM.render(
>   <Provider store={store}>
>     <TodoApp />
>   </Provider>, 
>   document.getElementById('root')
> );
> ```


## Milestone 2.5: Remove React State
In `src/todo-app.jsx` let's remove the old component level state.  If you are using hooks in your code, then just comment out the call to `useState`, and the calls to your state updater function.  

If you are not using hooks for component level state, then you can just comment out your state initialization code in your component class. 

Make sure to also remove the `todos` prop being passed to `TodoList`.

At this point you'll be getting errors, because we no longer have a todos array available in `TodoList`, so let's remedy that.


## Milestone 3: Access Redux State

The `react-redux` bindings library provides the `connect` function to hook up any component to the Redux store. This allows it to access state and dispatch actions to update the state. Let's `connect` `TodoList` to the store.

The `connect` function has an interesting function signature. The first argument should be a function that will receive the current state as an argument. It should return an object with key value pairs that will be passed to the connected component as props. The function is commonly called `mapStateToProps` since this name describes exactly what it does, but the actual variable name is not important. This function is where you can select the branches of the state tree your component is interested in and it will directly receive them as props – no matter how deeply nested it is. No more passing props down the component tree!

In our app, remember that our redux state is simply an array of todos, something like:

```javascript
[{ id: 1, text: 'todo1' }, { id: 2, text: 'todo2' }]
```

So, when we pass the todos down, we can just pass the entire state. In more complex apps, you will only be passing in a "slice" of the state, but we'll talk more about that later.

Let's get to work. In `src/todo-list.jsx` add the following function definition after the component:

```jsx
const mapStateToProps = (state) => {
  return {
    todos: state
  }
};
```

and now import `connect`

```jsx
import { connect } from 'react-redux';
```

and modify the export statement

```jsx
export default connect(mapStateToProps)(TodoList);
```

Take a moment to study how we use `connect` – it accepts a state-mapping function, returns a new function that accepts a React component, and that second function returns a new connected component that wraps ours and is responsible for passing it the props we specified in the mapper. (This pattern is called a "higher order component.") The connected wrapper component is subscribed to updates from the store and will rerender when state changes, passing the new values as props to our wrapped component through `mapStateToProps`.

You should now be able to see our new list of todos rendering on the page. Take a moment to inspect the page with the React dev tools. If you don't have them installed, [get them here](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en-US). To open the React dev tools once installed, open the regular Chrome dev tools and navigate to the new tab called `React`.

You should be able to inspect the component hierarchy. Try to locate the component created by connect to provide the props to `TodoList`.


## Milestone 4: Update State - Add Todo

In order to update state, we need an action object that can be given to the dispatch, which then sends it to reducer which updates the state. Typically we write action creators to return different types of action objects, so we keep our code DRY. 

Create a file `src/actions.js` and add the action creator:

```jsx
const addTodo = text => ({ type: 'ADD_TODO', text })
```

Remember that the Redux store does not allow direct access to the state. In order to update state you must `dispatch` an action describing how the state should change and have a reducer implemented to respond to that action by producing the new state. Actions are POJOs (plain old javascript objects) whose only requirement is a `type` key. The redux store provides the `dispatch` method, but we'll use the `react-redux` bindings to expose it to our components just like we did to expose state.

Let's refactor the add todo logic in `src/AddTodo.jsx` to dispatch an action to the Redux store. First we'll need to import our helper funtion:

```jsx
import { connect } from 'react-redux';
import { addTodo } from './actions';
```

Notice the that the action creator makes the todo and assigns it to the payload property. We'll use this to make our todos instead of the logic in the `handleSubmit` method. However, we need to dispatch the action to the store. So, next we'll use the action creator in our `mapDispatchToProps` function. Add the following right after the action creator:

```jsx
const mapDispatchToProps = (dispatch) => {
  return {
    handleAdd: text => dispatch(addTodo(text))
  }
};
```

This function is analogous to `mapStateToProps`: it will receive the store's dispatch method as an argument, and it is expected to return an object of key value pairs that will be passed to the component as props. Here our values are functions that invoke the `dispatch` method with an action.

Now let's use the dispatcher in the component. Refactor the `handleSubmit` method as follows:

```jsx
handleSubmit() {
  const text = this.state.todoText.trim()
  if (text === '') return;
  this.props.handleAdd(text);
  this.setState({ todoText: '' });
}
```

Our last step is to wire up the component with `connect`, which means a quick refactor of the export statement.

```jsx
export default connect(null, mapDispatchToProps)(AddTodo);
```

Take particular notice here that the first argument to `connect` is `null`. Why might this be the case? What did we previously pass as the first argument?

Since `AddTodo` doesn't need to access the current state, we don't need to provide a `mapStateToProps` function, but position is important and `mapDispatchToProps` must be the second argument, so we pass the `null` placeholder as our first argument.

Now that `handleAdd` is provided to our component as a prop by `connect` let's go back to `src/todo-app.jsx` and clean up the code we no longer need. `AddTodo` no longer requires `handleAdd` to be passed as a prop by `TodoApp` and naturally `addTodo` is no longer used and can also be removed.

> It is common to provide a `mapStateToProps` function, even if it returns an empty object:
> 
> ```javascript
> const mapStateToProps = (state, ownProps) => ({})
> ```
> 
> This allows the signature at the bottom to be consistent, and you don't have to keep track of whether a `null` is being passed or not.

If, in a frenzy of uncontrolled excitement, you fired up your app and tried adding a todo, you may have been disappointed to see nothing happen. Although we are currently dispatching an action, there is a second piece required to update the state. The reducer must be explicitly written to handle the action type you are dispatching, so our work is not yet done.

In `src/reducer.js` we'll now make our `todos` reducer actually do something interesting instead of just returning the current state. The action type we used in the action creator was `ADD_TODO` so we'll need to support updating the state in that case.

Before you rush into implementation, let's write some tests! TDD is easy to do with Redux and testing your business logic hugely improves the codebase. `create-react-app` provides the `Jest` testing framework out of the box; all you have to do is put a `whatever.test.js` file somewhere and then when you run `yarn test` it will pick it up!

Industry practice is to collocate test files alongside their implementation, bundled together under a `__tests__` folder. So let's create a file `src/__tests__/reducer.test.js` file.

```javascript
import { todos } from '../reducer'
import { addTodo } from '../actions'

describe('src.reducer', () => {
  describe('ADD_TODO', () => {
    beforeAll(() => {
      jest.spyOn(Date, 'now').mockImplementation(() => 1234567890)
    })
    
    it('should add a new todo to an empty list', () => {
      const state = []
      const newState = todos(state, addTodo('TDD is awesome'))
      expect(newState).toEqual([ {id: 1234567890, text: 'TDD is awesome'} ])
    })

    it('should add a new todo at the end of the list', () => {
      const state = [{ id: 1, text: 'TDD is awesome' }]
      const newState = todos(state, addTodo('Remember to check all the cases!'))
      expect(newState).toEqual([
        { id: 1, text: 'TDD is awesome' },
        { id: 1234567890, text: 'Remember to check all the cases!' }
      ])
    })
  })
})
```

Once you have tests that fail, write the implementation to make them pass!

```js
export const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, { id: Date.now(), text: action.text }];
    default:
      return state;
  }
};
```

When this reducer is invoked with an action of type `ADD_TODO` it will **create a new array** with all the current todos and append the payload, ie. the new todo. Remember the reducer must be implemented as a pure function meaning **you cannot mutate the existing array.**

Test your todo app. The list should now update as you add todos!


## Milestone 5: Update State - Delete Todo

Now that you've seen the Redux pattern, it's your turn to try it out. Refactor `TodoList` to delete todos from the Redux store.

You'll need to:

- Define an action type for deleting todos
- Make an action creator or don't – you choose
- Provide a mechanism for your component to dispatch an action of the type you defined
- Enable the store to recognize that type of action
- This isn't X-Men so remember, NO MUTATIONS
  - <details markdown="1">
      <summary>HINT: Not sure how to immutably remove an item from a list?</summary>
      Try using `Array#filter` to create a new array where it only has certain todos... what would be a way to filter out the todo in question?
    </details>
- Clean up any code that has been made superfluous
- If you are stuck at any point, please ask a question! We're happy to point you in the right direction!

## Milestone 6: Refactor MDTP

Learning how `mapDispatchToProps` worked by spelling out the functions was great, but it's time to stop typing so much. Try out refactoring the MDTP using the "object shorthand" and pass in the action creators values. Read more [here](https://react-redux.js.org/using-react-redux/connect-mapdispatch#defining-mapdispatchtoprops-as-an-object).

Typically, you use the same prop name as the action creator name. You'll want to rename `handleAdd` to be `addTodo` and then return a MDTP such as:

```javascript
const mapDispatchToProps = { addTodo }
```

## Milestone 7: Access `dispatch` Directly

You can also try passing nothing in for the second argument of `connect` in order to have `dispatch` passed as a prop to your component. Read more [here](https://react-redux.js.org/using-react-redux/connect-mapdispatch#default-dispatch-as-a-prop). Then you can invoke it directly with the action creator, something like:

```javascript
this.props.dispatch(addTodo(text))
```

Think about the strengths and weaknesses of the three approaches: defining MDTP as a function, defining it as an object, and having `dispatch` injected, and talk about them with a coworker. Which style do you prefer, and why?

`<opinion>`In most cases, it has been our experience that accessing `dispatch` directly is the best pattern to follow -- it's still clear what is a Redux action, and it leads to the least amount of boilerplate (it only gets worse when you add Flow or TypeScript on top). There are only a few cases where using the MDTP pattern is useful in creating certain advanced React patterns, and they're quite limited (we've used them in maybe 0.1% of our components).`</opinion>`

## Bonus: [Feature] “Complete” Todos

It would be neat if a user could add todos and mark them as completed in addition to deleting them. To toggle the state of the todo, the user should click on the todo text itself. You'll have to modify our current data model to track this new information. You also have lots of creative freedom in developing the UI. A more straight-forward implementaion could display the existing todo list and add styling to identify completed todos – strikethrough is a reasonable choice. Feeling fancy? Provide a visibility toggle so the user can view pending, completed, or all todos. A more complex implementation might render two separate lists, one for pending and one for completed. This is a great way to leverage the reusability of React components. Don't be limited by our suggestions! You could also try out keeping track of how many todos the user has marked completed in the current session with a counter.

You'll notice that `todos` only keeps track of the current todos state. If you add things like a visiblity filter, you'll want to store that in a separate "slice" or namespace of the state, e.g. `visibilityFilter`. You'll want to read up on [combineReducers](https://redux.js.org/api/combinereducers) to make it happen.
