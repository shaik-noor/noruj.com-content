# Events in JavaScript

Events are actions or occurrences that happen in the browser, such as user clicks, key presses, or page loads. JavaScript allows you to detect and respond to these events, making your web pages interactive.

## Event Types

JavaScript supports a wide variety of events:

### Mouse Events
- `click`: When an element is clicked
- `dblclick`: When an element is double-clicked
- `mousedown`: When the mouse button is pressed down
- `mouseup`: When the mouse button is released
- `mousemove`: When the mouse is moved
- `mouseover`: When the mouse enters an element
- `mouseout`: When the mouse leaves an element

### Keyboard Events
- `keydown`: When a key is pressed down
- `keyup`: When a key is released
- `keypress`: When a key that produces a character is pressed

### Form Events
- `submit`: When a form is submitted
- `reset`: When a form is reset
- `change`: When the value of a form element changes (for select, checkbox, radio)
- `input`: When the value of an input or textarea changes (real-time)
- `focus`: When an element receives focus
- `blur`: When an element loses focus

### Document/Window Events
- `load`: When the page has fully loaded
- `DOMContentLoaded`: When the HTML document has been completely parsed
- `resize`: When the window is resized
- `scroll`: When the document or element is scrolled
- `unload`: When the user navigates away from the page

## Adding Event Listeners

The recommended way to handle events is with the `addEventListener()` method:

```javascript
element.addEventListener(eventType, callbackFunction, options);
```

Example:
```javascript
const button = document.querySelector('button');

button.addEventListener('click', function() {
  console.log('Button clicked!');
});
```

### Multiple Listeners

You can add multiple listeners for the same event:

```javascript
button.addEventListener('click', function() {
  console.log('First handler');
});

button.addEventListener('click', function() {
  console.log('Second handler');
});
// Both functions will run when the button is clicked
```

### Removing Event Listeners

To remove an event listener, you need a reference to the callback function:

```javascript
function handleClick() {
  console.log('Button clicked!');
}

button.addEventListener('click', handleClick);

// Later, remove the listener
button.removeEventListener('click', handleClick);
```

Note: You can't remove anonymous functions with `removeEventListener`.

## The Event Object

When an event occurs, an event object is automatically passed to your event handler function. This object contains useful information about the event.

```javascript
button.addEventListener('click', function(event) {
  console.log(event.type);      // "click"
  console.log(event.target);    // The element that was clicked
  console.log(event.currentTarget); // The element the listener is attached to
  console.log(event.clientX, event.clientY); // Mouse coordinates (for mouse events)
});
```

### Common Event Object Properties

- `event.type`: The type of event (e.g., "click")
- `event.target`: The element that triggered the event
- `event.currentTarget`: The element to which the event handler is attached
- `event.clientX, event.clientY`: Mouse coordinates (for mouse events)
- `event.key, event.code`: Key information (for keyboard events)
- `event.preventDefault()`: Method to prevent the default browser behavior
- `event.stopPropagation()`: Method to stop event bubbling

## Event Propagation: Bubbling and Capturing

When an event occurs on an element, it doesn't just trigger there. It also "bubbles up" through the parent elements.

```html
<div id="outer">
  <div id="inner">
    <button id="button">Click me</button>
  </div>
</div>
```

```javascript
document.getElementById('button').addEventListener('click', function() {
  console.log('Button clicked');
});

document.getElementById('inner').addEventListener('click', function() {
  console.log('Inner div clicked');
});

document.getElementById('outer').addEventListener('click', function() {
  console.log('Outer div clicked');
});

// When the button is clicked, all three messages will display:
// "Button clicked"
// "Inner div clicked"
// "Outer div clicked"
```

This is called **event bubbling**: the event starts at the target element and bubbles up through ancestors.

### Event Capturing

Event capturing is the opposite of bubbling: the event starts at the top of the DOM tree and moves down to the target.

To use the capturing phase, set the third parameter of `addEventListener` to `true`:

