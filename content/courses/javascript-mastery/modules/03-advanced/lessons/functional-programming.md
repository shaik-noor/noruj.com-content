# Functional Programming in JavaScript

Functional programming (FP) is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing state and mutable data. JavaScript supports many functional programming concepts, making it a versatile language for this paradigm.

## Core Principles of Functional Programming

### 1. Pure Functions

A pure function:
- Always returns the same result given the same arguments
- Has no side effects (doesn't modify external state)
- Doesn't rely on external state

```javascript
// Impure function (relies on external state)
let counter = 0;
function incrementCounter() {
  counter++;
  return counter;
}

// Pure function
function add(a, b) {
  return a + b;
}
```

### 2. Immutability

In FP, data should never be modified after creation. Instead of changing existing data, you create new data structures.

```javascript
// Non-functional approach (mutable)
const numbers = [1, 2, 3];
numbers.push(4); // Modifies original array

// Functional approach (immutable)
const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4]; // Creates a new array
```

### 3. First-Class and Higher-Order Functions

JavaScript treats functions as first-class citizens, meaning they can be:
- Assigned to variables
- Passed as arguments
- Returned from other functions

A higher-order function is a function that takes one or more functions as arguments or returns a function.

```javascript
// Assigning a function to a variable
const greet = function(name) {
  return `Hello, ${name}!`;
};

// Higher-order function (takes a function as argument)
function executeTwice(fn, arg) {
  fn(arg);
  fn(arg);
}

executeTwice(console.log, "Hello");

// Higher-order function (returns a function)
function multiply(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiply(2);
console.log(double(5)); // 10
```

### 4. Function Composition

Building complex functions by combining simpler functions.

```javascript
// Simple functions
const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

// Manual composition
const result = square(double(addOne(5))); // square(double(6)) -> square(12) -> 144

// Helper for composition
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const transformValue = compose(square, double, addOne);
console.log(transformValue(5)); // 144
```

### 5. Declarative vs. Imperative

Functional programming favors a declarative style (what to do) over an imperative style (how to do it).

```javascript
// Imperative approach
function getEvenNumbers(numbers) {
  const evenNumbers = [];
  for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] % 2 === 0) {
      evenNumbers.push(numbers[i]);
    }
  }
  return evenNumbers;
}

// Declarative (functional) approach
function getEvenNumbers(numbers) {
  return numbers.filter(number => number % 2 === 0);
}
```

## Functional Programming Techniques

### 1. Map, Filter, Reduce

These array methods are the building blocks of functional JavaScript:

#### Map

Transforms each element in an array:

```javascript
const numbers = [1, 2, 3, 4];
const squared = numbers.map(x => x * x); // [1, 4, 9, 16]
```

#### Filter

Creates a new array with elements that pass a test:

```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evenNumbers = numbers.filter(x => x % 2 === 0); // [2, 4, 6]
```

#### Reduce

Accumulates values from an array into a single value:

```javascript
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((accumulator, current) => accumulator + current, 0); // 10

// More complex example: grouping objects by a property
const people = [
  { name: "Alice", age: 25 },
  { name: "Bob", age: 30 },
  { name: "Charlie", age: 25 },
  { name: "Dave", age: 30 }
];

const groupedByAge = people.reduce((acc, person) => {
  const age = person.age;
  acc[age] = acc[age] || [];
  acc[age].push(person);
  return acc;
}, {});

/*
Result:
{
  "25": [{ name: "Alice", age: 25 }, { name: "Charlie", age: 25 }],
  "30": [{ name: "Bob", age: 30 }, { name: "Dave", age: 30 }]
}
*/
```

### 2. Currying

Transforming a function with multiple arguments into a sequence of functions, each taking a single argument:

```javascript
// Regular function
function add(a, b, c) {
  return a + b + c;
}

// Curried version
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

// Using the curried function
console.log(curriedAdd(1)(2)(3)); // 6

// With arrow functions
const curriedAddArrow = a => b => c => a + b + c;
```

Currying allows partial application:

```javascript
const addTo10 = curriedAdd(10);
const add15 = addTo10(5);
console.log(add15(3)); // 18
```

### 3. Partial Application

Fixing a number of arguments to a function, producing a function of smaller arity:

```javascript
function partial(fn, ...presetArgs) {
  return function(...laterArgs) {
    return fn(...presetArgs, ...laterArgs);
  };
}

function multiply(a, b, c) {
  return a * b * c;
}

const multiplyBy5 = partial(multiply, 5);
console.log(multiplyBy5(2, 3)); // 5 * 2 * 3 = 30
```

### 4. Function Memoization

Caching function results to avoid repeated calculations:

```javascript
function memoize(fn) {
  const cache = {};
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache[key] !== undefined) {
      console.log('Retrieving from cache');
      return cache[key];
    }
    
    const result = fn(...args);
    cache[key] = result;
    return result;
  };
}

// Example: Expensive calculation
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

// Without memoization, fibonacci(50) would be extremely slow
const memoizedFib = memoize(function(n) {
  if (n <= 1) return n;
  return memoizedFib(n - 1) + memoizedFib(n - 2);
});

console.log(memoizedFib(40)); // Much faster than the original
```

### 5. Point-Free Style (Tacit Programming)

Writing functions without explicitly mentioning the arguments:

```javascript
// Not point-free
const numbers = [1, 2, 3, 4];
const evens = numbers.filter(function(n) {
  return n % 2 === 0;
});

// Point-free
const isEven = n => n % 2 === 0;
const evens = numbers.filter(isEven);
```

## Practical Examples

### Example 1: Data Transformation Pipeline

```javascript
const users = [
  { id: 1, name: "John", age: 25 },
  { id: 2, name: "Jane", age: 32 },
  { id: 3, name: "Bob", age: 17 },
  { id: 4, name: "Alice", age: 45 }
];

// Goal: Get the names of adults (age >= 18), capitalized, sorted alphabetically

// Imperative approach
function getAdultNamesSorted(users) {
  const adults = [];
  for (let i = 0; i < users.length; i++) {
    if (users[i].age >= 18) {
      adults.push(users[i]);
    }
  }
  
  const names = [];
  for (let i = 0; i < adults.length; i++) {
    names.push(adults[i].name.toUpperCase());
  }
  
  return names.sort();
}

// Functional approach
function getAdultNamesSorted(users) {
  return users
    .filter(user => user.age >= 18)
    .map(user => user.name.toUpperCase())
    .sort();
}

console.log(getAdultNamesSorted(users)); // ["ALICE", "JANE", "JOHN"]
```

### Example 2: Custom Functional Utilities

```javascript
// Implementation of pipe (left-to-right composition)
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);

// Implementation of compose (right-to-left composition)
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);

// Some basic functions
const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

// Using them
const transformWithPipe = pipe(addOne, double, square); // (((x + 1) * 2) ^ 2)
const transformWithCompose = compose(square, double, addOne); // square(double(addOne(x)))

console.log(transformWithPipe(3)); // ((3 + 1) * 2) ^ 2 = 64
console.log(transformWithCompose(3)); // square(double(addOne(3))) = square(double(4)) = square(8) = 64
```

### Example 3: Handling Asynchronous Operations

```javascript
// Fetch user data, then user's posts, then format the results
fetch('/api/users/1')
  .then(response => response.json())
  .then(user => {
    return fetch(`/api/users/${user.id}/posts`)
      .then(response => response.json())
      .then(posts => {
        return {
          user: user,
          posts: posts
        };
      });
  })
  .then(data => {
    return {
      username: data.user.name,
      postCount: data.posts.length,
      latestPost: data.posts[0]
    };
  });
```

## Libraries for Functional Programming

Several JavaScript libraries support functional programming:

### Lodash/FP

A utility library with immutable auto-curried data-last methods:

```javascript
const _ = require('lodash/fp');

const users = [
  { name: 'John', age: 30 },
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 40 }
];

const getOldestUser = _.flow(
  _.sortBy('age'),
  _.reverse,
  _.first,
  _.get('name')
);

console.log(getOldestUser(users)); // "Bob"
```

### Ramda

A more purely functional library:

```javascript
const R = require('ramda');

const multiply = (a, b) => a * b;
const addOne = x => x + 1;

const pipeline = R.pipe(
  R.map(R.multiply(2)),
  R.filter(R.gt(R.__, 10)),
  R.sum
);

console.log(pipeline([1, 2, 3, 4, 5, 6])); // (2 + 4 + 6 + 8 + 10 + 12) filtered to > 10 = 12
```

## Challenges with Functional Programming in JavaScript

### 1. Performance Considerations

Creating new objects instead of mutating can be less efficient:

```javascript
// Consider the performance implications of creating many new arrays
let numbers = [1, 2, 3, 4, 5];

// Less memory efficient but immutable
for (let i = 0; i < 1000; i++) {
  numbers = [...numbers, i];
}
```

### 2. JavaScript's Limitations

JavaScript wasn't designed as a purely functional language:
- No built-in immutable data structures
- No guarantee of tail-call optimization
- No built-in function composition or currying

## Exercise: Functional Programming in Action

1. Create a set of utility functions to process a list of products:
   - Filter products by price range
   - Sort products by rating
   - Group products by category
   - Get the total value of products

2. Write functions to process a collection of orders:
   - Calculate the total amount spent by a customer
   - Find the most popular product
   - Calculate average order value
   - Group orders by month

3. Implement a pipe function and use it to create a data transformation pipeline:
   - Filter out inactive users
   - Format user names (capitalize first letter)
   - Sort users by registration date
   - Extract user emails

Use functional programming principles for all exercises: pure functions, immutability, and higher-order functions.

## Conclusion

Functional programming offers a powerful and expressive way to write JavaScript. By focusing on pure functions, immutability, and composition, you can create more predictable, testable, and maintainable code.

While JavaScript isn't a purely functional language, it provides enough tools to apply functional programming principles effectively. Understanding these concepts can significantly improve your code quality and problem-solving approach.

Next, we'll explore design patterns in JavaScript.