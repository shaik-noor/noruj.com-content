# Variables and Data Types

Variables are used to store information in your code. Think of them as named containers for data.

## Declaring Variables

In modern JavaScript, we use `let` and `const` to declare variables.

- **`let`**: Use for variables whose value might change later.
  ```javascript
  let score = 10;
  score = 15; // Value can be updated
  ```
- **`const`**: Use for variables whose value should not change (constants).
  ```javascript
  const name = "Alice";
  // name = "Bob"; // This would cause an error
  ```

**Naming Rules:**
- Start with a letter, underscore (`_`), or dollar sign (`$`).
- Can contain letters, numbers, `_`, or `$`. 
- Case-sensitive (`age` is different from `Age`).
- Use descriptive names (e.g., `userName` instead of `u`).
- Convention: Use `camelCase` (e.g., `firstName`).

## Basic Data Types

JavaScript has several fundamental data types:

1.  **Number**: Represents numeric values (integers and decimals).
    ```javascript
    let age = 30;
    let price = 9.99;
    ```
2.  **String**: Represents text. Enclose in single (`'`) or double (`"`) quotes, or backticks (`` ` ``).
    ```javascript
    let greeting = "Hello";
    let message = 'World';
    let template = `User: ${name}`; // Backticks allow embedding variables
    ```
3.  **Boolean**: Represents logical values: `true` or `false`.
    ```javascript
    let isActive = true;
    let isLoggedIn = false;
    ```
4.  **Undefined**: A variable that has been declared but not assigned a value.
    ```javascript
    let city;
    console.log(city); // Output: undefined
    ```
5.  **Null**: Represents the intentional absence of any value.
    ```javascript
    let selectedItem = null;
    ```

## Complex Data Types

1.  **Object**: A collection of key-value pairs. Used to group related data.
    ```javascript
    const person = {
      firstName: "Bob",
      lastName: "Smith",
      age: 42
    };
    console.log(person.firstName); // Access value using dot notation
    ```
2.  **Array**: An ordered list of values. Used for collections.
    ```javascript
    const colors = ["red", "green", "blue"];
    console.log(colors[0]); // Access value by index (starts at 0)
    console.log(colors.length); // Get the number of items
    ```

## Checking Data Types

The `typeof` operator tells you the type of a variable:

```javascript
console.log(typeof 100);       // "number"
console.log(typeof "Hello");   // "string"
console.log(typeof true);      // "boolean"
console.log(typeof undefined); // "undefined"
console.log(typeof null);      // "object" (a known quirk in JS)
console.log(typeof {});        // "object"
console.log(typeof []);        // "object" (arrays are objects)
```

## Type Conversion

Sometimes you need to convert between types:

```javascript
// String to Number
let scoreText = "100";
let scoreNumber = Number(scoreText);
console.log(scoreNumber + 5); // 105

// Number to String
let value = 50;
let valueText = String(value);
console.log(valueText + " points"); // "50 points"
```

## Exercise

1.  Create a file named `my_variables.js`.
2.  Declare a `const` variable for your name and a `let` variable for your age.
3.  Declare a boolean variable `isLearning` and set it to `true`.
4.  Create an array `hobbies` with a few strings representing your hobbies.
5.  Create an object `details` with keys `city` and `country`.
6.  Use `console.log()` to print each variable and its type using `typeof`.
7.  Try changing the value of your age variable and print it again.

## Conclusion

Variables and data types are the foundation of storing and manipulating information in JavaScript. Understanding them is crucial for writing any program.

Next, we'll learn about operators to perform actions with these variables.
