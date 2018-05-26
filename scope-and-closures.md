# Notes for You Don't Know JavaScript - Scope and Closures

The original book can be found [here](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md)

While most of the ideas have been touched on in the first book in the series, here it starts to get a bit deeper into the nitty gritty.

**Scope can be thought of as the rules governing how and where variables are stored.**

JS is actually compiled like many languages. Unusually though, JS is usually compiled right before it is executed. It doesn't have time, like many other languages do, to really optimise.

**At a basic level, compilation goes like this:** the source code is 'tokenized' or ('lexed') into meaningful tidbits. These are then placed in a tree like structure, and from this, machine executable code is generated.

## Analogy to understand scope
**The JS engine and its two friends, compiler and scope.**\
Engine oversees the operation; compiler does the dirty work (see above); scope is the stickler with a list of all variables, where they belong and who can access them.

In a simple variable declaration, engine and her friends actually run through, making a list of all variable declarations. Only after this has been done do they run through again and actually start assigning values to this list. This is done by the engine, executing code that is created by compiler, after checking that its okay with scope.

`var answer = 42`
**LHS lookup** would be checking 'answer' (the container name of the article)\
**RHS lookup** would be checking '42' (the value stored in the variable)

The type of lookup that is performed may be more nuanced than you think. Declarations are LHS lookups because we don't care what value already exists in the variable, we simply check that it exists and assign a value to it.

```javascript
function logger(answer) {
  console.log(answer); // 42
}
logger(42);
```
(Function is declared - actually not a LHS lookup because it is handled by the compiler...) -> Answer variable within function is declared (LHS) -> Logger function is called (RHS lookup) -> 42 is assigned to answer within logger (LHS) -> log method is called within the console object (RHS) -> answer variable is passed into console log (RHS) ... phew!

### Nested scope
There are actually multiple scopes. If an RHS lookup does not return anything the engine would attempt to move up one layer of scope until it reached the topmost layer (the global scope).

ReferenceErrors are scope related - they mean that the LHS lookup was unsuccessful

## Lexical scope
Lexical scope is in contrast to *dynamic scope*, which is used by a small number of other programming languages.

Lexical scope pertains to the process where the source code undergoes lexing/tokenizing, where it is broken up into meaningful pieces (to a computer). Lexical scope is therefore scope that is defined at the time when this lexing occurs, at this point it is (or should be) set in stone.

### Lookups
Lookups happen from where they are called, and then farther and farther outer scopes are searched until they are found. **It is just the first matches that are returned.** This is a useful example:
```javascript
var a = 10, b = 20, c = 30;

function foo(a) {
  var b = a * 2;
  function bar(c) {
    console.log(a,b,c);
  }
  bar(3);
}

foo(1); // 1 2 3
```

In this case, although the variables a, b and c have been declared in the global scope, their values within the function scope will be returned because they are the first that are found.

Lookup occurs first within the bar function, then the foo function. At this point the variables are found, thus the lookup stops. This is a case in which the inner variables *shadow* those in the global scope.

### Cheating Lexical Scope
There are some ways to 'cheat' lexical scope, but they are generally frowned upon and lead to poorer performance. Basically, you should avoid them because they are not worth the performance tradeoff.

`eval()` is one way - it can insert dynamically produced code as if it were there from the start, and thus modify lexical scope. In strict mode, however, it creates its own scope.

`new Function()` is another - the last argument is a dynamically created function

`with` is another - it creates an object scope, but you run the risk of accidentally declaring a global variable with it

Strict mode completely disallows `with` and it restricts `eval()` in some instances

The JS Engine has a number of optimisations for compiling code, and these are undermined by cheating lexical scope. It is best practice to just not do it, and your code will be faster for it.


## Function vs. Block Scope
**Generally** speaking, it is functions that create scope in JS. That means that any variables or functions defined within a function are only accessible within that function.

### 'Hiding' variables with function scope
Perhaps the more intuitive way to think of functions is: we create the function, and then we write code inside of it. But here's another way: we use functions to *wrap* already existing pieces of code into self-contained units of code, with their own scope.

By wrapping blocks of code in functions we are able to hide their inner components. This is useful in light of the software design principle 'Principle of Least Privilege'.

