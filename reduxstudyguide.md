---
title: Redux Quick Start Guide
sidebar: auto
permalink: /guides/redux-quickstart/
toc: false
type: page
---

> This guide was built to help other Walmart engineers learn the ins and outs of Redux. This guide references the folder structure found in Electrode apps but the concepts are the same as in any Redux app.

## Redux - Create a Redux Reducer
- Let's say we want to implement a simple shopping cart. How can we do it in Redux?
- First, we need to keep the cart data somewhere. This is what the Redux `Store` and `Reducer` are for.
- The `store` holds the whole state tree of your application.
- Each `reducer` represents the first branches on that state tree.
- Typically there are separate files for each reducer and the names of reducers will correspond to their names on our store. For example we might have reducers named: `products.js`, `users.js`, `cart.js`, etc.
- This should be found in the folder `src/client/reducers`.
- Each reducer is effectively a mini-database on the front-end, acting as a single source of truth. We could create `src/client/reducers/cart.jsx`:

```jsx
const initialState = [];

const cartReducer = (state = initialState, action) => {
  switch (action.type) {

    default:
      return state;
  }
};

export default cartReducer;
```
> Note that it is initialized with an empty array since users start with no products.

It is not enough to create a reducer. It then needs to be combined with other reducers and added to the store. To do this we will have to edit our `src/client/reducers/index.js` file by importing our `cart` reducer and adding cart to `combineReducers`.

```jsx
import cart from "./cart";

/*
  Other code
*/

export default combineReducers({
  cart // add this line
  products, // other reducers
  user,
});
```

Next the combined reducer is imported when creating our store in `src/client/app.jsx` (which should already be implemented in Electrode).

> If you want to initialize state (ex. an item already added to the cart) in an Electrode app, you need to do so in `src/server/views/index-view.jsx`. This pattern is different than typical Redux because Electrode is optimized for server-side rendering.

## Redux - mapStateToProps
- Suppose we have a cart icon at the top of every page that shows us the *quantity*
of items currently in our cart.
- When products are added to the cart, how would we update the quantity being displayed in that cart icon?
- From our React component, we would need to "grab" a piece of the current state from Redux. We can add a function called mapStateToProps to our component for this:

```javascript
const mapStateToProps = (state) => {
  const { cart } = state;
  return { counter: cart.length };
};
```
- mapStateToProps does exactly what its name suggests - it **maps the Redux state to props in React**. Think of it like **reading** the mini-database.
- Notice how mapStateToProps just returns an object with the key `counter` mapped to `state.length`
- Because our cart store in Redux is really just an array of products, we can just use `state.length` to get the current size of the cart.
- Then, when we want to grab the current quantity of the cart, we can do so using props in React:

```javascript
// Within your component
this.props.counter // this.props.counter is a React binding to the Redux store
```
 - This is what's really cool about Redux: whenever our Redux store is updated, the counter (used in a React component) will ***automatically*** update as well to reflect the changes in state.
- But wait! Before we can use mapStateToProps it has to get passed to the `connect` function.

## Redux - connect()
- Inside `connect` is where the magic happens - where Redux will create a binding between the React component and the Redux state.
- The first argument it takes is `mapStateToProps` - although it's not required, which is why we could just pass in `null`. The second argument is `mapDispatchToProps` which we will talk about shortly. It can also be `null`.
- Think of `mapStateToProps` as the read in a database and `mapDispatchToProps` as the write.
- `connect` returns a bound function which you then call your component with.
- The following would be added to your `component`.

```javascript
import { connect } from "react-redux";

/*
  YourComponentName code
  New props available: this.props.counter
*/

// added from before
const mapStateToProps = (state) => {
  const { cart } = state;
  return { counter: cart.length };
};

// Typically this is put at the end of your component file.
export default connect(mapStateToProps)(YourComponentName);
```

> Notice that mapStateToProps receives one argument, state. This is the state tree for the entire store. You can choose which pieces of the state you want to use for your component. In this example we are only using `cart`. But you could just as easily use `products` or `users`.

## Redux - Actions
- Our cart/Redux store is currently empty.  How do we add products to it in Redux?
- Information gets sent to the Redux store in the form of ***actions***. Actions are handled by reducers in the store to determine how to change the state.
- ***Actions are Javascript objects*** that must have a *type* property - other than the *type* property, you can put whatever payload you want inside an action object.
- So, we could put in product data that we want to add to our cart.  Here's an example of an action:

```javascript
{ type: 'ADD_TO_CART', product  }
```

> This code snippet demonstrates ES6 "object shorthand" syntax; when the object key and the variable are the same name, you can write it once. The above can be more verbosely expressed as: 
>
> `{ type: 'ADD_TO_CART', product: product }`

## Redux - Action Creators

- Writing these actions out by hand each time leads to redundant code. DRY them up by making "action creators"
- Action creators simply return an object that is the action object

```javascript
const addToCart = (product) => ({ type: 'ADD_TO_CART', product })
const hideCart = () => ({ type: 'HIDE_CART' })
const showCart = () => ({ type: 'SHOW_CART' })
```

## Redux - Using dispatch()
- To send *actions* to the Redux store we use the `dispatch` method:

```javascript
dispatch({ type: 'ADD_TO_CART', product  })
```

When you don't provide a second argument to `connect` (the `mapDispatchToProps`), you will receive `dispatch` as a prop.

