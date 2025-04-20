# Arrays in JavaScript

Arrays are ordered lists of values. They can hold values of any type (numbers, strings, booleans, objects, even other arrays) and are one of the most commonly used data structures in JavaScript.

## Creating Arrays

### Array Literal (Most Common)

```javascript
const emptyArray = [];
const fruits = ["apple", "banana", "cherry"];
const numbers = [1, 5, 10, 15];
const mixed = [1, "hello", true, null, { name: "John" }];
```

## Accessing Elements

Array elements are accessed using zero-based index numbers inside square brackets `[]`.

```javascript
const colors = ["red", "green", "blue"];

console.log(colors[0]); // Output: "red" (first element)
console.log(colors[1]); // Output: "green" (second element)
console.log(colors[2]); // Output: "blue" (third element)

console.log(colors.length); // Output: 3 (number of elements)

// Accessing the last element
console.log(colors[colors.length - 1]); // Output: "blue"

// Accessing non-existent index
console.log(colors[3]); // Output: undefined
```

## Modifying Elements

You can change the value of an element by assigning a new value to its index.

```javascript
const letters = ["a", "b", "c"];
letters[1] = "B"; // Change the second element
console.log(letters); // Output: ["a", "B", "c"]
```

## Common Array Methods

Arrays come with many built-in methods to perform common operations.

### Adding/Removing Elements

-   `push(item1, ...)`: Adds one or more elements to the **end** of the array. Returns the new length.
    ```javascript
    const pets = ["cat", "dog"];
    pets.push("hamster");
    console.log(pets); // Output: ["cat", "dog", "hamster"]
    ```
-   `pop()`: Removes the **last** element from the array. Returns the removed element.
    ```javascript
    const pets = ["cat", "dog", "hamster"];
    const removedPet = pets.pop();
    console.log(pets); // Output: ["cat", "dog"]
    console.log(removedPet); // Output: "hamster"
    ```
-   `unshift(item1, ...)`: Adds one or more elements to the **beginning** of the array. Returns the new length.
    ```javascript
    const pets = ["cat", "dog"];
    pets.unshift("fish");
    console.log(pets); // Output: ["fish", "cat", "dog"]
    ```
-   `shift()`: Removes the **first** element from the array. Returns the removed element.
    ```javascript
    const pets = ["fish", "cat", "dog"];
    const removedPet = pets.shift();
    console.log(pets); // Output: ["cat", "dog"]
    console.log(removedPet); // Output: "fish"
    ```

### Other Useful Methods

-   `slice(startIndex, endIndex)`: Returns a **new** array containing a portion of the original array. It does *not* modify the original array. `endIndex` is optional and exclusive.
    ```javascript
    const letters = ["a", "b", "c", "d", "e"];
    const middle = letters.slice(1, 4); // Elements from index 1 up to (not including) 4
    console.log(middle); // Output: ["b", "c", "d"]
    console.log(letters); // Output: ["a", "b", "c", "d", "e"] (original unchanged)
    ```
-   `splice(startIndex, deleteCount, item1, ...)`: **Modifies** the original array by removing, replacing, or adding elements. Returns an array containing the deleted elements.
    ```javascript
    const months = ["Jan", "March", "April", "June"];
    // Insert "Feb" at index 1
    months.splice(1, 0, "Feb"); 
    console.log(months); // Output: ["Jan", "Feb", "March", "April", "June"]
    
    // Remove 1 element at index 3 ("April")
    const removed = months.splice(3, 1);
    console.log(months); // Output: ["Jan", "Feb", "March", "June"]
    console.log(removed); // Output: ["April"]
    ```
-   `indexOf(item)`: Returns the first index at which a given element can be found, or -1 if it is not present.
    ```javascript
    const tools = ["hammer", "wrench", "screwdriver"];
    console.log(tools.indexOf("wrench")); // Output: 1
    console.log(tools.indexOf("pliers")); // Output: -1
    ```
