
## Garbage collection 

One of the point taht cause lack of performacne [[Correctness vs performance]]  is the memory allocation and garbage collection.

The go garbage collector is a **tri-color** mark and seep **concurrent** algorithm.

### Pacing algorithm GC
the pacing algorithm tries to balance 3 things:
  - smallest heap size (to reduce usage of memory)
  - latency of the stop world  never more that 100microseconds per run
  - usage at least 25% of the CPU capacity


>[!hint]
>  Try to reduce the amount of allocation of our program. Reduce use of pointer semantics and there is less work of GC todo, and program can run faster.


>[!note]
> Larger heap doens't necessary mean  better performance because when the live heap gets to the tope ther is much work of the GC to reduce to the bottom.