```javascript
const Product = ({ product, dispatch }) => (
  <div>
    <span>{product.name}</span>
    <button onClick={() => { dispatch({ type: 'ADD_TO_CART', product }) }}>
      Add to Cart
    </button>
  </div>
)

const mapStateToProps = (state, ownProps) => ({
  product: state.products[ownProps.id]
})

export default connect(mapStateToProps)(Product)
```

Some developers do not like haven't direct access to `dispatch`. In that case, you can define a function called `mapDispatchToProps` wherein you can spell out which props will be passed down that have access to dispatch:

```javascript
const mapDispatchToProps = (dispatch) => ({
  addToCart(product) { return dispatch({ type: 'ADD_TO_CART', product  }) },
})
```
- `mapDispatchToProps` does exactly what it's name says  - it maps `dispatch` (a Redux store method) to a function (called `addToCart` in this example) that we can call from a React component through props.
- `mapDispatchToProps` returns a function which is passed 'up the chain' through connect to your component. Similarly to how `mapStateToProps` handles state, you can define what functions your component will need to invoke, and what actions those functions trigger. The keys in the object you return from `mapDispatchToProps` are available directly on `this.props` and can set as click events from within the JSX.
- When an action is attached to a function that is added to a component's props, that function can also be passed down to child components. Now we can just call addToCart directly from our component's props - for instance, when an "Add To Cart" button is clicked:

```javascript
<Button
onClick={() => this.props.addToCart(this.props.product)} >
 Add to Cart
</Button>
```

- But we also have to pass the `mapDispatchToProps` function to `connect`. All combined your component might look like this:

```javascript
import { connect } from "react-redux";

/*
  YourComponentName code
  New props available: this.props.counter and this.props.addToCart.
*/


const mapStateToProps = (state) => {
  const { cart } = state;
  return { counter: cart.length };
};

const mapDispatchToProps = (dispatch) => {
  return {
    addToCart: (product) => {
      return dispatch({ type: 'ADD_TO_CART', product: product  })
    }
  };
};

// Typically this is put at the end of your component file.
export default connect(mapStateToProps, mapDispatchToProps)(YourComponentName);
```

If you hate all this redundant typing, you're not alone. You can either a) pass the `dispatch` directly by not giving a `mapDispatchToProps` function, or use the "`mapDispatchToProps` shorthand"

- You pass in "action creators" as values on a MDTP *object* (not function)

```javascript
import { addToCart } from './actions'

const mapDispatchToProps = {
  addToCart,
}
```

## Redux - Change State with Reducers
- Thus far in our cart example, we've initialized the `cart` to an empty array and shown you what an `action` looks like.
- But, you might be wondering where is the code to actually add products to our cart array?
- And, when we call `this.props.addToCart` (which calls `dispatch` for us) where does the action data get sent to?
- Well, this is where ***reducers*** come into play.
- Remember this code?:

```javascript
const initialState = [];

const cartReducer = (state = initialState, action) => {
  switch (action.type) {
    default:
      return state;
  }
};

export default cartReducer;
```
- This is a reducer.  When an action object is **dispatched**, that object is sent to all the reducers.
- Reducers are just functions that take an action and the previous state, and then return a new state.
- The reducer function starts with `initialState`, and in subsequent invocations will be whatever `state` was returned previously from the reducer
- The reducer will handle that action if the `type` of the action is caught in the `switch(action.type)` statement.
- When there's no match on `type`, it falls through to the `default` which always returns the unchanged `state`
- The switch statement is meant to handle different types of actions - like `ADD_TO_CART`, `REMOVE_FROM_CART`, etc.
- We are just going through one action type - `ADD_TO_CART` - so let's handle that:

```javascript
const initialState = [];  // our cart data goes here, initially it's empty

const cartReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'ADD_TO_CART':
      const stateCopy = [...state]; // copy our current state to stateCopy

      // our store cart is just an array of items, so we can just push
      // any new items to it. We can mutate a copy, just not the original state
      stateCopy.push(action.product);
      return stateCopy;

    default:
      return state;
  }
};

export default cartReducer;
```

Expressed a little more tersely, you could write:

```javascript
    case 'ADD_TO_CART':
      return [...state, action.product];
```

- Our Redux state will now be updated every time a product is added to the cart.
- For a complete run down of reducers, check the Redux docs [here](http://redux.js.org/basics/Reducers.html).

## Redux - Constants

- Bothered by all those magic strings? `'ADD_TO_CART'`, `'REMOVE_FROM_CART'`, etc.?
- It's common to create a `constants.js` file and put those constants there, and use them in your action creators and reducers.
- You can also namespace the actions to the state slice to reduce likelihood of name collision with common action types: `cart/ADD_TO_CART`

## Redux - Immutability

- Immutability is a requirement of Redux because Redux represents your application state as "frozen object snapshots". With these discrete snapshots, you can save your state, or reverse state, and generally have more "accounting" for all state changes. This also makes debugging much easier.
- In the above, example, notice how we did not mutate the state that was passed into the reducer.
- Mutating your Redux state (`push/pop` on arrays, `obj[prop] = val` on objects) will lead to bugs, confusion, and sadness. **NEVER** mutate your Redux state
- There are many strategies for immutably transforming data.
  - `<opinion>`The [RamdaJS](https://ramdajs.com/) library provides the most expressive and powerful way to immutably transform your data. It also allows you to do functional programming in JavaScript.`</opinion>`
  - Other common libraries include [Lodash](https://lodash.com/) and [ImmutableJS](https://immutable-js.github.io/immutable-js/)   
