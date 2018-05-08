# Notes for [You Don't Know JavaScript - Up and Going](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md)

I will probably try to get through this first book quickly because I have worked with JavaScript a little bit before and I have a grasp some of the more foundational programming ideas.

But that's it, they are foundational. That's why it is necessary for me to go through and revisit from time to time. I will be typing (not copy and pasting) and testing out all the code snippets I come across also.


## 7 types in JavaScript:
The **values** in JS are typed rather than the **variables** themselves.

- `string`
- `number`
- `boolean`
- `null` and `undefined` (interesting that these two are listed together)
- `object`
- `symbol` (this is apparently new in ES6... I'd never heard of it)

I can use `typeof x;` to find out the type of a value, it returns a `string` value.
`typeof null;` returns `"object"`; apparently its a bug.


## Objects
Consist of a key and a value, each element is a 'key–value pair'. A key can contain any type of value.

Dot notation and bracket notation - dot notation is usually preferable because it is easier to read and more concise.

Bracket notation can be useful when plugging in a variable as a key within the object.

Moving on: arrays and functions are actually like subtypes of objects...

Arrays are denoted by square brackets, e.g: `var names = ['James', 'Somboon', 'Akanksha']`
They are a special type of object that uses an index number (starting at 0) as a key.

Functions are also a subtype of objects, even though `typeof` returns `'object'`
Functions can have properties but they are not often used.

Note the difference between `typeof foo` when foo is a function – and `typeof foo()`, which will plug in and check the type of whatever is returned by the foo function.


## The built-in types have method and properties by default
```javascript
a.length // property that returns the length of the variable`
a.toUpperCase() // returns a string with all uppercase letters
a.toFixed(x) // returns a float to x decimal places
```

From my understanding this is possible because the 'object' (uncapitalised) is a prototype of its 'native', or 'Object' capitalised.

I'm sure I will come to this more later.


## Comparing values always produces a boolean (true or false)
'Explicit' and 'implicit' coercion is crucial to understand, so that coercion can be predicted.

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
Even though b is actually a string, this works because it is **implicitly coerced**.

But be careful, because: `result = a + b;` will return '421', instead treating both as strings and concatenating...

### 'Truthy' values
- ``"any none empty string"``
- `42`
- `true`
- `[ "any", "array", "even", "if", "it's", "empty!" ]`
- `{ and: "objects", even: "empty", ones: "too" }`
- `function foo() {} function`

### 'Falsy' values
- `""` (an empty string)
- `0,` `-0` (how could this be?), `NaN`
- `null`, `undefined`
- `false`

Apparently we must be careful of thinking you have coerced a value to a boolean when you actually haven't done any such thing..

### Equality
`==` loosely equal and `===` strictly equal, where no implicit coercion will occur.
`0 == ""` is true but `0 === ""` is not.

This is a weird one... 'non-primitive' values are compared **by reference** only. So consider this example given in the book:

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
Reserve words
Variable names must be valid **identifiers**

## Scope
Using var to declare a variable either within an element, descending to all its children also, or globally at the topmost level.

### Hoisting
So, you can call a function at the top of a program and declare it at the end just fine. This works because of the way that JS is compiled.

If we set a variable without formally declaring it, it can break the scope and assign the value as a global variable. This is very bad practice.

The `let` keyword from ES6 allows us to be more precise with our scope. It will create a scope for that variable within any pair of `{}` (any block) including loops and conditionals. Var would only be within, for example, a function.

## Conditionals
Switch statement is a useful alternative for a long list of if elses in some cases.

The biggest gotcha is forgetting to break the statement when you want it to. Not breaking a statement and instead letting it run on can also be very useful.

Ternary operator is also a useful one for short statements.

## Strict mode
This is probably something that I should use that I haven't been. Why? It ensures the code is safer and more optimisable by the JS engine.

Put `"use strict";` at the beginning of anything, depending on where you want it in terms of scope; it can be just inside a function or it can be global.

One important thing is it stops variables from being **set** without first being **declared**.

## Functions as Values
Functions themselves are values.

They can be named or they can be anonymous.

They can be assigned to a variable or not.

## IIFE = Immediately Invoked Function Expression
Wrap a function in `( )`'s and add another `()` to the end to invoke it immediately. Makes sense.

```javascript
(function IIFE() {
  console.log('This function is declared and immediately called');
})();
```

This method is often used to do something immediately without affecting the surrounding scope, as scope is confined to the function itself.

## Closure
Now this concept confuses the hell out of me. Right now it is unintuitive to me...

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
So, now addFive can be used as a normal function.

Closures are particularly helpful with public APIs, variables can be instantiated from a function and values can be stored inside them privately. Still struggling to get my head around this one, but I'm sure it will come with practice.

Scopes and Closures is the next book, after all.

## This identifier
Okay, this will be interesting to look at. I have been using `this` for a while now, and I sort of know what its all about, but not really...
