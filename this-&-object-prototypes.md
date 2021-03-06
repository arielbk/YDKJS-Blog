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
In this case it is the `obj` object that provides context because is the call site of the function `foo`.

It is always just the last object which holds the function reference that provides the call site, in the case that object properties link together. For example: `obj1.obj2.foo()` – it is `obj2` that provides the call site.

One 'gotcha' when `this` is implicitly bound like this is when a function loses that binding. `var bar = obj.foo; bar();` (where foo is a function reference inside of the `obj` object) is actually synonymous with `var bar = foo`. The assignment does not retain the context of foo. **This can be tricky to spot when working with callback functions.** Even built-in ones, like `setTimeout`.

### Explicit Binding
Explicit binding allows us to explicitly define the context in which a function is called.
```javascript
foo.call(obj) // invokes foo in the context of the `obj` object
foo.apply(obj) // for this use case these two behave exactly the same
```

#### Hard Binding
We can actually assign a call or apply method to a variable and call on that as we wish, but as of ES5 we also have `Function.prototype.bind` as a built in method for hard binding.

`var bar = foo.bind(obj)` would return a new function with an explicitly bound context of `obj` and assign it to `bar`.
As of ES6, the returned function also has a `name` property on it. If we were to call `bar.name`, `"bound foo"` would be returned.

Many functions will allow you to enter an argument to run functions in a specific context, `forEach` is one of these.

## `new` Binding
This is an interesting one. The `new` keyword can be applied to any function call, in order to make it a *constructor call* to that function. An object is constructed and if the function does not itself return an object, that constructed object is set as an explicit binding.

Consider this code (from the book):
```javascript
function foo(a) {
	this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```

## Everything in order.
*Default binding* is, naturally, overwritten by any other `this` binding and *explicit binding* takes precedence over *implicit binding*.

`new` binding takes precedence over implicit binding.

The ES5+ implementation of .bind explicit binding, is actually able to be overridden by the `new` constructor call. With this we can pass in arguments to a function and hold them there as defaults...

This is the example from the book: 
```javascript
function foo(p1,p2) {
	this.val = p1 + p2;
}

var bar = foo.bind( null, "p1" ); // using `null` because it will be overridden by `new` anyway
var baz = new bar( "p2" );
baz.val; // p1p2
```

## Basic rules for `this`
In order of precedence:
1. If the function is called with `new`, `this` is the newly created object
2. `call` or `apply` (explicit binding)
3. If the function is called with a context (`obj.foo()`)
4. Otherwise, default binding. `this` becomes global variable, or `undefined` in strict mode.


### Exceptions

#### Ignored `this`
Passing `null` or `undefined` to `call`, `apply` or `bind` will cause them to ignore these values and instead revert to default binding.

You may do this in order to use `apply` to spread out the values of arrays as arguments to pass into a function (but note that ES6 now has the `...` spread operator), you may also use it to [*curry*](https://en.wikipedia.org/wiki/Currying) value into a function with `bind`.

This can be dangerous, because you may make a null argument call to a function that you did not author and `this` would refer to the global variable, creating a hard to track down bug.

A safer way to do this, then, is to pass in a completely empty object (`var ø = Object.create(null)`, as a safe fallback.

There are also options for *soft binding* functions by creating our own utility.

## Lexical `this`
ES6's arrow functions...
Arrow function ignores the traditional `this` rules, instead adhering to *lexical scope*, *binding* whatever `this` is at call time. Basically `this` inside of the arrow function is the `this` of whichever context it is called in, and it remains that. It cannot be changed.

**This is confusing and will take some time to get used to...**
Lexical binding is illustrated in the book with the following example:
```javascript
function foo() {
	// return an arrow function
	return (a) => {
		// `this` here is lexically adopted from `foo()`
		console.log( this.a );
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, not 3!
```

A great example from [Stackoverflow](https://stackoverflow.com/questions/44080949/value-of-this-inside-arrow-function-inside-an-object):
``` javascript
let arrow = {
  test: () => {
    console.log('arrow: this === window...', this === window);
  }
};

let nonarrow = {
  test: function() {
    console.log('non-arrow: this === window...', this === window);
  }
};

arrow.test(); // arrow: this === window... true
nonarrow.test(); // non-arrow: this === window... false
```

But why use 'tricks' to escape from the way that `this` works? It will lead to more difficult code. Stick to lexical scope or embrace the way that `this` works.


## Objects
As one of the six primary types, objects are the building-blocks upon which much of JS is built.
*Functions and arrays, for example, are known as *complex primitives*, they are objects with extra features added on.*

The relationship between these 'kind of' object subtypes is more complicated than you might think. A string, for example, could be either a string primitive or a String object. When it is defined with a literal (as it normally is and should be) it is a string literal. Object methods (such as `.length`) are available on it because it is automatically coerced.

**Contents** of an object are stored in *locations*, property names act as pointers to the data inside of them.

Object property names are *always* strings, even if they look like a number, and will be first be coerced when performing a lookup.

### Computed Property Names
These are a cool feature of ES6 that I didn't know about. Check it out:
```javascript
var prefix = "foo";

var myObject = {
	[prefix + "bar"]: "hello",
	[prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

### Property vs Method
JS doesn't technically have 'methods', we just access a property inside of a object that happens to be a function. The function doesn't *belong* to the object, but its `this` may be bound to it, only because it is defined at call time.

### Arrays
Arrays are more structured objects, they have *actual* numbered indices rather than string property names.
String property names can even be added to an array, but why? Just make it an object.

### Duplicating Objects
This is tricky because objects are reference based.

There are different ways to duplicate an object depending on whether we want a *shallow copy* or a *deep copy*, when an object is self referential, a *deep copy* could even send us on an infinite loop.

There is this trick for deep cloning:
```javascript
var newObj = JSON.parse( JSON.stringify( someObj ) );
```

ES6 has a method for shallow cloning:
```javascript
var newObj = Object.assign( {}, myObject );
```
I'm not sure why he doesn't include the ES6 spread operator here for shallow cloning:
```javascript
var newObjc = {...myObject};
```

### Property Descriptors
Examples from the book:
```javascript
var myObject = {
	a: 2
};

Object.getOwnPropertyDescriptor( myObject, "a" );
// {
//    value: 2,
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```

These are the defaults. We can also define this explicitly like so:
```javascript
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true,
	configurable: true,
	enumerable: true
} );

myObject.a; // 2
```

- **Writable** defines whether the value can be changed
- **Configurable** defines whether the property descriptors themselves can be changed. *A type error will be thrown if this is violated*. It also stops the `delete` operator from being used on the value. 
- **Enumerable** defines whether the value is included in enumeration such as a `for...in` loop.

Its important to remember that the immutability here is a shallow one. The reference cannot be changed, but the value at the reference itself can. (Like a `const` object in ES6).

**Prevent extensions** to an object with `Object.preventExtensions(myObject)`

`Object.seal(..)` prevents extensions and sets configurable to false for all properties. Values still can be changed.

`Object.freeze(..)` prevents extensions, sets configurable to false for all properties and disallows any value changes.

