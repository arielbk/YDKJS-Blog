# Notes for You Don't Know JavaScript - Up and Going

The original book can be found [here](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20&%20going/README.md)

I have used JavaScript a little bit before, but here I am starting from the beginning. These ideas are foundational, and that's why it is necessary to go through and revisit them from time to time. I will be typing (not copy and pasting) and testing out all the code snippets I come across and really trying to get a firm grasp of the concepts that I come across, then relaying them here in (as much as I can) my own words.


## 7 types in JavaScript:
(In JS it is the **values** themselves that are typed rather than the **variables**)

- `string`
- `number`
- `boolean`
- `null` and `undefined` (interesting that these two are listed together)
- `object`
- `symbol` (this is apparently new in ES6... will learn about this later)

I can use `typeof x;` to find out the type of a value, it returns a `string` value.
`typeof null;` returns `"object"`; this is a longstanding bug.


## Objects
They consist of a key and a value, where each element in the object is a 'key–value pair'. A key can contain any type of value.

Dot notation and bracket notation (`object.key` vs `object["key"]`) — dot notation is usually preferable because it is easier to read and more concise. Bracket notation can be useful when plugging in a variable as a key within the object.

Moving on: arrays and functions are actually like subtypes of objects...

Arrays are denoted by square brackets, e.g: `var names = ['James', 'Somboon', 'Akanksha']`
They are a special type of object that uses an index number (starting at 0) as a key. They are accessed through bracket notion: `names[0]` would return `"James"` and `names[2]` would return `"Akanksha"`.

Functions are also a subtype of objects, even though `typeof` returns `"function"`
Functions can have properties but they are not often used.

Note the difference between `typeof foo` when foo is a function – and `typeof foo()`, which will plug in and check the type of the return value of the foo function.


## The built-in types have method and properties by default
```javascript
a.length // property that returns the length of the variable`
a.toUpperCase() // returns a string with all uppercase letters
a.toFixed(x) // returns a float to x decimal places
```

From my understanding this is possible because the 'object' (uncapitalised) is a prototype of its 'native', or 'Object' (capitalised).

I'm sure I will come to this more later.


## Comparing values always produces a boolean (true or false)
Explicit:
```javascript
var num = 42;
var Number(a);
```

Implicit:
```javascript
var a = 42;
var b = "1";
var result = a * b; // returns the number 43
```
Even though b is actually a string, this works because `var b` is **implicitly coerced**.

But be careful, because: `result = a + b;` will return '421', instead treating both as strings and concatenating...

### 'Truthy' values
- `"any none empty string"`
- `42`
- `true`
- `[ "any", "array", "even", "if", "it's", "empty!" ]`
- `{ and: "objects", even: "empty", ones: 2 }`
- `function foo() { console.log("functions are truthy") }`

### 'Falsy' values
- `""` (an empty string)
- `0`, `-0`, `NaN` (as a sidenote - I would have thought `-0` would be simplified to `0`.. it is not)
- `null`, `undefined`
- `false`

We must be careful of thinking we have coerced a value to a boolean when actually we haven't..

### Equality
`==` loosely equal vs. `===` strictly equal (where no implicit coercion will occur)
`0 == ""` returns `true` but `0 === ""` returns `false`.

This is one to watch out for... 'non-primitive' values are compared **by reference** only. So consider this example given in the book:
```javascript
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";
a == c;		// true
b == c;		// true
a == b;		// false
```

### Inequality
`<` `>` `<=` and `>=` - pretty straightforward I guess...
If one value cannot be coerced then we can come across situations where `a > b`, `a < b` and `a == b` are all false.

## Variables
Reserve words we cannot use
Variable names must be valid **identifiers** - they must start with a letter, `$` or `_`, and they can only contain these characters and numbers

## Scope
Using var to declare a variable either within an element, descending to all its children also, or globally at the topmost level.

### Hoisting
So, you can call a function at the top of a program and declare it at the end just fine. This works because of the way that JS is compiled at runtime.

If we set a variable without formally declaring it, it can break the scope and assign the value as a global variable. This is very bad practice, but strict mode (mentioned later) prevents it.

The `let` keyword from ES6 allows us to be more precise with our scope. It will create a scope for that variable within any pair of `{}` (any block) including loops and conditionals, rather than only within a function.

## Conditionals
A switch statement can be a useful alternative for a long list of if-elses.
The biggest slip up with these is forgetting to break the statement. Purposefully breaking a statement and instead letting it run on can also be very useful.

`var conclusion = a > b ? "a is greater than b" : "a is not greater than b";`
Ternary operator is also a useful one for short if-else statements.

## Strict mode
It ensures the code is safer and more optimisable by the JS engine.

Put `"use strict";` at the beginning of anything, depending on where you want it in terms of scope; it can be just inside a function or it can be global.

One important thing is it stops variables from being **set** without first being **declared**.

## Functions as Values
Functions themselves are values. They can be named or they can be anonymous. They can be assigned to a variable or not.

## IIFE = Immediately Invoked Function Expression
Wrap a function in `()`'s and add another `()` to the end to invoke it immediately.
```javascript
(function IIFE() {
  console.log('This function is declared and immediately called');
})();
```

This method is often used to do something immediately without affecting the surrounding scope, because scope is confined to the function itself.

## Closure
Now this concept really confuses me. Right now it seems unintuitive...
Let's try to wrap my head around this. So creating a customisable adder function...

```javascript
function makeAdder(x) {
  function add(y) {
    return x + y;
  }
  return add;
}

