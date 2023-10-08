
## Escape analysis
Compiler performs static code analysis, one of such analysis is the escape analysis.

The escape analysis determine if a value should be placed on the stack or escaped to the heap.

> [!cite]
> Construction tell us nothing, sharing tell us everything 

First priotity of the compiler: the values stay on the stack because
- the value is already there and it efficient to access them.  The stack can give us tramendous performance because the memory if already allocated.
- the value is self-cleaning (and the garbage collector is not needed)

>[!hint]
>  We want to leverage our value semantics and keep value on the stack  becuase it has better performances (because garbage collector is not involved)


From a syntex prospestive `u` is a vlaue on the heap, and because the go routine can access only the data on the Active frame, the compiler knows that that value must be escaped into the heap.
The compiler The syntax of the language is abstracting the details, this reduce the [[Cognitive Load]].


BUT ther is a cost: the allocation of the variable on the heap and the garbage collection flow.

The `&` is a good [[Readibility]] operator, because by only looking at the code `return &u` you know that there is a cost because 
- it's on the heap
- it's shared to all the up stack
- escape analysis is going to perform allocation with garbage collection

Instead, having only `return u` tells you nothing becaue you need to read the rest of the code to understand the cost. 

```
 u := &user{}        // BAD: using pointer semantics during construction
 return u            // u can
```


> [!hint]
> Never use pointer semenatics during construction. Use the value semantics if you are going to assign that value that you are constructing to a variable. You increase [[Readibility]]


You the pointer semantics during construction in these cases:
 - if you return immediatedly the vaue ```
```
return &user{Name:"Bill"}

```

- if you pass the variable to a function call
```
foo(&user{Name:"Bill})
```


Why ? 
Construction doens't tell you where something is in memory ( construction teell you nothing)


###  Use semantics consistency 

```
func createuserV2() user{   // value semantics on the return type
  u := &user{Name:"bill"}   // pointer semantics on the construction
  ...  

  return *u                   // Value semantic on the return 
}
```

NOTE: the value never escape to the heap, the code is using pointer but it is using value semantics



### Stack growth

**Frame are not dynamic**
The size of the frame (stack frame) of a function are sized at compile time. If the compiler doesn't know the size of value it allocates it into the heap. Compiler knows the size of builtin and other types (`int`, `float`), but other not (like `collection` tha has a lenght on the type of the value).

**Contiguos stack**
What happend if a function goes out of stack space (2KB)? Go allocate a another stack 25% greater and copy the frames to the new stack. 
With this operation, go guarantees [[Integrity]] and minimize resource but with a cost, becuase the copy of the frame increase latency of the function call.

Since the stack can move, stack memory cannot be shared across go routines (you cannot have a pointer in the stack that point to a variable of another stack)

the heap is used when:
 - values are shared across go routines boundaries
 - values that cant stay in the frame because ther is in integrity issue
 - values shows size is not know at compile time