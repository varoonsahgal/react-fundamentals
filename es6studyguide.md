Here we've compiled a list of things in core Javascript and ES6 we think you should know before you start the React and Redux Fundamentals course.  Please read each section carefully - especially topics that may be new to you.

# Table of Contents
1. [Objects in JS - Foundational knowledge](#objects-in-javascript)
2. [The map() helper method](#the-map-helper-method)
3. [ES6 Arrow functions](#es6---fat-arrow-functions)
4. [ES6 Enhanced object literals](#es6---enhanced-object-literals)
5. [ES6 Classes](#es6---classes)
6. [ES6 Destructuring](#es6---destructuring)
7. [ES6 Export / Import](#es6---export--import)
8. [ES6 Spread and Rest Operator](#rest-and-spread-operator)
8. [ES6 const and let](#const-and-let)



### Objects in Javascript

- There are two different ways to create an object in Javascript.
- We could do something like this:

```javascript
const human = new Object();
```
### Object Literals

- But, the **preferred** way to create objects in Javascript is with some shorthand called an ***object literal***.  This code creates an empty object - note the use of the *curly braces*:

```javascript
const human = {};
```

- In order to initialize the object with some name/value pairs, we could do this:

```javascript
const human = {
      firstname: 'Andy', 
      lastname: 'Smith'
};
```

### Accessing object properties - dot operator

- To access the properties of an object we can use the dot operator, like this:

```javascript
human.firstname;
```

- We can also add ***new*** properties to objects with the dot operator.  Using the human object we defined earlier, we can do something like this:

```javascript
human.middlename = 'Jerry';
```

### Nested objects

- Objects can contain other objects.  Here's an example of how to do this - note that address is what's called a *nested object*:

```javascript
const human = {
      firstname: 'Andy', 
      lastname: 'Smith', 
      address: {
      	street: '123 Cedar Blvd',
        city: 'Sunnyvale',
        state: 'California'
      }
};
```

Now, the address property of the human object is an object as well.  To access the street property of the address object we can do something like this:

```javascript
console.log(human.address.street); //logs "123 Cedar Blvd" to the console
```

### Objects - copied by reference

- Suppose we have a simple object like this:

```javascript
const simpleObject = {
  someName: 'someValue'
};

```
- Let's say we want to create a copy of this object, like this:

```javascript
let simpleObjectCopy = simpleObject;
```

- It's important to know that now BOTH `simpleObjectCopy` and `simpleObject` reference the ***same*** object.

- So, if we modify the `someName` property in `simpleObjectCopy`, that changes the property in `simpleObject` as well.  So, for example:

```javascript
simpleObjectCopy.someName = "Name change";
```
-If we now access the `someName` property from `simpleObject`, it would output "Name change":

```javascript
console.log(simpleObject.someName); //this will log "Name change"
```

- This is because both of those objects - `simpleObject` and `simpleObjectCopy` - reference, or point to, the same object in memory.

### Using Object.assign

- If we want to creat an independent copy of an object, then we can use **Object.assign**.  Read [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) for more about that.

***

### The map() Helper Method
- Map is the most widely used array helper method.
- To understand map, let's look at a basic for loop and then refactor to use map:
```javascript
var numbers = [2, 4, 6];
var tripledNumbers = [];

for (var i=0; i < numbers.length; i++)
{
  tripledNumbers.push(numbers[i] * 3);
}

```

- Note in the code above that we use a separate array to store the numbers after they have been tripled.  This is because we want to avoid mutating data whenever possible (in this case the existing numbers array) to prevent unwanted side effects.
- Now, let's do the same exact thing we did above, but this time using a `map` helper:
```javascript
let numbers = [2, 4, 6];

let tripled = numbers.map(function(number){
  return number * 3;
})

```
- In the code above, we can see that map takes an anonymous function (a function with no name) as an argument.
- Each number in the `numbers` array is being passed into the anonymous function.
- Then that number is multiplied by 3 and placed in the array named `tripled`.
- So, the very first element in the numbers array - `2` - is passed into the anonymous function, then multiplied by 3, then stored in the `tripled` array.  Then the same thing for the next element - `4`- and finally for the last one, `6`.
- Note that we can name the argument to the anonymous function whatever we want.  In this case we called it `number`, but we could just as well have given it the name `x`, so this would work just fine too:
```javascript
let numbers = [2, 4, 6];

let tripled = numbers.map(function(x){
  return x * 3;
})
```
- But, the name `number` is appropriate in this case since the anonymous function is called for each number in the `numbers` array.
- Be aware that one common mistake is to forget the `return` keyword when using `map`.

***

### ES6 - Fat Arrow functions
- In ES5, a simple function would look like this:

```javascript
const multiply = function(x, y) {

  return x * y;

}
```

- In ES6, we can instead use what's called a ***fat arrow function***
- With the fat arrow function, we don't have to type out the word "function".
- So, by removing the `function` keyword and adding a fat arrow - the "=>" - after the arguments, we get this:

```javascript
const multiply =  (x, y) => {

  return x * y;

}
```
- Arrow functions are one of the most popular features of ES6. 
- But, there are other interesting features of the arrow function.
- One interesting thing is that if we only have one line in our function, we can remove the curly braces and the `return` keyword, like so:

```javascript
const multiply =  (x, y) => x * y;
```
- This is an alternative syntax we can use whenever we have a single Javascript expression.
- This obviously trims down the amount of code we need to write.
- This is called an `implicit return` since there is no explicit `return`.
- Note that this doesn't mean we could do something like this by adding the curly braces:
```javascript
const multiply =  (x, y) => {
  x * y;
}
```
- The code above won't work, we would need to add the `return` keyword like so:
```javascript
const multiply =  (x, y) => {
  return x * y;
}
```

### Fat arrow functions - single argument
- Let's say our fat arrow function has a single argument, like this:
```javascript
const triple =  (x) => {
  return x*3;
}
```
- If there is just a single argument to the function, then we can also remove the parentheses around that argument like this:
```javascript
const triple =  x => {
  return x*3;
}
```
- But, we can't do this if there are multiple arguments, so this will ***not*** work:
```javascript
const multiply =  x, y => {
  return x * y;
}
```

### Fat arrow functions - no argument
- If a function has *no* arguments, then we would still need to provide empty parentheses like this:
```javascript
const doSomething = () => {
  return 20;
}
```

### Fat arrow functions - lexical this
- Other than the more compact syntax, another benefit of fat arrow functions is the fact that they use what's called the lexical this.
- This feature saves us from using `bind` to retain scope, which is what we would have done in ES5.

***

### ES6 - Enhanced object literals
- Given an object like this - where both the key and the value have ***identical*** names: 
```javascript
const someObj = {
  someProperty: someProperty
}
```

- Because key and value have identical names, we can condense it to this instead:
```javascript
const someObj = {
  someProperty
}
```
- Note that this doesn't change anything about the behavior of the object, it's simply less text to type.
- Let's say we have a key value pair where the value is a function, like this:
```javascript
const someObj = {
  someFunction: function() {
    alert("Hello!!");
  }
}
```
- With enhanced object literals, we can get rid of the function keyword and the colon, like this:
```javascript
const someObj = {
  someFunction() {
    alert("Hello!!");
  }
}
```
- Again, note that no underlying behavior is changed by using this syntax - it's just less to type.

***
### ES6 - Classes
- Javascript doesn't really have true object inheritance, it's always had what is called *prototypal* inheritance.
- With ES6 the ***class*** keyword was introduced.
- Under the hood of ES 6 ***classes***, *prototypal* inheritance is still being used, but ***classes*** are nice syntactical sugar that abstract away some of the complexity of prototypal inheritance.
- Here's an example of what an empty class in ES6 would look like:
```javascript
class Dog {

}
```

- To instantiate the class we would do this:
```javascript
const dog = new Dog();
```
- To add a method inside a class we use the enhanced object literal syntax:
```javascript
class Dog {

  someMethod(){
  
  }
}
```
- Note that we do NOT use the `function` keyword when declaring a method, so if we do something like this we would get an error:
```javascript
class Dog {
  //gives an "Unexpected token" error
  function someMethod(){
  
  }
}
```
- Running the code above would give us an `Unexpected token` error.

- We can also add a constructor to our class like this:
```javascript
class Dog {
  
  constructor(someProperty) {
    this.someProperty = someProperty;
  }
  
  someMethod() {
  
  }
}
```
- Note that a constructor should just be called `constructor` in Javascript (not the same name as the class as you might be used to seeing in Java).
- The constructor should be used for some initialization code.
- Note the syntax for initialization inside the constructor - `this.someProperty = someProperty;`.
- We can also implement inheritance in Javascript with the `extends` keyword.  Suppose we have another class called `Animal`:
```javascript
class Animal {
  
  constructor(someProperty) {
    this.someProperty = someProperty;
  }
  
  animalMethod() {
  
  }
}
```
- We can have the Dog class extend from this Animal class like this - note the use of `extends Animal`:
```javascript
class Dog extends Animal {
  
  constructor(someProperty) {
    this.someProperty = someProperty;
  }
  
  someMethod() {
  
  }
}
```
- But if we try to compile the Dog class above, we will get an error saying "Syntax error: missing super() call in constructor".
- So, we have to add a call to super().  Like this:
```javascript
class Dog extends Animal {
  
  constructor() {
    super();
  }
  
  someMethod() {
  
  }
}
```
- The call to `super()` in `Dog` will call the constructor in the `Animal` class.
- As a rule, ES6 class constructors MUST call `super` if they are subclasses.  
- Another very important fact: We can not use the `this` keyword in a child class (in this case, the `Animal` class) constructor until `super` is called.

***
### ES6 - Destructuring
- One of the most commonly used features of ES6 is destructuring.
- Let's start with an example to understand it further.
- Suppose we have a simple object in ES5 called `human` like this (note we are also using `var` to be consistent with ES5):

```javascript
var human = {
      firstname: 'Andy', 
      lastname: 'Smith'
};
```
- Let's say we want to reference the `firstname` and `lastname` from this object later.
- We could do something like this:
```javascript
//ES5 code
var firstname = human.firstname;
var lastname = human.lastname;
```
- But with destructuring in ES6, we can make assigning properties off of an object a bit easier.
- This is what the ES6 code would look like using ***destructuring***:
```javascript
//ES6 code

const human = {
      firstname: 'Andy', 
      lastname: 'Smith'
};

const {firstname} = human; //equivalent to var firstname = human.firstname;
const {lastname} = human; //equivalent to var lastname = human.lastname;
```
- The ES6 code above is equivalent to the ES5 code also shown above.
- Don't let the use of curly braces in the ES6 destructuring syntax confuse you - we are **not** creating an object here.
- The first line in the code above is basically saying "I want to create a new variable called `firstname` and I want it to be set equal to `human.firstname`"
- So, the destructuring code is both creating a new variable and setting it equal to a property pulled off of the human object.
- We could even combine the two lines above into one:
```javascript
//ES6 code
const {firstname, lastname} = human; 
```
- This will declare two new variables `firstname` and `lastname`, which will be set equal to the respective properties on the `human` object.
- It's important to note that the variable name has to be the same as the property name on the object.
- So, something like this would not work:
```javascript
const {name, surname} = human; 
```
### Destructuring objects with function arguments
- We can also use destructuring to pull properties from objects that are passed to functions:
```javascript
const human = {
      firstname: 'Andy', 
      lastname: 'Smith'
};

function humanFunc({firstname, lastname}) {

  return `${firstname}.${lastname}`;

}

humanFunc(human);  //call the function, pass in human object

```

### Destructuring with arrays
- We can use destructuring with arrays as well.
- Suppose we have this array:
```javascript
const animals = [
  'dog',
  'cat',
  'mouse'
];
```
- We can get the first element in the array like this:

```javascript
const [animal] = animals;  //sets a variable called animal to 'dog'
```
- The line above declares a variable named `animal` and sets it equal to the first element in the `animals` array.
- Note that order matters when destructuring arrays.
- If we do something like this:

```javascript
const [animal1, animal2, animal3] = animals; 
```
- The code above would set the variable `animal1` to `dog`, `animal2` to `cat` and so on.
- The key here is that it's grabbing the values in the order in which they appear in the array.
- Also note the use of square brackets instead of curly braces when destructuring arrays versus destructuring objects.
- An interesting fact: all arrays have a `length` **property** - so to destructure the `length` property from an array, we would actually use curly braces:
```javascript
const {length} = animals;  //sets length to 3
```
- Remember that to destructure *properties*, we use curly braces, but for array elements use the square brackets.

### Destructuring arrays with function arguments
- Just like we used destructuring to pull properties from objects that are passed to functions, we can do the same for arrays.
- Take a look at this code for an example of destructuring with arrays as function arguments:

```javascript
const humans = ['Jack', 'Jill'];  //our array

//Array destructuring w/ function arguments in action:
//here guy will be assigned 'Jack' and girl assigned 'Jill'
function humanFunc([guy, girl]) {

  return `${guy}.${girl}`;

}

humanFunc(humans);  //call the function, pass in humans array

```
***

### ES6 - Export / Import

#### Export
- So after you finish your file, how do you make the content available to other files in your component or app? Export!
- There are two types of exports, `named` and `default`.
  - Named exports:
    - `export const myFunction = () => {}` // exports a function declared earlier
    - `export const foo = Math.sqrt(2);` // exports a constant
  - Default exports (only one per script):
    - `export default function() {}` or `export default class {}` or `const foo = () => {}; export default foo;`
- After your variables, functions, class, etc are exported, they can easily be imported in other files with the `import` command (synonymous with the require command in previous versions of javascript).

#### Import
- Once you've properly setup your export, you now need to import it.
- Two of the primary ways you will be importing are:
  - Import one or multiple members of a module. This inserts both foo and bar into the current scope.
    - `import {foo, bar} from 'my-module';` // if it's another file in the same directory you'd need to set the path. ex. `import { MyComponent } from './MyComponent';`
  - The simplest version directly imports the default. Default needs to be set during the export for this to work.
    - `import myDefault from 'my-module';`

***

### Rest and Spread Operator

* ES6 introduces a new operator - the “...”.  
* Depending on the context, the “...” is known as either the **rest** operator or the **spread** operator.  
* To reiterate - the "..." is known as either a spread or a rest operator depending on the context of how it's used.  
* Let’s cover it’s usage as the *spread* operator first.

### The Spread operator
* Imagine we have two arrays:

```javascript
const cars  = [‘Mazda’, ‘Toyota’]
const cars1 = [‘Ford’, ‘Tesla’]
```
* Now, if we want to join those two arrays into one, we could do something like this:

```javascript
cars.concat(cart1);
```
* That would give us:

```javascript
[‘Mazda’, ‘Toyota’, ‘Ford’, ‘Tesla’]
```

* But, we could also use the "..." as a spread operator instead, like this:

```javascript
[...cars, ...cars1]
```

* The end result would be to have the two arrays merged as one - which is what we got using concat as well.  

* So, using the spread operator would give us the same result as the concat:

```javascript
[‘Mazda’, ‘Toyota’, ‘Ford’, ‘Tesla’]
```

* So in this context, the “...” is what’s known as the spread operator, which will basically pull all of the elements from inside the array which the operator is applied against.  
* Another way of thinking about it is that the spread operator expands an array into just its elements. 
* If we look at [...cars, ...cars1], we could say that it’s saying first create an array then pull all of the elements out of both the cars and cars1 arrays, and finally create a new array from all of those elements.

* What if we do something like this - where we don’t use the spread operator in front of the cars1 array:

```javascript
[...cars, cars1]
```

* Then we would end up with this:

```javascript
[‘Mazda’, ‘Toyota’, [‘Ford’, ‘Tesla’]]
```

* Note that cars1 is still in its original array form, versus having the elements pulled out. 

* Why use the spread operator when we can use the concat operator instead?

* Well, suppose we wanted to add an element to the front of the array.  Then, we could simply do this:

```javascript
[‘Hyundai’, ...cars, ...cars1]
```

* The spread operator gives us quite a bit of flexibility.  


### The Rest Operator

* The rest operator is good when you would like to write a function that accepts arguments, but you’re not sure how many arguments you want to pass.  An example would help clarify.

* Suppose we have the following function:

```javascript
function multiplyNumbers([x, y, z])
{
  return x*y*z;
}
```

* The function above is fine if we want to just return the product of only 3 numbers.  
* But, what if we want to pass in more numbers?  
* We don’t want to change the argument list each time.  
* So, here we could use the **rest operator** instead:

```javascript
function multiplyNumbers(...numbers)
{
  return numbers.reduce((product, number) =>
  {
    return product * number;
  }, 1);
}
```

* The rest operator will take whatever arguments are passed to the function and then create an array out of them called numbers.

* So, if we call the function like this:

```javascript
multiplyNumbers(1, 4, 3, 8)
```

* Then, inside the multiplyNumbers function, numbers will be an array that looks like [1, 4, 3, 8].  
* Similarly, we could call multiplyNumbers with a different number of arguments - say 6 instead of 4 arguments like this: 

```javascript
multiplyNumbers(1, 4, 3, 8, 22, 99)
```

* And, it would still work fine.  That’s the beauty of the rest operator.


### Summary - rest versus spread
* Per the MDN docs, in a way rest syntax is the opposite of spread syntax.
* This is because spread 'expands' an array into its elements, while rest collects multiple elements and 'condenses' them into a single element.
* So, spread expands (or spreads), and rest condenses.
* Rest is used for destructuring arrays and objects.
* Remember rest syntax looks exactly the same as spread syntax.

***

### Const and Let

* With **const** and **let** being introduced in ES6 as new ways to declare variables, **var** is generally considered deprecated by the Javascript community.  

### Why const and let?

* With const and let our intent with our code is clearer versus just using var.  If someone else were to read our code, it would also be much easier for them to understand it if they see the const and let identifiers:

### More notes on const

* There are two rules to be aware of with a variable declared with the keyword **const** 1.  It can not change through re-assignment, and 2. it can not be re-declared.  

* So, suppose we have a simple const variable declared like this:

```javascript
const x = 10;
```

If we try to set it to another value like this:

```javascript
const x = 7;
```

* Then, we would get an “Uncaught TypeError: Assignment to constant variable.”.  

* The const variable x would still hold the value of 10, because it can not be changed through re-assignment. 

* But, a potentially confusing thing here is that if we declare some const object like this:

```javascript
const y = { };
```

* Then, we could still add a property to the object like so - in this case the property name is “foo” with a value of “bar”:

```javascript
y.foo = ‘bar’;

console.log(y); //this would log {foo : 'bar'}
```


* So, this doesn’t throw an error - why is that?  
* Well, one other very important thing to know is that the variable y stores the address of where the object lives in memory, and not the object itself. 
* The object itself is stored at the address which is what the variable stores. 
* That address in memory is also commonly referred to as a **reference**.  
* So, when we declare y to be const we are actually saying that the reference (the address in memory) is constant - not the object itself.  
* That’s an important distinction to understand.  So, when we do this:

```javascript
const y = { };

y.foo = ‘bar’;
```

* We are changing the object itself and that is fine because that is NOT what is constant.  But, when we do this :

```javascript
const y = { };

y = {foo: “bar” };
```
* Then we will get an “Uncaught TypeError: Assignment to constant variable.”, because we are trying to have “y” replace the constant reference to an object in memory that y is currently holding.  
* Because it’s constant, we can’t have it point to a different place in memory, which is what we are doing when we set y to an entirely new object. 
* But we can modify the existing object since that is not what is constant.  
* Because arrays in Javascript are objects, this behavior (and the discussion above) applies to arrays declared with const as well.  