var addFive = makeAdder(5);
console.log(addFive(2)); // 7
```

So when the makeAdder function is invoked by assigning it to a variable, a value is passed in. For `addFive` this is 5. That value is then passed into the `add` function within `makeAdder` and there it is **remembered**.

`makeAdder` then actually returns the function within itself, `add()`, along with the value that was passed into it and saved.

So when makeAdder returns the function the variable definition effectively becomes:

```javascript
var addFive = function add(y) {
                return 5 + y;
              }
```
So, now addFive can be used as a function with the value passed in when it was created remembered.

Closures are particularly helpful with public APIs, variables can be instantiated from a function and values can be stored inside them privately. Still struggling to get my head around this one, but I'm sure it will come with practice. Scopes and Closures is the next book, after all.

## This identifier
Okay, this will be interesting to look at. I have been using `this` for a while now, and I sort of know what its all about, but not really...
**`this` does not refer to the function itself, but rather how it was called**

`this` can work in one of four ways:
- if there is nothing within a basic function that `this` can refer to it will search for a global variable, or else return `undefined` in strict-mode
- if you call a function as a value within an object, `this` refers to that object
- when the call() method is used, `this` belongs to the function where the call is
- if you use `new` then `this` is reset to a brand new instance of the function

## Prototypes
If you reference a property on an object and it doesn't exist on that object explicitly, the objects prototype will be searched as a sort of 'fallback'.

This behaviour can emulate class and inheritance, but it is not that. It is far more realistic and beneficial to view it as 'behaviour delegation' – covered indepth later.

## Old and New
Many of the techniques in JS will not work on older browsers, and this needs to be addressed. We have two options: polyfilling and transpiling.

### Polyfilling
A term coined by Remy Sharp in 2010. It is basically a fallback option that provides the same or similar functionality for older browsers.

Be really careful when trying to implement a polyfill yourself, and stick as closely to specification as possible. The safer option is to use a polyfill or a shim already out there.

### Transpiling
Transpiling is a portmanteau of transforming and compiling. It is necessary when there is new **syntax** added to the language because it is not possible to polyfill these.

By using a polyfill we can produce more current, readable and performance-optimised code while still serving working code to older browsers.

JS is changing constantly and will continue, so get into the habit of using a transpiler now.

### Non-JavaScript
A lot of the stuff that we write in JS is not actually directly controlled by JavaScript itself.

Think about the DOM API. That's just what it is: an API. It is an interface that interacts with the browser, many of which are written in C or C++. `document.getElementById()`, `alert()` or `console.log()` are all just ways that JS can interface with the browser itself.

Keep this in mind.

## Review

This has been a run-through of the foundations of JavaScript programming. The following books will get into some of the more gritty details of the language.

Thinking about it, I'm still not entirely sure why you would assign a function to a variable in the first place. Why do this when you can just name the variable... The answer will become clear very soon, I'm sure.
