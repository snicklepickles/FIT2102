## Eager vs Lazy
- In lazy evaluation, expressions are evaluated when needed
```js
const eagerDate = Date();

const lazyDate = function() {
	return Date();
}

function printTime() {
	console.log("Eager: " + eagerDate)
	console.log("Lazy: " + lazyDate())
}

setTimeout(printTime, 1000)
// Eager: ...
// Lazy: ...
```
- [[JavaScript]] (and all imperative languages) evaluates expressions eagerly
```js
function sillyNaturalNumbers(initialValue) {
	return sillyNaturalNumbers(initialValue + 1);
}

function lazyNaturalNumbers(v) {
	return selector => selector(v, lazyNaturalNumbers(v+1))
}

const value = (v,_) => v
const next = (_, f) => f

lazyNaturalNumbers(1)(value) // 1
lazyNaturalNumbers(1)(next)(value) // 2
```
## Lazy Sequence
```js
interface LazySequence<T> {
   value: T;
   next():LazySequence<T>;
}

function naturalNumbers() {
   return function _next(v:number):LazySequence<number> {
       return {
           value: v,
           next: ()=>_next(v+1)
       }
   }(1) // immediately invoked function expression (IIFE)
}
```
![[Lazy Evaluation-20240728115743724.png|376]]

_Eager Evaluation_: A strategy where expressions are evaluated immediately as they are bound to variables.

_IIFE (Immediately Invoked Function Expression)_: A JavaScript function that runs as soon as it is defined, used to create local scopes and encapsulate code.

_Lazy Evaluation_: A strategy where expressions are not evaluated until their values are needed, allowing for the creation of infinite sequences and delayed computations.