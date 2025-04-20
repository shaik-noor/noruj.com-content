# Asynchronous JavaScript

JavaScript is primarily a single-threaded language, meaning it can only execute one operation at a time. However, many operations (like network requests or file reading) can take time to complete. Asynchronous programming allows JavaScript to perform these operations without blocking the main thread.

## Synchronous vs. Asynchronous

### Synchronous Code
Executes line by line, with each line waiting for the previous one to complete.

```javascript
console.log("First");
console.log("Second");
console.log("Third");

// Output:
// First
// Second
// Third
```

### Asynchronous Code
Allows operations to run in the "background" and continue execution without waiting.

```javascript
console.log("First");
setTimeout(() => {
  console.log("Second"); // Runs after 2 seconds
}, 2000);
console.log("Third");

// Output:
// First
// Third
// Second (after 2 seconds)
```

## Callbacks

Callbacks are functions passed as arguments to other functions, which then "call back" to your code when they finish.

### Simple Callback Example

```javascript
function doSomethingAsync(callback) {
  setTimeout(() => {
    const result = 42;
    callback(result);
  }, 1000);
}

doSomethingAsync(function(result) {
  console.log(`Got result: ${result}`);
});
console.log("After calling doSomethingAsync");

// Output:
// After calling doSomethingAsync
// Got result: 42 (after 1 second)
```

### Callback Hell

When you have multiple nested callbacks, the code can become difficult to read and maintain:

```javascript
fetchUserData(userId, function(userData) {
  fetchUserPosts(userData.id, function(posts) {
    fetchPostComments(posts[0].id, function(comments) {
      displayComments(comments);
      
      // More nested callbacks...
    }, handleError);
  }, handleError);
}, handleError);
```

This pyramid-shaped code is called "callback hell" or the "pyramid of doom."

## Promises

Promises provide a cleaner way to handle asynchronous operations. A Promise represents a value that might not be available yet but will be resolved at some point in the future.

### Promise States

- **Pending**: Initial state, neither fulfilled nor rejected
- **Fulfilled**: The operation completed successfully
- **Rejected**: The operation failed

### Creating a Promise

```javascript
const myPromise = new Promise((resolve, reject) => {
  // Asynchronous operation
  setTimeout(() => {
    const success = true;
    
    if (success) {
      resolve("Operation successful!");  // fulfilled
    } else {
      reject("Error occurred!");  // rejected
    }
  }, 1000);
});
```

### Using Promises

```javascript
myPromise
  .then((result) => {
    console.log(result);  // Executes if resolved
    return "Next step";
  })
  .then((nextResult) => {
    console.log(nextResult);  // Chains another step
  })
  .catch((error) => {
    console.error(error);  // Catches any errors
  })
  .finally(() => {
    console.log("Promise complete, regardless of outcome");
  });
```

### Converting Callback to Promise

```javascript
// Callback version
function getDataWithCallback(id, callback) {
  setTimeout(() => {
    if (id > 0) {
      callback(null, { id: id, name: "Item " + id });
    } else {
      callback(new Error("Invalid ID"));
    }
  }, 1000);
}

// Promise version
function getDataWithPromise(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (id > 0) {
        resolve({ id: id, name: "Item " + id });
      } else {
        reject(new Error("Invalid ID"));
      }
    }, 1000);
  });
}

// Using the promise
getDataWithPromise(5)
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Promise Chaining

Promises can be chained to avoid callback hell:

```javascript
fetchUserData(userId)
  .then(userData => {
    console.log(userData);
    return fetchUserPosts(userData.id);
  })
  .then(posts => {
    console.log(posts);
    return fetchPostComments(posts[0].id);
  })
  .then(comments => {
    displayComments(comments);
  })
  .catch(error => {
    console.error("Error in the chain:", error);
  });
```

### Multiple Promises

#### Promise.all()
Waits for all promises to resolve or any to reject:

```javascript
const promise1 = fetchUserData(1);
const promise2 = fetchUserData(2);
const promise3 = fetchUserData(3);

Promise.all([promise1, promise2, promise3])
  .then(results => {
    console.log("All resolved:", results); // Array of resolved values
  })
  .catch(error => {
    console.error("At least one rejected:", error);
  });
```

#### Promise.race()
Returns as soon as any promise resolves or rejects:

```javascript
const promise1 = fetchSlowData();
const promise2 = fetchFastData();

Promise.race([promise1, promise2])
  .then(result => {
    console.log("First to complete:", result);
  })
  .catch(error => {
    console.error("First to fail:", error);
  });
```

#### Promise.allSettled()
Waits for all promises to settle (resolve or reject):

```javascript
Promise.allSettled([promise1, promise2, promise3])
  .then(results => {
    // Results is an array of objects with status and value/reason
    results.forEach(result => {
      if (result.status === 'fulfilled') {
        console.log('Success:', result.value);
      } else {
        console.log('Failed:', result.reason);
      }
    });
  });
