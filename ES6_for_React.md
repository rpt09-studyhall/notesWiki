
# ES2015 Highlights for React

ES2015 (ES6) is common in React projects. Highlights here:

## Table of Contents
- [ES2015 Highlights for React](#es2015-highlights-for-react)
  - [Table of Contents](#table-of-contents)
  - [Array's .map() method](#arrays-map-method)
  - [Array's .filter() method](#arrays-filter-method)
  - [Chaining .map() and .filter() together](#chaining-map-and-filter-together)
  - [Array's .reduce() method](#arrays-reduce-method)
    - [.map, .filter, or .reduce?](#map-filter-or-reduce)
  - [ES6, Harmony, ES2015, WTH?](#es6-harmony-es2015-wth)
    - [let & const](#let--const)
    - [Template Literals](#template-literals)
    - [Destructuring](#destructuring)
      - [Object Literal Shorthand](#object-literal-shorthand)
    - [Iteration](#iteration)
    - [Spread syntax](#spread-syntax)
    - [Combining arrays with concat](#combining-arrays-with-concat)
    - [Rest parameter](#rest-parameter)
      - [Variadic functions](#variadic-functions)
    - [Updates to Functions](#updates-to-functions)
      - [Arrow functions](#arrow-functions)
        - [Concise and body block syntax](#concise-and-body-block-syntax)
        - [Arrow functions and `this` keyword](#arrow-functions-and-this-keyword)
      - [Default function parameters](#default-function-parameters)
      - [Defaults and destructuring arrays](#defaults-and-destructuring-arrays)
      - [Defaults and destructuring objects](#defaults-and-destructuring-objects)
    - [Classes](#classes)
    - [New Built-ins: Sets](#new-built-ins-sets)
      - [Create a Set](#create-a-set)
      - [Modifying Sets](#modifying-sets)
      - [Checking Set Length](#checking-set-length)
      - [Check if Item Exists](#check-if-item-exists)
      - [Retrieve all Values](#retrieve-all-values)
      - [Looping over Sets](#looping-over-sets)
        - [Using the SetIterator](#using-the-setiterator)
        - [Using a `for...of` Loop](#using-a-forof-loop)
    - [Promises](#promises)
      - [Indicate Successful or Failed Request](#indicate-successful-or-failed-request)
      - [Promises Return Immediately](#promises-return-immediately)

## Array's .map() method

Gets called on an existing array and returns a new array based what's returned from the function that's passed as an argument.

```
const names = ['Joe', 'Leslie', 'Magee'];

const nameLengths = names.map( name => name.length );
``` 

The [`.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) method returns a new array, it does not modify the original array.

```javascript
// For musicData, return a string for each item in the array
// as: <album> by <artist> sold <sales> copies

const musicData = [
    { artist: 'Adele', name: '25', sales: 1731000 },
    { artist: 'Drake', name: 'Views', sales: 1608000 },
    { artist: 'Beyonce', name: 'Lemonade', sales: 1554000 },
    { artist: 'Chris Stapleton', name: 'Traveller', sales: 1085000 },
    { artist: 'Pentatonix', name: 'A Pentatonix Christmas', sales: 904000 },
    { artist: 'Original Broadway Cast Recording', name: 'Hamilton: An American Musical', sales: 820000 },
    { artist: 'Twenty One Pilots', name: 'Blurryface', sales: 738000 },
    { artist: 'Prince', name: 'The Very Best of Prince', sales: 668000 },
    { artist: 'Rihanna', name: 'Anti', sales: 603000 },
    { artist: 'Justin Bieber', name: 'Purpose', sales: 554000 }
];

// could also use ES6 template strings here
const albumSalesStrings = musicData.map( obj => obj.name + ' by '
+ obj.artist + ' sold ' + obj.sales + ' copies' );

console.log(albumSalesStrings);
``` 

## Array's .filter() method

JavaScript's [`Array.filter()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) method is similar to the `.map()` method:

* it is called on an array
* it takes a function as an argument
* it returns a new array

The difference is that the function passed to `.filter()` is used as a test, and only items in the array that pass the test are included in the new array.

```javascript
const names = ['Joe', 'Leslie', 'Magee'];
const shortNames = names.filter( name => name.length < 5 );
```

Example: filter the `musicData` array to return only album names between 10 and 25 characters:

```javascript
musicData.filter( obj => 10 <= obj.name.length <= 25 );
```

## Chaining .map() and .filter() together

What makes `.map()` and `.filter()` cool is that they can be combined. Because both methods return arrays, we can chain the method calls together so the returned data from one can be a new array for the next. Booyah.

**`.filter()` first:**

We'll want to run things in the order `.filter()` first and then `.map()`. Because `.map()` runs the function once for each item in the array, it will be faster if the array were already filtered (smaller).

```javascript
musicData.filter( album => album.sales > 1000000 ).map( album => album.artist + ' is a great performer' );
```

Look through some of our existing code and try converting our `for` loops to `.map()` calls or see if we can remove any `if` statements by using `.filter()`.

## Array's .reduce() method

The central idea of [.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) is that it takes in a large amount of data but returns a _single value_. `.reduce()` is a higher-order function, meaning that it takes in a function (i.e., a callback) as an argument.

Recall that `.map()` and `.filter()` are also higher-order functions. Both functions accept a callback function as their first argument.

To see how powerful `.reduce()` can be, consider the following `iceCreamStats` array, showing the number of gallons of ice cream each team member has eaten.

```javascript
const iceCreamStats = [
  {
    name: 'Chris',
    gallonsEaten: 3.8
  },
  {
    name: 'Allen',
    gallonsEaten: 5.2
  },
  {
    name: 'Meng',
    gallonsEaten: 1.9
  },
  {
    name: 'Ryan',
    gallonsEaten: 7923
  }
];
```

For the total amount of ice cream eaten, we could add up the totals ourselves, or reduce this data down to just a single number, and that's exactly what the `.reduce()` method does.

```javascript
iceCreamStats.reduce( (accumulator, currentValue) => {
  return accumulator + currentValue.gallonsEaten;
}, 0);
```

![Illustrated .reduce() function](img/annotated-reduce.jpg)

Tunes example:

```javascript
const musicData = [
    { artist: 'Adele', name: '25', sales: 1731000 },
    { artist: 'Drake', name: 'Views', sales: 1608000 },
    { artist: 'Beyonce', name: 'Lemonade', sales: 1554000 },
    { artist: 'Pentatonix', name: 'A Pentatonix Christmas', sales: 904000 },
    { artist: 'Prince', name: 'The Very Best of Prince', sales: 668000 },
    { artist: 'Rihanna', name: 'Anti', sales: 603000 },
    { artist: 'Twenty One Pilots', name: 'Blurryface', sales: 738000 }
];
```

Just Reduce:

```javascript
const totalAlbumSales = musicData.reduce( (accumulator, currentValue) => {
    return accumulator + currentValue.sales;
}, 0);
```

Filter & Reduce:

```javascript
const totalAlbumSales = musicData.filter(ea => ea.artist.length + ea.name.length < 25).reduce( (accumulator, currentValue) => {
    return accumulator + currentValue.sales;
}, 0);
```

### .map, .filter, or .reduce?

If I have an array:

If I need to remove an item, I use `.filter`,
if I need to transform the array into a new array, I use `.map`,
if I need to transform that array into anything other than an array , I use `.reduce`.

## ES6, Harmony, ES2015, WTH?

Same same names for new JS syntax & features. [Articles tagged: ECMAScript 2015](https://developer.mozilla.org/en-US/docs/tag/ECMAScript%202015) on MDN. The [actual language spec](https://www.ecma-international.org/ecma-262/6.0/#sec-arrow-function-definitions). Features with online REPL [at Babel](https://babeljs.io/docs/en/learn/), and a Goog article on [some tooling](https://developers.google.com/web/shows/ttt/series-2/es2015) we might use with ES2015.

And, a [handy site to bookmark](http://es6-features.org/) for ES5 vs ES6 comparisons.

### let & const

Variables declared with `let` and `const` eliminate the issue of variable hoisting in a function because they're scoped to the block, not to the function. Back in the day, when we used `var`, variables were either scoped *globally* or *locally* to an entire function scope.

* Variables declared with `let` can be reassigned, but can't be redeclared in the same scope
* Variables declared with `const` must be assigned an initial value, but can't be redeclared in the same scope, and can't be reassigned

When to use?

* use `let` when we plan to reassign new values to a variable, and
* use `const` when we don't plan on reassigning new values to a variable

Since `const` is the strictest way to declare a variable, we may want to just always declare variables with `const` because it'll make code easier to reason about since we know the identifiers won't change throughout the lifetime of our app. If we find that we need to update a variable or change it, then go back and switch it from `const` to `let`. (Find in VS Code w ⇧⌘O or ⇧F12)

### Template Literals

Also called "template strings". **Template literals** are essentially string literals that include embedded expressions. Denoted with `backticks` instead of single quotes ( `''` ) or double quotes ( `""` ), template literals can contain placeholders that are represented using `${expression}`. This makes it much easier to build strings.

```javascript    
const student = {
  name: 'Mr. Wolf',
  guardian: 'Mr. Pink'
};

const teacher = {
  name: 'Mrs. Brown',
  room: 'Freddy'
}

// The Old Way:
let message = student.name + ' please see ' + teacher.name + ' in ' + teacher.room + ' to pick up your report card.';

// New way with template literals:
let message = `${student.name} please see ${teacher.name} in ${teacher.room} to pick up your report card.`;
```

* Embedded expressions inside template literals can do more than just reference variables: perform operations, call functions and use loops inside embedded expressions.

### Destructuring

In ES6, we can extract data from arrays and objects into distinct variables using _destructuring_.

Allows us to specify the elements we want to extract from an array or object _on the left side of an assignment_.

```javascript
const point = [10, 25, -34];
const [x, y, z] = point;
console.log(x, y, z);  // 10 25 -34
```

In this example, the brackets `[ ]` represent the array being destructured and `x`, `y`, and `z` represent the variables where we want to store the values from the array.

We can ignore values when restructuring arrays `const [x, , z] = point;` ignores the `y` coordinate and discards it.

```javascript
const gemstone = {
  type: 'quartz',
  color: 'rose',
  karat: 21.29
};

const {type, color, karat} = gemstone;
console.log(type, color, karat); // quartz rose 21.29
```

We can also specify the values we want to select when destructuring an object. For example, `let {color} = gemstone;` will only select the `color` property from the `gemstone` object.

#### Object Literal Shorthand

When adding object properties with the same name as variable names being assigned to them:

```javascript
let type = 'quartz';
let color = 'rose';
let carat = 21.29;

// old way
const gemstone = {
  type: type,
  color: color,
  carat: carat
};

// new way
const gemstone = { type, color, carat };

console.log(gemstone);
```
Shorthand to add methods to objects.

```javascript
let type = 'quartz';
let color = 'rose';
let carat = 21.29;

const gemstone = {
  type,
  color,
  carat,
  calculateWorth: function() {
    // calculates worth of gemstone based on type, color, and carat
  }
};
```
Since we only need to reference the gemstone's `calculateWorth` property in order to call the function, having the function keyword is redundant, so it can be dropped.
```javascript
let gemstone = {
  type,
  color,
  carat,
  calculateWorth() { ... }
};
```

### Iteration

The **for ...of** loop combines parts of the **for** and **for ...in** loops and iterates over data types that follow the [iterable protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols). By default this includes the data types String, Array, Map, and Set. Objects are not iterable by default.

```javascript
// for...in loop:
const digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

for (const index in digits) {
  console.log(digits[index]);
}
```
The **forEach loop** is another type of for loop in JavaScript. However, `forEach()` is actually an array method, so it can only be used with arrays. There is also no way to stop or break a forEach loop. If we need that type of behavior in our loop, we'll have to use a basic for loop.

The **for...of** loop is used to loop over any type of data that is _iterable_.

```javascript
// for...of loop
const digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

for (const digit of digits) {
  console.log(digit);
}
```
This makes the for...of loop a concise version of all the for loops.

It's good practice to use plural names for objects that are collections of values. That way, when we loop over the collection, we can use the singular version of the name when referencing individual values in the collection. For example, `for (const button of buttons) {...}`.

We can stop or break a for...of loop at anytime.

```javascript
const digits = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

for (const digit of digits) {
  if (digit % 2 === 0) {
    continue;
  }
  console.log(digit); // 1 3 7 9
}
```

### Spread syntax

The **spread operator**, written with three consecutive dots ( `...` ), is [new in ES6](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) and gives us the ability to expand, or _spread_, [iterable objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Iterators) into multiple elements.

```javascript
const books = ["Don Quixote", "The Hobbit", "Alice in Wonderland", "Tale of Two Cities"];
console.log(...books);

// prints
// Don Quixote The Hobbit Alice in Wonderland Tale of Two Cities

//  Also:
var player = {score: 1, name: 'Joe'};
let newPlayer = Object.assign({}, player, {score: 2});
// is same as:
let newPlayer = {...player, score: 2};
```

### Combining arrays with concat

One example of when the spread operator can be useful is when combining arrays. If we needed to combine multiple arrays in the old days before the spread operator, we were forced to use the Array's `concat()` method.

```javascript
// old (1975) way:
const fruits = ["apples", "bananas", "pears"];
const vegetables = ["corn", "potatoes", "carrots"];
const produce = fruits.concat(vegetables);
console.log(produce);

// ["apples", "bananas", "pears", "corn", "potatoes", "carrots"]

// new (2018) way:
const produce = [fruits, vegetables];
console.log(produce);  // [Array[3], Array[3]] Not correct

const produce = [...fruits, ...vegetables]
// [ "apples", "bananas", "pears", "corn", "potatoes", "carrots"]
```

### Rest parameter

Spread operator _spreads_ an array into multiple elements, the _rest parameter_ bundles multiple elements into an array.

The **rest parameter**, also written with three consecutive dots ( `...` ), allows us to represent an indefinite number of elements as an array. Like when assigning the values of an array to variables:

```javascript
const order = [20.17, 18.67, 1.50, "cheese", "eggs", "milk", "bread"];
const [total, subtotal, tax, ...items] = order;
console.log(total, subtotal, tax, items);
// 20.17 18.67 1.5 ["cheese", "eggs", "milk", "bread"]
```
Using the rest parameter, `items` is assigned the "rest of the values" in the array (as an array).

#### Variadic functions

Another use case for the rest parameter [(spread syntax)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) is when we're working with variadic functions. Variadic functions are functions that take an indefinite number of arguments.

A `sum()` function that accepts any number of arguments:

```javascript
function sum(...nums) {
  let total = 0;  
  for(const num of nums) {
    total += num;
  }
  return total;
}
  

// `average` function accepts any # of arguments:
function average(...nums) {
  if (!nums.length) return 0;
  let total = 0;
  for(const num of nums) {
    total += num;
  }
  return total / nums.length;
}
```

### Updates to Functions

Arrow functions, class keyword, and default parameters.

#### Arrow functions

```javascript
// old way: convert names to uppercase
const upperizedNames = ['fudge', 'ice cream', 'licorice'].map(function(name) { 
  return name.toUpperCase();
});

// new way w arrow function
const upperizedNames = ['fudge', 'ice cream', 'licorice'].map(
  name => name.toUpperCase()
);
```
The parameter list appears before the arrow function's arrow. With only **one** parameter in the list, we can skip the parentheses around it. If there are **two or more** items in the parameter list, or **zero**, we need to wrap the list in parentheses:

```javascript
// empty parameter list requires parentheses
const sayHi = () => console.log('Hello World!');
sayHi();

// multiple parameters requires parentheses
const orderIceCream = (flavor, cone) => 
  console.log(`Here's your ${flavor} ice cream in a ${cone} cone.`);
orderIceCream('chocolate', 'waffle');
```

##### Concise and body block syntax

Concise - With single expression as the function body:

```javascript
const upperizedNames = ['Farrin', 'Kagure', 'Asser'].map(
  name => name.toUpperCase()
);
```

* has no curly braces surrounding function body
* automatically returns the expression

Body block syntax: when we need more than one line of code:

```javascript
const upperizedNames = ['Farrin', 'Kagure', 'Asser'].map( name => {
  name = name.toUpperCase();
  return `${name} has ${name.length} characters in their name`;
});
```
* uses curly braces to wrap function body
* `return` statement need to actually return something from the function

##### Arrow functions and `this` keyword

With regular functions, the value of `this` depends on how the function is called. With arrow functions, it depends on where that function is located in the code.

**`this` in Regular Functions:**  

Depending how it's called, `this` could be any one of these:  

1. A new object: if the function is called with `new`    
    `const mySundae = new Sundae('Chocolate', ['Sprinkles', 'Hot Fudge']);`
The value of `this` in the `Sundae` constructor function is a new object because it was called with `new`
2. A specified object: if the function is invoked with `call`/`apply`
    `const result = obj1.printName.call(obj2);`
`this` inside `printName()` refers to `obj2` since the first parameter of `call()` is to explicitly set what `this` refers to.
3. A context object: if the function is a method of an object
    `data.teleport()`
`this` inside `teleport()` refers to `data`
4. The global object or undefined: if the function is called with no context
    `teleport();`
`this` inside `teleport()` is either the global object, or if in strict mode, it's `undefined`

For more detail on how `this` is determined, see [this all makes sense now](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md) from Kyle Simpson.

With regular functions, the value of `this` is set based on how the _function is called_. With arrow functions, the value of `this` is based on _the function's surrounding context_. In other words, the value of `this` _inside_ an arrow function is the same as the value of `this` _outside_ the function.

#### Default function parameters

```javascript
function greet(name = 'Student', greeting = 'Welcome') {
  return `${greeting} ${name}!`;
}

greet(); // Welcome Student!
greet('Matt'); // Welcome Matt!
greet('Joe', 'Howdy'); // Howdy Joe!
```

To create a default parameter, add an equal sign ( `=` ) and then whatever we want the parameter to default to if an argument is not provided.

#### Defaults and destructuring arrays

We can combine default function parameters [with destructuring](https://davidwalsh.name/destructuring-function-arguments) to create powerful functions.
```javascript
function createGrid([width = 5, height = 5] = []) {
  return `Generates a ${width} x ${height} grid`;
}

createGrid([]); // Generates a 5 x 5 grid
createGrid([2]); // Generates a 2 x 5 grid
createGrid([2, 3]); // Generates a 2 x 3 grid
createGrid([undefined, 3]); // Generates a 5 x 3 grid
```

If no argument is passed when `createGrid()` is called, it will use the `=[]` default empty array. Since it's empty, there's nothing to restructure into `width` & `height`, so the default values apply. `createGrid()` generates a 5 x 5 grid.

#### Defaults and destructuring objects

Just like array destructuring with array defaults, a function can have an object be a default parameter and use object destructuring:

```javascript
function createSundae({scoops = 1, toppings = ['Hot Fudge']} = {}) {
  const scoopText = scoops === 1 ? 'scoop' : 'scoops';
  return `Your sundae has ${scoops} ${scoopText} with ${toppings.join(' and ')} toppings.`;
}

createSundae({}); // Your sundae has 1 scoop with Hot Fudge toppings.
createSundae({scoops: 2}); // Your sundae has 2 scoops with Hot Fudge toppings.
createSundae({scoops: 2, toppings: ['Sprinkles']}); // Your sundae has 2 scoops with Sprinkles toppings.
createSundae({toppings: ['Cookie Dough']}); // Your sundae has 1 scoop with Cookie Dough toppings.
```
To use a default value for the first property, but change the next, just pass an object with the property we want to change:

```javascript
function createSundae({scoops = 1, toppings = ['Hot Fudge']} = {}) { ... }

createSundae({toppings: ['Hot Fudge', 'Sprinkles', 'Caramel']});

function buildHouse({floors = 1, color = 'red', walls = 'brick'} = {}) {
  return `Your house has ${floors} floor(s) with ${color} ${walls} walls.`;
} 
```

### Classes

JavaScript is not a class-based language, it uses functions to create objects, and links objects together by prototypal inheritance. JavaScript classes are just a Jedi mind-trick over regular functions and prototypal inheritance.

```javascript
class Plane {
  constructor(numEngines) {
    this.numEngines = numEngines;
    this.enginesActive = false;
  }

  startEngines() {
    console.log('starting engines...');
    this.enginesActive = true;
  }
}
```

`Class` is just a function. From the code above, if we check `typeof Plane;` we'll get `// function`

Commas are not used to separate properties or methods in a Class. If we add them, we'll get a `SyntaxError` of `unexpected token ,`

Benefits of classes

1. Less setup
    * There's a lot less code that we need to write to create a function
2. Clearly defined constructor function
    * Inside the class definition, we can clearly specify the constructor function.
3. Everything's contained
    * All code that's needed for the class is contained in the class declaration. Instead of having the constructor function in one place, then adding methods to the prototype one-by-one, we can do everything all at once.

**Things to look out for when using classes**

1. `class` is not magic
    * The `class` keyword brings with it a lot of mental constructs from other, class-based languages. It doesn't magically add that functionality to JavaScript classes.
2. `class` is a mirage over prototypal inheritance
    * Under the hood, a JavaScript class just uses prototypal inheritance.
3. Using classes requires the use of `new`
    * When creating a new instance of a JavaScript class, the `new` keyword must be used

    ```javascript
    class Toy {
       ...
    }
    
    const myToy1 = Toy(); // throws an error
    const myToy2 = new Toy(); // this works
    ```
    
### New Built-ins: Sets

A [set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) is a collection of distinct items. `{2, 4, 5, 6}` is a set because each number is unique and appears only once. We can represent something similar in JS using an array. But, arrays don't enforce unique items only.

A "Set" is a new built-in object in ES6 that behaves like a mathematical set & works like an array.

* Sets are not index-based - we don't refer to items in a set based on their position
* Items in a set can't be accessed individually

#### Create a Set

```javascript
const games = new Set();
console.log(games);
```

To create a Set from a list of values, use an array:

```javascript
const games = new Set(['Super Mario Bros.', 'Banjo-Kazooie', 'Mario Kart', 'Super Mario Bros.']);
console.log(games);

// Set {'Super Mario Bros.', 'Banjo-Kazooie', 'Mario Kart'}
```

Notice the Set automatically removes the duplicate entry `"Super Mario Bros."` when the Set is created.

#### Modifying Sets

Use `.add()` and `.delete()` methods to add & delete items from a Set. Use `.clear()` to delete all items from a Set.

If we attempt to `.add()` a duplicate item to a Set, we won't receive an error, but the item will not be added to the Set. Also, if we try to `.delete()` an item that is not in a Set, we won't receive an error, and the Set will remain unchanged. Both methods return `true` if an item is successfully added or deleted from the Set and `false` if unsuccessful.

#### Checking Set Length

`.size` property returns the number of items in a Set.

#### Check if Item Exists

Use the `.has()` method, which returns `true` if the item exists in the Set, or `false` if it doesn't.

#### Retrieve all Values

`.values()` method returns values in a Set. The return value of the `.values()` method is a `SetIterator` object.

#### Looping over Sets

Sets are built-in iterables. That means:

1. We can use the Set's default iterator to step through each item in a Set, one by one
2. We can use the new `for...of` loop to loop through each item in a Set.

##### Using the SetIterator

Because the `.values()` method returns a new iterator object (called `SetIterator`), we can store that iterator object in a variable and loop through each item in the Set using `.next()`.

```javascript
const iterator months.values();
iterator.next();
// Object {value: 'January', done: false}
```

If we run `.next()` again:

```javascript
  iterator.next();
  // Object {value: 'February', done: false}
```

And so on until `done` equals `true` that marks the end of the Set.

##### Using a `for...of` Loop

Using a `for...of` loop is easier:

    const colors = new Set(['red', 'orange', 'yellow', 'green', 'blue', 'violet', 'brown', 'black']);
    for (const color of colors) {
      console.log(color);
    }

### Promises

The new way to handle async requests. "Do this thing now, then notify me when it's done so I can pick up where I left off."

A JavaScript Promise is created with the new [Promise constructor function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) - `new Promise()`. A promise will let us start some work that will be done **asynchronously** and let us get back to our regular work. When we create the promise, we give it the code that will be run asynchronously. We provide this code as the argument of the constructor function:

```javascript
new Promise(function () {
  window.setTimeout(function createSundae(flavor = 'chocolate') {
    const sundae = {};
    // request ice cream
    // get cone
    // warm up ice cream scoop
    // scoop generous portion into cone!
  }, Math.random() * 2000);
});
```

This code creates a promise that will start in a few seconds after I make the request. Then there are a number of steps that need to be made in the `createSundae` function.

#### Indicate Successful or Failed Request

But once that's all done, how does JavaScript notify us that it's finished and ready for us to pick back up? It does that by passing two functions into our initial function. Typically we call these `resolve` and `reject`.

The function gets passed to the function we provide the Promise constructor - typically the word "resolve" is used to indicate that this function should be called when the request completes successfully. Notice the `resolve` on the first line:

```javascript
new Promise(function (resolve, reject) {
    window.setTimeout(function createSundae(flavor = 'chocolate') {
        const sundae = {};
        // request ice cream
        // get cone
        // warm up ice cream scoop
        // scoop generous portion into cone!
        resolve(sundae);
    }, Math.random() * 2000);
});
```

Now when the sundae has been successfully created, it calls the `resolve` method and passes it the data we want to return - in this case the data that's being returned is the completed sundae. So the `resolve` method is used to indicate that the request is complete and that it completed *successfully*.

If there is a problem with the request and it couldn't be completed, then we could use the second function that's passed to the function. Typically, this function is stored in an identifier called "reject" to indicate that this function should be used if the request fails for some reason. Note the `reject` on the first line:

```javascript
new Promise(function (resolve, reject) {
  window.setTimeout(function createSundae(flavor = 'chocolate') {
    const sundae = {};
      // request ice cream
      // get cone
      // warm up ice cream scoop
      // scoop generous portion into cone!
      if ( /* iceCreamConeIsEmpty(flavor) */ ) {
        reject(`Sorry, we're out of that flavor :-(`);
      }
      resolve(sundae);
  }, Math.random() * 2000);
});
```

So the `reject` method is used when the request *could not be completed*. Notice that even though the request fails, we can still return data - in this case we're just returning text that says we don't have the desired ice cream flavor.

A Promise constructor takes a function that will run and then, after some amount of time, will either complete successfully (using the `resolve` method) or unsuccessfully (using the `reject` method). When the outcome has been finalized (the request has either completed successfully or unsuccessfully), the promise is now *fulfilled* and will notify us so we can decide what to do with the response.

#### Promises Return Immediately

The first thing to understand is that a Promise will immediately return an object.

```javascript
const myPromiseObj = new Promise(function (resolve, reject) {
    // sundae creation code
});
```

That object has a `.then()` method on it that we can use to have it notify us if the request we made in the promise was either successful or failed. The `.then()` method takes two functions:

1. the function to run if the request completed successfully
2. the function to run if the request failed to complete

```javascript
mySundae.then(function(sundae) {
    console.log(`Time to eat my delicious ${sundae}`);
}, function(msg) {
    console.log(msg);
    self.goCry(); // not a real method
});
```

So, the first function that's passed to `.then()` will be called and passed the data that the Promise's `resolve` function used. In this case, the function would receive the `sundae` object. The second function will be called and passed the data that the Promise's `reject` function was called with. In this case, the function receives the error message "Sorry, we're out of that flavor :-(" that the `reject` function was called within the Promise code above.
