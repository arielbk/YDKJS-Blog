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

## What actually is `this`, then?
`this` is an *runtime binding*, rather than an *authortime binding*. This means that context is created depending on how a function is executed, and it will remain until the function has finished executing.
