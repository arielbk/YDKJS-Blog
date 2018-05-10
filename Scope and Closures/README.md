# Notes for You Don't Know JavaScript - Scope and Closures

The original book can be found [here](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md)

**Scope can be thought of as the rules governing how and where variables are stored.**

JS is actually compiled like many languages. Unusually though, JS is usually compiled right before it is executed. It doesn't have time, like many other languages do, to really optimise.

At a basic level, compilation goes like this: the source code is tokenized into meaningful tidbits. These are then placed in a tree like structure, and from this, machine executable code is generated.

## Analogy to understand scope
The JS engine and its two friends, compiler and scope.
Engine oversees the operation; compiler does the dirty work (see above); scope is the stickler with a list of all variables, where they belong and who can access them.

In a simple variable declaration, engine and her friends actually run through, making a list of all variable declarations, only after this has been done do they run through again and actually start assigning values. This is done by the engine executing code that is created by compiler, before first checking that its okay with scope.

`var answer = 42`
LHS lookup would be checking 'answer' (the container name of the article)
RHS lookup would be checking '42' (the value stored in the variable)

The type of look up that is performed may be nuanced than you think. Declarations are LHS lookups because we don't care what value already exists in the variable.

```javascript
function logger(answer) {
  console.log(answer); // 42
}
logger(42);
```
(Function is declared - actually not a LHS lookup because it is handled by the compiler...) -> Answer variable within function is declared (LHS) -> Logger function is called (RHS lookup) -> 42 is assigned to answer within logger (LHS) -> log method is called within the console object (RHS) -> answer variable is passed into console log (RHS) ... phew!

## Nested scope
There are actually multiple scopes. If an RHS lookup not return anything the engine would attempt to move up one layer of scope until it reached the topmost layer (the global scope).

ReferenceErrors are scope related - the LHS lookup was unsuccessful
