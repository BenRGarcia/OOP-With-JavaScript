# JavaScript Objects

> JS Objects in general terms

## What is an Object

* A container of key-value pairs (and should contain related 'state' and 'behavior')

## Examples

```js
// fig. 1a

const myObject = {
  /*=========*/
  /* 'State' */
  /*=========*/
  name: 'Mister Business',
  species: 'Cat',
  isHairy: true,
  age: 14,
  '-$i1ly_N@m3d?PR0p': 'Perhaps not a good name for a property'
  /*============*/
  /* 'Behavior' */
  /*============*/
  // One way to make a method
  makeNoise1: function() {
    return 'Purrrr...';
  },
  // My preferred way to make a method
  makeNoise2() {
    return 'Meow!';
  },
  // ES6 arrow function way to make a method (use with caution, could mess up binding of 'this')
  makeNoise3: () => {
    return 'Hisssss!';
  }
};
```

### How To Access Object Values

* With 'dot notation' and 'bracket notation'

```js
// fig. 1b

// 1) Dot Notation
console.log(myObject.name); // -> 'Mister Business'
console.log(myObject.makeNoise1); // -> 'Purrrr...'

// 2a) Bracket Notation (with string of property's name -- mostly shouldn't use bracket notation this way, dot notation would be more appropriate)
console.log(myObject['name']); // -> 'Mister Business'
console.log(myObject['makeNoise2']); // -> 'Meow!'
// (Required to use bracket notation for this one)
console.log(myObject['-$i1ly_N@m3d?PR0p']); // -> 'Perhaps not a good name for a property'

// 2b) Bracket Notation (with variable -- this will come in handy)
let myVar1 = 'name';
let myVar2 = 'makeNoise3';
console.log(myObject[myVar1]); // -> 'Mister Business'
console.log(myObject[myVar2]); // -> 'Hisssss!'
```

### How to Change an Object's Properties

* With 'dot notation' and 'bracket notation'

```js
// fig. 1c

// Change an existing property's value with dot notation
myObject.name = 'Mister Peanut Butter';
console.log(myObject.name); // -> 'Mister Peanut Butter'

// Change an existing property's value with bracket notation (string)
myObject['name'] = "Santa's Little Helper";
console.log(myObject.name); // -> "Santa's Little Helper"

// Change an existing property's value with bracket notation (variable)
let myVar3 = 'age';
myObject[myVar3] = 15;
console.log(myObject.age); // -> 15
```

### Constructors

* A.K.A. 'Making new objects'

```js
// fig. 2

// Object literal ('literally' declare a variable set equal to curly braces -- enclosing key/value pairs is optional)
const myEmptyObject = {}; // yup, you just made a new object (although it is empty)
const myNonEmptyObject = { name: 'Bojack' }; // yup, you just made another new object

// Using the Object() constructor provided by JavaScript
const anotherEmptyObject = new Object(); // An identical result as 'myEmptyObject' above
const anotherNonEmptyObject = new Object({ name: 'Roxanne' });

// Pre-ES6 use of constructors
var Animal(name, species) { // 'Animal' is the constructor, capitalized by convention
  this.name = name;
  this.species = species;
}
var dog = new Animal('Comet', 'Canine'); // This 'instantiates' an object of type 'Animal' -- also, the 'new' keyword is really important not to forget!!
console.log(dog.name); // -> 'Comet'

// ES6 use of constructors
class Mammal {
  constructor(name, species) {
    this.name = name;
    this.species = species;
  }
}
const elephant = new Mammal('Dumbo', 'Loxodonta africana');
console.log(elephant.name); // -> 'Dumbo'
```