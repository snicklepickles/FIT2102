- An approach to modelling complex, asynchronous behaviours that uses many [[Functional Programming in JS|FP]] principles
	- Applications of functions to elements of containers to transform to a new container
		- E.g. map, filter, reduce etc. over arrays
	- Use of function composition and higher-order functions to define complex transformations from simple, reusable function elements
## Observable Streams
- Data structure in the Reactive Extensions for JavaScript (RxJS) library
- Wraps a collection of things in a container
- The function `of` creates an Observable that will emit the specified elements in its parameter list in order
- Nothing happens until we *initialise* the stream 
	- By *subscribing* to the observable
	- Passing in an “effectful” function that is applied to each element in the stream
```js
of(1,2,3,4)
  .subscribe(console.log)
```
![[of1234.gif|704]]
- The requirement to invoke `subscribe` is conceptually *similar* to the [[Lazy Evaluation#Lazy Sequence|lazy sequence]]
- *Difference*:
	- Lazy-sequences are “pull-based” data structures
		- “Pull” values out one at a time by calling `next`
	- Observables handle “streams” of things, such as asynchronous UI
![[Functional Reactive Programming-20240728150828259.png]]
- Transformations have to be composed (not chained) inside a `pipe`
```js
const isEven = x=>x%2===0,
      square = x=>x*x
range(10)
  .pipe(
    filter(isEven),
    map(square))
  .subscribe(console.log)
```
![[even.gif|713]]
```js
// first Euler problem
range(1000)
  .pipe(
    filter(x=> x%3===0 || x%5===0),
    scan((a,v)=>a+v),
    last())
  .subscribe(console.log); 
```
![[euler 1.gif]]