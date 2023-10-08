### Memory Leak 

A memory leak is when something is allocated in the heap but it is not deleted (cleaned).

In c++ the programmer should free the allocated object into the heap
In go the Escapea nalysis [[Language Variable Struct Constant]] that knows when the heap allocation is free at runtime.

In go, a memory leak is whne you mantain a reference into the heap and that reference nevere goes away. 

Two most causes of memory leak in go
- **goroutine**: a never ending go routine that is started but never stoppe
- **map**: if you don't delete the key in the map the key from the map grows and grows
- **append()**: if append a slice with different value `data = appen(bill, "Hello")`
- **close not calling:**
