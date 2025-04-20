# Error Handling and Debugging

Every developer encounters errors in their code. Understanding how to properly handle errors and debug effectively is crucial for writing robust JavaScript applications.

## Types of Errors

JavaScript has several built-in error types:

### SyntaxError
Occurs when your code has incorrect syntax.

```javascript
// Missing closing parenthesis
console.log("Hello World";
// SyntaxError: missing ) after argument list
```

### ReferenceError
Occurs when you try to use a variable that doesn't exist.

```javascript
console.log(undefinedVariable);
// ReferenceError: undefinedVariable is not defined
```

### TypeError
Occurs when an operation is performed on a value of the wrong type.

```javascript
const name = "John";
name.toFixed(2); // TypeError: name.toFixed is not a function
```

### RangeError
Occurs when a numeric value is outside the range of allowed values.

```javascript
const arr = new Array(-1); // RangeError: Invalid array length
```

### URIError
Occurs when using global URI handling functions incorrectly.

```javascript
decodeURIComponent('%'); // URIError: URI malformed
```

### EvalError
Historically occurred with `eval()` issues but is rarely used in modern JavaScript.

## Try...Catch Statement

The `try...catch` statement allows you to "try" running some code and "catch" potential errors.

```javascript
try {
  // Code that might throw an error
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  // Code to handle the error
  console.error("An error occurred:", error.message);
}
```

### The Error Object

When an error is caught, the `catch` block receives an `Error` object with these properties:

- `name`: The error type (e.g., "SyntaxError", "TypeError")
- `message`: A human-readable description of the error
- `stack`: A stack trace showing where the error occurred (non-standard but widely supported)

```javascript
try {
  throw new Error("Something went wrong!");
} catch (error) {
  console.log(error.name);     // "Error"
  console.log(error.message);  // "Something went wrong!"
  console.log(error.stack);    // Shows the stack trace
}
```

### Finally Block

The `finally` block executes regardless of whether an error occurred.

```javascript
function processFile() {
  let file = openFile();
  
  try {
    // Process the file
    return "Processing complete";
  } catch (error) {
    console.error("Error processing file:", error);
    return "Processing failed";
  } finally {
    // This will execute even if there's a return statement above
    file.close(); // Always close the file
  }
}
```

### Nesting Try...Catch

You can nest try...catch blocks for more granular error handling:

```javascript
try {
  try {
    throw new Error("Inner error");
  } catch (innerError) {
    console.log("Inner catch:", innerError.message);
    throw innerError; // Re-throw the error
  }
} catch (outerError) {
  console.log("Outer catch:", outerError.message);
}
```

## Throwing Custom Errors

You can throw your own errors using the `throw` statement:

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error("Division by zero is not allowed");
  }
  return a / b;
}

try {
  const result = divide(10, 0);
  console.log(result);
} catch (error) {
  console.error("Error:", error.message); // "Error: Division by zero is not allowed"
}
```

### Custom Error Types

You can create your own error classes by extending the built-in `Error` class:

```javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

class DatabaseError extends Error {
  constructor(message, code) {
    super(message);
    this.name = "DatabaseError";
    this.code = code;
  }
}

// Using custom errors
function fetchUser(id) {
  if (!id) {
    throw new ValidationError("User ID is required");
  }
  
  const user = database.find(id);
  if (!user) {
    throw new DatabaseError("User not found", 404);
  }
  
  return user;
}

try {
  const user = fetchUser(null);
} catch (error) {
  if (error instanceof ValidationError) {
    console.log("Validation issue:", error.message);
  } else if (error instanceof DatabaseError) {
    console.log(`Database error (${error.code}):`, error.message);
  } else {
    console.log("Unknown error:", error);
  }
}
```

## Error Handling Strategies

### 1. Returning Error Objects

For synchronous functions, you can return an object indicating success or failure:

```javascript
function findUser(id) {
  if (!id) {
    return { success: false, error: "ID is required" };
  }
  
  // Find user logic
  const user = { id: id, name: "User " + id };
  return { success: true, data: user };
}

const result = findUser(5);
if (result.success) {
  console.log("User found:", result.data);
} else {
  console.log("Error:", result.error);
}
```

### 2. Error-First Callbacks

In callback-based APIs, the first parameter is typically reserved for an error:

```javascript
function readFile(path, callback) {
  fs.readFile(path, 'utf8', (err, data) => {
    if (err) {
      callback(err, null); // Pass the error as the first argument
    } else {
      callback(null, data); // No error, pass null as first argument
    }
  });
}

