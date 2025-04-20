# Closures and Scope

Closures are one of the most powerful and often misunderstood features in JavaScript. Understanding closures requires a solid grasp of how scope works in JavaScript.

## Scope in JavaScript

Scope defines where variables are accessible in your code. JavaScript has several types of scope:

### Global Scope

Variables declared outside of any function or block are in the global scope and can be accessed from anywhere.

```javascript
const globalVariable = "I'm global";

function someFunction() {
  console.log(globalVariable); // Accessible here
}

if (true) {
  console.log(globalVariable); // Also accessible here
}
```

### Function Scope

Variables declared within a function are only accessible inside that function and any nested functions.

```javascript
function outerFunction() {
  const functionScoped = "I'm function scoped";
  
  console.log(functionScoped); // Accessible
  
  function innerFunction() {
    console.log(functionScoped); // Also accessible
  }
  
  innerFunction();
}

outerFunction();
// console.log(functionScoped); // Error: functionScoped is not defined
```

### Block Scope

Variables declared with `let` and `const` within a block (between `{}`) are only accessible within that block.

```javascript
if (true) {
  const blockScoped = "I'm block scoped";
  let anotherBlockScoped = "Me too";
  
  console.log(blockScoped); // Accessible
}

// console.log(blockScoped); // Error: blockScoped is not defined
```

## Understanding Closures

A closure is formed when a function retains access to variables from its outer (enclosing) scope, even after the outer function has finished executing.

### Basic Closure Example

```javascript
function createCounter() {
  let count = 0; // This variable is "enclosed" in the closure
  
  function increment() {
    count++; // Accessing the enclosed variable
    return count;
  }
  
  return increment;
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3

// Each call to counter() remembers and updates the count variable
// even though createCounter() finished executing long ago
```

In this example, `count` continues to exist and maintain its state because the `increment` function forms a closure over it.

### Multiple Closures

Each function call creates a new closure with its own enclosed variables:

```javascript
function createCounter() {
  let count = 0;
  
  return function() {
    count++;
    return count;
  };
}

const counter1 = createCounter();
const counter2 = createCounter();

console.log(counter1()); // 1
console.log(counter1()); // 2
console.log(counter2()); // 1 (separate count variable in a different closure)
console.log(counter1()); // 3
```

### Practical Uses of Closures

#### 1. Data Privacy / Encapsulation

Closures allow you to create private variables that can't be accessed directly:

```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable
  
  return {
    deposit: function(amount) {
      if (amount > 0) {
        balance += amount;
        return `Deposited ${amount}. New balance: ${balance}`;
      }
      return "Invalid deposit amount";
    },
    withdraw: function(amount) {
      if (amount > 0 && amount <= balance) {
        balance -= amount;
        return `Withdrew ${amount}. New balance: ${balance}`;
      }
      return "Invalid withdrawal or insufficient funds";
    },
    getBalance: function() {
      return `Current balance: ${balance}`;
    }
  };
}

const account = createBankAccount(100);
console.log(account.getBalance()); // "Current balance: 100"
console.log(account.withdraw(30)); // "Withdrew 30. New balance: 70"
console.log(account.deposit(50));  // "Deposited 50. New balance: 120"
// console.log(account.balance);   // undefined - can't access balance directly
```

#### 2. Factory Functions

Functions that create and return objects with methods that maintain access to the factory's scope:

```javascript
function createPerson(name) {
  let age = 0; // Private variable
  
  return {
    getName: function() {
      return name;
    },
    getAge: function() {
      return age;
    },
    setAge: function(newAge) {
      if (newAge >= 0) {
        age = newAge;
      }
    },
    celebrateBirthday: function() {
      age++;
      return `Happy birthday, ${name}! You are now ${age} years old.`;
    }
  };
}

const john = createPerson("John");
john.setAge(30);
console.log(john.getName()); // "John"
console.log(john.getAge());  // 30
console.log(john.celebrateBirthday()); // "Happy birthday, John! You are now 31 years old."
```

#### 3. Memoization (Caching Function Results)

Closures can store previous results to avoid repeating expensive operations:

