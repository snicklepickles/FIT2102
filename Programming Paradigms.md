## Imperative
- Programs are a sequence of statements that change a program’s state
- Follow the **Turing machine** model
- E.g., Assembler, Python
## Procedural
- *Imperative* in nature, but with *procedures*
- Contain named *subroutines* that may be invoked from elsewhere in the program
- With *parameterised* variables that may be passed in
- Result:
	- Explicit return value, or
	- Mutate the value of variables referenced elsewhere in the program (**side effect**)
	- **Pascal** differentiates between functions & procedures
## Object-Oriented
- Objects capture the set of data (state) & behaviours (methods) associated with entities in the system
## Declarative
- Declares *what* a procedure/function should do, rather than *how* it should do it
- Examples:
	- **SQL** describes a relational database query (*what* should be returned) without having to tell the database *how* the computation should be performed
	- **HTML** describes what a webpage should look like without specifying how to render it
	- Both SQL & HTML rely on an engine which already knows how to do the task
	- **Prolog** is both declarative & *Turing Complete*
		- Performs arbitrarily complex computations through *recursive* definitions
## Functional 
- Built around the concept of *composable functions*
- Support higher order functions
	- Can take other functions as arguments
	- Can return new functions as their result
- Follow the rules of the [[Lambda Calculus]]
- **Haskell** adheres closely to the mathematical definition of functions that are *pure*
	- No side effects, only return an explicit value
- Pure functional programs tend to be closer to a **declarative** definition of the solution
	- Manipulation of state is made explicit from the function parameter & return types
	- Rather than hidden in side effects
## OO vs FP 
- Kingdom of Nouns vs Kingdom of Verbs
	- Nouns: Descriptions of data
	- Verbs: Functions
### OO
- Organising programs & data into class hierarchies
- Rigid structure around data
- Static view of the world
	- Messy when dealing with dynamic state
### FP
- Focuses on functions as *transformations* of data

|                      | Functional                                      | Object-Oriented                                                                     |
| -------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------------- |
| Unit of Composition  | Functions                                       | Objects (classes)                                                                   |
| Programming Style    | Declarative                                     | Imperative                                                                          |
| Control Flow         | Functions, recursion, and chaining              | Loops and conditionals                                                              |
| Polymorphism         | [[Parametric]]                                  | Sub-Typing                                                                          |
| Data and Behaviour   | Loosely coupled through pure, generic functions | Tightly coupled in classes with methods                                             |
| State Management     | Treats objects as immutable                     | Favours mutation of objects through instance methods                                |
| Thread Safety        | Pure functions easily used concurrently         | Can be difficult to manage                                                          |
| Encapsulation        | Less essential                                  | Needed to protect data integrity                                                    |
| Model of Computation | [[Lambda Calculus]]                             | Imperative ([[Levels of Abstraction#Von Neumann Architecture\|von Neumann]]/Turing) |