readFile('file.txt', (error, data) => {
  if (error) {
    console.error("Error reading file:", error);
    return;
  }
  console.log("File content:", data);
});
```

### 3. Promise Rejection

For asynchronous operations, use Promise rejection:

```javascript
function fetchData(url) {
  return fetch(url)
    .then(response => {
      if (!response.ok) {
        throw new Error(`HTTP error! Status: ${response.status}`);
      }
      return response.json();
    })
    .catch(error => {
      console.error("Fetch error:", error);
      throw error; // Re-throw for the caller to handle
    });
}
```

### 4. Async/Await with Try...Catch

Combining async/await with try...catch provides clean error handling:

```javascript
async function getUserData(userId) {
  try {
    const response = await fetch(`https://api.example.com/users/${userId}`);
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error("Error fetching user data:", error);
    // You can re-throw, return a default value, or handle differently
    return { error: true, message: error.message };
  }
}
```

## Global Error Handling

### Uncaught Exceptions

For browser environments, you can set up a global handler for uncaught exceptions:

```javascript
window.addEventListener('error', function(event) {
  console.error('Global error handler:', event.error);
  // You can log to a server, show a user-friendly message, etc.
  // Optionally prevent the browser from showing its error console:
  // event.preventDefault();
});
```

### Unhandled Promise Rejections

For unhandled promise rejections:

```javascript
window.addEventListener('unhandledrejection', function(event) {
  console.error('Unhandled promise rejection:', event.reason);
  // You can log to a server, show a user-friendly message, etc.
  // Optionally prevent the default handling:
  // event.preventDefault();
});
```

## Debugging Techniques

### 1. `console` Methods

The `console` object provides various methods for debugging:

```javascript
// Basic logging
console.log("Regular message");
console.info("Informational message");
console.warn("Warning message");
console.error("Error message");

// Structured data
console.table([{ name: "John", age: 30 }, { name: "Jane", age: 25 }]);

// Grouping related messages
console.group("User Details");
console.log("Name: John");
console.log("Age: 30");
console.groupEnd();

// Timing operations
console.time("Operation");
// ... some operation
console.timeEnd("Operation"); // Outputs: "Operation: 123ms"

// Conditional logging
console.assert(1 === 2, "This will show because the assertion fails");

// Stack trace
console.trace("Where am I called from?");
```

### 2. Breakpoints in DevTools

Chrome DevTools and other browser developer tools allow you to set breakpoints:

1. Open DevTools (F12 or Right-click > Inspect)
2. Go to the Sources tab
3. Find your JavaScript file
4. Click on the line number where you want to pause execution

#### Types of Breakpoints:
- **Line breakpoints**: Pause at a specific line
- **Conditional breakpoints**: Pause only when a condition is true
- **DOM breakpoints**: Pause when DOM elements change
- **XHR/fetch breakpoints**: Pause when network requests occur
- **Event listener breakpoints**: Pause on specific events

### 3. debugger Statement

The `debugger` statement creates a breakpoint in your code:

```javascript
function calculateTotal(items) {
  let total = 0;
  for (const item of items) {
    debugger; // Execution will pause here when DevTools is open
    total += item.price * item.quantity;
  }
  return total;
}
```

### 4. Performance Profiling

To identify performance bottlenecks:

1. In Chrome DevTools, go to the Performance tab
2. Click the record button
3. Perform the actions you want to measure
4. Stop recording and analyze the results

### 5. Memory Leak Detection

To find memory leaks:

1. In Chrome DevTools, go to the Memory tab
2. Take a heap snapshot
3. Perform actions that might cause leaks
4. Take another snapshot
5. Compare snapshots to identify retained objects

## Common Debugging Scenarios

### 1. Undefined is not a function

```javascript
// Error: TypeError: undefined is not a function
someObject.nonExistentMethod();

// Fix: Check if the method exists before calling
if (typeof someObject.nonExistentMethod === 'function') {
  someObject.nonExistentMethod();
}
```

### 2. Cannot read property of undefined/null

```javascript
// Error: TypeError: Cannot read property 'name' of undefined
const user = undefined;
console.log(user.name);

// Fix: Use optional chaining (ES2020)
console.log(user?.name); // Returns undefined instead of throwing an error

// Or use a logical AND check
console.log(user && user.name);
```

### 3. Handling Asynchronous Debugging

```javascript
// Difficult to debug
fetchData()
  .then(processData)
  .then(displayData)
  .catch(handleError);

// Better for debugging: add console.log at each step
fetchData()
  .then(data => {
    console.log('Fetched data:', data);
    return processData(data);
  })
  .then(processed => {
    console.log('Processed data:', processed);
    return displayData(processed);
  })
  .catch(error => {
    console.error('Error in chain:', error);
    handleError(error);
  });
```

## Exercise: Error Handling Practice

Create a form validation function that demonstrates proper error handling:

```javascript
/*
1. Create a user registration validator with these rules:
   - Username: Must be 3-20 characters with no special symbols except underscores
   - Email: Must be a valid email format
   - Password: At least 8 characters with at least one number and one uppercase letter
   - Age: Must be a number between 18 and 100

2. Create a custom ValidationError class

3. Implement the validator function that throws appropriate errors

4. Write a function that uses try/catch to handle the validation
*/

// Example starter code:
class ValidationError extends Error {
  constructor(field, message) {
    super(message);
    this.name = "ValidationError";
    this.field = field;
  }
}

function validateUser(userData) {
  // Implement validation logic here
  // Throw ValidationError with appropriate messages when validation fails
}

function registerUser(userData) {
  try {
    validateUser(userData);
    // If valid, proceed with registration
    console.log("User registration successful!");
    return true;
  } catch (error) {
    if (error instanceof ValidationError) {
      console.error(`Validation error in ${error.field}: ${error.message}`);
    } else {
      console.error("Registration failed:", error);
    }
    return false;
  }
}

// Test the validation
const user = {
  username: "john_doe",
  email: "invalid-email",
  password: "password",
  age: "twenty"
};

registerUser(user);
```

## Conclusion

Effective error handling and debugging are essential skills for any JavaScript developer. By anticipating potential errors, providing meaningful error messages, and using the right debugging techniques, you can build more robust applications and save countless hours of troubleshooting.

Next, we'll look at how to optimize your JavaScript code for better performance.