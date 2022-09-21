# ES6 Exercises Instructions - Please Read:

Here are the ES6 specific exercises that we like for you to complete as part of the React and Redux Fundamentals Course.  
We also link to the appropriate sections in the [ES6 study guide](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md) to help you complete the exercises below.

## JSFiddle -  Saving and testing your ES6 exercises answers

Please use [JSFiddle](https://jsfiddle.net) to complete each exercise - you actually do NOT need to create an account but you can if you want.


## Debugging with JSFiddle

You can test your JS code in the Fiddle by wrapping whatever output you want to see with a `console.log`.  Then you can run the JS code by clicking the run button in the top left of the page.


## Saving code with JSFiddle

Finally, when you are satisfied with your JS code for a given exercise, you can hit the save button at the top left.  This will generate a unique url that you can share with other people.  Notice that if you modify the code and hit save again it will generate another URL for you to use.



***

# Mandatory ES6 Exercises
1. [Arrow Functions Exercise](#arrow-functions-exercise)
2. [Destructuring Exercise 1a](#destructuring-exercise-1a)
3. [Destructuring Exercise 1b](#destructuring-and-map-exercise-1b)
4. [ES6 Classes Exercise 1a](#es6-classes-exercise-1a)
5. [ES6 Classes Exercise 1b](#es6-classes-exercise-1b)
6. [Rest Operator Exercise](#rest-operator-exercise)
8. [Rest Operator Exercise 2](#rest-operator-exercise-2)
7. [Spread Operator Exercise](#spread-operator-exercise)
7. [Spread Operator Exercise 2](#spread-operator-exercise-2)
7. [Spread Operator Exercise 3](#spread-operator-exercise-3)
7. [Spread Operator Exercise 4](#spread-operator-exercise-4)
7. [Spread Operator Exercise 5](#spread-operator-exercise-5)
7. [Spread Operator Exercise 6](#spread-operator-exercise-6)

***

## Arrow Functions Exercise
- Below we have a factorial function that clearly uses the 'function' keyword.  **Your challenge exercise is to refactor the factorial function below to use [ES6 fat arrow syntax](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md#es6---fat-arrow-functions)).**  Keep in mind that there isn't anything wrong with using the `function` keyword, but it does look better with the fat arrow syntax instead.
- **Your second challenge exercise:** Once you refactor to use fat arrow syntax, take it a step further and use just one expression in your factorial function body, so you can remove the curly braces and the `return` keyword.  Hint: use the ternary operator for this.
### Fat arrow function rules
- Keep in mind the rules for fat arrow functions:
```
- For functions with just one argument, the parentheses around the argument list are not required.
- For functions with just a single expression in the body, we can remove the curly
  braces and 'return' keyword.
```
**Factorial with "function" keyword:**
```javascript
const factorial = function (n) {
  if (n === 0) return 1

  return n * factorial(n - 1)
}
```
***
## Destructuring Exercise 1a
- Below we have some code that references `bio` *twice* inside the `isDeveloper` function.
```javascript
const bio = {
  title: 'Developer',
  department: 'GEC'
}

function isDeveloper(bio) {
  var title = bio.title
  var department = bio.department
  return title === 'Developer' && department === 'GEC'
}
```
- **Your challenge exercise is to refactor the code used to reference the `title` and `department` properties.  You should use [***destructuring***](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md#es6---destructuring).** Try to get the `isDeveloper` function down to a single line using an arrow function.
***
## Destructuring and Map() Exercise 1b
- Suppose we have an array of arrays representing someone's shopping cart on an e-commerce site.  For example, our array of arrays would look something like this:
```javascript
const cart = [
  [ 'Hersheys Bar', '1.00', '504' ],
  [ 'Almonds', '5.00', '321'],
  [ 'Lotion', '2.50', '287' ]
]
```
- Each array in our cart has the structure of `[item, price, sku]` - in that exact order. **Now, your challenge exercise is to convert this array of arrays into an array of *objects* instead.**  Each object inside the array should have the keys `item`, `price`, and `sku` - along with the associated values for each key.  The resulting array of objects should be assigned to a variable named `cartAsObjects`.
- Your data structure should have a setup like this:
```javascript
const cartAsObjects = [
  { item: 'Hersheys Bar', price: '1.00', sku: '504' }
  // ... and so on...
]
```
- So, to summarize, take the array of arrays (reproduced below) and convert it into an array of ***objects*** stored in an array called `cartAsObjects`.  You must use both [***array destructuring***](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md#destructuring-objects-with-function-arguments) and the [***map***](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md#the-map-helper-method) method.
```javascript
const cart = [
  [ 'Hersheys Bar', '1.00', '504' ],
  [ 'Almonds', '5.00', '321'],
  [ 'Lotion', '2.50', '287' ]
]
```
- Remember that to destructure arrays we must use the square brackets (the `[]`) instead of the curly braces (the `{}`).
***
## ES6 Classes Exercise 1a
- Create an [ES6 class](https://github.com/varoonsahgal/react-fundamentals/blob/main/es6studyguide.md#es6---classes) called `Homosapien`.  **Your challenge exercise is to do some basic initialization for instances of the Homosapien class inside the constructor.**  Here is what you need to do specifically:

```
- The constructor will accept a name and age.
- You need to assign those 'name' and 'age' properties to the instance.
- There should be a class method, `speak`, which `console.log`s the string "UNGH!"
- Create an instance of the class, check its properties, and invoke the `speak` method.
```
***
## ES6 Classes Exercise 1b
- Now that we have our Homosapien class, your challenge exercise is to create a subclass of the `Homosapien` class called `JavascriptDeveloper`.
- The requirements for the `JavascriptDeveloper` class are:

```
- The JavascriptDeveloper class should initialize instances with a `company` property of `'Salesforce'`
- The class should override the `speak` method to `console.log` the string `"JavaScript is the most popular programming language!"`
- Create an instance of the class, check its properties, and invoke the `speak` method.
```
***
## Rest Operator Exercise
- Refactor the function below to use the rest operator:
```javascript
function product(v, w, x, y, z) {
  var numbers = [v,w,x,y,z]

  return numbers.reduce(function(acc, number) {
    return acc * number
  }, 1)
}
```

***
## Rest Operator Exercise #2
- Refactor the function below to use only the rest operator:
```javascript
function unshift(array, x, y, z) {
  return [x, y, z].concat(array)
}
```
***
## Spread Operator Exercise
- Refactor the function below to use spread operator (arr1 and arr2 are arrays):
```javascript
function join(arr1, arr2) {
  return arr1.concat(arr2)
}
```

## Spread Operator Exercise #2
- Refactor the following code to use a spread operator to destructure `first` and `tail` in one line.

```javascript
const arr = [1, 2, 3, 4, 5]
const first = arr[0]
const tail = arr.slice(1)
```

## Spread Operator Exercise #3
- Refactor the function below to create a shallow copy of an array using the spread operator.

```javascript
function shallowCopyArray(arr) {
  const copy = []
  for (let i = 0; i < arr.length; i++) {
    copy.push(arr[i])
  }
  return copy
}

// Expected usage:

const arr = [1, 2, 3]
const copy = shallowCopyArray(arr)
arr === copy // false
copy[0] = 'yo'
console.log(arr) // [1, 2, 3]
console.log(copy) // ['yo', 2, 3]
```

## Spread Operator Exercise #4

- Refactor the function below to create a shallow copy of an object using the spread operator (technically ES8 but it's used everywhere)

```javascript
function shallowCopyObject(obj) {
  const copy = {}
  for (const prop in obj) {
    copy[prop] = obj[prop]
  }
  return copy
}

// Expected usage:

const obj = { name: 'Andrew' }
const copy = shallowCopyObject(obj)
obj === copy // false
copy.name = 'Billy'
console.log(obj) // { name: 'Andrew' }
console.log(copy) // { name: 'Billy' }
```

## Spread Operator Exercise #5

- Refactor the code below to declare `agelessPerson` with just one line:

```javascript
const person = { name: 'Andrew', age: 33, position: 'Software Developer' }
const agelessPerson = {}
for (const prop in person) {
  if (prop !== 'age') {
    agelessPerson[prop] = person[prop]
  }
}
```

## Spread Operator Exercise #6

- Refactor the function below to create a new object that is a merge of the two objects together using just one line:

```javascript
function merge(obj1, obj2) {
  const result = {}
  for (const prop in obj1) {
    result[prop] = obj1[prop]
  }
  for (const prop in obj2) {
    result[prop] = obj2[prop]
  }
  return result
}
```
