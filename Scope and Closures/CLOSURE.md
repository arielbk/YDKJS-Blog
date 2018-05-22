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
