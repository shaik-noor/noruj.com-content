# Functions in JavaScript

Functions are reusable blocks of code that perform a specific task. They help organize code, make it more readable, and avoid repetition.

## Defining Functions

There are several ways to define functions in JavaScript:

### 1. Function Declaration

This is the most common way.

```javascript
// Define the function
function greet(name) {
  console.log(`Hello, ${name}!`);
}

// Call the function
greet("Alice"); // Output: Hello, Alice!
```
-   `function` keyword starts the declaration.
-   `greet` is the function name.
-   `name` is a parameter (an input the function accepts).
-   The code inside `{}` is the function body.
-   Function declarations are "hoisted," meaning they can be called before they are defined in the code.

### 2. Function Expression

A function can also be defined as part of an expression, often assigned to a variable.

```javascript
const add = function(num1, num2) {
  return num1 + num2; // 'return' sends a value back from the function
};

// Call the function
let sum = add(5, 3);
console.log(sum); // Output: 8
```
-   The function itself doesn't have a name (it's anonymous) but is assigned to the `add` variable.
-   Function expressions are *not* hoisted.

### 3. Arrow Functions (ES6)

A more concise syntax for writing functions, especially useful for short functions or when dealing with the `this` keyword (covered later).

```javascript
// Arrow function expression
const multiply = (a, b) => {
  return a * b;
};

// If the function body is just one expression, you can omit {} and 'return'
const subtract = (a, b) => a - b;

// If there's only one parameter, you can omit ()
const square = x => x * x;

console.log(multiply(4, 5)); // Output: 20
console.log(subtract(10, 4)); // Output: 6
console.log(square(7));      // Output: 49
```

## Parameters and Arguments

-   **Parameters:** Variables listed inside the parentheses `()` in the function definition (e.g., `name`, `num1`, `num2`).
-   **Arguments:** The actual values passed to the function when it is called (e.g., `"Alice"`, `5`, `3`).

### Default Parameters (ES6)

You can provide default values for parameters if no argument is passed.

```javascript
function greetUser(name = "Guest") { // "Guest" is the default value
  console.log(`Welcome, ${name}!`);
}

greetUser("Bob");   // Output: Welcome, Bob!
greetUser();        // Output: Welcome, Guest!
```

## The `return` Statement

Functions often compute a value. The `return` statement specifies the value the function should output. When `return` is executed, the function stops immediately.

```javascript
function calculateArea(width, height) {
  if (width <= 0 || height <= 0) {
    return "Invalid dimensions"; // Exit early if dimensions are invalid
  }
  const area = width * height;
  return area; // Return the calculated area
}

let roomArea = calculateArea(10, 5);
console.log(roomArea); // Output: 50

let invalidArea = calculateArea(-5, 10);
console.log(invalidArea); // Output: Invalid dimensions
```
If a function doesn't have a `return` statement, it implicitly returns `undefined`.

## Function Scope

Variables declared inside a function (using `let` or `const`) are local to that function. They cannot be accessed from outside.

```javascript
function testScope() {
  let localVar = "I am local";
  console.log(localVar); 
}

testScope(); // Output: I am local
// console.log(localVar); // Error: localVar is not defined outside the function
```
Functions can access variables defined outside of them (in parent scopes or globally).

## Exercise

1.  Create a file `functions_exercise.js`.
2.  Write a function declaration named `printInfo` that takes `name` and `age` as parameters and prints a message like "Name: [name], Age: [age]".
3.  Write a function expression assigned to a variable `calculateCircleArea` that takes a `radius` parameter and returns the area (Area = π * r^2). Use `Math.PI` for pi.
4.  Write an arrow function named `isEven` that takes a number and returns `true` if the number is even, and `false` otherwise.
5.  Call each of your functions with sample arguments and print the results.
6.  Modify `printInfo` to have a default age of `unknown`.

## Conclusion

Functions are a cornerstone of JavaScript programming, allowing you to create modular, reusable, and organized code. Mastering different ways to define and use them is crucial.

Next, we'll explore Arrays in more detail, including common methods for manipulating them.