# Prototypes and Inheritance

JavaScript's inheritance model is prototype-based, which differs significantly from the class-based inheritance found in languages like Java or C++. Understanding prototypes is essential for mastering JavaScript.

## What Are Prototypes?

In JavaScript, every object has a hidden link to another object called its prototype. When you try to access a property that doesn't exist on an object, JavaScript automatically looks for it in the object's prototype, then in that object's prototype, and so on, forming what's called the "prototype chain."

### The Prototype Chain

```javascript
const animal = {
  eats: true,
  walk() {
    console.log("Animal walking");
  }
};

const rabbit = {
  jumps: true,
  __proto__: animal // Set animal as rabbit's prototype
};

// rabbit has access to properties from animal
console.log(rabbit.eats); // true
rabbit.walk(); // "Animal walking"

// The prototype chain looks like: rabbit -> animal -> Object.prototype -> null
```

**Note**: `__proto__` is the historical getter/setter for the prototype. Modern JavaScript provides `Object.getPrototypeOf()` and `Object.setPrototypeOf()`. The `__proto__` syntax is shown here for clarity but isn't recommended for production code.

## The Object.prototype

Almost all objects in JavaScript are instances of `Object` and inherit properties from `Object.prototype`, though they may be overridden.

```javascript
const obj = {};

console.log(obj.toString()); // "[object Object]"
// toString() comes from Object.prototype

// This is equivalent to:
console.log(Object.prototype.toString.call(obj));
```

## Constructor Functions and Prototypes

Before ES6 classes, constructor functions were the primary way to create "classes" in JavaScript.

```javascript
// Constructor function (convention: capitalized name)
function Person(name) {
  this.name = name;
}

// Adding a method to the prototype
Person.prototype.greet = function() {
  console.log(`Hello, my name is ${this.name}`);
};

// Creating instances
const alice = new Person("Alice");
const bob = new Person("Bob");

alice.greet(); // "Hello, my name is Alice"
bob.greet();   // "Hello, my name is Bob"

// The prototype chain: alice -> Person.prototype -> Object.prototype -> null
```

When you call a constructor with `new`:

1. A new empty object is created
2. The function is called with `this` set to the new object
3. The object's internal [[Prototype]] is set to the constructor's `prototype` property
4. The function returns the object (unless it explicitly returns something else)

### The prototype vs. \_\_proto\_\_

There's an important distinction:

- `prototype`: A property of constructor functions where shared methods are defined
- `__proto__`: The reference that instances have to their constructor's prototype

```javascript
console.log(Person.prototype === alice.__proto__); // true
```

## Inheritance with Prototypes

Before ES6 classes, implementing inheritance required some manual work:

```javascript
// Parent constructor
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

// Child constructor
function Dog(name, breed) {
  // Call the parent constructor
  Animal.call(this, name);
  this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
// Fix the constructor property
Dog.prototype.constructor = Dog;

// Add a method to Dog.prototype
Dog.prototype.bark = function() {
  console.log("Woof!");
};

const rex = new Dog("Rex", "German Shepherd");
rex.eat(); // "Rex is eating" (inherited from Animal)
rex.bark(); // "Woof!" (from Dog)
```

## Classes in JavaScript (ES6+)

ES6 introduced `class` syntax, which provides a cleaner way to work with prototypes. Under the hood, classes still use prototypes.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  eat() {
    console.log(`${this.name} is eating`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }
  
  bark() {
    console.log("Woof!");
  }
  
  // Override parent method
  eat() {
    super.eat(); // Call parent method
    console.log("...and making a mess");
  }
}

const rex = new Dog("Rex", "German Shepherd");
rex.eat();
// "Rex is eating"
// "...and making a mess"
rex.bark(); // "Woof!"
```

## Object.create()

`Object.create()` creates a new object with the specified prototype:

```javascript
const personPrototype = {
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
};

const john = Object.create(personPrototype);
john.name = "John";
john.greet(); // "Hello, my name is John"

// Check the prototype chain
console.log(Object.getPrototypeOf(john) === personPrototype); // true
```

## Checking Prototype Relationships

```javascript
// Check if an object is in another object's prototype chain
console.log(Animal.prototype.isPrototypeOf(rex)); // true

// Get the prototype of an object
console.log(Object.getPrototypeOf(rex) === Dog.prototype); // true

