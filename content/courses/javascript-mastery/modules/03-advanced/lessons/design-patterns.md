# Design Patterns in JavaScript

Design patterns are reusable solutions to common problems in software design. They represent best practices evolved over time by experienced developers. Learning these patterns helps you write more maintainable, flexible, and efficient code.

## Introduction to Design Patterns

Design patterns can be categorized into three main types:

1. **Creational Patterns** - Focus on object creation mechanisms
2. **Structural Patterns** - Deal with object composition and relationships
3. **Behavioral Patterns** - Concerned with object interaction and responsibility

Let's explore the most useful design patterns in JavaScript.

## Creational Patterns

### 1. Singleton Pattern

Ensures a class has only one instance and provides a global point of access to it.

```javascript
class Singleton {
  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    
    Singleton.instance = this;
    this.data = [];
    return this;
  }
  
  addItem(item) {
    this.data.push(item);
  }
  
  getItems() {
    return this.data;
  }
}

// Usage
const instance1 = new Singleton();
const instance2 = new Singleton();

instance1.addItem("Item 1");
instance2.addItem("Item 2");

console.log(instance1.getItems()); // ["Item 1", "Item 2"]
console.log(instance1 === instance2); // true
```

A more modern JavaScript approach using modules:

```javascript
// database.js
let instance;

class Database {
  constructor() {
    this.connections = 0;
    console.log("Database initialized");
  }
  
  connect() {
    this.connections++;
    console.log(`Connection #${this.connections} established`);
  }
}

export default function getDatabase() {
  if (!instance) {
    instance = new Database();
  }
  return instance;
}

// Usage in different modules
// module1.js
import getDatabase from './database.js';
const db1 = getDatabase();
db1.connect(); // "Connection #1 established"

// module2.js
import getDatabase from './database.js';
const db2 = getDatabase();
db2.connect(); // "Connection #2 established" (same instance)
```

**When to use**: When you need exactly one instance of a class, like configuration managers, connection pools, or application state.

### 2. Factory Pattern

Creates objects without exposing the instantiation logic to the client.

```javascript
class Car {
  constructor(make, model) {
    this.make = make;
    this.model = model;
    this.type = 'car';
  }
  
  getInfo() {
    return `${this.make} ${this.model} (${this.type})`;
  }
}

class Truck {
  constructor(make, model, payload) {
    this.make = make;
    this.model = model;
    this.payload = payload;
    this.type = 'truck';
  }
  
  getInfo() {
    return `${this.make} ${this.model} (${this.type}) - Payload: ${this.payload}kg`;
  }
}

class VehicleFactory {
  createVehicle(make, model, options = {}) {
    if (options.type === 'truck') {
      return new Truck(make, model, options.payload || 1000);
    } else {
      return new Car(make, model);
    }
  }
}

// Usage
const factory = new VehicleFactory();
const car = factory.createVehicle('Toyota', 'Corolla');
const truck = factory.createVehicle('Ford', 'F150', { 
  type: 'truck', 
  payload: 2000 
});

console.log(car.getInfo()); // "Toyota Corolla (car)"
console.log(truck.getInfo()); // "Ford F150 (truck) - Payload: 2000kg"
```

**When to use**: When you need to create objects that share certain characteristics without knowing the specific types in advance.

### 3. Builder Pattern

Separates the construction of a complex object from its representation.

```javascript
class HouseBuilder {
  constructor() {
    this.house = {};
  }
  
  addFloors(floors) {
    this.house.floors = floors;
    return this;
  }
  
  addDoors(doors) {
    this.house.doors = doors;
    return this;
  }
  
  addWindows(windows) {
    this.house.windows = windows;
    return this;
  }
  
  addPool(hasPool) {
    this.house.hasPool = hasPool;
    return this;
  }
  
  addGarage(capacity) {
    this.house.garage = { capacity };
    return this;
  }
  
  build() {
    return this.house;
  }
}

// Usage
const house = new HouseBuilder()
  .addFloors(2)
  .addDoors(4)
  .addWindows(12)
  .addPool(true)
  .addGarage(2)
  .build();

