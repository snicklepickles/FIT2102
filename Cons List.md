```js
const cons = (_head, _rest)=> selector=> selector(_head, _rest);
const list123 = cons(1, cons(2, cons(3, null)));
```
- `cons` is a function that takes 2 parameters, `_head` & `_rest`
	- Returns a function, that itself takes a function (`selector`) as an argument
	- `selector` is applied to `_head` & `_rest`
- We pass a `selector` function to the list to access its elements
```js
list123((_head, _rest)=> _head) // 1
list123((_,r)=>r)((h,_)=>h) // 2
```
- Create accessor functions by passing appropriate selector function:
```js
const
    head = list=> list((h,_)=>h),
    rest = list=> list((_,r)=>r)

const one = head(list123), // ===1
    list23 = rest(list123),
    two = head(list23), // ===2
    ... // and so on
```
- Map function:
```js
const map = (f, list)=> !list ? null
                              : cons(f(head(list)), map(f, rest(list)));

const list234 = map(x => x + 1, list123);
```
- In the above, we are using [[JavaScript#Closures|closures]] to store data
## Church Encoding
- Consists of only lambda ([[JavaScript#Anonymous Functions|anonymous]]) functions
![[Cons List-20240724145509569.png]]
- To flip the notation:
![[Cons List-20240724150003065.png]]
## Types
- With [[TypeScript#Type Annotations|type annotations]]
![[Cons List-20240724150211192.png]]
![[Cons List-20240724150244869.png]]
- In use:
![[Cons List-20240724150400086.png]]