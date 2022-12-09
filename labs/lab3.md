
In this lab, we will continue to expand on our Todo List app to be able to handle server interactions -- such as loading data from the server and saving it. Async network calls are a core part of an webapp, so let's figure out how to do them in React and Redux. If you are stuck at any point don’t hesitate to ask for assistance.

#### Required concepts before starting:
- **React** - passing props, using map, creating components. 
- **Redux** - store, reducers, connect, mapStateToProps, mapDispatchToProps. 

> If any of these concepts above need a refresher, please follow the links before getting started with the lab or throughout the lab as needed.

#### Why we are doing this lab:

In the previous lab, all our initial todos were hard-coded into the app, and would lose all your hard work creating todos when the browser reloaded. Not exactly a positive user experience, to be sure.

Nearly every webapp includes asynchronous server interactions to read and store data. They are accomplished in a special way within React and Redux, so we need to figure out how they work.

Also, here's the presentation material for this portion of the class: https://docs.google.com/presentation/d/1GdHTYWFwrFSZaDRBwqZgQSg7up476LpNdLCbJtOTQBY/edit?usp=sharing



## Milestone 1: Get Starter Repo

We will mostly be working from what we built in the previous lab, with a few extra additions. The project will now have a fake server to read and add todos to a database (stored locally in a JSON file). Load up the starter project:

```bash
git clone --single-branch --branch lab2-ref-updated https://github.com/varoonsahgal/rrf-todo.git
```

You will need to install yarn (another package manager like npm):

```
cd rrf-todo
npm install --global yarn
```



Then just running `yarn` will install the packages:

```
$ yarn
```
You may see some warnings and even some gyp errors after installation but you should be fine to proceed regardless.

Once everything is installed, you can run the app with:

```
$ yarn start
```



### Explore the API

You will now need to run a fake server locally to manage GET and POST requests. The repo is all set up to do it, you can just run the server from your terminal:

```bash
$ yarn server
```

Leave it running in a terminal session. You can then check out the API using some GET requests in your browser. Visit:

