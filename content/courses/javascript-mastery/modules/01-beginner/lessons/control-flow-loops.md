# Control Flow: Loops (Repeating Actions)

Loops allow you to execute a block of code repeatedly, which is useful for tasks like processing lists of items or running code a specific number of times.

## The `for` Loop

The `for` loop is commonly used when you know how many times you want the loop to run.

It has three parts, separated by semicolons:
1.  **Initialization:** Runs once before the loop starts (e.g., `let i = 0`).
2.  **Condition:** Checked before each iteration. If `true`, the loop continues; if `false`, it stops.
3.  **Final Expression:** Runs after each iteration (e.g., `i++`).

```javascript
// Print numbers 0 to 4
for (let i = 0; i < 5; i++) {
  console.log(`Iteration number: ${i}`);
}
// Output:
// Iteration number: 0
// Iteration number: 1
// Iteration number: 2
// Iteration number: 3
// Iteration number: 4

// Looping through an array
const colors = ["red", "green", "blue"];
for (let i = 0; i < colors.length; i++) {
  console.log(`Color: ${colors[i]}`);
}
// Output:
// Color: red
// Color: green
// Color: blue
```

## The `while` Loop

The `while` loop executes a block of code as long as a specified condition remains true. The condition is checked *before* each iteration.

```javascript
let count = 0;

while (count < 3) {
  console.log(`Count is: ${count}`);
  count++;
}
// Output:
// Count is: 0
// Count is: 1
// Count is: 2
```
**Caution:** Be careful with `while` loops. If the condition never becomes false, you'll create an infinite loop, which can crash your browser or program.

## The `do...while` Loop

The `do...while` loop is similar to `while`, but the condition is checked *after* the code block executes. This guarantees the code block runs at least once, even if the condition is initially false.

```javascript
let attempts = 0;

do {
  console.log(`Attempt number: ${attempts + 1}`);
  attempts++;
} while (attempts < 3);
// Output:
// Attempt number: 1
// Attempt number: 2
// Attempt number: 3

// Example where it runs once even if condition is false initially
let num = 5;
do {
    console.log("This runs once.");
    num++;
} while (num < 5);
// Output: This runs once.
```

## Looping Through Arrays (Modern Methods)

While `for` loops work for arrays, JavaScript provides more modern and often cleaner ways:

### `for...of` Loop

Iterates directly over the *values* of an iterable object (like an array).

```javascript
const fruits = ["apple", "banana", "cherry"];

for (const fruit of fruits) {
  console.log(`Fruit: ${fruit}`);
}
// Output:
// Fruit: apple
// Fruit: banana
// Fruit: cherry
```

### `forEach` Method

Executes a provided function once for each array element.

```javascript
const numbers = [1, 2, 3];

numbers.forEach(function(number, index) {
  console.log(`Index ${index}: ${number}`);
});
// Output:
// Index 0: 1
// Index 1: 2
// Index 2: 3
```

## `break` and `continue`

-   **`break`**: Immediately exits the current loop.
-   **`continue`**: Skips the rest of the current iteration and proceeds to the next one.

```javascript
// Using break to find the first number greater than 5
const values = [1, 3, 7, 4, 9, 2];
for (const value of values) {
  if (value > 5) {
    console.log(`Found first number > 5: ${value}`);
    break; // Exit the loop
  }
  console.log(`Checking ${value}...`);
}
// Output:
// Checking 1...
// Checking 3...
// Found first number > 5: 7

// Using continue to skip odd numbers
for (let i = 0; i < 5; i++) {
  if (i % 2 !== 0) { // If i is odd
    continue; // Skip to the next iteration
  }
  console.log(`Even number: ${i}`);
}
// Output:
// Even number: 0
// Even number: 2
// Even number: 4
```

## Exercise

1.  Create a file `loops_exercise.js`.
2.  Use a `for` loop to print numbers from 1 to 10.
3.  Use a `while` loop to print numbers from 10 down to 1.
4.  Create an array of your favorite foods.
5.  Use a `for...of` loop to print each food item from the array.
6.  Use the `forEach` method to print each food item along with its index in the array.
7.  Write a loop that prints numbers from 1 to 10, but use `continue` to skip number 5, and use `break` to exit the loop if the number is greater than 8.

## Conclusion

Loops are essential for automating repetitive tasks and processing collections of data like arrays. Choosing the right type of loop (`for`, `while`, `do...while`, `for...of`, `forEach`) can make your code more readable and efficient.

Next, we'll learn how to group code into reusable blocks called Functions.