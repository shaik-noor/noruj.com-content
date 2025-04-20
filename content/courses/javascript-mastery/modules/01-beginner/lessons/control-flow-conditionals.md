# Control Flow: Conditionals (Making Decisions)

Conditional statements allow your code to make decisions and execute different blocks of code based on whether a condition is true or false.

## The `if` Statement

The `if` statement executes a block of code only if a specified condition is true.

```javascript
let temperature = 25;

if (temperature > 20) {
  console.log("It's a warm day!"); 
}
// Output: It's a warm day!

if (temperature < 0) {
  console.log("It's freezing!"); // This block is skipped
}
```

## The `else` Statement

The `else` statement provides an alternative block of code to execute if the `if` condition is false.

```javascript
let age = 16;

if (age >= 18) {
  console.log("You are an adult.");
} else {
  console.log("You are a minor.");
}
// Output: You are a minor.
```

## The `else if` Statement

You can check multiple conditions in sequence using `else if`.

```javascript
let score = 75;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else if (score >= 70) {
  console.log("Grade: C"); // This condition is met first
} else if (score >= 60) {
  console.log("Grade: D");
} else {
  console.log("Grade: F");
}
// Output: Grade: C
```
JavaScript checks the conditions from top to bottom and executes the block for the *first* true condition it finds. The final `else` is optional and runs if none of the preceding `if` or `else if` conditions were true.

## The `switch` Statement

The `switch` statement is useful when you have a single value that you want to compare against multiple possible cases.

```javascript
let day = "Monday";
let message;

switch (day) {
  case "Monday":
    message = "Start of the work week.";
    break; // Important: exits the switch
  case "Friday":
    message = "Almost weekend!";
    break;
  case "Saturday":
  case "Sunday": // Multiple cases can share one block
    message = "It's the weekend!";
    break;
  default: // Optional: runs if no case matches
    message = "It's a regular day.";
}

console.log(message);
// Output: Start of the work week.
```
- The `break` statement is crucial. Without it, execution would "fall through" to the next case.
- The `default` case handles any value that doesn't match the other cases.

## Truthy and Falsy Values

In JavaScript, conditions don't strictly need to be `true` or `false`. Other values can be treated as `true` (truthy) or `false` (falsy) in a boolean context (like an `if` statement).

**Falsy values:**
- `false`
- `0` (zero)
- `""` (empty string)
- `null`
- `undefined`
- `NaN` (Not a Number)

**Truthy values:**
- Everything else, including non-empty strings (`"hello"`), non-zero numbers (`1`, `-1`), objects (`{}`), and arrays (`[]`).

```javascript
let userName = "";

if (userName) { // userName is an empty string (falsy)
  console.log(`Hello, ${userName}`);
} else {
  console.log("Please enter your name.");
}
// Output: Please enter your name.

let itemsInCart = 5;
if (itemsInCart) { // itemsInCart is 5 (truthy)
  console.log("Proceed to checkout.");
}
// Output: Proceed to checkout.
```

## Exercise

1.  Create a file `conditionals_exercise.js`.
2.  Declare a variable `hour` representing the current hour (0-23).
3.  Write an `if/else if/else` chain to print "Good morning", "Good afternoon", or "Good evening" based on the `hour`.
4.  Declare a variable `userRole` (e.g., "admin", "editor", "viewer").
5.  Write a `switch` statement to print the permissions for each role (e.g., "Full access" for admin, "Can edit content" for editor, "Can view content" for viewer, "Unknown role" for default).
6.  Declare a variable `inputValue` and assign it an empty string. Write an `if` statement that checks if `inputValue` is truthy and prints it, otherwise prints "Input is empty". Test with a non-empty string too.

## Conclusion

Conditional statements (`if`, `else if`, `else`, `switch`) are fundamental for controlling the flow of your program based on different conditions.

Next, we'll look at how to repeat blocks of code using loops.