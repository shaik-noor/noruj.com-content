# Objects in JavaScript

Objects are collections of key-value pairs. They are used to represent more complex entities and group related data and functionality together. Keys are usually strings (or Symbols), and values can be any data type (numbers, strings, booleans, arrays, even other objects or functions).

## Creating Objects

### Object Literal (Most Common)

```javascript
const emptyObject = {};

const person = {
  firstName: "John",
  lastName: "Doe",
  age: 30,
  isStudent: false,
  address: { // Nested object
    street: "123 Main St",
    city: "Anytown"
  },
  hobbies: ["reading", "hiking"] // Array as a value
};
```

## Accessing Properties

You can access the values (properties) of an object using either dot notation or bracket notation.

### Dot Notation

Simpler and more common when the key is a valid identifier (no spaces, doesn't start with a number, etc.).

```javascript
console.log(person.firstName); // Output: "John"
console.log(person.age);       // Output: 30
console.log(person.address.city); // Accessing nested object property
```

### Bracket Notation

Required when the key has special characters, spaces, or is stored in a variable. The key inside the brackets must be a string or a variable holding a string.

```javascript
console.log(person["lastName"]); // Output: "Doe"
console.log(person["address"]["street"]); // Output: "123 Main St"

// Using a variable for the key
let propertyName = "age";
console.log(person[propertyName]); // Output: 30

// Key with space (requires bracket notation)
const car = {
  "make and model": "Toyota Camry"
};
console.log(car["make and model"]); // Output: "Toyota Camry"
```

## Modifying Properties

Assign a new value to an existing property using dot or bracket notation.

```javascript
person.age = 31;
person["isStudent"] = true;
person.address.city = "New City";

console.log(person.age); // Output: 31
console.log(person.isStudent); // Output: true
```

## Adding New Properties

Assign a value to a new key using dot or bracket notation.

```javascript
person.email = "john.doe@example.com";
person["phone number"] = "555-1234";

console.log(person.email); // Output: "john.doe@example.com"
```

## Deleting Properties

Use the `delete` operator.

```javascript
delete person.isStudent;
delete person["phone number"];

console.log(person.isStudent); // Output: undefined
```

## Methods: Functions in Objects

When a function is a property of an object, it's called a method. Methods can perform actions related to the object.

```javascript
const calculator = {
  add: function(a, b) {
    return a + b;
  },
  subtract(a, b) { // Shorthand method syntax (ES6)
    return a - b;
  }
};

console.log(calculator.add(5, 3));      // Output: 8
console.log(calculator.subtract(10, 4)); // Output: 6
```

## The `this` Keyword

Inside a method, the `this` keyword refers to the object the method was called on. It allows a method to access other properties of the *same* object.

```javascript
const user = {
  name: "Alice",
  balance: 100,
  greet() {
    // 'this' refers to the 'user' object
    console.log(`Hello, my name is ${this.name}.`);
  },
  deposit(amount) {
    this.balance += amount;
    console.log(`Deposited ${amount}. New balance: ${this.balance}`);
  }
};

user.greet();    // Output: Hello, my name is Alice.
user.deposit(50); // Output: Deposited 50. New balance: 150
```
**Note:** The behavior of `this` can be complex, especially with arrow functions and different ways functions are called. We'll explore it more later.

## Iterating Over Object Properties

Unlike arrays, objects don't have a built-in order. Here are common ways to loop through an object's properties:

### `for...in` Loop

Iterates over the *keys* (property names) of an object.

```javascript
const book = {
  title: "The Great Gatsby",
  author: "F. Scott Fitzgerald",
  year: 1925
};

for (const key in book) {
  // It's good practice to check if the property belongs directly to the object
  if (Object.hasOwnProperty.call(book, key)) { 
    console.log(`${key}: ${book[key]}`);
  }
}
// Output:
// title: The Great Gatsby
// author: F. Scott Fitzgerald
// year: 1925
```

### `Object.keys(obj)`

Returns an array of an object's own property *keys*.

```javascript
const keys = Object.keys(book);
console.log(keys); // Output: ["title", "author", "year"]

keys.forEach(key => {
  console.log(`${key}: ${book[key]}`);
});
```

### `Object.values(obj)`

Returns an array of an object's own property *values*.

```javascript
const values = Object.values(book);
console.log(values); // Output: ["The Great Gatsby", "F. Scott Fitzgerald", 1925]
```

### `Object.entries(obj)`

Returns an array of an object's own `[key, value]` pairs.

```javascript
const entries = Object.entries(book);
console.log(entries);
// Output: [ ["title", "The Great Gatsby"], ["author", "F. Scott Fitzgerald"], ["year", 1925] ]

for (const [key, value] of entries) {
  console.log(`${key} -> ${value}`);
}
```

## Exercise

1.  Create a file `objects_exercise.js`.
2.  Create an object `movie` with properties `title`, `director`, `releaseYear`, and `rating`.
3.  Print the `title` and `director` using dot notation.
4.  Print the `releaseYear` using bracket notation.
5.  Add a new property `genre` to the object.
6.  Change the `rating` to a new value.
7.  Add a method `getSummary` to the object that returns a string like "[title] directed by [director], released in [releaseYear].". Use `this` inside the method.
8.  Call the `getSummary` method and print the result.
9.  Use `Object.keys()` and `forEach` to print all the property names of the `movie` object.
10. Use a `for...in` loop to print all key-value pairs of the `movie` object.

## Conclusion

Objects are essential for modeling real-world things or complex data structures in JavaScript. They allow you to group related data and functions, making your code more organized and representative.

Next, we'll look at the Document Object Model (DOM) and how JavaScript interacts with HTML.