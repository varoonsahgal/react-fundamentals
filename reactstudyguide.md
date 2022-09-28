Here we've compiled a handy guide/tutorial of various topics in the React JS library we think you should know before you start the React and Redux Fundamentals course.  Please be sure to read our [ES6 Study guide](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md) before reading this guide.  This is by no means a comprehensive guide to React, but something that's meant to help you hit the ground running in our course.  Also, please read each section carefully - especially topics that may be new to you.

For in depth coverage, consult the [official site](https://reactjs.org/docs/getting-started.html), where you can find Getting Started Guides, Concept Guides, and the API docs.

# Table of Contents

1. [React Overview](#what-is-react)
3. [Using JSX with React](#react-and-jsx)
2. [Components in React Overview](#components-in-react)
4. [Events in React](#event-handlers)
5. [Understanding render()](#react---what-is-render)
6. [Props in React](#react---props)
7. [Constructors in React Components](#react-components---constructors)
8. [Different ways to create a Component in React](#react---creating-a-component)
9. [State in React](#react-state)
10. [Presentational vs Container Components](#presentational-and-container-components)
11. [Rendering a List of Components](#rendering-a-list-of-components)
12. [React Hooks](#hooked-on-state)

### What is React?

- React is a JavaScript library for building user interfaces.
- React is not a MVC framework - it's just the "V" in MVC - the View.  So, React is just a library for rendering your views, and is not a framework.
- It popularized the idea of using declarative, functional programming to represent the UI
- Whereas with a mess of jQuery code you have no idea what your app might look like at any given time, with React you can know *exactly* how your app should look given enough context about its data.
- React allows you to define a top-level component, i.e. your `App`, which is composed of subcomponents with their own encapsulated logic.

```
TodoApp
|__ Header
|__ AddTodo
    |__ input
    |__ button
|__ TodoList
    |__ Todo
        |__ checkbox
        |__ label
        |__ DeleteButton
            |__ ...
```

- Data "flows" from the top-level (or parent) components to its lower-level (or children) components. It talks about "one-way data flow," contrasted with Angular's magical "two-way" data binding approach ([figure](https://stackoverflow.com/a/37566693/2672869)).
- You then take your top-level component and "render" it to the DOM using the `react-dom` library:

```javascript
ReactDOM.render(
  <TodoApp />,
  document.getElementById('app'),
)
```

- You can play with React in [this CodePen](https://reactjs.org/redirect-to-codepen/hello-world), feel free to follow along with our examples. You'll start with the "app" inlined, and it's quite simple, but you could extract it out to make it easier to play with:

```javascript
const App = () => (
  <h1>Hello, world!</h1>
)

ReactDOM.render(
  <App />,
  document.getElementById('root')
)
```

### What makes React performant?

- Laying out elements on the DOM is slow.  This is because the layout algorithm is quite complex.
- React doesn't actually help speed up layout.
- But, React does ensure that only the *necessary* parts of the DOM are updated.
- React accomplishes this by using what's called a ***virtual DOM***.  Think of the virtual DOM as a blueprint for the actual DOM.  This is key to React's performance.
- You can also read [here](https://www.codecademy.com/articles/react-virtual-dom) for more info on this topic.
- Keep in mind that the details of how the virtual DOM work are abstracted away from you as a programmer.  So, you don't need to think about it when writing React code.


### React and JSX

- When working with React, you will often see files with a `.jsx` extension.  What is JSX?
- Consider this syntax:

```javascript
const element = <h1>Hello world!</h1>
```

- The odd tag syntax is neither a string nor HTML, but actually a function!
- JSX is an extension to the JavaScript language used with React. (Technically, it's syntax that is transpiled by Babel into valid JavaScript.)
- JSX looks like HTML, but isn't exactly HTML.
- Without JSX, you'd have to create React elements like this:

```javascript
React.createElement(
  MyButton,
  { color: 'blue' },
  'Click Me',
)
```

- With JSX, you can write:

```jsx
<MyButton color="blue">
  Click Me
</MyButton>
```

- Fundamentally, JSX just provides syntactic sugar so that we don't have to call the `React.createElement(component, props, ...children)` function directly ourselves.
- So, you can see how much nicer the JSX code looks!
- JSX is not required to work with React but makes your React code easier to understand. 99.99% of React developers us it.

### JSX Expressions
- We can use curly braces -- `{ }` -- to embed regular JavaScript inside our JSX.  You will see many examples of this below.

```javascript
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;
```

- Any JavaScript expression can be written between these curly braces.  We could call a function, evaluate an expression, concatenate strings, or write literal values like strings or numbers.
- It has to be an expression - something that returns a value.
- We can not use an if/else statement since that's a JavaScript statement, which doesn't evaluate to a value. You can, however, use a ternary operator `expr ? cond1 : cond2`

### Components in React?

- With React we can create what's called ***components*** and then re-use them just like built-in HTML elements.
- For example, we could create a Product Carousel component that could be used on the Shopping Cart page to show products currently in a customer's cart, and then we can re-use that same Product Carousel component on the Product Search page.
- This way we can design, build, and test a component once.  And, when we re-use a component, we don't have to worry about the underlying JavaScript and behavior used to build it.
- Components can be shared within a company for others to use - a lot of companies have a React component explorer to share component code...

### What's In a Component, Anyway?

- What would a simple component in React actually look like?  Here's an example:

```javascript
import React from "react"

const MyComponent = () => {
  return <h1>Hello World!</h1>
}
```

- Currently, the only thing our `MyComponent` component does is render a snippet of HTML code, `<h1>hello World!</h1>`.
- The first line simply imports libraries from the react npm module. This is necessary to write JSX, the line with `<h1>Hello World!</h1>`
- In the second line, you can see we name our component "MyComponent". It's simply a function.
- In the third line, we return `<h1>Hello World!</h1>`.  This looks exactly like HTML, but it is not - it's what's called JSX (more on this later - and don't worry, it's fairly easy to understand!). What we return from our component function is what will be rendered to the DOM.

Any function that returns some JSX is a valid "function component."

You can also write React components as a class:


```javascript
import React from "react"

class MyComponent extends Component {
  render() {
    return <h1>Hello World!</h1>
  }
}
```

These two are equivalent in React's eyes.

#### Which one should I use?

The community by and large is shifting toward function components, because they are now functionally (haha) equivalent to class components. (This wasn't always the case, but not something you need to worry about young padawan.) They are also easier to understand and can be more easily optimized for performance. The [React docs](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines) are pretty clear that function components are the path forward.

For this guide, we will mostly be focusing on writing function components, because they tend to have far fewer "gotchas" that plagued writing class-based components. (Just ask a veteran React developer about using `bind` statements in the constructor and watch the horror flash across their eyes...)

### React Components as Children

- A very important feature of React is the fact that a given component can render other React components.
- For example, suppose we want to use the `MyComponent` component (shown above) in another component we can call `TopComponent`.  How would we do this?
- Here is what it could look like:

```javascript
import React from "react"
import MyComponent from './MyComponent'; // we have to import our MyComponent component first

const TopComponent = () => (
  <div>
    <MyComponent />
    <p>React is pretty cool!</p>
  </div>
)
```
- Pay special attention to the line `<MyComponent />`.  This is where we render the `MyComponent` component, which will simply output 'Hello World'.
- Note that `<MyComponent />` is *JSX* syntax (it's a JSX tag), not XML or HTML.  Read below for more details on JSX.
- So, to summarize: the MyComponent component was defined as an ES6 class.  Then, to render the `MyComponent` component from `TopComponent`, we put `MyComponent` inside a JSX tag - `<MyComponent />` and returned it.

### React - User-defined components versus Built-in elements

- When an element type starts with a lowercase letter, it refers to an element that's built into React - normal DOM tags like `div` or `span` -- which result in a string 'div' or 'span' being passed to React.createElement.
- User-Defined components must be capitalized.  So, any React components you create must start with a capital letter.  The examples we used above -- `MyButton` and `App` are user-defined components.

### React properties

- All properties (the values passed after the element tag) should be camelCased.
- What works in raw HTML usually works in JSX-land, except for some notable exceptions:
  - JSX uses `className` instead of `class` for CSS styles.  Because JSX gets compiled into JavaScript, and the `class` keyword is reserved in JavaScript, `className` is used by React for the attribute to apply a CSS class to an element (technically, `className` is also how it's referred to in the [JS web API](https://developer.mozilla.org/en-US/docs/Web/API/Element/className)).  Here's an example of some JSX code:

  ```jsx
  <div className="someStyle">
    Testing
  </div>
  ```

  - In a normal HTML DOM element, we would use all lowercase for an event - like `onclick`:

  ```javascript
  <button onclick="doSomething()">
    Do Something
  </button>
  ```

  - But, in a React element, we use camelCase instead of lowercase - note that it's `onClick` (with a capital "C") and not `onclick`. Note that in the JSX code above we are using the *curly braces* to embed JavaScript inside our JSX.  In this case, we are making a call to a function named `doSomething`.

  ```javascript
  <button onClick={doSomething}>
    Do Something
  </button>
  ```

  - More docs [here](https://reactjs.org/docs/dom-elements.html).

### Event Handlers

- In the code above you see how we would call an event handler named `doSomething()`.
- But where would we define that event handler?
- In ES6 class-based components, we can simply add the event handler `doSomething()` as ***a method on the class***.
- Here is what that would look like:
```javascript
import React from "react"

const App = () => {
  // this function is declared within the context (or closure)
  // of the component instance
  const doSomething = () => {
    alert('hello world!')
  }

  return (
    <button onClick={doSomething}>Testing</button>
  )
}
```
- In the example above, we have a simple button called "Testing".
- When clicked, the button will call `doSomething` and alert "hello world" to the page.

### Must return a single node

- An important fact for React novices to know is that the return value of a given component is only allowed to return a single node.
- What does that mean?  Well, take this code for example:

```javascript
import React from "react"

const App = () => {
  return (
    <h1>Hello from App</h1>
    <h2>Some text</h2>
  )
}
```

- If we run the code above, we would get an error saying `Syntax error: Adjacent JSX elements must be wrapped in an enclosing tag.`
- Why do we get that error?  Well, remember that our JSX code gets compiled into a JavaScript call to the `React.createElement` function whenever the compiler sees a JSX element.
- So, the code above actually gets compiled to this (roughly):

```javascript
return React.createElement('h1', null, 'Hello from App') React.createElement('h2', null, 'Some text')
```

- Do you see the problem with the code above?  The problem is that it's the equivalent of trying to return a function followed by another function.  JavaScript doesn't like that, hence the error message.
- So, how do we solve this problem? There's two paths:

#### Wrap in another node

- We can wrap the nodes with a parent node, such as a `div` tag.

```javascript
import React from "react"

const App = () => {
  return (
    <div>
      <h1>Hello from App</h1>
      <h2>Some text </h2>
    </div>
  )
}
```

- By adding a div tag to wrap around the h1 and h2 tags, we solve the problem.
- Note also that we added parentheses after `return`.  With the parentheses, we can have whatever whitespace we want. It's common styling in the React community to wrap multi-line JSX in parentheses.

#### Use React Fragment

Many web developers were pitching fits about all these unnecessary `div`s floating around in their HTML. So, React created a `Fragment` element, which allows you to logically return a collection of nodes, but the `Fragment` isn't actually sent to the DOM.

```javascript
import React from "react"

const App = () => (
  <React.Fragment>
    <h1>Hello from App</h1>
    <h2>Some text </h2>
  </React.Fragment>
)
```

That syntax was a bit cumbersome, so they offer a shorthand as well:

```javascript
import React from "react"

const App = () => (
  <>
    <h1>Hello from App</h1>
    <h2>Some text </h2>
  </>
)
```

Neat, eh? Fragments require special support in Babel, but if you use `create-react-app` starter kit that we'll use in our labs, you'll be just fine.

### React - props

- In order to pass data into our components we can use what's called ***props*** in React.
- Props look just like attributes for HTML elements.
- Here's an example of how to pass a prop called `text` to a component called **ChildComponent**:

```javascript
<ChildComponent text="An example of a prop"/>
```

- We can define the name of the prop to be whatever we want - so `text` could just as well be called `mySpecialText`.
- Suppose we are rendering `Greeter` from a parent component - `ParentComponent`:

```javascript
const WorldGreeter = () => (
  <Greeter text="World" />
)
```
- Then, `Greeter` would just use `this.props.text` in order to access the props data passed from `WorldGreeter`:
```javascript
const Greeter = (props) => (
  <h1>Hello {props.text}</h1>
)
```

- Note that we are using curly braces in order to retrieve the `props` inside `Greeter`
- Props are an object passed as the first (and only) argument to a function component
- And, to reiterate, the props data is flowing down from `WorldGreeter` - which passes in the text "World" - to `Greeter`.
- `Greeter` will then simply return an `h1` tag that says `Hello World`.
- Note that `props` is essentially just an object that looks like this:

```javascript
{ text: 'World' }
```

- Props are a way of passing data from parent components to child components.
- Props are how different components communicate with each other.
- With props, data always moves from top to bottom - parent to child component.
- A very important fact to remember: ***props are read-only***.
- You can destructure the props object for easier reference:

```javascript
const Greeter = ({ text }) => (
  <h1>Hello {text}</h1>
)
```

***

### React - Creating a Component

- In all of our examples thus far, we used ES6 based classes to create React components.
- We wanted to be consistent with our examples to minimize confusion.
- But, there are actually ***three*** different ways to create components in React.  It is very important to be aware of all the different approaches.
- One way is to use `React.createClass()`.  This was added to React ***before*** ES6 classes were introduced:

```jsx
const MyComponent = React.createClass({
  render() {
    return <div className={this.props.className}/>;
  }
});
```

- Note that `React.createClass()` has been [deprecated](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html), in favor of ES6 Classes, but you might still see it from time to time.

- Another way to create a component is to use ES6 Classes - which you have seen occasionally:
```jsx
class MyComponent extends React.Component {
  render() {
    return <div className={this.props.className}/>;
  }
}
```

- The newest way is to use a ***function component***, which looks like this:
```jsx
const MyComponent = (props) => (
  <div className={props.className}/>
);
```
- In a function component, the "this" keyword can't be used because there is no class (and hence no constructor either).

***

### React State

- If the only thing React could have were props, everything would be static. We need "state" to represent dynamic behavior and data in response to user interactions.
- Before React hooks were introduced, only ***class*** based components could use state. 
- State in class components is a plain JavaScript object that is used to *record* user events. It looks like this:

```javascript
class Counter extends React.Component {
  state = {
    clicked: 0,
  }

  incrementClicks = () => {
    this.setState({ clicked: this.state.clicked + 1 })
  }

  render() {
    const { clicked } = this.props
    return (
      <button onClick={this.incrementClicks}>
        Clicked {clicked} times
      </button>
    )
  }
}
```

- Each class has its own state object. They are not shared across components.
- Never mutate this.state directly or set it to a different object. It should only be updated using `this.setState()`
- This is per the [React docs](https://facebook.github.io/react/docs/react-component.html#state)
- When a component's state is changed, the component along with all of it's child components will be immediately re-rendered.
- This means that the render() method will be called for both that component and all of it's child components.

#### Hooked on State

- We now have "React Hooks", introduced in v16.8, which allow us to have state in function components. Here's the above code written using React hooks:

```javascript
import React, { useState } from 'react'

const Counter = () => {
  const [clicked, setCount] = useState(0)
  const incrementClicks = () => {
    setCount(clicked + 1)
  }
  return (
    <button onClick={incrementClicks}>
      Clicked {clicked} times
    </button>
  )
}
```

- Notice the use of `useState`. It is a function that takes the initial state. It returns a tuple with two values: the current state, and a function that can be used to update the state. Note that `clicked` and `setCount` are not special names -- they could be anything -- but generally the pattern people follow is `[stateName, setStateName]`.
- When the second position value (called `setCount` in the code above) is invoked with a new value, the whole element will re-render, i.e. re-run the function in its entirety.
- `useState` is smart enough to only initialize on the first render, i.e. when the component mounts to the DOM. In subsequent re-runs, it does not continuously reset the `clicked` state back to `0`, otherwise that'd be pretty useless state indeed!
- Because I Said So™️, `useState` has two strict rules:
  - They can only be used at the top-level; they can't be used in loops, conditions, or nested functions
  - They can only be used in React component functions
- You can have multiple pieces of state in the same component:

```javascript
const App = () => {
  // Declare multiple state variables!
  const [age, setAge] = useState(42)
  const [fruit, setFruit] = useState('banana')
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }])
  // ...
}
```
- You *could* use a POJO (Plain Ol' JavaScript Object) to represent all this state in a single place, but you run into challenges around transforming the object immutable that can be dodged by writing out each state handler separately.
- The [React guide on state hooks](https://reactjs.org/docs/hooks-state.html) is thorough and also an excellent read

### React - state versus props

- Props are passed into our component and are readonly.
- *State* are values that can change over time
- But, unlike props, *state* is meant to be managed and ***updated*** by the component.

### Presentational and Container components

- Some people make a distinction between **presentational** components versus **container** components. The former is just concerned with presentation (typically stateless), the latter is concerned with how things work. It was born out of [this post](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) by Dan Abramov (one of the most influential figures in the React community).
- `<opinion>`The concern over separating presentational from container components is passé and there's better ways to do it now. Physically separating presentational and container components causes a proliferation of inter-connected files that increase complexity and confusion around locating the right bit of code to fix. Amusingly, even the original blog post that gave birth to all the fervor now includes a foreword stating that he has evolved his opinions about this pattern, and it's not worth doing.`</opinion>`

### Rendering a list of components

- We use the `Array#map` method ([docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)) a ton in React. Remember that `map` takes an array and returns a *transformed array* based on the callback function it is given. For example:

```javascript
const numbers = [2, 4, 6]
const tripled = numbers.map(n => n * 3) // [6, 12, 18]
```

- We can use `map` to return JSX as well! For example, if you wanted to wrap those in `li` elements, and then use that in an unordered list:

```javascript
const numbers = [2, 4, 6]
const tripled = numbers.map(n => (<li>{n}</li>))
const listOfTripledNumbers = (
  <ul>
    {tripled}
  </ul>
)
```

- More commonly, we'd be reading off `props` or `state` rather than some hard-coded values for the purposes of iteration (e.g. display all the `todos`)
- So, we can just embed the name of the array in curly braces, and React will take care of rendering the elements of that array to the page as shown above.*
  - * NOTE: there's another thing about `key` that you'll learn in one of the labs to complete your understanding about React and lists, so hold tight.
- You can also read more about this [here](https://facebook.github.io/react/docs/lists-and-keys.html)
