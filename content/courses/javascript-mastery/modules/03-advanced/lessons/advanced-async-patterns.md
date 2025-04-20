# Advanced Asynchronous JavaScript Patterns

While you've already learned about basic asynchronous JavaScript concepts like callbacks, Promises, and async/await, this lesson dives deeper into advanced asynchronous patterns that can help you handle complex scenarios in your applications.

## Promise Patterns and Techniques

### Promise Chaining Revisited

Promise chains allow you to perform sequential asynchronous operations:

```javascript
fetchUser(userId)
  .then(user => fetchUserPosts(user.id))
  .then(posts => fetchCommentsForPosts(posts))
  .then(comments => {
    // Process comments
    return processComments(comments);
  })
  .catch(error => {
    console.error("Error in chain:", error);
  });
```

**Important**: Each `.then()` should return a value or another Promise to maintain the chain.

### Error Handling Strategies

There are several strategies for error handling in Promise chains:

#### 1. Catch at the End

```javascript
fetchData()
  .then(processData)
  .then(saveData)
  .catch(handleError); // Catches any error in the chain
```

#### 2. Intermediate Catches

```javascript
fetchData()
  .then(processData)
  .catch(error => {
    // Handle fetch and process errors
    console.error("Error fetching or processing:", error);
    return { data: [], recovered: true }; // Provide fallback data
  })
  .then(saveData) // Will still be called with the fallback data
  .catch(error => {
    // Handle save errors
    console.error("Error saving:", error);
  });
```

#### 3. Local Error Handling

```javascript
fetchData()
  .then(
    data => processData(data), 
    error => {
      // Only handles fetchData errors
      console.error("Fetch error:", error);
      return { data: [], recovered: true };
    }
  )
  .then(saveData)
  .catch(error => {
    // Catches any errors from processData or saveData
    console.error("Later stage error:", error);
  });
```

### Advanced Promise Methods

#### Promise.all

Resolves when all Promises resolve, or rejects when any Promise rejects:

```javascript
const userPromise = fetchUser(userId);
const postsPromise = fetchPosts(userId);
const preferencesPromise = fetchPreferences(userId);

Promise.all([userPromise, postsPromise, preferencesPromise])
  .then(([user, posts, preferences]) => {
    // All data is available here
    displayDashboard(user, posts, preferences);
  })
  .catch(error => {
    // If any promise fails, this will execute
    console.error("Failed to load dashboard:", error);
  });
```

#### Promise.allSettled

Like `Promise.all`, but waits for all Promises to complete regardless of whether they fulfill or reject:

```javascript
Promise.allSettled([
  fetchCriticalData(),
  fetchNonCriticalData(),
  fetchOptionalData()
])
  .then(results => {
    // results is an array of objects with status and value/reason properties
    results.forEach((result, index) => {
      if (result.status === 'fulfilled') {
        console.log(`Promise ${index} fulfilled with value:`, result.value);
      } else {
        console.log(`Promise ${index} rejected with reason:`, result.reason);
      }
    });
    
    // Process whatever data was successfully retrieved
    const availableData = results
      .filter(result => result.status === 'fulfilled')
      .map(result => result.value);
    
    processAvailableData(availableData);
  });
```

#### Promise.race

Settles as soon as any Promise settles (either fulfills or rejects):

```javascript
// Set a timeout for an API call
const dataPromise = fetchData();
const timeoutPromise = new Promise((_, reject) => {
  setTimeout(() => reject(new Error("Request timed out")), 5000);
});

Promise.race([dataPromise, timeoutPromise])
  .then(data => {
    // Data arrived before timeout
    processData(data);
  })
  .catch(error => {
    // Either the fetch failed or it timed out
    console.error("Operation failed:", error);
  });
```

#### Promise.any

Resolves with the value of the first Promise that fulfills, or rejects if all Promises reject:

```javascript
// Try multiple CDNs to load a resource
Promise.any([
  fetchFromCDN1(resource),
  fetchFromCDN2(resource),
  fetchFromCDN3(resource)
])
  .then(resource => {
    // First successful response
    useResource(resource);
  })
  .catch(error => {
    // All CDNs failed
    console.error("Failed to load from any CDN:", error);
  });
```

### Creating Custom Promise Utilities

#### Retry Logic

```javascript
function fetchWithRetry(url, options = {}, retries = 3, backoff = 300) {
  return new Promise((resolve, reject) => {
    function attempt(attemptsLeft) {
      fetch(url, options)
        .then(resolve)
        .catch(error => {
          if (attemptsLeft <= 1) {
            reject(error);
            return;
          }
          
          console.log(`Retrying in ${backoff}ms... (${attemptsLeft - 1} attempts left)`);
          setTimeout(() => attempt(attemptsLeft - 1), backoff);
        });
    }
    
    attempt(retries);
  });
}

// Usage
fetchWithRetry('/api/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error("All attempts failed:", error));
```