```

## Async/Await

Async/await is syntactic sugar built on top of promises, making asynchronous code look and behave more like synchronous code.

### Basic Syntax

```javascript
async function fetchData() {
  try {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Error fetching data:", error);
  }
}
```

The `async` keyword declares that a function returns a promise.
The `await` keyword pauses execution until the promise is settled.

### Rewriting Promise Chains

```javascript
// Promise chain version
function getUser() {
  return fetchUserData(userId)
    .then(userData => {
      return fetchUserPosts(userData.id)
        .then(posts => {
          return fetchPostComments(posts[0].id);
        });
    });
}

// Async/await version
async function getUser() {
  try {
    const userData = await fetchUserData(userId);
    const posts = await fetchUserPosts(userData.id);
    const comments = await fetchPostComments(posts[0].id);
    return comments;
  } catch (error) {
    console.error("Error:", error);
  }
}
```

### Parallel Operations with Async/Await

Using `await` sequentially can be slow for independent operations. Use `Promise.all` for parallel execution:

```javascript
async function getMultipleUsers() {
  try {
    // Sequential - slower
    const user1 = await fetchUserData(1);
    const user2 = await fetchUserData(2);
    
    // Parallel - faster
    const [user3, user4] = await Promise.all([
      fetchUserData(3),
      fetchUserData(4)
    ]);
    
    return [user1, user2, user3, user4];
  } catch (error) {
    console.error(error);
  }
}
```

## Real-World Example: Fetching Data from APIs

The `fetch` API returns promises, making it perfect for async/await:

```javascript
async function fetchUserProfile(userId) {
  try {
    // Get user data
    const userResponse = await fetch(`https://api.example.com/users/${userId}`);
    if (!userResponse.ok) throw new Error(`HTTP error! Status: ${userResponse.status}`);
    const userData = await userResponse.json();
    
    // Get user posts in parallel
    const [postsResponse, friendsResponse] = await Promise.all([
      fetch(`https://api.example.com/users/${userId}/posts`),
      fetch(`https://api.example.com/users/${userId}/friends`)
    ]);
    
    // Check responses and parse JSON
    if (!postsResponse.ok) throw new Error(`HTTP error! Status: ${postsResponse.status}`);
    if (!friendsResponse.ok) throw new Error(`HTTP error! Status: ${friendsResponse.status}`);
    
    const posts = await postsResponse.json();
    const friends = await friendsResponse.json();
    
    // Combine the data
    return {
      user: userData,
      posts: posts,
      friends: friends
    };
  } catch (error) {
    console.error("Error fetching user profile:", error);
    throw error; // Re-throw to let the caller handle it
  }
}

// Using the function
fetchUserProfile(123)
  .then(profile => {
    console.log("User profile:", profile);
  })
  .catch(error => {
    console.error("Failed to load profile:", error);
  });
```

## Error Handling in Asynchronous Code

### With Promises

```javascript
fetchData()
  .then(result => {
    // Success case
    return processData(result);
  })
  .catch(error => {
    // Handles errors from fetchData and processData
    console.error("An error occurred:", error);
  });
```

### With Async/Await

```javascript
async function getData() {
  try {
    const result = await fetchData();
    const processed = await processData(result);
    return processed;
  } catch (error) {
    // Catches errors from both fetchData and processData
    console.error("An error occurred:", error);
    // You can also re-throw or return a default value
  }
}
```

## Handling Timeouts

Sometimes you need to limit how long to wait for an asynchronous operation:

```javascript
async function fetchWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, {
      signal: controller.signal
    });
    clearTimeout(timeoutId);
    return await response.json();
  } catch (error) {
    clearTimeout(timeoutId);
    if (error.name === 'AbortError') {
      throw new Error(`Request timed out after ${timeout}ms`);
    }
    throw error;
  }
}
```

## Exercise: Building a Weather App

Create a simple weather application that fetches weather data from an API. The HTML structure:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Weather App</title>
</head>
<body>
  <h1>Weather Checker</h1>
  
  <input id="city-input" type="text" placeholder="Enter city name">
  <button id="search-btn">Search</button>
  
  <div id="weather-container">
    <h2 id="city-name"></h2>
    <p id="temperature"></p>
    <p id="description"></p>
  </div>
  
  <script src="weather.js"></script>
</body>
</html>
```

Your task is to complete the `weather.js` file to:
1. Fetch weather data from a weather API (like OpenWeatherMap) when the search button is clicked
2. Use async/await for the fetch operation
3. Handle any potential errors during the fetch
4. Update the weather-container with the fetched weather information
5. Display a loading indicator during the fetch

## Conclusion

Asynchronous JavaScript is essential for creating responsive web applications. While callbacks were the original solution, modern JavaScript offers promises and async/await, which make asynchronous code much more readable and maintainable.

Next, we'll explore error handling techniques in more depth and how to debug JavaScript code.