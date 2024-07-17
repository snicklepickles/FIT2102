```javascript
const z = 1 // immutable at global scope

let w = 1 // mutable at global scope
w = 2
```
- Unlike expressions, statements evaluate to `undefined`
- Statements require a side effect to be useful
	- Line 3: creating a variable in the current scope
```javascript
1 + 1 // expression
// statements (⋖ undefined)
const x = 1 + 1
{
	const x = 1+1
    const y = 2
    console.log(x+y)
}
```

```js
function sumTo(n) {
   return n ? n + sumTo(n-1) : 0;
}
```
We consider this recursive loop a more “declarative” coding style than the imperative loops.

It is closer to the _inductive definition_ of sum than a series of steps for how to compute it.
- No _mutable_ variables used
- Each expression in this code is _“pure”_: it has no _effects_ outside the expression. Thus, you could replace each element of code with something else that produces the same result for a given input (such as a simple look up of a precomputed cache) and it would work the same.
- Therefore: this code has the property of _referential transparency_.
- The code succinctly states the _loop invariant_.
## Stack Overflow & Tail Recursion
- Functional languages rely on recursion because they have no other way to create loops *without* mutable variables
- Need to *scale* to real-world computations
- When a recursive function is written such that the recursive call is in the *tail position*, many modern compilers are able to transform the recursion into a loop with **constant** memory use
	- Commonly a `while` loop
	- Known as **tail call optimisation**
- Tail recursive `sumTo`:
```js
function sumTo(n, sum = 0) {
   return n ? sumTo(n-1, sum + n)
            : sum;
}
	``` 
- 2nd parameter `sum` stores the computation as recursion proceeds (**accumulator**)

The important change is that the recursive call (on the branch of execution that requires it) is now the *very last* operation to be executed before the function returns
- `sum + n` occurs before the recursive call
- Therefore, no local state needs to be stored on the stack.

> In tail recursion, since the current function's state is no longer needed after the recursive call, the JavaScript engine can optimize the recursion by reusing the current stack frame for the next call. This optimization is known as **tail call optimization (TCO)**.
## Functions as Parameters
```js
function sumTo(n, f = x => x) {
   return n ? f(n) + sumTo(n-1, f) : 0;
}

function square(x) {
   return x * x;
}

sumTo(10, square)
```
## Objects
- When declared with `const`, only *weakly immutable*
- *Destructuring syntax:*
```js
// equivalent statements
const {aProperty} = myObj;
const aProperty = myObj.aProperty;

// pass object -> function
const point = {x:123, y:456};
function showX({x}) {
   console.log(x);
}

// init object properties with variables
const x = 123, tempY = 456;
const point = {x /* variable name used as property name */,
               y:tempY /* value from variable but new property name */};
point
```
## Arrays
```js
const a = [1,2,3];
const [x,y,z] = a;
```
## Dynamic Typing
- JS is not type-checked by a compiler
	- Run-time failures, rather than compile-time
## Anonymous Functions
```js
['tim', 'sally', 'anne'].forEach(function(person) {
    console.log('hello ' + person);
})

// arrow syntax in EC6
['tim', 'sally', 'anne'].forEach(person => console.log('hello ' + person))

// => implicit return val
['tim', 'sally', 'anne'].map(person => "hello " + person)

// multi-line requires explicit return
['tim', 'sally', 'anne'].map(person=> {
   const message = "hello " + person;
   console.log(message);
   return message;
})
```
## Arrow Functions
```js
const greeting = person => 'hello' + person;

// multi-param anon func
const greeting = (greeting, person) => greeting + ' ' + person
const greeting = (greeting, person)=> {
    const msg = greeting + ' ' + person
    console.log(msg)
    return msg
};
```
## Reduce
- Applies function to each element in array to compute an **aggregate** value for whole array
- `accumulator` is either:
	- The 2nd argument to `reduce` in first call (in our case, `0`)
	- For every other call, the result by previous call to function