// Check if a property is directly on the object or inherited
console.log(rex.hasOwnProperty('breed')); // true
console.log(rex.hasOwnProperty('eat')); // false (it's on the prototype)
```

## Property Descriptors and Object Configuration

JavaScript allows fine-grained control over property behavior through property descriptors.

```javascript
const person = {
  firstName: "John",
  lastName: "Doe"
};

// Define a property with a descriptor
Object.defineProperty(person, 'fullName', {
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  set(value) {
    [this.firstName, this.lastName] = value.split(' ');
  },
  enumerable: true, // Will show up in Object.keys() and for...in loops
  configurable: true // Can be redefined or deleted
});

console.log(person.fullName); // "John Doe"
person.fullName = "Jane Smith";
console.log(person.firstName); // "Jane"
console.log(person.lastName); // "Smith"

// Get the descriptor for a property
const descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor.enumerable); // true
console.log(descriptor.configurable); // true
```

### Preventing Object Modifications

JavaScript provides ways to restrict object modifications:

```javascript
const user = {
  name: "Alice",
  age: 30
};

// Make object non-extensible (can't add new properties)
Object.preventExtensions(user);
user.email = "alice@example.com"; // Fails silently or throws error in strict mode
console.log(user.email); // undefined

// Seal an object (can't add or delete properties, but can modify existing ones)
const config = { theme: "dark", fontSize: 16 };
Object.seal(config);
config.fontSize = 18; // Works
delete config.theme; // Fails
config.newProp = true; // Fails

// Freeze an object (can't add, delete, or modify properties)
const constants = { PI: 3.14159, E: 2.71828 };
Object.freeze(constants);
constants.PI = 3; // Fails
```

## Mixins: Composing Objects

Mixins provide a way to "mix" functionality from multiple sources:

```javascript
// Mixin 1
const withLogging = {
  log(message) {
    console.log(`[${new Date().toISOString()}] ${message}`);
  }
};

// Mixin 2
const withStorage = {
  save(key, data) {
    localStorage.setItem(key, JSON.stringify(data));
  },
  load(key) {
    return JSON.parse(localStorage.getItem(key));
  }
};

// Target object
class App {
  constructor(name) {
    this.name = name;
  }
}

// Apply mixins
Object.assign(App.prototype, withLogging, withStorage);

const app = new App("MyApp");
app.log("Application started"); // Uses the logging mixin
app.save("user", { id: 1, name: "John" }); // Uses the storage mixin
```

## Prototype Methods vs. Instance Methods

Understanding the difference between prototype and instance methods helps optimize memory usage:

```javascript
// Instance methods (memory inefficient if many instances)
function BadCar(make, model) {
  this.make = make;
  this.model = model;
  
  // Each car instance gets its own copy of this function
  this.getDescription = function() {
    return `${this.make} ${this.model}`;
  };
}

// Prototype methods (memory efficient)
function GoodCar(make, model) {
  this.make = make;
  this.model = model;
}

// All GoodCar instances share this single function
GoodCar.prototype.getDescription = function() {
  return `${this.make} ${this.model}`;
};
```

## When to Use Prototypes vs. Classes

- **Prototypes**: Useful for understanding JavaScript's internals, adding methods to built-in objects, or when you need fine-grained control.
- **Classes**: Provide a cleaner syntax for most use cases, especially for larger object hierarchies.

## Exercise: Building a Class Hierarchy

Implement a library system with these requirements:

1. Create a base `LibraryItem` class with properties:
   - id (unique identifier)
   - title
   - year
   - isCheckedOut (boolean)
   - dueDate (null when not checked out)
   - Methods: checkOut(), returnItem(), getDaysOverdue()

2. Create three subclasses:
   - `Book` (additional properties: author, pages)
   - `Movie` (additional properties: director, runtime in minutes)
   - `MusicAlbum` (additional properties: artist, tracks)

3. Each subclass should override a `getItemDetails()` method that returns a string with its specific details.

4. Create a `Library` class that manages a collection of items with methods:
   - addItem(item)
   - removeItem(id)
   - findItemById(id)
   - checkOutItem(id)
   - returnItem(id)
   - getOverdueItems()

Use prototypes/classes (your choice) to implement this system with proper inheritance.

## Conclusion

Prototypes are the foundation of JavaScript's object-oriented capabilities. Understanding how the prototype chain works is crucial for effective JavaScript programming, even when using modern class syntax.

Next, we'll examine advanced functional programming concepts in JavaScript.