- [http://localhost:8000/todos](http://localhost:8000/todos)
- [http://localhost:8000/todos/1](http://localhost:8000/todos/1)
- [http://localhost:8000/todos/2](http://localhost:8000/todos/2)

We will eventually use this server to read and store our todos and create persistence!

(For the curious and precocious, the server runs from a package [`json-server`](https://www.npmjs.com/package/json-server). It runs a barebones server that handles GET, POST, PUT, and DELETE requests like a simple CRUD server. It uses `lowdb` as a database and persists data to a JSON file (currently stored in `server/db.json`). A wonderful tool for making a basic CRUD backend.)

## Milestone 2: Receive Todos

Before we concern ourselves with server interactions, we need to improve the functionality of our Redux system. Right now it can add or remove a todo, but what about loading a collection of todos? We'll need a new action and reducer, `receiveTodos`, to receive a payload of todos and replace the state with them.

### Test

Because we're all TDD aficionados by now, let's begin with a failing test!

<details markdown="1">
  <summary>🤔 Pause and think: what would we test? Try writing your own test before peeking.</summary>

```javascript
describe('src.reducer', () => {
  describe('#receiveTodos', () => {
    it('should load a collection of todos into state', () => {
      const state = []
      const data = [
        { id: 1, text: 'foo' },
        { id: 2, text: 'bar' },
      ]
      const newState = todos(state, receiveTodos(data))
      expect(newState).toEqual(data)
    })
  })
})
``` 

> Pro tip: When writing reducer tests, it's not uncommon to test combinations of actions. You can write `state0`, `state1`, etc., or `s0`, `s1`, etc. for short.

</details>

<p><details markdown="1">
<summary>For a taste of advanced TDD patterns and a little bit of functional programming, read in here!</summary>

```javascript
const runActions = (reducer, initialState, ...actions) => (
  actions.reduce(
    (acc, action) => [...acc, (reducer(acc[acc.length - 1], action))],
    [initialState],
  )
)
```

Then you can express multiple transformations more succinctly:

```javascript
it('should work in succession', () => {
  const [s0, s1, s2, s3] = runActions(todos,
    [],
    receiveTodos([/* ... */]),
    addTodo('baz'),
    removeTodo(/* ... */),
  )

  // make assertions
})
```

</details></p>

### Implement

Now that you have a failing test, go make it pass by writing the actual reducer ! The answer isn't provided this time, but you should be able to do it based on the work you did in lab 2!
### Use

Great! Let's make use of the `receiveTodos` action right away in `src/index.js`. Where we initially loaded `initialTodos` directly into state, let's use the action so we interact with our redux state using its interface.

```javascript
const initialTodos = [
  { 'id': 1, 'text': 'use redux in todo app' },
  { 'id': 2, 'text': 'complete bonus milestone' },
]

const store = createStore(todos)

store.dispatch(receiveTodos(initialTodos))
```

Be sure to also import the `receiveTodos` action creator inside your index.js file.

> Pro tip: The more that you can stop assuming you know the shape of your redux state throughout your app, the better. We want to encapsulate our redux state using our actions as much as possible. 

## Milestone 3: Load Todos

Now that we have the functionality in place to load an arbitrary payload of todos from somewhere, let's actually get it from the server!

### Making XHR Requests

For this lab, we'll use the [axios](https://github.com/axios/axios) library to handle our HTTP requests. Add it to your project with

```
$ yarn add axios
```

<p><details markdown="1">
<summary>A brief, opinionated aside about `axios` vs. `fetch`</summary>

A lot of people write guides using `fetch` these days, which is available in the major browsers. While it's a perfectly fine way to do XHR requests (read [this guide](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)), it is our opinion that axios still outshines the fetch API. Axios provides more robust functionality, more intelligently sets headers, and offers a cleaner, more useful API (no need to `JSON.stringify` or call `.json()` on responses like in fetch). 

</details></p>

You are encouraged to read through the axios [documentation](https://github.com/axios/axios) to learn more, but the salient point here is that you can call

```javascript
axios.get('someUrl')
```

and get a promise response back. Let's try using it in the browser directly to start... Go to `src/index.js`, import axios, and then set it on the window so you can play with it in the Developer Tools.

```javascript
import axios from 'axios'

window.axios = axios
```

Once your app reloads, open up the Developer Tools and try fetching the collection of todos:

```javascript
axios.get('http://localhost:8000/todos').then(res => console.log(res))
```

You'll get an object that looks roughly like:

```
{
  config: ...,
  data: (this is the data from the server),
  headers: ...,
  status: 200,
  ...
}
```

The most useful thing is the `data` on the `res`. This contains your server data of todos!

### Making XHR Requests in React

First, let's stop adding the todos as hard-coded values from `src/index.js`:

```javascript
// const initialTodos = [
//   { 'id': 1, 'text': 'use redux in todo app' },
//   { 'id': 2, 'text': 'complete bonus milestone' },
// ]

const store = createStore(todos)

// store.dispatch(receiveTodos(initialTodos))
```

Now let's practice making the XHR request in React. We'll put it inside the `TodoList` component for now.

🤔 STOP and think: why would we want to put the request in `TodoList` as opposed to any other component, e.g. `AddTodo`, or `TodoApp`? What's the pros and cons?

We need to wrap our code that produce side effects in a `useEffect` call. If we didn't wrap it in `useEffect`, those side effects would run before the React component had been mounted or re-rendered to the DOM, and generally we want that to happen *afterwards*.

[This article](https://overreacted.io/a-complete-guide-to-useeffect/) is a super deep dive into `useEffect` if you ever want to kill an hour and learn a lot more about React.

Let's try requesting the todos from the server and then sending them to Redux using `receiveTodos`. (Remember to import `axios` and `receiveTodos`, and to pass `receiveTodos` in through the MDTP or access directly on `dispatch`!)

```javascript
const TodoList = ({ todos, receiveTodos, loadTodos, removeTodo }) => {
  useEffect(() => {
    axios.get('http://localhost:8000/todos')
      .then(({ data }) => {
        receiveTodos(data)
      })
  })
  return /* ... */
}
```

If you saved this and ran your app, you'd create an infinite loop of fetching data - which may look like this:

<img width="750" alt="Screen Shot 2022-09-29 at 9 21 11 PM" src="https://user-images.githubusercontent.com/25653204/193169588-dc833768-0fa6-4ed2-ad5a-ddaae00acc96.png">


🤔 STOP and think: What happened?!

Remember that `useEffect` runs every time the component renders. Inside `useEffect`, we'll pass a *new array object* containing the todos. That new array will replace the old state. Redux detects changes based on identity (that means `[] === []` is `false`), so Redux triggers a re-render in the component. The component fires `useEffect` again, and the cycle repeats.

Breaking the cycle requires telling `useEffect` to only run on mount, and optionally when any other specified paramter changes. The parameters are passed in as an array as the second argument to `useEffect`. If you don't want it to ever re-run (i.e. only on mount), you'd pass in an empty array `[]`.

```javascript
const TodoList = ({ todos, receiveTodos, loadTodos, removeTodo }) => {
  useEffect(() => {
    axios.get('http://localhost:8000/todos')
      .then(({ data }) => {
        receiveTodos(data)
      })
  }, []) // <-- changed line
  return /* ... */
}
```

<details markdown="1">
<summary>BONUS: learn more about using `async` with `useEffect`</summary>

The progressive among you might be rightly tempted to refactor to use the ES2017 `async/await` keywords. If you try to do something like this...

```javascript
const TodoList = ({ todos, receiveTodos, loadTodos, removeTodo }) => {
  useEffect(async () => {
    const data = await axios.get('http://localhost:8000/todos')
    // ...
  }, [])
  /* ... */
}
```

...you'll receive a slap on the wrist from React:

![screencap]({{ site.baseurl }}/images/async-useeffect.png)

You simply need to use a regular function with `useEffect`, and then create async IIFE (Immediately Invoked Function Expression) inside:

```javascript
const TodoList = ({ todos, receiveTodos, loadTodos, removeTodo }) => {
  useEffect(() => {
    (async () => { // a function expression...
      // ...
    })() // ... that is immediately invoked
  }, [])
  /* ... */
}
```

</details>

## Milestone 4: Load w/ Redux

Cool! So now we have data fetching with React... but now we're entangling business logic with our View layer; the `TodoList` component has deep knowledge about how to talk to a server and load data into the Redux state, rather than simply focusing on rendering something.

Also, if we ever wanted to replicate that loading behavior in a different component (maybe `TodosSidebarList` or something), suddenly we're running into trouble with code duplication.

Wouldn't it be great if Redux could handle our network requests business logic along with all our state management? [Redux thunks](https://github.com/reduxjs/redux-thunk) come to the rescue! <span style="font-size:30px">🦸‍♀️</span>

### Set up redux to handle thunks

First, add `redux-thunk` to your project with `$ yarn add redux-thunk`.

Right now Redux's `dispatch` can only handle objects. We need to improve the system to handle functions, or thunks. We do this by giving it some middleware that knows how to do it. Inside your `src/index.js` file, improve the creation of the store:

```javascript
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'

const store = createStore(todos, applyMiddleware(thunk))
```

### Write a failing test

Time to write another failing test! Create a file `src/__tests__/actions.test.js` and get started. We'll start with a bit of setup...

```javascript
import thunk from 'redux-thunk'
import configureStore from 'redux-mock-store'
import axios from 'axios'
import { loadTodos } from './actions'

// pass an initial state to `mockStore` function 
// to get a mocked store with that initial state
const mockStore = configureStore([thunk])

// stub out `axios` so it doesn't make any real
// HTTP requests, and you can stub responses
// with `axios.get.mockResolvedValue(...)`  
jest.mock('axios')
```

Note that this calls for a new library, `redux-mock-store`, which you should add with yarn.

Now it's time to write that failing test...

<details markdown="1">
  <summary>🤔 Try writing your own test before looking at the answer.</summary>

```javascript
describe('actions', () => {
  describe('#loadTodos', () => {
    it('should talk to server', async () => {
      const store = mockStore([])

      // fake data returned from server
      const serverTodos = [{ id: 1, text: 'TDD your thunks for much win' }]
      axios.get.mockResolvedValue({ data: serverTodos })

      // Dispatch
      await store.dispatch(loadTodos())

      // Assert
      expect(axios.get.mock.calls).toEqual([
        ['http://localhost:8000/todos'],
      ])
      expect(store.getActions()).toEqual([
        { type: 'RECEIVE_TODOS', todos: serverTodos },
      ])
    })
  })
})
```

</details><br />

Once that's finished, write a thunk to make it pass.

<details markdown="1">
  <summary>🤔 Try writing your own implementation before looking at the answer.</summary>

```javascript
export const loadTodos = () => (
  async (dispatch, getState) => {
    const { data } = await axios.get('http://localhost:8000/todos')
    dispatch(receiveTodos(data))
  }
)
```
</details><br />

Now go use it in the `TodosList` component, and dispatch the thunk from `useEffect` rather than making the network call directly. We won't show the code here, it's up to you to replace it and do all the necessary wiring. Reach out to an instructor if you're feeling stuck.

## Milestone 5: Add Todo

Now that you're loading the todos from the server, it's time to start saving those todos to the database! Follow the same TDD pattern from before: write a failing test checking for the expected behavior, then write the implementation!

Hints

- You can create a new resource with a `POST` request to the collection
- You shouldn't provide an `id`, we'll expect the server to give us that information back
- `POST` requests to a REST endpoint canonically expect the created resource to be returned in the response.
  - You can use the response data to add the todo to your own list, with the correct `id`!

## Milestone 6: Delete Todo

Same story, but now we'll implement clearing todos from the database; otherwise we'll only be able to add todos, and get overwhelmed with our ever-growing list! Use a `DELETE` request (with `axios.delete`) to the specific resource in question, e.g. `DELETE /todos/3`.

## Milestone 7: Improve Loading UX

You're reaching the challenge mode now! Most of the answers won't be provided here, but feel free to ask for guidance if you get stuck. 

Notice how the app flashes a "No todos remaining" message just before the todos are loaded from the server? That's a bit misleading. Web UIs are all about providing immediate, useful feedback to the user in a world where requests don't happen instantaneously. The more you naturally provide from the transitions during web requests, which may take a couple seconds to complete, the better you will become as a web developer.

A common pattern is to keep tracking of a `loading` flag somewhere, that starts off as `true` and is set to `false` once the server call has been completed.

🤔 Stop and think: would it be best to put the `loading` flag in React state, or Redux state? Both of them can be the right answer, but under what circumstances?

For this exercise, let's assume we should put it in Redux. It's time to practice managing different slices of your state tree and [combining reducers](https://redux.js.org/api/combinereducers).

Let's call the state slice `ui` for lack of a better name. (More realistically, you would put this flag on the `todos` state and make it a more complex object, but for the sake of learning we'll put it in a separate state slice.)

You'll follow the same steps as you've done before to create the reducer. In `src/reducer.js`, create a `ui` reducer function, following the similar patterns. Have it respond to a `TODOS_LOADED` action type. Do it TDD, and add the necessary action creator.

Now we'll need to add this reducer onto the redux state with `combineReducers`.

```javascript
import { createStore, applyMiddleware, combineReducers } from 'redux'

const rootReducer = combineReducers({ todos, ui })
const store = createStore(rootReducer, applyMiddleware(thunk))
```  

Remember that state slices can communicate with other state slices through actions. In your `loadTodos` thunk, you'll want to dispatch your `todosLoaded` action once the todos have been loaded into the Redux state.

Once all the business logic is in place, you can add it to your React component. Pull down the appropriate state and showing a loading text (or spinner or whatever you want) until the server finishes loading.

## BONUS: Test Your React

Finish early? Go check out [enzyme](https://airbnb.io/enzyme/), the most popular testing library for React components. See if you can figure out how to test your `TodoApp`. Since the app is quite small, you're encouraged to write unit tests with `mount` that holistically test the behavior of the app based on user interactions.

1. Check that it starts off with a loading message
1. Once the server responds (use a fake response!), it should show the todos
1. If no todos exist, it should say as such
1. When you click the "X", it should remove the todo
1. When you add some text and submit, it should add it to the todo list

There's not a lot of good resources on smart testing for React. [Kent C. Dodds](https://testingjavascript.com/) has written some influential posts that are worth checking out.
