- True functioning programming languages mandates the functions be **pure** (no **side effects**)
## Side Effects
- Changes to state outside the result explicitly returned by the function
- Example: changing the value of a variable declared outside the function scope
	- Mutating global state can cause difficult-to-diagnose bugs
	- Hard to prove correct/unit test deep inside functions (hidden coupling)
- Side effects can occur:
	- Intentionally through sloppy coding practices
	- Unintentionally (e.g., global vs local)
- Bad for *transparency* & *maintainability*
## Function Purity
- A pure function:
	- Has no side effects
	- Always produces same result for same input
### Referential Transparency
- The property of being able to substitute an expression that evaluates to some value, with that value, without affecting the behaviour of the program
```js
// can substitute square(2) with 4
const square = x=>x*x
const four = square(2)
```
- What if `square` depended on data stored in memory? → diff result if data mutates
- E.g., global variable, environment variable from IO, user input
- If `square` is **impure**, i.e. produces side effects (*hidden output*) or is affected by side effects (*hidden inputs*), then `square(2)` is not referentially transparent
```js
function pureSquares(a) {
    return a.map(x=> x*x)
}

function impureSquares(a) {
   let i = 0
   while (i < a.length) {
	   a[i] = a[i] * a[i++];
   }
}
   
const myArray=[1,2,3]
impureSquares(myArray) // [1,4,9]
impureSquares(myArray) // [1,16,81]
```
- Purely functional languages enforce referential transparency through *immutable* variables
	- JS, `const` is only shallow immutability
```js
let messagesSent = 0;
function send(message, recipient) {
   let success = recipient.notify(message);
   if (success) {
       ++messagesSent;
   } else {
       console.log("send failed! " + message);
   }
   console.log("messages sent " + messagesSent);
}
```
1. Mutates state of `messagesSent` from enclosing scope
2. Likely does something to recipient
3. Outputs to console (changes state of console’s output stream, side effect beyond return value)
## Functional Patterns
### Eliminating Loops
- Loops are the source of many bugs
	- Fence-post errors, range errors, typos, incrementing the wrong counter, etc.
	- 4 distinct places to make errors
```js
for ([initialization]; [condition]; [final-expression])
   statement
```
## Callbacks
- Callback functions passed as *event handlers* are a situation where semantic difference between *arrow* syntax **vs** *regular anonymous function* syntax matters
```js
element.addEventHandler('click',
e=>{
// do something when the event occurs,
// maybe using the result of the event e
});
```
- Arrow: `this` is bound to context of *caller*
- `function`: `this` is bound to context of *callee*
```js
function Counter() {
    this.count = 0;
    setInterval(function increment() {
        console.log(this.count++)
    }, 500);
}
const ctr = new Counter();
```
- Output, each line 500 ms apart:
> NaN
> NaN
> NaN
> …
- `this` is referring to `increment`, has no `count`
	- Apply `++` to `undefined` => `NaN`
```js
function Counter() {
    this.count = 0;
    setInterval(()=>console.log(this.count++), 500);
}
new Counter();
```
- Take `this` of *enclosing* scope
> 0
> 1
> 2
> …
## Continuations
- Pass result of computation to user-provided continuation function
```js
function continuationPlus(a, b, done) {
   done(a+b);
}

continuationPlus(3, 5, console.log)
```
### Tail-Recursive Continuations
- Can rewrite *tail-recursive* functions to end with continuations
	- Specify some custom action to perform when recursion is *complete*
![[Functional Programming in JS-20240725174029214.png]]
```js
function tailRecFactorial(a, n) {
   return n<=1 ? a : tailRecFactorial(n*a, n-1);
}
```
- Final operation is recursive call to self
- No additional computation after this call
```js
function continuationFactorial(
a, n, finalAction=(result)=>{})
{
   if (n<=1) finalAction(a);
   else continuationFactorial(n*a, n-1, finalAction);
}
```
- Allows further action to be specified & executed upon completion
### Uses
- Essential in *asynchronous programming*
- Abounds in web programming
	- E.g., HTTP request from client → server
	- Callback function invoked upon response
### Examples
- `setTimeout` schedules action to occur after delay
- `setTimeout` itself returns immediately after dispatching the job
	- E.g., to the JavaScript event loop
```js
setTimeout(()=>console.log('done.'), 0);
// the above tells the event loop to execute
// the continuation after 0 milliseconds delay.
// even with a zero-length delay, the synchronous code
// after the setTimeout will be run first…
console.log('job queued on the event loop…');
```
>	job queued on the event loop…
>	done.
## Function & Method Chaining
- Linked list operations:
```ts
const
  map = (f,l) => l ? ({data: f(l.data), next: map(f,l.next)}) : null
, filter = (f,l) => !l ? null :
                    (next =>
                        f(l.data) ? ({data: l.data, next})
                                  : next
                    ) (filter(f,l.next))
                    // the above is using an immediately invoked
                    // function expression (IIFE) such that the function
                    // parameter `next` is used like a local variable for
                    // filter(f,l.next)
, take = (n,l) => l && n ? ({data: l.data, next: take(n-1,l.next)})
                         : null;
```
- Chained functions:
```ts
take(2,
   filter(x=> x%2 === 0,
       map(x=> x+1, l)
   )
)

take(2, filter(x=> x%2 === 0, map(x=> x+1, l)))
```
## Fluent Interfaces
- In above example, must be read inside-out to understand flow
- Need to keep track of brackets
- In OO languages, class definitions allow for method chaining
	- Provide methods that return an instance of the class itself, or another chainable class
```js
class List {
   constructor(private head) {}
   map(f) {
       return new List(map(f, this.head));
   }
...

new List(l)
   .map(x=>x+1)
   .filter(x=>x%2===0)
   .take(2)
```
- This is called *fluent* programming style
- Interfaces in OO languages that chain a sequence of method calls (as above) are called *fluent* interfaces
- Does not **enforce purity**
- Type system does not warn you whether method:
	- *Mutates* object upon which it is invoked and simply returns `this`, or
	- *Creates* a new object, leaving the original untouched
- `List.map` (above) creates a new list and is pure
## Spread Operator
```js
const studentVersion1 = {
  name: "Tim",
  assignmentMark: 20,
  examMark: 15
};

const studentVersion2 = {
    ...studentVersion1,
    assignmentMark: 19
};

// pure function
function updateExamMark(student, newMark) {
    return {...student, examMark: newMark};
}

const studentVersion3 = updateExamMark(studentVersion2, 19);
```
## Rationale for Pure Functions
* How can pure functions be efficient if the only way to mutate data structures is by returning a modified copy of the original?
1. Purity helps us avoid errors in state management through wanton mutation effects
	* Modern programming: *correctness > efficiency*
2. Properly structured data permits *log(n)* time copy-updates
	* Sufficient for most purposes
## Glossary
_Callback_: A function passed as an argument to another function, to be executed after some event or action has occurred.

_Chained Functions_: A programming pattern where multiple function calls are made sequentially, with each function returning an object that allows the next function to be called.

_Continuation:_ A function that takes a result and performs some action with it, instead of returning the result directly. Used extensively in asynchronous programming.

_Fluent Interface_: A method chaining pattern where a sequence of method calls is made on the same object, with each method returning the object itself or another chainable object.

_Pure Function_: A function that always produces the same output for the same input and has no side effects.

_Referential Transparency_: An expression that can be replaced with its value without changing the program’s behavior, indicating no side effects and consistent results.

_Side Effects_: Any state change that occurs outside of a function’s local environment or any observable interaction with the outside world, such as modifying a global variable, writing to a file, or printing to a console.