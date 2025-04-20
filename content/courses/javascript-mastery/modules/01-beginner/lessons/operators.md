# Operators in JavaScript

Operators are special symbols used to perform operations on values (called operands).

## 1. Arithmetic Operators

Used for mathematical calculations:

-   `+` (Addition): `5 + 3` results in `8`
-   `-` (Subtraction): `10 - 4` results in `6`
-   `*` (Multiplication): `6 * 2` results in `12`
-   `/` (Division): `8 / 4` results in `2`
-   `%` (Modulo - Remainder): `10 % 3` results in `1` (10 divided by 3 is 3 with a remainder of 1)
-   `**` (Exponentiation - ES2016): `2 ** 3` results in `8` (2 to the power of 3)

**Increment/Decrement:**
-   `++` (Increment): Increases a number by 1. `let x = 5; x++;` makes `x` become `6`.
-   `--` (Decrement): Decreases a number by 1. `let y = 5; y--;` makes `y` become `4`.

```javascript
let apples = 5;
let oranges = 3;
let totalFruit = apples + oranges; // 8

let price = 19.99;
let quantity = 3;
let totalCost = price * quantity; // 59.97

let count = 10;
count++; // count is now 11
```

## 2. Assignment Operators

Used to assign values to variables:

-   `=` (Assignment): `let age = 30;`
-   `+=` (Add and assign): `x += 5` is shorthand for `x = x + 5`
-   `-=` (Subtract and assign): `y -= 2` is shorthand for `y = y - 2`
-   `*=` (Multiply and assign): `z *= 10` is shorthand for `z = z * 10`
-   `/=` (Divide and assign): `w /= 4` is shorthand for `w = w / 4`
-   `%=` (Modulo and assign): `r %= 3` is shorthand for `r = r % 3`

```javascript
let score = 100;
score += 50; // score is now 150
score -= 25; // score is now 125
```

## 3. Comparison Operators

Used to compare two values, resulting in `true` or `false`:

-   `==` (Equal to): Checks if values are equal (performs type coercion). `5 == "5"` is `true`. **Avoid using this.**
-   `===` (Strictly equal to): Checks if values AND types are equal. `5 === "5"` is `false`. `5 === 5` is `true`. **Use this.**
-   `!=` (Not equal to): Checks if values are not equal (performs type coercion). `5 != "5"` is `false`. **Avoid using this.**
-   `!==` (Strictly not equal to): Checks if values OR types are not equal. `5 !== "5"` is `true`. `5 !== 8` is `true`. **Use this.**
-   `>` (Greater than): `10 > 5` is `true`
-   `<` (Less than): `10 < 5` is `false`
-   `>=` (Greater than or equal to): `10 >= 10` is `true`
-   `<=` (Less than or equal to): `5 <= 10` is `true`

```javascript
let userAge = 18;
let votingAge = 18;
let canVote = userAge >= votingAge; // true

let itemPrice = 50;
let budget = 100;
let canAfford = itemPrice <= budget; // true

console.log(100 === "100"); // false (different types)
console.log(100 !== 100);   // false (same value and type)
```

## 4. Logical Operators

Used to combine or modify boolean values (`true`/`false`):

-   `&&` (Logical AND): Returns `true` if BOTH operands are `true`.
    `true && true` is `true`
    `true && false` is `false`
-   `||` (Logical OR): Returns `true` if AT LEAST ONE operand is `true`.
    `true || false` is `true`
    `false || false` is `false`
-   `!` (Logical NOT): Reverses the boolean value.
    `!true` is `false`
    `!false` is `true`

```javascript
let hasTicket = true;
let isMember = false;
let canEnter = hasTicket || isMember; // true (needs only one to be true)

let isAdult = true;
let hasPermission = false;
let canWatchMovie = isAdult && hasPermission; // false (needs both to be true)

let isRaining = true;
let needUmbrella = !isRaining; // false
```

## 5. String Concatenation

The `+` operator can also join strings together:

```javascript
let firstName = "Jane";
let lastName = "Doe";
let fullName = firstName + " " + lastName; // "Jane Doe"

let city = "New York";
let info = "User lives in " + city; // "User lives in New York"
```
**Note:** Template literals (backticks `` ` ``) are often preferred for combining strings and variables:
```javascript
let fullName = `${firstName} ${lastName}`;
let info = `User lives in ${city}`;
```

## Operator Precedence

JavaScript follows an order of operations (like PEMDAS/BODMAS in math). Multiplication/Division happen before Addition/Subtraction. Use parentheses `()` to control the order:

```javascript
let result1 = 5 + 3 * 2; // 5 + 6 = 11 (multiplication first)
let result2 = (5 + 3) * 2; // 8 * 2 = 16 (parentheses first)
```

## Exercise

1.  Create a file `operators_exercise.js`.
2.  Declare two number variables, `num1` and `num2`.
3.  Calculate and print their sum, difference, product, and quotient.
4.  Declare a variable `counter` initialized to 0. Increment it and print the new value.
5.  Compare `num1` and `num2` using `===`, `>`, and `<=`. Print the results.
6.  Declare two boolean variables, `isAvailable` and `isCheap`. Use `&&` and `||` to determine if you should buy an item based on these conditions. Print the results.
7.  Create a string variable with your first name and another with your last name. Combine them into a full name using the `+` operator and print it.

## Conclusion

Operators are essential for performing calculations, making comparisons, and controlling the logic flow in your programs.

Next, we'll see how to use comparison and logical operators to make decisions in our code using Control Flow (`if`/`else`).