-   `includes(item)`: Returns `true` if an array includes a certain value, `false` otherwise. (More readable than `indexOf !== -1`).
    ```javascript
    const tools = ["hammer", "wrench", "screwdriver"];
    console.log(tools.includes("wrench")); // Output: true
    console.log(tools.includes("pliers")); // Output: false
    ```
-   `join(separator)`: Joins all elements of an array into a string. A separator string can be specified.
    ```javascript
    const parts = ["Header", "Body", "Footer"];
    const html = parts.join("\n"); // Join with newline character
    console.log(html);
    // Output:
    // Header
    // Body
    // Footer
    ```

## Iterating Over Arrays

Common ways to loop through array elements:

-   **`for` loop:** (Covered in Loops lesson)
-   **`for...of` loop:** (Covered in Loops lesson)
-   **`forEach(callbackFn)`:** Executes a function for each element.
    ```javascript
    const prices = [10, 20, 30];
    prices.forEach(function(price, index) {
      console.log(`Item ${index + 1} costs $${price}`);
    });
    // Output:
    // Item 1 costs $10
    // Item 2 costs $20
    // Item 3 costs $30
    ```

## Powerful Iteration Methods (Functional Approach)

These methods take a callback function and often return a new array, promoting immutability (not changing the original array).

-   **`map(callbackFn)`:** Creates a **new** array by calling a function on every element in the original array and collecting the results.
    ```javascript
    const numbers = [1, 2, 3, 4];
    const doubled = numbers.map(function(num) {
      return num * 2;
    });
    console.log(doubled); // Output: [2, 4, 6, 8]
    console.log(numbers); // Output: [1, 2, 3, 4] (original unchanged)
    ```
-   **`filter(callbackFn)`:** Creates a **new** array with all elements that pass the test implemented by the provided function (the callback should return `true` or `false`).
    ```javascript
    const numbers = [1, 2, 3, 4, 5, 6];
    const evenNumbers = numbers.filter(function(num) {
      return num % 2 === 0;
    });
    console.log(evenNumbers); // Output: [2, 4, 6]
    ```
-   **`find(callbackFn)`:** Returns the **first element** in the array that satisfies the provided testing function. Otherwise `undefined` is returned.
    ```javascript
    const users = [{id: 1, name: "Alice"}, {id: 2, name: "Bob"}, {id: 3, name: "Charlie"}];
    const bob = users.find(function(user) {
      return user.name === "Bob";
    });
    console.log(bob); // Output: {id: 2, name: "Bob"}
    ```
-   **`reduce(callbackFn, initialValue)`:** Executes a reducer function on each element, resulting in a single output value (e.g., sum, accumulated object).
    ```javascript
    const numbers = [1, 2, 3, 4, 5];
    const sum = numbers.reduce(function(accumulator, currentValue) {
      return accumulator + currentValue;
    }, 0); // 0 is the initial value for the accumulator
    console.log(sum); // Output: 15
    ```

## Exercise

1.  Create a file `arrays_exercise.js`.
2.  Create an array `tasks` with a few initial string values (e.g., "Learn JavaScript", "Practice Coding").
3.  Add a new task "Read Documentation" to the end of the array using `push`.
4.  Remove the first task from the array using `shift`.
5.  Add a new task "Ask Questions" to the beginning using `unshift`.
6.  Find the index of "Practice Coding" using `indexOf`.
7.  Create a new array `importantTasks` containing the first two tasks from the `tasks` array using `slice`.
8.  Use `forEach` to print each task in the `tasks` array, prefixed with "Task: ".
9.  Create an array of numbers. Use `map` to create a new array where each number is squared.
10. Create an array of numbers. Use `filter` to create a new array containing only numbers greater than 10.
11. Create an array of numbers. Use `reduce` to calculate the product of all numbers in the array.

## Conclusion

Arrays are versatile and fundamental in JavaScript. Understanding how to create, access, modify, and iterate over them using various methods is essential for almost any JavaScript application.

Next, we'll dive deeper into Objects.