console.log(house);
// Output: { floors: 2, doors: 4, windows: 12, hasPool: true, garage: { capacity: 2 } }
```

**When to use**: When you need to create complex objects with numerous optional components or configurations.

### 4. Prototype Pattern

Creates new objects by cloning existing ones (prototypes).

```javascript
const carPrototype = {
  init(make, model) {
    this.make = make;
    this.model = model;
    this.currentSpeed = 0;
    return this;
  },
  
  accelerate() {
    this.currentSpeed += 10;
    console.log(`${this.make} ${this.model} is now going ${this.currentSpeed} mph`);
  },
  
  brake() {
    this.currentSpeed = Math.max(0, this.currentSpeed - 10);
    console.log(`${this.make} ${this.model} slowed down to ${this.currentSpeed} mph`);
  }
};

// Usage
const car1 = Object.create(carPrototype).init("Toyota", "Camry");
const car2 = Object.create(carPrototype).init("Honda", "Civic");

car1.accelerate(); // "Toyota Camry is now going 10 mph"
car2.accelerate(); // "Honda Civic is now going 10 mph"
car1.accelerate(); // "Toyota Camry is now going 20 mph"
```

**When to use**: When you need to create many instances that share common properties and methods.

## Structural Patterns

### 1. Module Pattern

Provides a way to organize and encapsulate code, creating private and public methods.

```javascript
const shoppingCart = (function() {
  // Private variables
  let items = [];
  
  // Private functions
  function calculateTotal() {
    return items.reduce((total, item) => total + item.price, 0);
  }
  
  // Public interface
  return {
    addItem(item) {
      items.push(item);
    },
    
    removeItem(index) {
      items.splice(index, 1);
    },
    
    getItems() {
      return [...items]; // Return a copy to protect the private array
    },
    
    getItemCount() {
      return items.length;
    },
    
    getTotal() {
      return calculateTotal();
    }
  };
})();

// Usage
shoppingCart.addItem({ name: "Book", price: 29.99 });
shoppingCart.addItem({ name: "Laptop", price: 999.99 });
console.log(shoppingCart.getItems());
console.log(shoppingCart.getTotal()); // 1029.98
```

With ES6 modules, this pattern is now built into the language:

```javascript
// shoppingCart.js
// Private variables
let items = [];

// Private functions
function calculateTotal() {
  return items.reduce((total, item) => total + item.price, 0);
}

// Public interface (exports)
export function addItem(item) {
  items.push(item);
}

export function removeItem(index) {
  items.splice(index, 1);
}

export function getItems() {
  return [...items];
}

export function getItemCount() {
  return items.length;
}

export function getTotal() {
  return calculateTotal();
}

// Usage in another file
import * as shoppingCart from './shoppingCart.js';
shoppingCart.addItem({ name: "Book", price: 29.99 });
```

**When to use**: When you need to organize and encapsulate related functionality.

### 2. Decorator Pattern

Attaches additional responsibilities to objects dynamically.

```javascript
class Coffee {
  cost() {
    return 5;
  }
  
  description() {
    return "Regular Coffee";
  }
}

// Decorators
class MilkDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }
  
  cost() {
    return this.coffee.cost() + 1.5;
  }
  
  description() {
    return `${this.coffee.description()} with Milk`;
  }
}

class CaramelDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }
  
  cost() {
    return this.coffee.cost() + 2;
  }
  
  description() {
    return `${this.coffee.description()} with Caramel`;
  }
}

// Usage
let myCoffee = new Coffee();
console.log(myCoffee.description()); // "Regular Coffee"
console.log(myCoffee.cost()); // 5

myCoffee = new MilkDecorator(myCoffee);
console.log(myCoffee.description()); // "Regular Coffee with Milk"
console.log(myCoffee.cost()); // 6.5

myCoffee = new CaramelDecorator(myCoffee);
console.log(myCoffee.description()); // "Regular Coffee with Milk with Caramel"
console.log(myCoffee.cost()); // 8.5
```

**When to use**: When you need to add responsibilities to objects dynamically and transparently.

### 3. Adapter Pattern

Converts the interface of a class into another interface that clients expect.

```javascript
// Old API
class OldCalculator {
  operations(term1, term2, operation) {
    switch(operation) {
      case 'add':
        return term1 + term2;
      case 'sub':
        return term1 - term2;
      default:
        return NaN;
    }
  }
}

// New API
class NewCalculator {
  add(term1, term2) {
    return term1 + term2;
  }
  
  subtract(term1, term2) {
    return term1 - term2;
  }
  
  multiply(term1, term2) {
    return term1 * term2;
  }
}

// Adapter
class CalculatorAdapter {
  constructor() {
    this.newCalculator = new NewCalculator();
  }
  
