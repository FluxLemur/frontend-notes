# Notes from [A Reintroduction to Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)

## Numbers
- Numbers are 64-bit doubles
- `Math` object is built in (abs, pow, random, etc.)
- `parseInt` ~= `+`
    - except `+ '123x'` throws while `psrseInt('123x')` works
- `NaN` is toxic, `isNaN`
- `Infinity` and `-Infinity`, `isFinite`

## Strings
- Unicode characters,
- `'hello'.length`, `.charAt()`, `.substr()`, etc.

## Bool, null, undefined
- explicit conversion `Boolean('test')`
- `false`, `0`, `""`, `NaN`, `null`, and `undefined` become `false`
- `null` explicit non-value
- `undefined` == uninitialized

## Variables
- `let` and `const` are block-scope
- `const` does not mean immutable! Just that the variable cannot be re-assigned.
  (Unlike C++)
- `var` is function scope (even if you declare in the middle!)

`var` example:
```
(function() {
    console.log(v); // undefined
    { var v = 1; }
    console.log(v); // 1
}())
```
`let` example:
```
(function() {
    console.log(v); // throws
    { let v = 1; }
    console.log(v); // throws
}())
```

## Operators
- `+` for string concatination
```
'3' + 4 + 5;  // "345"
 3 + 4 + '5'; // "75"
```
- `==` does type coercion
    - `123 == '123' // true`
- `===` strict comparison (and `!==`)

## Control Structure
- if, else if, else, while, do while same as C
- `for (const value of array) { ... }`
- `for (const property in object) { ... }`

## Arrays
```
let a = ['dog', 'cat', 'hen'];
a[100] = 'fox';
a.length; // 101
```
- `[1, 2, 3].forEach(function(currentValue, index, array)`
- [Array docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

## Functions
- `function add(x, y) { return x+y; }`
    - `add(); // NaN` since both `x` and `y` are undefined
    - `add(2, 3, 4); // 5` since 4 is ignored
- `arguments` array-like object
- `function avg(...args)` like `def f(*args)` in Python
- use `f.apply(null, [1, 2, ...]` or `f(...args)` (spread operator)
- anonymous functions `let x = function() {};`
- to recursively call an anonymous function, just provide it a name:
    - `let x = function count() {}`

## Objects
- objects are functions
```
function makePerson(first, last) {
  return {
    first: first,
    last: last,
    fullName: function() {
      return this.first + ' ' + this.last;
    },
    fullNameReversed: function() {
      return this.last + ', ' + this.first;
    }
  };
}

// NOTE how `this` is not `s` in the following example
// (instead, `this` will be the global object)
s = makePerson('Simon', 'Willison');
var fullName = s.fullName;
fullName(); // undefined undefined
```

Shorthand, for the above:
```
// common pracrice to capitalize Constructor functions
function Person(first, last) {
  this.first = first;
  this.last = last;
  this.fullName = function() { ... }
  this.fullNameReversed = function() { ... }
}

// constructor functions must be called with `new`
let you = new Person('You', 24);
you.name = 'Simon';
you['name'] = 'Simon';
```

Downside of the above is that every Person duplicates the member function code.
We can do better still by using a "prototype chain".
```
function Person(first, last) {
  this.first = first;
  this.last = last;
}
Person.prototype.fullName = function() {
  return this.first + ' ' + this.last;
};
Person.prototype.fullNameReversed = function() {
  return this.last + ', ' + this.first;
};
```

- NOTE: you can set prototype functions on even built-in classes:
`String.prototype.reversed = function() { ... }`
- `apply` first param is what to populate as `this`:
```
function newImpl(constructor, ...args) {
  var o = {}; // Create an object
  constructor.apply(o, args);
  return o;
}
// `newIpml` ~= `new` keyword (minus prototype chain creation)
```
- `f.apply(obj, args)` === `f.call(obj, arg[0], args[1], ...)`

### [More on inheritance and prototype chains](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
- "property shadowing" is when an object implements a parameter of the prototype.
  The _object_ param takes precedence.
- `Object.create` for making your own prototype chain.
- ES2015 introduced many OO concepts:
```
class Polygon {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}

class Square extends Polygon {
  constructor(sideLength) {
    super(sideLength, sideLength);
  }
  get area() {
    return this.height * this.width;
  }
  set sideLength(newLength) {
    this.height = newLength;
    this.width = newLength;
  }
}

var square = new Square(2);
```

- Performance considerations
    - Trying to access nonexistent properties will always traverse the full prototype chain.
    - When iterating over the properties of an object, every enumerable
      property that is on the prototype chain will be enumerated
- NOTE: it's bad practice to extend built in prototypes (like `Object.prototype`)

## [Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- basically just a lambda function
```
(param1, param2, …, paramN) => { statements }
(param1, param2, …, paramN) => expression
// equivalent to: => { return expression; }

// Parentheses are optional when there's only one parameter name:
(singleParam) => { statements }
singleParam => { statements }

// The parameter list for a function with no parameters should be written with a pair of parentheses.
() => { statements }

// Parenthesize the body of function to return an object literal expression:
params => ({foo: bar})

// Rest parameters and default parameters are supported
(param1, param2, ...rest) => { statements }
(param1 = defaultValue1, param2, …, paramN = defaultValueN) => {
statements }

// Destructuring within the parameter list is also supported
var f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c; f(); // 6
```

## [Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)
- Functions are "hoisted" to the top of the scope they're defined in.
- (i.e. you can use a function and then define it in the next line)
- Functions are hoisted, `var` is hoisted
- Class declarations are *not* hoisted.

## [Template Literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
- e.g. Python format strings
```
`Value is ${5 + 1}`
```
