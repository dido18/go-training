# Semantics 


- the data in the array is contiguous 

```
car fruits [5]string

fruit[0] = "apple"
fruit[1] = "Betty"
```

### Range mechanics


pointer semantics
```

for i = range fruits {
   fruits[1] = "jack"
   if i == 1{
	   fmt.Println(fruits[1]) // jack
   }
}

```

value semantics 
```

for i, v := range fruits{ // the array fruits is copied
   fruits[1] = "jack"
   if i == 1{
	   fmt.Println(v) // "betty", because fruits is copy, the v is the old one
   }
}
```


NOT DO THIS: mix the semantics  is bad

```
for i, v := range &fruits{ // copy the adrress of the array
   fruits[1] = "jack"
   if i == 1{
	   fmt.Println(v)    // "jack" bute this is a mixing semantic
   }
}

```


# Slice

The slice is the most important data structure in go.
Use the slice, unless you have reasonable motivation to don't use it.


**Reference types**
(maybe it is a outdated tem https://github.com/go101/go101/wiki/About-the-terminology-%22reference-type%22-in-Go ???) 
slice, map, pointer, channel, function, are reference type becuase
- they have a pointer
- when the types is assigned the zero value it is nil

``` go
fruits := make([]string, 5)
fruits[0] = "Apple"
fruits[1] = "Orange"
fruits[2] = "Banana"
fruits[3] = "Grape"
fruits[4] = "Plum"
```

A slice is a 3 word (24 bytes) structure with
 - pointer: that points to the backing array  (stored in the heap)
 - length: is the number of elements you can access from the pointer (the number of elements it contains)
 - capacity: the number of elements the slice can growth (the number of elements in the underlay array counting from the first element in the slice)

> [!note]
> The only part stored in the heap is the backing array.  The escape analysis works only on that. The slice sis stores into the stack

>[!note]
> Capacity can be larger that Lenght (Capacity >= Length), but not the opposite.
> Capacity is for growth.

The slice ia passed using **value semantic** . Only the 3 word structure is copied into down the stack and not the backing array.

![[slices.excalidraw]]



If the capacity **Capacity larger than length**  it means that the array can growth up to the capacity 

```
fruits = make([]string, 5, 8) // slice of length 5, and capacity 8
```


> [!warning]
>  Using the pointer to modify the element of the slice  can cause side effetct because multiple part of the program can modify the same backing array.


#### Nil slice
it is a slice initialized with zero-value (poiter = nil, len=0, cap =0) 

``` go
var data []string  // create the slice structure with [nil, 0, 0]
```


#### Empty slice
it is a empty literal construction with ampty. The lenght and cap are zero BUT the pointer is not nil and points to the empty struct (see)

``` go
data := []string  // create the slice structure with [pointer->struct{}, 0, 0]
```


#### Empty struct
It is a zero allocation type struct. It means that it doesn't need allocation, becuase it is a 8byte value tide in the runtime (like a global variable) that this empty struct is referencing.

NOTE: if we create millions of empty stract they have the same sddress.

``` go
var es struct{}
```

#### Append 

The api for `append()` is a **value semantic mutation api.** 
It receive the copy of the slice, it mutate it, and return back. It modify the 3word of slice passsed as value [[Pointers]] and mutate the backing array in the heap.


``` go 
var data []string

lastCap = cap(data)

for record := 1; records <=1e5; record++{
	value := fmt.Sprinf("Rec: %d", record)
	data = append(data, value)
}
```


![[slice-append.excalidraw|700]]



Growth of the backing array
- up to Cap =1024  the array is double (2,4,8,16,32,64,...)
- after the array is enlarge by 25%

> [!Important]
> The append api can cuase memory leak  [[Memory Leak]] if `data = append(bil, "hello"`


Other oprimizations
 - build the slice with a fixed capacity (if know before) don't require to copy the backing array and enlarge it 
 ```
 data = make([]string, 0, 1024)  // initialize an array 
```
- create with ``make([]string, 1024)`` that allocates a zeroed array and returns a slice. Access the slice via the index and not use the append function.

```
data = make([]string, 1024) // allocates a zeroed array and returns a slice that refers to that array

for i:=0:; i < 1024; i++{
	data[i] = value
}
```


Example
```
package main
import "fmt"

func main() {
	a := make([]int, 5)
	printSlice("a", a)

	b := make([]int, 0, 5)
	printSlice("b", b)

	c := b[:2]
	printSlice("c", c)

	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}

// OYTPUT

a len=5 cap=5 [0 0 0 0 0]
b len=0 cap=5 []
c len=2 cap=5 [0 0]
d len=3 cap=3 [0 0 0]
```

>[!important]
> Append works off of length.  if `make(type, 100)` and use the `append()`  the resul array id a 100 + number of peends.


> [!note]
>   If you know upfront the lenght of the slice make it with lenght and capacity `make(type, len, cap)` and use index to store values


>[!example]
>

```
package main

import "fmt"

type Person struct {
	Name string
}

func main() {
	names := []string{"Pippo", "Pluto", "Paperino", "Paperina"}

	// build a zero-value slice
	var peopleZeroValue []Person
	fmt.Printf("peopleZeroValue (before): %+v \n", peopleZeroValue)
	for _, n := range names {
		fmt.Printf("peopleZeroValue: len %d Cap %d \n", len(peopleZeroValue), cap(peopleZeroValue))
		peopleZeroValue = append(peopleZeroValue, Person{Name: n})
	}

	fmt.Printf("peopleZeroValue (after): %+v \n\n", peopleZeroValue)

	// allocates a zeroed array and returns a slice. Access the slice via the index and not use the append function.
	peopleMakeLen := make([]Person, len(names))
	fmt.Printf("peopleMakeLen (before): %+v \n", peopleMakeLen)
	for i := 0; i < len(names); i++ {
		fmt.Printf("peopleMakeLen: len %d Cap %d \n", len(peopleMakeLen), cap(peopleMakeLen))
		peopleMakeLen[i] = Person{Name: names[i]}
	}

	fmt.Printf("peopleMakeLen (after): %+v \n\n", peopleMakeLen)

	// allocates a zero-value slice with a backing array of Capacity size. the append function can be used because len=0
	peopleMakeLenCap := make([]Person, 0, len(names))
	fmt.Printf("peopleMakeLenCap (before): %+v \n", peopleMakeLenCap)
	for i := 0; i < len(names); i++ {
		fmt.Printf("peopleMakeLenCap: len %d Cap %d \n", len(peopleMakeLenCap), cap(peopleMakeLenCap))
		peopleMakeLenCap = append(peopleMakeLenCap, Person{Name: names[i]})
	}

	fmt.Printf("peopleMakeLenCap (after): %+v \n\n", peopleMakeLenCap)

}

```


Output 
```
peopleZeroValue (before): [] 
peopleZeroValue: len 0 Cap 0 
peopleZeroValue: len 1 Cap 1 
peopleZeroValue: len 2 Cap 2 
peopleZeroValue: len 3 Cap 4 
peopleZeroValue (after): [{Name:Pippo} {Name:Pluto} {Name:Paperino} {Name:Paperina}] 

peopleMakeLen (before): [{Name:} {Name:} {Name:} {Name:}] 
peopleMakeLen: len 4 Cap 4 
peopleMakeLen: len 4 Cap 4 
peopleMakeLen: len 4 Cap 4 
peopleMakeLen: len 4 Cap 4 
peopleMakeLen (after): [{Name:Pippo} {Name:Pluto} {Name:Paperino} {Name:Paperina}] 

peopleMakeLenCap (before): [] 
peopleMakeLenCap: len 0 Cap 4 
peopleMakeLenCap: len 1 Cap 4 
peopleMakeLenCap: len 2 Cap 4 
peopleMakeLenCap: len 3 Cap 4 
peopleMakeLenCap (after): [{Name:Pippo} {Name:Pluto} {Name:Paperino} {Name:Paperina}]
```

## Slices of slice

The slice is a view of the backing array.
The backing array is shared.

> [!example]
> 

``` c
package main
Letters [A B C], Len 3, Cap 5 

Slice [C ], Len 2, Cap 3 
Slice [C D], Len 2, Cap 3
import "fmt"

func main() {
	letters := make([]string, 3, 5)
	letters[0] = "A"
	letters[1] = "B"
	letters[2] = "C"
	fmt.Printf("Letters %+v, Len %d, Cap %d \n\n", letters, len(letters), cap(letters))

	slice := letters[2:4]
	fmt.Printf("Slice %+v, Len %d, Cap %d \n", slice, len(slice), cap(slice))
	slice[1] = "D"
	fmt.Printf("Slice %+v, Len %d, Cap %d", slice, len(slice), cap(slice))
	fmt.Printf("Letters %+v, Len %d, Cap %d \n\n", letters, len(letters), cap(letters))
}

// Letters [A B C], Len 3, Cap 5 

// Slice [C ], Len 2, Cap 3 
// Slice [C D], Len 2, Cap 3
```



![[slice-of-slice.excalidraw]]


>[!warning] 
> Side effect: Since  the slice share the backing array, modyfying the values from a slice it modify the data from all the other slice pointing to the same element. 
> For example,  `letters[1] = "D" ` change the value also for other the letters slice

Solution to avoid problem side effect problem of slices

- **Third argument of the splice** ` [a: a+len: cap]`: Use a third argument that set the capacity equal to the length, In this way, when the append is called in the slice, since the length is equal to the capacity, the backing array is copied into another array and the values are added. We are using the `copy-on-write` because the array is copied.
- `copy()` manually copy the value of a slice to another slice. 

## Slices and references



``` c
package main

import "fmt"

type user struct {
	likes int
}

func main() {
	users := make([]user, 3)

	u1 := &users[1]
	u1.likes++

	// user 1 has 1 like the other have zero.
	for i := range users {
		fmt.Printf("User %+v, Likes %d \n", i, users[i].likes)
	}
	
	// append a new users 
	users = appens(users, 

}
```