```js
// sum numbers
[5,8,3,1,7,6,2].reduce((accumulator,x) => accumulator + x, 0) 
```
- `test` is a *predicate* function (returns true/false)
- `accumulator` is a Boolean (default is set to `true`)
- Using `&&` operator, if an element in the array fails the test, `accumulator=false`
```js
const all = (test, array) => array.reduce(
    (accumulator, x) => accumulator && test(x),
    true);
    
all(x => x < 5, [1, 2, 3]); // true
all(x => x < 5, [1, 3, 5]); // false

// in-built every()
[1, 2, 3].every(x => x < 5);
[1, 3, 5].every(x => x < 5);
```
## Closures
- A function & the set of variables it accesses from its enclosing space is called a *closure*
- Variables from the enclosing scope that are accessed by the closure are said to be *captured* by the closure
```js
function add(x) {
    return y => y+x;
}
const addNine = add(9)
addNine(10)
```
- The binding of x to a value *persists* beyond the scope of `add`
```js
add(1)(2)
// ^ is a curried version of plus
function plus(x,y) { return x + y }
plus(1,2)
```
- Functions which operate on multiple parameters, but split the parameters across multiple nested single parameter functions, are said to be [[Currying|curried]]
	- Can be written in either arrow syntax, `function` keyword, or both
```js
function add(x) {
  return function(y) {
    return x+y
  }
}

const add = x=>y=>x+y
```
- Curried wrapper for `sumTo` from before:
```js
function sumOf(f) {
    return n => sumTo(n, f)
}

const sumOfSquares = sumOf(square)
sumOfSquares(10)
```
## Classes
```js
class Person {
   constructor(name, occupation) {
       this.name = name
       this.occupation = occupation
   }
   get greeting() {
       return `Hi, my name’s ${this.name} and I ${this.occupation}!`
   }
   sayHello() {
       console.log(this.greeting)
   }
}
```
- JS classes support single-inheritance to achieve polymorphism
```js
class LoudPerson extends Person {
   sayHello() {
       console.log(this.greeting.toUpperCase())
   }
}

const tims = [
   new Person("Tim","lecture Programming Paradigms"),
   new LoudPerson("Tim","shout about Programming Paradigms")
]

tims.forEach(t => t.sayHello())
```
## Polymorphism
### Subtyping Polymorphism
- In OO languages, the compiler enforces that objects must be instances of a common base class or interface to be treated as such
### Duck Typing
- In JS, with no compile-time typecheck, a kind of polymorphism is possible such that:
	- If 2 objects both present a similarly named method that is callable in the same way, there is nothing preventing you from using that method on each object as if it is the same
### [[Parametric Polymorphism]]
- Key to strongly-typed functional programming languages
- Also a feature of many modern OO languages
- TypeScript generics
## Dependency Injection
- Functional approach
```js
class Person {
   constructor(name, occupation, voiceTransform = g => g) {
       this.name = name
       this.occupation = occupation
       this.voiceTransform = voiceTransform
   }
   get greeting() {
       return `Hi, my name’s ${this.name} and I ${this.occupation}!`
   }
   sayHello() {
       console.log(this.voiceTransform(this.greeting))
   }
}
const tims = [
   new Person("Tim", "lecture Programming Paradigms"),
   new Person("Tim", "shout about Programming Paradigms", g => g.toUpperCase())
]
tims.forEach(t => t.sayHello())

```
- Filter property defaults to the *identity function* (g => g)
- User of `Person` class can inject a dependency on another function from outside the class when they construct an instance
- Lightweight style of code reuse/specialisation

## Glossary
_Anonymous Function_: A function defined without a name, often used as an argument to other functions. Also known as lambda function.

_Closure_: A function and the set of variables it accesses from its enclosing scope.

_Currying_: The process of transforming a function that takes multiple arguments into a sequence of functions that each take a single argument.

_Higher-Order Function_: A function that takes other functions as arguments or returns a function as its result.

_Immutable Variable_: A variable declared with const whose value cannot be reassigned.

_Mutable Variable_: A variable declared with let that can be reassigned to different values.

_Parametric Polymorphism_: A type of polymorphism where functions or data types can be written generically so that they can handle values uniformly without depending on their type.

_Weakly Immutable_: A property of const-declared objects in JavaScript, where the variable reference is immutable, but the object’s properties can still be changed.

_Pure Function_: A function that always produces the same output for the same input and has no side effects.

_Referential Transparency_: An expression that can be replaced with its value without changing the program’s behavior, indicating no side effects and consistent results.

_Side Effects_: Any state change that occurs outside of a function’s local environment or any observable interaction with the outside world, such as modifying a global variable, writing to a file, or printing to a console.