The aim of this principle is to optimise security and to only show what is minimally necessary; to only allow access to what is needed for the user.

So, instead of declaring variables in the global scope, if they are only required within a particular function, then declare them within the function itself. This is better software design.

**Not only that, but declaring variables inside of functions will lead to less collisions in the outer scope**

Code libraries will often define a ***global namespace*** where all variables within the library are defined as properties within an object:
```javascript
var exampleLibrary = {
  itemName: "name",
  addItem: function() {
    // function to 'add an item', for example
  },
  deleteItem: function() {
    // function to 'delete an item', for example
  }
}
```
In this example the variable `itemName` and the functions `addItem` and `deleteItem` are not global, they are instead defined within the *namespace* of `exampleLibrary`. Their names do not take up words in the global scope.

Function scope thus allows for **modular design**

If we want to wrap a code snippet and run it immediately we can do so with an IIFE (Immediately Invoked Function Expression, as explained in the first book). This will stop the name of the function itself from convoluting the global namespace.
```javascript
(function foo() {
  console.log("This is an IIFE. The function's name (foo) is not 'remembered' in the global scope");
})();
```
As with any other function, we are also able to pass in arguments if we wish.

Function expressions (basically, functions wrapped in brackets) are often used in callback functions.They do not need to be named, but can rather anonymous.

It is, however, best practice to name them because there are advantages.
- It makes for easier to read code
- So that it can refer to itself
- For easier debugging (as the function name appears in stack traces)


## Blocks as Scopes
While many programming languages allow for not just *function* scope but also *block* scope, traditionally JavaScript does not.
```javascript
function doTheThing() {
  var i = 10;

  for (var i = 0; i < 5; i++) {
    console.log(i); // 0 1 2 3 4
  }

  i += 5;
  console.log(i); // 10
}
```
In the above example we may have expected the second `console.log(i)` to return `15`. The variable `i` is changed by the for loop because the `i` is not block scoped within the for loop.

Instead, `i` is declared and assigned the value `10`. In the for loop it is assigned the value `0` and incremented until it reaches `5`, the loop does not complete this cycle and breaks. `5` is then added to the existing `i` value of `5` and this value is logged, `10`.

This is confusing and one must be very careful of this: `var`s within loops and conditionals are on the same level of scope as the function they are enclosed in.

But there *is* ways of creating block scope in JS

`with` (as mentioned above) - its generally bad practice to use but it does create block scope.

```javascript
try {
  gobbledygook; // this should throw an error
}
catch (err) {
  console.log(err); // this will log the error
}
```
The `catch` clause above actually block scopes the error passed into it, but there are problems with some linters complaining about redefinitions.

### The `let` Keyword
From ES6 we have been blessed with the `let` keyword, which lets us implicitly define variables within a block's scope.

It is suggested to actually wrap any block scopes in an explicit pair of braces to make it super clear where we are scoping our block:
```javascript
var goAhead = true;

if (goAhead) {
  { // we are making it super clear that we are using block scope here
    let dreams = 'infinity';
    console.log(dreams); // infinity
  }
}
console.log(dreams); // ReferenceError
```

A massive gotcha with the `let` keyword: **hoisting will not work with block scoped variables**. That means that you cannot call a variable before you have declared it.\
This will not work:
```javascript
var goAhead = true;

if (goAhead) {
  {
    console.log(dreams); // Uncaught ReferenceError: dreams is not defined
    let dreams = 'infinity';
  }
}
```

Another really useful feature of block scoping is **garbage collection**. Basically, we can allow the JavaScript to 'forget' variables within a scope after it is done with them, in order to optimise our programs.

We must be very careful when re-factoring code, because with the `let` keyword, scopes within blocks can easily be broken.

### The `const` Keyword
Another addition in ES6 is `const`, which can be used to declare a block scoped variable, but one that is fixed. It is constant and if you try to alter its value, you will be thrown an error. This is a useful way to enforce intentions from the beginning.


## Hoisting
**Declaration happens before assignment**.

Consider:
```javascript
console.log(answer); // undefined
var answer = 42;
```
Two things happen in the last line:
1. The declaration of `var answer`
2. The assignment of 42 to the variable `answer`

