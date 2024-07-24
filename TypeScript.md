- Superset of [[JavaScript]] that introduces **type annotations**
- When declaring variables, functions, parameters, etc.
- Default approach: *gradually* typed
	- In tutorials: *strict* typing (cannot use `any`)
## Type Annotations
- For primitives (`number`, `string`, `boolean`), TS compiler infers type
```ts
let x: number, s: string, b: boolean = false;
x = "hello" // type error

// union types
let x: number | string;

function theFunction(x: number, y: number): number {
  return x + y; // returns a number
}
```
## Type Aliases
- Using `type` keyword
- Without aliases
![[TypeScript-20240724142051250.png]]
- With aliases
![[TypeScript-20240724142107897.png]]
## Higher Order Functions
### Currying
- Translating a function that takes multiple arguments into a sequence of *unary* functions
- `multiply` is a *curried* function
![[TypeScript-20240724141007245.png]]
![[TypeScript-20240724141321691.png]]
```ts
function curry<U,V,W>(f:(x:U,y:V)=>W): (x:U)=>(y:V)=>W {
  return x=>y=>f(x,y)
}
```
## Interfaces
- For typing objects with multiple properties
```ts
interface Student {
  name: string
  mark: number
}

// immutable
type ImmutableStudent = Readonly<Student>;

// all in one
type ImmutableStudent = Readonly<{
  name: string
  mark: number
}>

// aliasing types
type CurriedFunc<U,V,W> = (x:U)=>(y:V)=>W

function curry<U,V,W>(f:(x:U,y:V)=>W): CurriedFunc<U,V,W> {
  return x=>y=>f(x,y)
}
```

```ts
interface Student {
  name: string
  assignmentMark: number
  examMark: number
  markAdjustment(): number
}

function averageScore(students: Student[]): number {
  return students.reduce((total, s) =>
    total + s.assignmentMark + s.examMark + s.markAdjustment(), 0)
    / students.length
}
```
## Union Types
```ts
function setLeftPadding(elem: Element, value: number | string | (()=>string)) {
    if (typeof value === "number")
        elem.setAttribute("style", `padding-left:${value}px`)
    else if (typeof value === "function")
        elem.setAttribute("style", `padding-left:${value()}`)
    else
        elem.setAttribute("style", `padding-left:${value}`)
}

setLeftPadding(headings,100);
setLeftPadding(headings,"100px");
setLeftPadding(headings, () => "100px");
```
## Generic Types
- Type parameters **must** start with a capital
```ts
interface Student<T> {
  id: T;
  name: string;
}

const exampleStudent: Student<number> = {
  id: 12345,
  name: "John Doe",
}

const exampleStudent: Student<string> = {
  id: "jdoe0001@student.university.edu",
  name: "John Doe",
}
```
- A form of [[Parametric Polymorphism]]
	- `T` and `U` may be referred to as type parameters or type variables
```ts
interface Student<T,U> {
  id: T;
  name: string;
  someOtherThingThatWeDontCareMuchAbout: U
}
```
- Type parameters can be added to:
	- Interfaces
	- ES6 classes
	- Type aliases
	- Functions
```ts
function binarySearch2<T>(arr:T[], key:T): number {
    function bs(start:number, end:number): number {
        if(start >= end) return -1;
        const mid = Math.floor((start + end) / 2);
        if(key > arr[mid]) return bs(mid + 1, end);
        if(key < arr[mid]) return bs(start, mid);
        return mid;
    }
    return bs(0,arr.length);
}

const studentsByEmail = [
  {id: "cindy@monash.edu", name: "Cindy Wu"},
  {id: "harry@monash.edu", name: "Harry Smith"},
]
```
- Advantage over JS `any`: consistency
```ts
// type error
binarySearch(numberIds,"harry@monash.edu")
```
- Pass comparison function as parameter:
```ts
function binarySearch3<T>(arr:T[], key:T, compare: (a:T,b:T)=>number): number {
    function bs(start:number, end:number): number {
        if(start >= end) return -1;
        const mid = Math.floor((start + end) / 2),
              comp = compare(key,arr[mid]);
        if(comp>0) return bs(mid + 1, end);
        if(comp<0) return bs(start, mid);
        return mid;
    }
    return bs(0,arr.length);
}

binarySearch3(students, (a,b)=>/* return 1 if a is greater than b, 0 if they are the same, -1 otherwise */)
```
### Generic Curry
![[TypeScript-20240724142545924.png]]
![[TypeScript-20240724142641932.png]]
![[TypeScript-20240724143159187.png]]
## Linked Lists
![[TypeScript-20240724143904514.png]]
### Concat
![[TypeScript-20240724144129156.png|558]]
- `[1, 2 ,3] + [4, 5, 6]`