#### Cancellable Promises

Using AbortController (modern browsers):

```javascript
function cancellableFetch(url) {
  const controller = new AbortController();
  const signal = controller.signal;
  
  const promise = fetch(url, { signal })
    .then(response => response.json());
  
  // Return both the promise and a way to cancel it
  return {
    promise,
    cancel: () => controller.abort()
  };
}

// Usage
const { promise, cancel } = cancellableFetch('/api/data');

promise
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch was cancelled');
    } else {
      console.error('Fetch error:', error);
    }
  });

// Later, if needed:
cancel(); // This will abort the fetch
```

#### Promise Queue for Rate Limiting

```javascript
class PromiseQueue {
  constructor(concurrency = 1) {
    this.concurrency = concurrency;
    this.queue = [];
    this.activeCount = 0;
  }
  
  add(promiseGenerator) {
    return new Promise((resolve, reject) => {
      // Add to queue
      this.queue.push({
        promiseGenerator,
        resolve,
        reject
      });
      
      this.processQueue();
    });
  }
  
  processQueue() {
    if (this.activeCount >= this.concurrency || this.queue.length === 0) {
      return;
    }
    
    // Take the next item from the queue
    const { promiseGenerator, resolve, reject } = this.queue.shift();
    this.activeCount++;
    
    try {
      // Execute the promise
      promiseGenerator()
        .then(result => {
          resolve(result);
          this.activeCount--;
          this.processQueue();
        })
        .catch(error => {
          reject(error);
          this.activeCount--;
          this.processQueue();
        });
    } catch (error) {
      reject(error);
      this.activeCount--;
      this.processQueue();
    }
  }
}

// Usage
const queue = new PromiseQueue(2); // Allow 2 concurrent requests

function fetchData(id) {
  return () => fetch(`/api/data/${id}`).then(r => r.json());
}

// Add 5 tasks but only 2 will run at a time
queue.add(fetchData(1)).then(data => console.log("Data 1:", data));
queue.add(fetchData(2)).then(data => console.log("Data 2:", data));
queue.add(fetchData(3)).then(data => console.log("Data 3:", data));
queue.add(fetchData(4)).then(data => console.log("Data 4:", data));
queue.add(fetchData(5)).then(data => console.log("Data 5:", data));
```

### Advanced Async/Await Patterns

#### Error Handling with Async/Await

Using try/catch blocks:

```javascript
async function loadUserData(userId) {
  try {
    const user = await fetchUser(userId);
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts);
    
    return { user, posts, comments };
  } catch (error) {
    console.error("Failed to load user data:", error);
    
    // Rethrow or return a default value
    return { user: null, posts: [], comments: [] };
  }
}
```

#### Helper Function for Cleaner Error Handling

```javascript
// Helper that returns [error, data] pairs
async function tryCatch(promise) {
  try {
    const data = await promise;
    return [null, data];
  } catch (error) {
    return [error, null];
  }
}

// Usage
async function getUserData(userId) {
  const [userError, user] = await tryCatch(fetchUser(userId));
  
  if (userError) {
    console.error("Error fetching user:", userError);
    return null;
  }
  
  const [postsError, posts] = await tryCatch(fetchPosts(user.id));
  
  return {
    user,
    posts: postsError ? [] : posts
  };
}
```

#### Parallel Execution with Async/Await

```javascript
async function loadDashboard(userId) {
  try {
    // Start all fetches in parallel
    const userPromise = fetchUser(userId);
    const postsPromise = fetchPosts(userId);
    const prefsPromise = fetchPreferences(userId);
    
    // Await all results
    const [user, posts, prefs] = await Promise.all([
      userPromise,
      postsPromise,
      prefsPromise
    ]);
    
    return { user, posts, prefs };
  } catch (error) {
    console.error("Dashboard load failed:", error);
    throw error;
  }
}
```

#### Sequential vs. Concurrent Execution

Processing an array of items sequentially:

```javascript
async function processItemsSequentially(items) {
  const results = [];
  
  for (const item of items) {
    // Wait for each item to complete before moving to the next
    const result = await processItem(item);
    results.push(result);
  }
  
  return results;
}
```

Processing an array of items concurrently:

```javascript
async function processItemsConcurrently(items) {
  // Create an array of Promises
  const promises = items.map(item => processItem(item));
  
  // Wait for all to complete
  return Promise.all(promises);
}
```

Limited concurrency:

```javascript
async function processItemsWithLimitedConcurrency(items, concurrency = 2) {
  const results = [];
  const inProgress = new Set();
  
  for (const item of items) {
    // Create a promise for this item
    const promise = processItem(item)
      .then(result => {
        results.push(result);
        inProgress.delete(promise);
      });
    
    inProgress.add(promise);
    
    // If we've hit the limit, wait for one to complete
    if (inProgress.size >= concurrency) {
      await Promise.race(inProgress);
    }
  }
  
  // Wait for any remaining promises
  await Promise.all(inProgress);
  
  return results;
}
```

