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
- `scan` is similar to `reduce`
	- Applies accumulator function to elements coming through the Observable
	- Instead of outputting a single value, it emits a stream of the running accumulation
	- Use `last` to produce an Observable with the final value
- `zip` gives **dot** product of the 2 streams
```js
const
  columns = of('A','B','C'),
  rows = range(3);

zip(columns,rows)
  .subscribe(console.log)
```
> [“A”,0]  
> [“B”,1]  
> [“C”,2]
![[Functional Reactive Programming (FRP)-20240731144916933.png]]
- `mergeMap` gives **cartesian** product of 2 streams
	- Gives us a way to take, for every element of a stream, a whole other stream, but flattened (or projected) together with the parent stream
	- Produces a single stream with all of the values
```js
columns.pipe(
  mergeMap(column=>rows.pipe(
    map(row=>[column, row])
  ))
).subscribe(console.log)
```
> [“A”, 0]  
> [“A”, 1]  
> [“A”, 2]  
> [“B”, 0]  
> [“B”, 1]  
> [“B”, 2]  
> [“C”, 0]  
> [“C”, 1]  
> [“C”, 2]
![[Functional Reactive Programming (FRP)-20240731145256014.png]]
- `map` produces an Observable of Observables
	- Each branch represents an observable stream
![[Functional Reactive Programming (FRP)-20240731145731055.png]]
## Merge
- Streams that are generated with `of` and `range` have all their elements available immediately
	- Result: elements of one followed by the elements of the other
```js
merge(columns,rows)
  .subscribe(console.log)
```
> A  
> B  
> C  
> 0  
> 1  
> 2
![Mouse drag geometry](https://tgdwyer.github.io/assets/images/chapterImages/functionalreactiveprogramming/merge.gif)
- `merge` applied to **asynchronous** streams will merge the elements in the order that they arrive in the stream
```js
const
  key$ = fromEvent<KeyboardEvent>(document,"keydown"),
  mouse$ = fromEvent<MouseEvent>(document,"mousedown");
```
- Convention: End variable names referring to Observable streams with `$`
```js
key$.pipe(
  map(e=>e.key)
).subscribe(console.log)

mouse$.pipe(
  map(_=>"!!")
).subscribe(console.log)

merge(key$.pipe(map(e=>e.key)),
      mouse$.pipe(map(_=>"!!"))
).subscribe(console.log)
```
![[Functional Reactive Programming (FRP)-20240731150308062.png]]
## Cheatsheet
- https://tgdwyer.github.io/functionalreactiveprogramming
## Examples of Asynchronous Processing
- RESTful web services
	- Client sends non-blocking request
	- No guarantee of server response time
- In UIs, events are triggered by user interaction with different parts of the interface
	- May happen at any time
- Robotics and other systems with sensors
	- The system must respond to events in the world
- Most of these systems work on an **event model**
	- *Single*-threaded multitasking 
	- Program (after initialisation) polls a FIFO queue for incoming events
	- When an event is popped from the queue, any subscribed actions for the event will be applied
- In JS, the first event loop you are likely to encounter is the browser’s
	- Every object in the DOM has events that can be subscribed to, by passing in a callback function which implements the desired action
- Single event → straightforward
- Nested (potentially bifurcating) sequence of events → ?
- **Observer pattern**:
	- Adding Event Listeners to UI elements for which we want interactive behaviour

```js
const svg = document.getElementById("svgCanvas")!;
const rect = document.getElementById("draggableRect")!;
rect.addEventListener('mousedown',e => {
    const
        xOffset = Number(rect.getAttribute('x')) - e.clientX,
        yOffset = Number(rect.getAttribute('y')) - e.clientY,
        moveListener = (e:MouseEvent)=>{
            rect.setAttribute('x',String(e.clientX + xOffset));
            rect.setAttribute('y',String(e.clientY + yOffset));
        },
        done = ()=>{
            svg.removeEventListener('mousemove', moveListener);
        };
    svg.addEventListener('mousemove', moveListener);
    svg.addEventListener('mouseup', done);
})
``` 
- the flow of control is not very linear or clear;
- we’re declaring callback functions inside of callback functions and the side effect of the program (that is, the change of state to the underlying web page by moving the rectangle) is hidden in the deepest nested part of the program;
- we have to manually unsubscribe from events when we’re done with them by calling `removeEventListener` (or potentially deal with weird behaviour when unwanted zombie events fire).

|                                        | Primary Hypothyroidism/ Secondary Hyperthyroidism | Graves’ Disease | Hashimoto’s Disease | TRH-Secreting Tumour | TSH-Secreting Tumour | TRH-Suppressing Tumour | TSH-Suppressing Tumour |
| -------------------------------------- | ------------------------------------------------- | --------------- | ------------------- | -------------------- | -------------------- | ---------------------- | ---------------------- |
| TRH levels                             | HIGH                                              | LOW             | HIGH                | HIGH                 | LOW                  | LOW                    | HIGH                   |
| TSH levels                             | HIGH                                              | LOW             | HIGH                | HIGH                 | HIGH                 | LOW                    | LOW                    |
| T3 and T4 levels                       | LOW                                               | HIGH            | LOW                 | HIGH                 | HIGH                 | LOW                    | LOW                    |
| Antibodies or immunoglobulins present? | NO                                                | YES             | YES                 | NO                   | NO                   | NO                     | NO                     |
| Goiter present?                        | YES                                               | YES             | YES (POSSIBLE)      | YES (POSSIBLE)       | YES                  | NO                     | NO                     |
| Is patient hypo or hyper-thyroid?      | HYPO                                              | HYPER           | HYPO                | HYPER                | HYPER                | HYPO                   | HYPO                   |
