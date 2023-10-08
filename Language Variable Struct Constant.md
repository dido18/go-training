#golang 

## Variables

 **type** is everything (is life). without type we can't have integrity and it is difficult to understan the costs 
 
 A sequnce of bit patten (a byte) is nothing without the type infromation.
 
```
00010010 // if int represents 10, it the type is color represents RED, etcs..
```

the type provides two information:
- size of the memory that is allocated
- representation: what it represents that bytes

```quote 
If we don't undertand size we don't understand cost
```

Example:
```
var a int
var b int32
```

How much size of bytes is allocated for an int ? and int32? 
in order to anser this question we need to knoe what is the architecture where the program is running.

``` bash 
$ go version 
$ go version go1.19.1 linux/amd64

```

the architecture is an amd with a *address size* of 64 bits (8 bytes). in a genral term we use the word size as a value that may change size depending ion the architecture.

the size of the int can change with the architecture, and this is the [[Mechanical empaty with the architecture]] that we may reach and takes into consideration

### Initialization

- **zero-value initialization**: a memory is allocated with a zero value state (ensure 100% that the value is zero) using the `var` keyword.
```
	var counter int  // initialize the value with nil (zero value)
```

- productivity initisalization `:=` ( comes from Pascal influnce). Declare and initialize the variable. 
	`aa := 0 `


### Conversion over casting

Go doesn't have casting, it has conversion. It is a [[Integrity]] choice
With go you can convert one type to another (and not cast).
- casting: change the same variable to another type, by enlarging/decreaseing the size bytes. CONS: you can read/overwrite other memory allocated.
- conversion: create a new variable with a new name and the new type
```
aaa := int32(10)  // create a new variable with a type of int32
```



## Struct types
the struct word is the core way to define user-define data. 

### type declaration 

```
type example {
   flag bool  // 1 byte
   count int16 // 2 bytes
   pi float54  // 4 bytes
}

var p1 example // declare a variable of a type example set to its zero value
```

how much memory is allocated fo the value `p1` ? it is not 4 +2 + 1 = 7 bytes because the alignment of the architectures [[Mechanical sympathy]]

**Alignements**: the architecture is making alignment in order to perform read and write more efficient.
the hardware architecture works with word boundaries. If a value is not aligned to the boundaries the read and write can be inefficiente, because it requires to read across different boundaries.
Looking at the size of a value to determine what is the alignemet and makesure it fit in memory with the boundaries.

If a value don't fill a boundaries a **padding** is used. a padding is a portion of the memory that is not used but it is used to align the value to a word boundaries.

Having lots of padding can have impact on the memory footprint, but this is a micro-optimization for perfomance [[Correctness vs performance]] and not optimize for productivity. Readibility-first decision.

micro-optimization: order by the value in a struct from the bigger types at the top to the lower bytes size to the button.


**Literal construction** 

there is nothing wrong with this construction, but be consistent with the impact on [[Readibility]]

```
e2 := example{
  flag: true,
  counter: 10,
  pi: 3.14
}
```


## Unnamed/literal type

this can be used to have a type that is used only in onepoint without polluting the code with struct. 
choose the unnamed type for [[Readibility]]
A function in go is a literal value (it doesn not have a named type)

```
// declare a unnamed value with its zero value
 var e1 struct {
    flag bool
    counteter int16
    pi float32
}
```


### Implicit/excplicit  conversion
Go doens't allow implicit conversion for namedn type, you need to use explicti conversion. Instead with unamend type (literal struct, function) you can do implicit conversion.

the explicit conversion permits to mantain integrity [[Integrity]] of the code and show your intent [[Readibility]]

```
type bill struct {
   flag float
}

type alice struct {
   flag float
}

var b bill
var a alice

a = b // compiler error. implicit conversion in not allowed

a = bill(b)  // explicit conversion

```


[[Pointers]]

[[Escape analysis]]

## Constants

The `const` variable only exist at compile time.
There are two type of constant
- constant of a kind
- constant of a type

### Kind promotion 

TODO: to be better understand