## Advanced Asynchronous Patterns

### Generators for Asynchronous Control Flow

Generators offer another way to handle asynchronous operations:

```javascript
function* fetchDataGenerator() {
  try {
    const user = yield fetchUser();
    console.log("User:", user);
    
    const posts = yield fetchPosts(user.id);
    console.log("Posts:", posts);
    
    const comments = yield fetchComments(posts[0].id);
    console.log("Comments:", comments);
    
    return { user, posts, comments };
  } catch (error) {
    console.error("Error:", error);
    return null;
  }
}

// Runner function to execute a generator
function runGenerator(generatorFn) {
  const generator = generatorFn();
  
  function handle(result) {
    if (result.done) {
      return Promise.resolve(result.value);
    }
    
    return Promise.resolve(result.value)
      .then(res => handle(generator.next(res)))
      .catch(err => handle(generator.throw(err)));
  }
  
  return handle(generator.next());
}

// Usage
runGenerator(fetchDataGenerator)
  .then(result => console.log("Final result:", result));
```

### The Observer Pattern with RxJS

RxJS is a library for reactive programming that provides powerful tools for handling asynchronous data streams:

```javascript
// You'll need to install RxJS: npm install rxjs
import { fromEvent, from } from 'rxjs';
import { debounceTime, switchMap, map, catchError } from 'rxjs/operators';

// Create an observable from an input element's keyup events
const searchInput = document.getElementById('search');
const searchResults = document.getElementById('results');

fromEvent(searchInput, 'keyup')
  .pipe(
    debounceTime(300), // Wait for 300ms of inactivity
    map(event => event.target.value), // Extract the input value
    switchMap(query => {
      // Cancel previous request if a new one comes in
      return from(fetch(`/api/search?q=${query}`))
        .pipe(
          switchMap(response => from(response.json())),
          catchError(error => {
            console.error('Search error:', error);
            return []; // Return empty array on error
          })
        );
    })
  )
  .subscribe(results => {
    // Update UI with results
    displayResults(results);
  });

function displayResults(results) {
  searchResults.innerHTML = results
    .map(item => `<li>${item.name}</li>`)
    .join('');
}
```

### Web Workers for Parallel Processing

Web Workers allow you to run JavaScript in background threads:

```javascript
// main.js
function calculateWithWebWorker(data) {
  return new Promise((resolve, reject) => {
    const worker = new Worker('worker.js');
    
    worker.onmessage = function(event) {
      resolve(event.data);
      worker.terminate();
    };
    
    worker.onerror = function(error) {
      reject(error);
      worker.terminate();
    };
    
    worker.postMessage(data);
  });
}

// Usage
const bigArray = Array.from({ length: 10000000 }, (_, i) => i);

calculateWithWebWorker(bigArray)
  .then(result => console.log("Result:", result))
  .catch(error => console.error("Worker error:", error));
```

```javascript
// worker.js
self.onmessage = function(event) {
  const data = event.data;
  
  // Perform expensive computation
  const result = data.reduce((sum, val) => sum + val, 0);
  
  // Send the result back to the main thread
  self.postMessage(result);
};
```

### Service Workers for Offline Support

Service Workers can provide offline functionality and intercept network requests:

```javascript
// Register a service worker
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js')
    .then(registration => {
      console.log('Service Worker registered with scope:', registration.scope);
    })
    .catch(error => {
      console.error('Service Worker registration failed:', error);
    });
}
```

```javascript
// service-worker.js
const CACHE_NAME = 'my-site-cache-v1';
const urlsToCache = [
  '/',
  '/styles/main.css',
  '/scripts/main.js',
  '/images/logo.png'
];

// Install phase - cache resources
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});

// Fetch phase - serve from cache or network
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => {
        // Cache hit - return the response
        if (response) {
          return response;
        }
        
        // Clone the request because it's a stream and can only be consumed once
        const fetchRequest = event.request.clone();
        
        return fetch(fetchRequest).then(response => {
          // Check if we received a valid response
          if (!response || response.status !== 200 || response.type !== 'basic') {
            return response;
          }
          
          // Clone the response because it's a stream and can only be consumed once
          const responseToCache = response.clone();
          
          caches.open(CACHE_NAME)
            .then(cache => {
              cache.put(event.request, responseToCache);
            });
          
          return response;
        });
      })
  );
});
```

### SharedWorker for Communication Between Tabs

SharedWorkers allow communication between different tabs or windows of the same origin:

