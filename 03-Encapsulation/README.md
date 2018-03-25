# Encapsulation and Information Hiding

> Keep things out of the global namespace, prevent or limit access to state/behavior not meant to be accessed from the outside

## How Do We 'Encapsulate'

* Basically, put related state/behavior inside an object -- Voila! It has been encapsulated...

Only the object's name will be in the global namespace. No state or behavior will be modified or called unless the object's name is directly invoked. This helps protect an object's state and behavior from modification from truly accidental means.

## Why and How is Information Hidden

* The 'Why': an object's public interface should be as simple as warranted, and its private state and methods may be necessarily complex. If you've just created a perfect object with its intended public interface, you'd want to protect the internal complexity (of which the outside need not concern itself with) from erroneous or innappropriate use/modification.

* The 'How': via convention, 'Closures', or 'IIFE'

## Convention

* prefix a property name with an underscore

```js
const cat = {
  _firstName: 'Mister',
  _lastName: 'Pajamas',
  _myMethod() {
    // ... other stuff
  },
  getName() {
    return `${this._firstName} ${this._lastName}`;
  }
};
```

See how ```_firstName``` and ```_lastName``` are prefixed with an underscore? By convention, this sends a message to other programmers not to interact with those properties directly. In this simple example, we can see that I've defined as my intended public interface ```cat.getName()``` that would return to the caller ```'Mister Pajamas'``` when called. I am sending the message, "No need to interact (a.k.a. risk accidental change) with ```_firstName``` or ```_lastName``` directly -- I've given you a method to call if you want that info"

**Pros**:

* This one is simple to implement, just prefix an underscore
* This is a common convention, and it's very easy to understand a programmer's intent
* This approach also has a positive side effect when debugging
* By trusting other programmer's judgement in adhering to your intentions, they can still access those properties if they deem it necessary

**Cons**:

* You have to trust other programmers judgement in adhering to your intentions -- they may, or may not exercise good judgement
* Since the underscore literally changes nothing about that property's accessibility in JavaScript, they are still wholly subject to erroneous or innappropriate use/modification.

## Closures

> Technical Definition [(from MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures): A closure is the combination of a function and the lexical environment within which that function was declared.

* In simpler words, functions create a new scope in JavaScript. This is important! We can put things inside a function's curly braces to **actually** keep the outside from accessing it. Think of the movie *Inception*... we're about to put a function inside a function!!

```js
// Functions create a new scope
function myFunction() {
  let myVariable = 'I cannot be accessed from outside this function!';
}
// BOOM -- can't access that variable, and one cannot modify what one cannot access :-P
console.log(myVariable); // -> undefined

// But what if we need to both protect AND access that variable?
function anotherFunction() {
  let anotherVariable = "I cannot be accessed directly!";
  // Simply put a function inside a function which returns the variable you want access)... mind = blown
  function accessVariable() {
    return anotherVariable;
  }
  // Here's where we call the inner function that returns the variable
  accessVariable();
}
// BOOM -- still hidden from the outside
console.log(anotherVariable); // -> undefined
// But this totally works!
console.log(anotherFunction()); // -> "I cannot be accessed directly!"
```

Okay, so that is pretty neat, huh! But we can also use closures by initially returning anonymous functions like this:

```js
function outerFunction() {
  let innerVariable = "I am protected inside a function's scope!";
  return function() {
    return innerVariable;
  }
}
// Yup... it's still protected inside the function's scope
console.log(innerVariable); // -> undefined
// This variable will store the return value of 'outerFunction()' -- which happens to be the inner anonymous function
let returnedFunction = outerFunction();
// And now if we call returnedFunction()...
console.log(returnedFunction()); // -> "I am protected inside a function's scope!"
```

```returnedFunction()```'s lexical environment is inside of ```outerFunction()``` -- which means it totally has access to ```innerVariable``` even though the variable wasn't expressly returned by ```outerFunction()```!! And of course the benefit to all this rigamarole is that you've now declared a variable whose value can be accessed, but not modified, from the outside.

## IIFE (Immediately Invoked Function Expression, a.k.a Self-Executing Anonymous Function)