  operations(term1, term2, operation) {
    switch(operation) {
      case 'add':
        return this.newCalculator.add(term1, term2);
      case 'sub':
        return this.newCalculator.subtract(term1, term2);
      case 'mult':
        return this.newCalculator.multiply(term1, term2);
      default:
        return NaN;
    }
  }
}

// Usage - client code expects the old interface
const oldCalc = new OldCalculator();
const newCalc = new CalculatorAdapter();

console.log(oldCalc.operations(10, 5, 'add')); // 15
console.log(newCalc.operations(10, 5, 'add')); // 15
console.log(newCalc.operations(10, 5, 'mult')); // 50
```

**When to use**: When you need to make incompatible classes work together.

### 4. Proxy Pattern

Provides a surrogate or placeholder for another object to control access to it.

```javascript
class ExpensiveAPI {
  getData() {
    // Imagine this makes expensive network calls
    console.log("Fetching data from expensive API...");
    return { results: [1, 2, 3, 4, 5] };
  }
}

class CachedAPIProxy {
  constructor() {
    this.api = new ExpensiveAPI();
    this.cache = null;
  }
  
  getData() {
    if (!this.cache) {
      console.log("No cache available, fetching fresh data");
      this.cache = this.api.getData();
    } else {
      console.log("Returning cached data");
    }
    return this.cache;
  }
  
  clearCache() {
    console.log("Cache cleared");
    this.cache = null;
  }
}

// Usage
const api = new CachedAPIProxy();
api.getData(); // API call made
api.getData(); // Cache returned
api.getData(); // Cache returned
api.clearCache();
api.getData(); // API call made again
```

**When to use**: When you need to control access to an object, add functionality (like caching), or for lazy initialization.

## Behavioral Patterns

### 1. Observer Pattern

Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  subscribe(eventName, callback) {
    if (!this.events[eventName]) {
      this.events[eventName] = [];
    }
    this.events[eventName].push(callback);
    
    // Return an unsubscribe function
    return () => {
      this.events[eventName] = this.events[eventName].filter(cb => cb !== callback);
    };
  }
  
  emit(eventName, ...args) {
    const callbacks = this.events[eventName] || [];
    callbacks.forEach(callback => {
      callback(...args);
    });
  }
}

// Usage
const emitter = new EventEmitter();

const unsubscribe = emitter.subscribe('userUpdated', user => {
  console.log(`User updated: ${user.name}`);
});

emitter.emit('userUpdated', { id: 1, name: 'John Doe' });
// Outputs: "User updated: John Doe"

// Stop receiving updates
unsubscribe();

// This won't trigger the callback anymore
emitter.emit('userUpdated', { id: 1, name: 'Jane Doe' });
```

**When to use**: When changes to one object may need to be reflected in other objects without coupling them together.

### 2. Strategy Pattern

Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

```javascript
class ShippingStrategy {
  calculate(package) {
    // Abstract method, to be implemented by subclasses
  }
}

class FedExStrategy extends ShippingStrategy {
  calculate(package) {
    // FedEx specific calculation
    return package.weight * 2.45;
  }
}

class UPSStrategy extends ShippingStrategy {
  calculate(package) {
    // UPS specific calculation
    return package.weight * 1.95 + 5;
  }
}

class USPSStrategy extends ShippingStrategy {
  calculate(package) {
    // USPS specific calculation
    return package.weight * 1.5 + 2;
  }
}

class Shipping {
  constructor(strategy) {
    this.strategy = strategy;
  }
  
  setStrategy(strategy) {
    this.strategy = strategy;
  }
  
  calculate(package) {
    return this.strategy.calculate(package);
  }
}

// Usage
const package = { weight: 10, destination: 'US' };
const shipping = new Shipping(new FedExStrategy());

console.log(`FedEx: $${shipping.calculate(package)}`);

shipping.setStrategy(new UPSStrategy());
console.log(`UPS: $${shipping.calculate(package)}`);

shipping.setStrategy(new USPSStrategy());
console.log(`USPS: $${shipping.calculate(package)}`);
```

A more modern JavaScript approach using functions:

```javascript
// Strategies as functions
const shippingStrategies = {
  fedex: package => package.weight * 2.45,
  ups: package => package.weight * 1.95 + 5,
  usps: package => package.weight * 1.5 + 2
};

function calculateShipping(package, strategy) {
  return shippingStrategies[strategy](package);
}

// Usage
const package = { weight: 10 };
console.log(`FedEx: $${calculateShipping(package, 'fedex')}`);
console.log(`UPS: $${calculateShipping(package, 'ups')}`);
```

