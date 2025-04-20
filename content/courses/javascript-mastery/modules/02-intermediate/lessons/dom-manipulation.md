# DOM Manipulation

The Document Object Model (DOM) is a programming interface for HTML documents. It represents the page as objects that JavaScript can manipulate to create dynamic, interactive web pages.

## What is the DOM?

Think of the DOM as a tree structure where each HTML element becomes a node:

```
document
  └── html
      ├── head
      │   ├── title
      │   └── meta
      └── body
          ├── h1
          ├── p
          └── div
              └── span
```

JavaScript can:
- Find elements in this tree
- Change their content, attributes, and style
- Add or remove elements
- Respond to user interactions (clicks, scroll, etc.)

## Accessing DOM Elements

There are several ways to select elements from the DOM:

### By ID

Use `getElementById()` to find a unique element with a specific ID.

```javascript
// HTML: <div id="main-content">...</div>
const mainContent = document.getElementById("main-content");
```

### By Class Name

Use `getElementsByClassName()` to find elements with a specific class.

```javascript
// HTML: <p class="highlight">...</p>
const highlightedElements = document.getElementsByClassName("highlight");
// Returns an HTMLCollection (array-like object)
```

### By Tag Name

Use `getElementsByTagName()` to find elements with a specific tag.

```javascript
// Find all paragraphs
const paragraphs = document.getElementsByTagName("p");
```

### CSS Selectors (Modern Approach)

`querySelector()` and `querySelectorAll()` allow you to use CSS selectors to find elements.

```javascript
// Find first element with class "highlight"
const firstHighlight = document.querySelector(".highlight");

// Find all elements with class "highlight"
const allHighlights = document.querySelectorAll(".highlight");
// Returns a NodeList (array-like object)

// More complex selector
const nestedItems = document.querySelectorAll("ul.menu > li");
```

## Modifying DOM Elements

Once you've selected an element, you can change its properties:

### Changing Text Content

```javascript
// Simple text content
element.textContent = "New text content";

// HTML content (use with caution)
element.innerHTML = "Text with <strong>HTML</strong> tags";
```

### Changing Attributes

```javascript
// Get attribute
const imgSrc = imageElement.getAttribute("src");

// Set attribute
linkElement.setAttribute("href", "https://example.com");

// Remove attribute
buttonElement.removeAttribute("disabled");

// Check if attribute exists
if (element.hasAttribute("data-id")) {
  // Do something
}

// Modern shorthand properties
imageElement.src = "new-image.jpg";
linkElement.href = "https://example.com";
```

### Changing CSS Styles

```javascript
// Inline styles
element.style.color = "blue";
element.style.fontSize = "16px"; // Note: camelCase, not kebab-case
element.style.display = "none"; // Hide the element

// Adding/removing CSS classes (better approach)
element.classList.add("active");
element.classList.remove("hidden");
element.classList.toggle("selected");
element.classList.contains("highlighted"); // Returns true/false
```

## Creating and Adding Elements

### Creating Elements

```javascript
// Create a new element
const newParagraph = document.createElement("p");

// Add content to it
newParagraph.textContent = "This is a new paragraph.";

// Add attributes
newParagraph.className = "new-item";
newParagraph.id = "intro";
```

### Adding Elements to the DOM

```javascript
// Append at the end of a parent element
parentElement.appendChild(newElement);

// Insert before a specific element
parentElement.insertBefore(newElement, referenceElement);

// Modern methods
parentElement.append(newElement); // Similar to appendChild
parentElement.prepend(newElement); // Insert at the beginning
referenceElement.before(newElement); // Insert before reference
referenceElement.after(newElement); // Insert after reference
```

### Removing Elements

```javascript
// Remove an element
elementToRemove.remove();

// Older approach
elementToRemove.parentElement.removeChild(elementToRemove);
```

## Event Handling

Events allow you to respond to user actions (clicks, key presses, etc.) or browser actions.

### Adding Event Listeners

```javascript
const button = document.querySelector("button");

button.addEventListener("click", function() {
  alert("Button was clicked!");
});

// Using an arrow function
button.addEventListener("click", () => {
  console.log("Button clicked");
});

// Named function
function handleClick() {
  console.log("Button clicked");
}
button.addEventListener("click", handleClick);
```

### Common Events

- **Mouse events**: `click`, `dblclick`, `mousedown`, `mouseup`, `mouseover`, `mouseout`
- **Keyboard events**: `keydown`, `keyup`, `keypress`
- **Form events**: `submit`, `reset`, `change`, `input`
- **Window events**: `load`, `resize`, `scroll`
- **Drag events**: `dragstart`, `drag`, `dragend`, `drop`

### Event Objects

Event listeners receive an event object with information about the event.

```javascript
button.addEventListener("click", function(event) {
  console.log(event.target); // The element that triggered the event
  console.log(event.type);  // "click"
  
  // Prevent default behavior
  event.preventDefault();
  
  // Stop event from bubbling up
  event.stopPropagation();
});
```

## A Simple DOM Example

Putting it all together:

```javascript
// Wait until the DOM is fully loaded
document.addEventListener("DOMContentLoaded", () => {
  // Select elements
  const addButton = document.getElementById("add-button");
  const textInput = document.getElementById("text-input");
  const itemsList = document.getElementById("items-list");
  
  // Add event listener
  addButton.addEventListener("click", () => {
    // Get the input value
    const newItemText = textInput.value.trim();
    
    if (newItemText !== "") {
      // Create new list item
      const newItem = document.createElement("li");
      newItem.textContent = newItemText;
      
      // Add a class
      newItem.classList.add("list-item");
      
      // Add a delete button
      const deleteButton = document.createElement("button");
      deleteButton.textContent = "Delete";
      deleteButton.classList.add("delete-btn");
      
      // Add delete functionality
      deleteButton.addEventListener("click", () => {
        newItem.remove();
      });
      
      // Append the button to the list item
      newItem.appendChild(deleteButton);
      
      // Add the new item to the list
      itemsList.appendChild(newItem);
      
      // Clear the input
      textInput.value = "";
    }
  });
});
```

## Exercise: Interactive To-Do List

Create a simple to-do list using the DOM. The HTML structure is already set up:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Simple To-Do List</title>
  <style>
    .completed {
      text-decoration: line-through;
      color: gray;
    }
  </style>
</head>
<body>
  <h1>My To-Do List</h1>
  
  <input type="text" id="task-input" placeholder="Enter a new task">
  <button id="add-task-btn">Add Task</button>
  
  <ul id="task-list">
    <!-- Tasks will be added here -->
  </ul>
  
  <script src="todo.js"></script>
</body>
</html>
```

Your task is to complete the `todo.js` file that:
1. Allows adding tasks when the button is clicked
2. Lets users mark tasks as completed by clicking on them (adding the "completed" class)
3. Includes a delete button for each task
4. Prevents empty tasks from being added

## Conclusion

DOM manipulation is essential for creating dynamic web applications. It allows you to respond to user interactions and update the content without reloading the page.

Next, we'll learn about Events in more detail and delve into asynchronous JavaScript.