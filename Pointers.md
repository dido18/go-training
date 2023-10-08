## Pointers
One of the 4 reasons of lack of performance  [[Correctness vs performance]] is how the memory is allocated. Pointers are very related.

> [!note]
> By default, in golang everything is passed by value. What you see what you get


### Path of execution

Examples:
- threads: are the path of execution in the operation system levels. the OS schedules threads
- goroutines: are the path of execution ot the go's level.

![[path-of-execution.excalidraw|900]]

Area of data sections
- Data segment: global variable, constant 
- Stack: data structure that every thread is given 
	- thread: usually 1MB of stack mem 
	- go routine: 2KB of memory for go routine
		- frame of memory: a slice of the stack where the go routine can access data
- Heap


Go routine and memory
when a goroutin is started, a frame of memory is alloated. and it can ONLY access the data in that frame.

**Program boundary**

two types of data
- address: the addres of variable is a data !!!
- value 

### Value semantics (pass-by-value)

Every new functiont hat is called, a new data frame is created.
the parameters are by default passed by value, this means that they are copied and written in the frame of the function call.

![[value-semantic.excalidraw|600]]

In pass-by-value you WYSIWYG https://en.wikipedia.org/wiki/WYSIWYG 

Value and pointer semantics are everything if you want to write code focus on correctness [[Correctness vs performance]] .

But what is the cost of value semantics ? we need to know the costs to be  [[Good engeneering]] 

Benefits:
- mutate memory in isolation. it ensures that only the data in that sandabx is accessed: 
- the memory mutation is happening in isolation and immutable. Give us the  [[Integrity]]

Costs of value semantic (pass by value)
- inefficient: multiple copy of the data around when cross the program boundaries

### Pointer semantics

Share a piece of data across program boundaries.


> [!tip]
> If you don't need to share something across program boundaries, you don't need a pointers semantics.
> Pointer are  for sharing.


Pointers variable: are variable that store address. Its size is always 8 bytes of memory (depending on the architeture).  The addrress data is stored in this variable.

![[pointer-semantic.excalidraw 1]]

`inc *int ` it is a variable that contains a pointer to a type int. the type is important because the compiler need to knoe the size etcs of the type.

Passing a pointer variable means that 

**Indirect memory read/write**
read or write a memory through a pointer.

Example:
` *int++` : incrment the value pointer by the pointer

passing a pointer to a goroutine, allow the go routine to access the memory outside its memory space.

Benefits:
- data is not duplicated. More efficiency in data manipulation

Costs:
- **side effect**s: having a pointer a gorouting can mutate memory outside of the program boundaries. With multiple go routines using the same pointer can cuase data races.

Active frame
``` note 
Every memory below the Active frame is invalid because can be reused. Every  memory above of the active frame is a level of integrity
```

Zero value 


[[Zettelkasten/## Factory Function]]





