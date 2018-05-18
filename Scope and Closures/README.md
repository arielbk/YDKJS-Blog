# Notes for You Don't Know JavaScript - Scope and Closures

The original book can be found [here](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20&%20closures/README.md)

**Scope can be thought of as the rules governing how and where variables are stored.**

JS is actually compiled like many languages. Unusually though, JS is usually compiled right before it is executed. It doesn't have time, like many other languages do, to really optimise.

**At a basic level, compilation goes like this:** the source code is 'tokenized' or ('lexed') into meaningful tidbits. These are then placed in a tree like structure, and from this, machine executable code is generated.

## Analogy to understand scope
The JS engine and its two friends, compiler and scope.
Engine oversees the operation; compiler does the dirty work (see above); scope is the stickler with a list of all variables, where they belong and who can access them.

In a simple variable declaration, engine and her friends actually run through, making a list of all variable declarations, only after this has been done do they run through again and actually start assigning values. This is done by the engine executing code that is created by compiler, before first checking that its okay with scope.

`var answer = 42`
LHS lookup would be checking 'answer' (the container name of the article)
RHS lookup would be checking '42' (the value stored in the variable)

The type of look up that is performed may be more nuanced than you think. Declarations are LHS lookups because we don't care what value already exists in the variable.

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

## Lexical scope
Lexical scope is in contrast to *dynamic scope*, which is used by a small number of other languages.

Lexical scope pertains to the process where the source code undergoes lexing/tokenizing, where it is broken up into meaningful (to a computer) pieces. Lexical scope is therefore scope that is defined at the time when this lexing occurs, at this point it is (or should be) set in stone.

## Lookups
Look ups happen from where they are called, and then further outer scopes are searched until they are found. **It is just the first matches that are returned.** This is a useful example:

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

Look up occurs first within the bar function, then the foo function. At this point the variables are found, thus the look up stops. This is a case in which the inner variables *shadow* those in the global scope.

## Cheating Lexical
There are some ways to 'cheat' lexical scope, but they are generally frowned upon and lead to poorer performance. Basically, you should avoid them because they are not worth the performance tradeoff.

`eval()` is one way - it can insert dynamically produced code as if it were there from the start, and thus modify lexical scope. In strict mode, however, it creates its own scope.

`new Function()` is another - the last argument is a dynamically created function

`with` is another - it creates an object scope, but you run the risk of accidentally declaring a global variable with it

Strict mode completely disallows `with` and it restricts `eval()` in some instances

The JS Engine has a number of optimisations for compiling code, and these are undermined by cheating lexical scope. It is best practice to just not do it.
