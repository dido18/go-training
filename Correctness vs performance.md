Focus on correctnes and then on performance (optimize for correctness).
You can use profiles, debugger to optimize for perfromcance BUt force to mantain  correct and readable code.


the lack of performance is caused by 4 places
- latency of I/O, networking, Disk I/O
- memory allocation (and garbage collector)
- how the data is accessed
- algorithm effeciency (the number of steps)



Source:

- Bill Kennedy - Ultimate go programming https://learning.oreilly.com/videos/ultimate-go-programming/9780135261651/9780135261651-UGP2_01_01_02/