**When to use**: When you have multiple algorithms for a specific task and want to switch between them at runtime.

### 3. Command Pattern

Encapsulates a request as an object, thereby allowing parameterization of clients with different requests, queuing of requests, and logging of operations.

```javascript
class Light {
  constructor(location) {
    this.location = location;
    this.isOn = false;
  }
  
  toggle() {
    this.isOn = !this.isOn;
    console.log(`${this.location} light is now ${this.isOn ? 'on' : 'off'}`);
  }
}

class LightCommand {
  constructor(light) {
    this.light = light;
  }
  
  execute() {
    this.light.toggle();
  }
}

class RemoteControl {
  constructor() {
    this.commands = [];
  }
  
  addCommand(command) {
    this.commands.push(command);
  }
  
  pressButton(index) {
    if (this.commands[index]) {
      this.commands[index].execute();
    }
  }
}

// Usage
const kitchenLight = new Light('Kitchen');
const bedroomLight = new Light('Bedroom');

const kitchenLightCommand = new LightCommand(kitchenLight);
const bedroomLightCommand = new LightCommand(bedroomLight);

const remote = new RemoteControl();
remote.addCommand(kitchenLightCommand);
remote.addCommand(bedroomLightCommand);

remote.pressButton(0); // Kitchen light is now on
remote.pressButton(1); // Bedroom light is now on
remote.pressButton(0); // Kitchen light is now off
```

**When to use**: When you need to parameterize objects with operations, queue operations, implement undo/redo functionality, or log changes.

### 4. State Pattern

Allow an object to alter its behavior when its internal state changes.

```javascript
class TrafficLight {
  constructor() {
    this.states = {
      green: new GreenLightState(this),
      yellow: new YellowLightState(this),
      red: new RedLightState(this)
    };
    this.currentState = this.states.green;
  }
  
  change() {
    this.currentState.change();
  }
  
  setState(state) {
    this.currentState = state;
  }
}

class LightState {
  constructor(light) {
    this.light = light;
  }
  
  change() {
    throw new Error("This method should be overridden");
  }
}

class GreenLightState extends LightState {
  change() {
    console.log("Green light changing to yellow");
    this.light.setState(this.light.states.yellow);
  }
}

class YellowLightState extends LightState {
  change() {
    console.log("Yellow light changing to red");
    this.light.setState(this.light.states.red);
  }
}

class RedLightState extends LightState {
  change() {
    console.log("Red light changing to green");
    this.light.setState(this.light.states.green);
  }
}

// Usage
const trafficLight = new TrafficLight();
trafficLight.change(); // "Green light changing to yellow"
trafficLight.change(); // "Yellow light changing to red"
trafficLight.change(); // "Red light changing to green"
```

**When to use**: When an object's behavior changes based on its internal state and it has many different states.

## Exercise: Implementing Design Patterns

1. **Shopping Cart**: Create a shopping cart system using the Module pattern. The cart should allow adding items, removing items, and calculating the total. It should also support discount coupons that can be applied to the total.

2. **UI Component Library**: Implement a simple UI component library with a Factory pattern that can create different types of components (Button, Input, Checkbox) based on a configuration object.

3. **Form Validation**: Create a form validation system using the Strategy pattern, with different validation strategies for email, password, and numeric fields.

4. **Chat Application**: Build a simple chat application structure using the Observer pattern where users can subscribe and unsubscribe from chat rooms, and messages are broadcasted to all subscribers.

## Real-world applications

Design patterns are used extensively in many frameworks and libraries:

- **React**: Uses the Observer pattern for state management
- **jQuery**: Implements the Facade pattern to simplify DOM manipulation
- **Express**: Uses the Middleware pattern (a variation of the Chain of Responsibility pattern)
- **Redux**: Implements the Observer pattern and the Command pattern

## Conclusion

Design patterns provide tested, proven solutions to common problems in software design. By understanding and applying these patterns, you can write more maintainable, flexible code and communicate more effectively with other developers. However, it's important to remember that patterns should be used judiciously - not every problem needs a pattern, and force-fitting a pattern can lead to unnecessary complexity.

Next, we'll explore advanced asynchronous programming techniques in JavaScript, including advanced Promise patterns and different concurrency models.