## Syntax vs Semantics
### Syntax
- Set of symbols & rules for combining them (*grammar*) into a correctly structured program
- Often arbitrary, and chosen for:
	- Historical reasons
	- Ease of implementation
	- Aesthetics
### Semantics
- The processes a computer follows when executing a program in a given language
- Relates to the meaning of a program
- How does it structure data?
- How does it execute or evaluate?
- Different languages lend themselves to *fundamentally* different approaches to programming
	- Different abstractions for modelling problems
	- Different ways of executing & evaluating
## Levels of Abstraction
### High-Level Abstraction
- Compiler or interpreter transforms human-readable instructions to machine operations
### Assembly Language
- Still requires a compiler
- Operations correspond 1-to-1 with machine operations
### Machine Language
- Operations and their arguments (operands) represented by binary numbers
- Executed either directly in hardware or by a *microprogram* embedded in the microprocessor
## The Machine Level
### Von Neumann Architecture
- A program is just data that can be loaded into memory
- A program is a list of instructions that:
	- Read data from memory
	- Manipulate it
	- Write it back to memory
![[Levels of Abstraction-20240715180154235.png]]
- **ALU**: Arithmetic Logic Unit
- **CU**: Control Unit
- **Clock**: Synchronises CPU operations with other system components
- **Data bus**: Transfers instructions & operations between CPU & memory
- **Address bus**: When the CPU needs to read/write to a memory location, it specifies that location on the address bus
![[Levels of Abstraction-20240715180356336.png]]
- Programs run on an *x86* machine according to the *Instruction Execution Cycle*:
	- CPU fetches instruction from instruction queue, increments instruction pointer
	- CPU decodes instruction and decides if it has operands
	- If necessary, CPU fetches operands from registers or memory
	- CPU executes the instruction
	- If necessary, CPU stores result, sets status flags, etc.
### Memory
- *Registers* are locations on the CPU with very low-latency access
	- Due to physical proximity to the execution engine
- Modern x86 processors also have 2-3 levels of *cache memory* physically on-board the CPU
	- Slower than registers, faster than main memory
- CPU handles movement of instructions & data between levels of cache memory & main memory automatically
> **TL;DR**: It can be very difficult to predict how long a particular memory access will take.
## Models of Computation
- The von Neumann model & **Turing machines** follow an *imperative* paradigm of *sequential* instruction execution
- **Lambda calculus** is roughly a contemporary of these other models, but based on *mathematical* functions, their applications & composition
- **Imperative** programming languages are roughly abstractions of *machine architecture*
- **Functional** programming languages are roughly abstractions of *lambda calculus*