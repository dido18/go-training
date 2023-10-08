Access the main memory is incredible slow, this is why the caching systems are here.

A i7-9xx core the latency to access the data in the various memoty is:
- L1 cache: 4 cycles of latency (1.3 ns)
- L2 cache: 11 cycle is latency (3.6ns)
- L3 cache: 39 cycles of latency (13 ns)
- main memory: 107 cycle of latenc (35.66 ms) - 27 times slower !!!


The main take ways:
- The **main memory is slow to access** (If performance matter)
- The **total amount of memory** is the toral amount of cache (e.g, 8 MB), NOT the size of the main memory
- **Small is fast**. If the data you are working is small enough that  it can fit into cache you see better perfomance, 
 
## **Chaching system**
The caching system load data from the main memory to caches (and vicevera) in **cache line**  (64 bytes) chunk only. 

Objective: mantain the chache line near to the processor (inside L1 or L2 caches) so it can be read without access the main memory.

> [!important]
> If performance matters, we have to write code with predictable access pattern to memory [predictable access patttern is everything] 


**Prefetch**
Prefetch is everything, it loads cache lines into memory. If the access pattern is predictable it can be more effecient becuase it can load memory in advance.

The slice [[Language Variable Struct Constant]] is the most important data structure in go

### TLB cache
It is another cache that the OS is managing.
The unit of processing is the **page** (that can be 4k, 8K ,16K)
The OS create a chace of virtual addresses to OS page to physical location.

The TLB is used by the hardware to convert a virtual address to physical.
With TLB miss, the cost to load the memory from physical is huge.
