# Notes for You Don't Know JavaScript - This and Object Prototypes

The original book can be found [here](https://github.com/arielbk/You-Dont-Know-JS/blob/master/this%20&%20object%20prototypes/README.md)

## `this` - the Why
`this` allows us to call a function in various *contexts*.

For example:
```javascript
function yellName() {
  return this.name.toUpperCase();
}

function ageDivider() {
  return this.age / 2;
}

var person1 = {
  name: 'Ariel',
  age: 26
}

yellName.call(person1); // returns 'ARIEL'
ageDivider.call(person1); //returns 13
```
Values, and thus context, are not passed in to the functions as arguments explicitly, and this can allow for cleaner design and usability.

### Clearing up confusion
**using `this` in a function does not mean that it simply refers to itself**, despite what the name may suggest.

```javascript
function foo() {
  this.count++; // refers to global object, not the function itself... :S
  console.log(this.count);
}
foo.count = 0;
foo(); // NaN - confusion ensues
```
**This is a misunderstanding of how `this` works**. If the `foo` function needed to point to itself, a self-referral on the named object would suffice: `foo.count++` (not `this.count++`). This avoids the use of `this` altogether.

**And if we wanted to use `this`, we would pass in the variable with a call method.** `foo.call(foo)`

**`this` is not a way to 'bridge' lexical scope**.

## What is `this`, then?
`this` is a *runtime binding*, as opposed to an *author time binding*. This means that context is created depending on how a function is executed, and it will remain until the function has finished executing.

*How*, or rather *where*, a function is called, is its **call-site**.

## Call-sites (vs call-stacks)
An example to illustrate the difference:
```javascript
function foo() { // call stack: foo()
  console.log('foo');
  bar(); // call site for `bar`
}
function bar() { // call stack: foo() -> bar()
  console.log('bar');
  baz(); // call site for `baz()`
}
function baz() { // call stack: foo() -> bar() -> baz()
  console.log('baz');
}
foo(); // call site for `foo`, logs: 'foo' // 'bar' // 'baz'
```
The *call stack* - functions are executed within each other, stacking up in their execution. The last on the stack is the (traditionally) the first to finish. The stack is built up as function calls on top of each other. The call stack is then reduced as functions complete, from the top to the bottom.

The *call site*, on the other hand, is where the function is actually called from.

Debuggers can be very helpful in finding contexts and call sites where functions actually originate from.

## Rules for `this`
The following are some rules that `this` follow, so watch out for them.

### 1. Default binding
```javascript
function foo() {
  console.log('this.number');
}

var number = 42;
foo(); // 42
```
Because the function is *called* in the global scope, its `this` is set to the variable declaration of `number`, 42. **This will not happen in strict mode**.

### 2. Implicit binding
```javascript
function foo() {
  console.log(this.number);
}
var obj = {
  number: 42,
  foo: foo
}
obj.foo(); // 42
```
In this case it is the `obj` object that provides context; in this case it is the call site of the function `foo`.

It is always just the last object which holds the function reference that provides the call site, in the case that object properties link together. For example: `obj1.obj2.foo()` – it is `obj2` that provides the call site.

One 'gotcha' when `this` is implicitly bound like this is when a function loses that binding because of some subtle mistake. If you assign a function call from an object to a variable, for instance, the function called by that variable will no longer have the context of the object, because the object property is passed by reference to the new variable, and not by value.

This idea becomes more applicable and dangerous when we consider callback functions.

To be continued...