```javascript
// main.js
const sharedWorker = new SharedWorker('shared-worker.js');

// Listen for messages from the shared worker
sharedWorker.port.onmessage = event => {
  console.log('Message received from shared worker:', event.data);
};

// Connect to the worker
sharedWorker.port.start();

// Send a message to the worker
function sendMessage(message) {
  sharedWorker.port.postMessage({
    source: window.location.href,
    message: message
  });
}

// Example usage
document.getElementById('send-btn').addEventListener('click', () => {
  const message = document.getElementById('message-input').value;
  sendMessage(message);
});
```

```javascript
// shared-worker.js
const connections = [];

self.onconnect = function(e) {
  const port = e.ports[0];
  connections.push(port);
  
  port.onmessage = function(event) {
    // Broadcast the message to all connected ports except the sender
    connections.forEach(connection => {
      if (connection !== port) {
        connection.postMessage(event.data);
      }
    });
  };
  
  port.start();
};
```

## Performance Optimization in Asynchronous Code

### Debouncing and Throttling

Debouncing delays execution until after a certain amount of time has passed since the last call:

```javascript
function debounce(func, wait) {
  let timeout;
  
  return function executedFunction(...args) {
    const later = () => {
      clearTimeout(timeout);
      func(...args);
    };
    
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
  };
}

// Usage
const debouncedSearch = debounce(searchFunction, 300);

// In an event handler
searchInput.addEventListener('input', event => {
  debouncedSearch(event.target.value);
});
```

Throttling ensures a function is called at most once in a specified time period:

```javascript
function throttle(func, limit) {
  let inThrottle;
  
  return function(...args) {
    if (!inThrottle) {
      func(...args);
      inThrottle = true;
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

// Usage
const throttledScroll = throttle(() => {
  console.log('Scroll event fired');
}, 1000);

window.addEventListener('scroll', throttledScroll);
```

### Batching Operations

Batching groups multiple operations together for efficiency:

```javascript
class RequestBatcher {
  constructor(batchSize = 10, waitTime = 200) {
    this.batchSize = batchSize;
    this.waitTime = waitTime;
    this.queue = [];
    this.timer = null;
  }
  
  add(item) {
    return new Promise((resolve, reject) => {
      this.queue.push({ item, resolve, reject });
      
      if (this.queue.length >= this.batchSize) {
        this.flush();
      } else if (!this.timer) {
        this.timer = setTimeout(() => this.flush(), this.waitTime);
      }
    });
  }
  
  async flush() {
    if (this.timer) {
      clearTimeout(this.timer);
      this.timer = null;
    }
    
    if (this.queue.length === 0) return;
    
    const batch = this.queue.splice(0, this.batchSize);
    const items = batch.map(({ item }) => item);
    
    try {
      // Send batch request
      const results = await sendBatchRequest(items);
      
      // Resolve individual promises
      batch.forEach(({ resolve }, index) => {
        resolve(results[index]);
      });
    } catch (error) {
      // Reject all promises
      batch.forEach(({ reject }) => {
        reject(error);
      });
    }
    
    // Process any remaining items
    if (this.queue.length > 0) {
      this.flush();
    }
  }
}

// Example usage
const batcher = new RequestBatcher();

function saveItem(item) {
  return batcher.add(item);
}

// Instead of sending 100 separate requests, they'll be batched in groups of 10
for (let i = 0; i < 100; i++) {
  saveItem({ id: i, name: `Item ${i}` })
    .then(result => console.log(`Item ${i} saved:`, result))
    .catch(error => console.error(`Failed to save item ${i}:`, error));
}

// Implementation of batch request sender
async function sendBatchRequest(items) {
  // In a real application, this would send a batch request to the server
  console.log(`Sending batch of ${items.length} items`);
  return items.map(item => ({ ...item, saved: true }));
}
```

## Exercise: Advanced Async Patterns

1. **Rate-Limited API Client**: Create a rate-limited API client that can make a maximum number of requests per second, queuing additional requests when the limit is reached.

2. **Parallel File Processor**: Build a system that can process multiple files in parallel, but with a configurable concurrency limit. Each file should be read, processed, and then written back.

3. **Retryable Fetch with Exponential Backoff**: Implement a fetch wrapper that automatically retries failed requests with an exponential backoff strategy (i.e., increasing wait times between retries).

4. **Real-time Data Aggregation**: Build a system that collects data from multiple sources at different intervals, aggregates it, and provides a unified view that updates in real-time.

## Conclusion

Advanced asynchronous patterns in JavaScript enable you to handle complex scenarios with elegance and efficiency. By combining these patterns, you can build applications that are responsive, resilient, and scalable.

Whether you're managing API calls, handling user interactions, or processing large datasets, these patterns provide the tools you need to write sophisticated asynchronous code.

Next, we'll explore advanced JavaScript for browsers, including DOM manipulation techniques, browser APIs, and performance optimization strategies.