```javascript
document.getElementById('outer').addEventListener('click', function() {
  console.log('Outer div clicked - capturing phase');
}, true);

// Now with both capturing and bubbling:
// "Outer div clicked - capturing phase" (capturing phase)
// "Button clicked" (target phase)
// "Inner div clicked" (bubbling phase)
// "Outer div clicked" (bubbling phase)
```

### Stopping Propagation

You can stop event bubbling with `stopPropagation()`:

```javascript
document.getElementById('inner').addEventListener('click', function(event) {
  console.log('Inner div clicked');
  event.stopPropagation(); // Prevents the event from bubbling up further
});

document.getElementById('outer').addEventListener('click', function() {
  console.log('This will not be logged when clicking the inner div');
});
```

## Preventing Default Behavior

Many elements have default behaviors (links navigate, forms submit, etc.). You can prevent these with `preventDefault()`:

```javascript
document.querySelector('a').addEventListener('click', function(event) {
  event.preventDefault(); // Prevent navigation
  console.log('Link clicked, but not navigating');
});

document.querySelector('form').addEventListener('submit', function(event) {
  event.preventDefault(); // Prevent form submission
  console.log('Form submitted, but not sending data');
  // Validate form, then submit manually if needed
});
```

## Event Delegation

Event delegation is a technique where you attach a single event listener to a parent element instead of multiple listeners on child elements. It relies on event bubbling.

### Without Event Delegation

```javascript
// Adding listeners to each button - inefficient for many buttons
document.querySelectorAll('button').forEach(button => {
  button.addEventListener('click', function() {
    console.log('Button clicked', this.textContent);
  });
});
```

### With Event Delegation

```javascript
// One listener on the parent container
document.getElementById('button-container').addEventListener('click', function(event) {
  if (event.target.tagName === 'BUTTON') { // Check if a button was clicked
    console.log('Button clicked', event.target.textContent);
  }
});
```

Benefits of event delegation:
1. Better performance (fewer event listeners)
2. Works for dynamically added elements
3. Less memory usage
4. Cleaner code

## Custom Events

You can create and dispatch your own custom events:

```javascript
// Create a custom event
const productAddedEvent = new CustomEvent('productAdded', {
  detail: { productId: 123, name: 'Laptop', price: 999 }
});

// Listen for the custom event
document.addEventListener('productAdded', function(event) {
  console.log(`Product added: ${event.detail.name}`);
});

// Dispatch the custom event
document.dispatchEvent(productAddedEvent);
```

## Exercise: Interactive Image Gallery

Create a simple image gallery with event delegation. The HTML structure:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Image Gallery</title>
  <style>
    .gallery {
      display: flex;
      flex-wrap: wrap;
    }
    .thumbnail {
      width: 100px;
      height: 100px;
      margin: 10px;
      cursor: pointer;
    }
    .lightbox {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.8);
      justify-content: center;
      align-items: center;
    }
    .lightbox img {
      max-width: 80%;
      max-height: 80%;
    }
  </style>
</head>
<body>
  <h1>Image Gallery</h1>
  
  <div class="gallery" id="gallery">
    <img src="image1.jpg" class="thumbnail" data-full="image1-large.jpg" alt="Image 1">
    <img src="image2.jpg" class="thumbnail" data-full="image2-large.jpg" alt="Image 2">
    <img src="image3.jpg" class="thumbnail" data-full="image3-large.jpg" alt="Image 3">
    <!-- Add more images as needed -->
  </div>
  
  <div class="lightbox" id="lightbox">
    <img id="lightbox-img" src="" alt="Large preview">
  </div>
  
  <script src="gallery.js"></script>
</body>
</html>
```

Your task is to complete the `gallery.js` file to:
1. Use event delegation to handle clicks on the thumbnails
2. Show the lightbox with the full-size image when a thumbnail is clicked
3. Close the lightbox when clicking anywhere on it
4. Prevent the default behavior if links are used instead of images

## Conclusion

Events are a fundamental part of creating interactive web applications. They allow your code to respond to user actions and browser state changes. Understanding event propagation, delegation, and the event object gives you the tools to create efficient and responsive web interfaces.

Next, we'll learn about Asynchronous JavaScript, including callbacks, promises, and async/await.