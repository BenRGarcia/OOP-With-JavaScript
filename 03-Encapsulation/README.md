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

* You have to trust other programmer's judgement in adhering to your intentions -- they may or may not exercise good judgement
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

BOOM! ```returnedFunction()```'s lexical environment is inside of ```outerFunction()``` -- which means it totally has access to ```innerVariable``` even though that variable wasn't expressly returned by ```outerFunction()```!! Wow!

The moral of the story is that the benefit to all this rigamarole is that you've now declared a variable whose value can be accessed, but not modified, from the outside.

So with this newfound knowledge bomb, I find it helpful to classify members of a closure according to a writing by [David Crockford](http://crockford.com/javascript/private.html):

* Public - members which can be accessed from the outside (e.g. generally any property of an object)
* Private - members which cannot be directly accessed from the outside
* Priviledged - members which are accessible by public members and the outside and can also access private variables and methods

Here's a simple example using a constructor to make the point:

```js
// function name capitalized by convention to indicate that it is a constructor
function MemberTypes(param) {
  // Private members
  let privateVariable = "Refer to me as 'private'";
  let privateSecret = "Shhh... Don't tell";
  // Public members
  this.param = param;
  this.isAwesome = true;
  // Priviledged members
  this.makePolite = function() {
    return privateVariable += ", please";
  }
  this.getPrivateData = function() {
    return `${privateVariable},${privateSecret}`;
  }
}
// Also a public member, has only indirect access to private members via priviledged member
MemberTypes.prototype.makePrivateInfoArray = function() {
  return this.getPrivateData().split(",");
}

// Here comes the magic, let's first make a new object from the constructor
let mixedMemberObject = new MemberTypes('I am a public parameter!');

// Private members - cannot be accessed from the outside
console.log(mixedMemberObject.privateVariable); // -> undefined
console.log(mixedMemberObject.privateSecret); // -> undefined

// Public members - can be accessed from the outside, doesn't access private members directly ('directly' being the operative word)
console.log(mixedMemberObject.param); // -> 'I am a public parameter!'
console.log(mixedMemberObject.isAwesome); // -> true
console.log(mixedMemberObject.makePrivateInfoArray()); // -> ["Refer to me as 'private'", "Shhh... Don't tell"]

// Priviledged members - can be accessed from the outside, can access/operate/modify private members
console.log(mixedMemberObject.makePolite()); // -> "Refer to me as 'private', please"
console.log(mixedMemberObject.getPrivateData()); // -> "Refer to me as 'private', please,Shhh... Don't tell"
```

**Pros**:

* We have officially discovered a way to actually implement information hiding with public/private/priviledged members!! Woohoo!

**Cons**:

* We violated an OOP principle of 'separation of concerns' -- our ```MemberTypes()``` constructor was responsible for more than just data initialization of an object :-( Unfortunately it was also responsible for defining methods which should be a job reserved for our prototypes (not the end of the world, but...)
* A side effect of defining methods in the constructor rather than in the prototype is that our method's code is duplicated in every instantiation... we'll use much less memory if a single copy of a method only exists in the prototype, not in every instance of an object...

## IIFE (Immediately Invoked Function Expression, a.k.a Self-Executing Anonymous Function)

> What is the answer to the problem of 'separation of concerns' with closures, and how to we provide access to a closure from outside the constructor while still having prototype members hide data of an instantiated object? To put more functions inside a function and then immediately call the function -- DUHHHH!!! :-P

Let's first get past the crazy looking syntax of an IIFE before we take on how to apply it:

```js
const myFirstIIFE = (function() {
  return "IIFE's just took it to another level...";
}());
// Let's see what happens... (notice that 'myFirstIIFE' doesn't have curly braces after it in the console.log())
console.log(myFirstIIFE); // -> IIFE's just took it to another level...
```

Here are the things worth noting:

1. This is the essence of an IIFE: ```var myVar = (function(){}());```
2. It looks like we just set a variable ```myVar``` equal to an anonymous function (and that we did), but the anonymous function is wrapped in its entirety in a pair of parenthesis.
    * The anonymous function is wrapped in parenthesis because JS needs those in order for it to be 'immediately invoked' -- consider it nothing more than 'a thing that JavaScript needs me to do so that the anonymous function can be called immediately after it was declared'
3. So... about those other parenthesis...
    * Let's strip away the outer parenthesis now that we know it's just something JS needs from us
    * We are now left with: ```function(){}()```
    * Although this syntax looks crazy, it's similar to the following:
      ```js
      // Step 1 - Define variable as anonymous function
      var intermediateVariable = function() {
        return 'stuff';
      };
      // Step 2 - Declare variable, store return value of function
      var myResult = intermediateVariable();

      // IIFE version, all in one statement (skipping the redundancy of 'intermediateVariable')
      var myResult = (function(){return 'stuff';}());
      ```
    * So, we declared a variable's value as an anonymous function, and then immediately afterwards we called the function

The beauty of an IIFE is that because that extra set of ```()```'s follows the anonymous function's closing curly brace, our function is automatically called and its return value is stored in the variable -- **and our anonymous function can only ever be called once**!!! This... Is... HUGE!

So what does all this mean? Well...

1. We still use closures to our advantage in making public/private/priviledged members
2. IIFE makes it possible for priviledged members to be defined outside of the constructor scope (thereby solving our 'separation of concerns issue'), and still allows them to be shared over all object instances
3. Most importantly, we have perhaps discovered a definitive solution to making constructors that truly adhere to the OOP Principles information hiding and encapsulation

## Here is a big crazy example

> Code assignment: Make an object that manages interactions with Google's Firebase Database using an IIFE

Pseudocode:

```js

```