```javascript
function createMemoizedFunction(func) {
  const cache = {}; // Private cache in closure
  
  return function(arg) {
    if (cache[arg] !== undefined) {
      console.log(`Result for ${arg} retrieved from cache`);
      return cache[arg];
    }
    
    const result = func(arg);
    cache[arg] = result;
    console.log(`Result for ${arg} calculated and cached`);
    return result;
  };
}

// Example: expensive calculation
function calculateSquare(n) {
  console.log(`Calculating square of ${n}...`);
  // Simulating expensive operation
  let result = 0;
  for (let i = 1; i <= 1000000; i++) {
    result = n * n;
  }
  return result;
}

const memoizedSquare = createMemoizedFunction(calculateSquare);

console.log(memoizedSquare(5)); // Calculates and caches
console.log(memoizedSquare(5)); // Retrieved from cache
console.log(memoizedSquare(10)); // Calculates and caches
```

#### 4. Event Handlers with Data

Closures allow you to create event handlers with access to specific data:

```javascript
function setupButtonHandler(buttonId, message) {
  const button = document.getElementById(buttonId);
  
  button.addEventListener('click', function() {
    // This function is a closure that "remembers" the message
    alert(message);
  });
}

setupButtonHandler('btn1', 'Button 1 was clicked');
setupButtonHandler('btn2', 'Button 2 was clicked');
```

## Common Closure Pitfalls

### Loop Variables in Closures

A classic issue is using closures inside loops with variables that change:

```javascript
// Incorrect - all functions share the same 'i'
function createFunctions() {
  const functions = [];
  
  for (var i = 0; i < 3; i++) {
    functions.push(function() {
      console.log(i); // Will log the final value of i (3) for all functions
    });
  }
  
  return functions;
}

const fns = createFunctions();
fns[0](); // 3
fns[1](); // 3
fns[2](); // 3
```

Solutions:

```javascript
// Solution 1: Use let instead of var (block scope)
function createFunctionsFixed1() {
  const functions = [];
  
  for (let i = 0; i < 3; i++) {
    functions.push(function() {
      console.log(i); // Each iteration creates a new block-scoped i
    });
  }
  
  return functions;
}

// Solution 2: Use an IIFE (Immediately Invoked Function Expression)
function createFunctionsFixed2() {
  const functions = [];
  
  for (var i = 0; i < 3; i++) {
    functions.push((function(capturedI) {
      return function() {
        console.log(capturedI);
      };
    })(i)); // Immediately invoke with current i value
  }
  
  return functions;
}

const fns1 = createFunctionsFixed1();
fns1[0](); // 0
fns1[1](); // 1
fns1[2](); // 2
```

### Memory Leaks

Closures can potentially cause memory leaks if they keep references to large objects:

```javascript
function createLargeObjectHandler() {
  // Large object that would normally be garbage collected
  const largeData = new Array(1000000).fill('data');
  
  return function() {
    // This function keeps a reference to largeData
    console.log(largeData[0]);
  };
}

// Solution: Only keep what you need
function optimizedHandler() {
  // Large object
  const largeData = new Array(1000000).fill('data');
  // Extract only what you need
  const firstItem = largeData[0];
  
  return function() {
    // Only referencing a small piece of data
    console.log(firstItem);
  };
}
```

## Lexical vs. Dynamic Scope

JavaScript uses lexical (static) scoping, which means that function access to variables is determined by where the function is defined, not where it's called:

```javascript
function createFunction() {
  const name = 'Local name';
  
  return function displayName() {
    console.log(name); // Uses name from createFunction's scope
  };
}

const name = 'Global name';
const display = createFunction();
display(); // Logs "Local name", not "Global name"
```

## Exercise: Advanced Closures

1. Create a function `createPasswordValidator(minLength)` that returns a function which validates if a given password meets these criteria:
   - At least `minLength` characters long
   - Contains at least one uppercase letter
   - Contains at least one number
   - The function should return an object with a `valid` boolean and a `message` string.

2. Create a `createCalculator` function that returns an object with methods for basic math operations (add, subtract, multiply, divide). The calculator should maintain a running total that can be accessed or reset.

3. Implement a `createCounter` function that accepts min, max, and step parameters. It should return an object with `increment`, `decrement`, `getValue`, and `reset` methods. The counter should not go below min or above max.

## Conclusion

Closures are a powerful JavaScript feature that allows functions to maintain access to their outer scope even after the outer function has completed. They are fundamental to many JavaScript patterns and techniques, including data privacy, factory functions, memoization, and more.

By mastering closures, you gain the ability to write more elegant, efficient, and powerful JavaScript code.

Next, we'll dive into prototypes and inheritance, another advanced but essential JavaScript topic.