The compiler will run through the code before it executes it and deal with all declarations and their associated scope before any values are actually assigned. Because of this, when `console.log` is run, `answer` has been *declared* but the number `42` has not yet been *assigned* to it. This explains the return value of `undefined`.

One way to think of this phenomena, is that the *declaration* is moved to the top, it is metaphorically *hoisted*.

Functions are the same, except their 'innards' are actually hoisted as well...\
A subtle point is that functions are actually hoisted first, before any variables.

Declarations for a function will actually be overwritten as we move down the code. This is a tricky one that can seem counterintuitive.\
Here's a good example to illustrate it:
```javascript
function sayHello() {
 return 'Hello!';
}

console.log(sayHello()); // Hi, Hey!

function sayHello() {
	return 'Hi, Hey!';
}
```


# Scope Closure
Closure is an elusive concept to many developers, yet **it is everywhere in JS**. To really understand them means that we can use them consciously.

Even this simple code uses closure in some way:
```javascript
var a = 2
function foo() {
  console.log(a); // 2
}
```
The global scope, where the `var a` is declared and assigned a value, is said to *have closure over* the function, foo.\
This is a trivial example though, we can go deeper with it.

```javascript
function compute() {
  var answer = 42;

  function findQuestion() {
    return '7 * ' + answer / 7;
  }

  return findQuestion;
}

var tellMe = compute();
console.log(tellMe()); // '7 * 6'
```
Let's break this snippet down.\
The `compute` function is declared. Inside the function, the variable `answer` is declared and assigned a value.

Now look at the end of the `compute` function. It returns the inner function `findQuestion` itself. *It does not execute and return the return value of `findQuestion`, it returns the value itself.*

So, let's dive into that inner function, `findQuestion`. It simply returns a string, beginning with `7 * ` and then the variable `answer` divided by 7. Pretty straightforward. It has access to the variable because it is within the scope of `compute`.

Afterwards, in the global scope, we declare the variable `tellMe` and give it the value of whatever is returned when we execute the `compute` function. So that would be the function `computes` inner function, `findQuestion`. Fine.

We execute and log the return value of `tellMe`, and this is where the magic happens. The function `findQuestion` is run, from outside of `compute` but it still has access to its scope. It can still access the `answer` variable, and appropriately returns, `'7 * 6'`. This is the essence of closure.

## Loops & Closure
Closure can cause particular confusion when it comes to loops, specifically, callback functions within loops.

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout( function logger() {
    console.log(i); // (5) 5
  }, i*1000);
}
```
This snippet simply returns the number 5, 5 times. Why is that?\
Because function callbacks will run *after* the loop has run through. `var i` reaches 5, the loop stops, and then the callback function is run 5 times.

**The callback function and the for loop both belong to the same scope.**

We can create a scope for the callback function by using an IIFE, and pass in an instance of i every time for it to use, like so:

```javascript
for (var i = 0; i < 5; i++) {
  ( function() {
    var j = i;
    setTimeout ( function logger() {
    console.log(j); // 0 1 2 3 4
  }, i*1000)
  })();
}
```

### Block scoping and the `let` keyword
The `let` allows us to block scope the loop, meaning we could place `let j = i;` at the top, but it also has another extremely helpful attribute when it is used in the head of a for loop: **it is declared afresh for each iteration of the loop.**

That means that to solve the problem above we can just use the `let` keyword:
```javascript
for (let i = 0; i < 5; i++) {
  setTimeout(function logger() {
    console.log(i); // 0 1 2 3 4
  }, i*1000);
}
```
Simple, and elegant.


## Modules
The basic module pattern is as follows:
```javascript
function moduleCreator() {
  var something = 'yeah';
  var andAnother = 'nah';

  function logSomething() {
    console.log(something);
  }

  function splitAnother() {
    console.log(andAnother.split(''));
  }

  return {
    logSomething: logSomething,
    splitAnother: splitAnother,
  }
}

var myModule = moduleCreator();

myModule.logSomething(); // 'yeah'
myModule.splitAnother(); // ['n', 'a', 'h']
```
// What it does
// Why would we want this

To be continued
