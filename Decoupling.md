
Objective: add thin layers of decoupling and know the cost and impact of decoupling. Minimize pollution maximize the advantages of decoupling.

## From concrete data to decoupling behaviour

- **Concrete Data**:  the data is the core, our work is based around data. The perfomance  comes from the data [[Correctness vs performance]], how we access the data is fundamental for performance.
- **Decoupling**: decoupling is done with *Behaviour*.  

Starts from the concrete data and move up. 


![[decoupling.excalidraw|800]]

> [!WARNING] Wrong approach of most of developers
> NEVER do the opposite that starts from decoupling (i.e. adding interface before) and then move to data


## Method 

Go allows data to have behaviour through `method`. 

A method is a function that has a receiver.
The method is syntattic sugar to give a piece of data the [[Behaviour]].

>[!Hint]
  Decouling with a method must be the exception not the rule. 
  The use of `functions` is the first choice and add behaviour after.


>[!warning]  
>OOP  has a different way of think data because with a class is data that has always associated a behaviour. 


### Value or Pointer receivers ?

The types of receivers are:
 - *value recevier*: it is recevieing a copy of the data
```
	func (u user) notify()
```

- *pointer recevier*:  it is sharing access to the data
```
   func (u *user) changeEmail())
```

How to chose from the two ?

Data drives the semantics not the opposite.
Before you have to decide if the data is pointer or value and then decide the behaviour.

> [!important]
> WRONG DEV THINK: if the method Im writing has to mutate the data use the pointer recevier, if the methos don't mutate the data use value recevier with a copy.


> [!example]


```
// value of type user can be used to call methods with both vlaue and pointer receiver
bill := user{"Bill", "bill@email.com"}
bill.changeEmail("bil@hotmail.com")  // get the pointer 
bill.notify()                        // copy the value


// Pointers of type user can also be used to both the receivers
bill := &user{"Bill", "bill@email.com"}
bill.changeEmail("bil@hotmail.com")
bill.notify()                          // BAD: mixed semantics. take the value of 
                                         pointer
```


>[!warning]
> Don't use the pointer semantics on construction `&user` [[Escape analysis]]

## Decoupling cost 

``` go 
type data struct {
  name string
  age int
}

// value semantic
func (d data) displayName(){
  fmt.Println("my name is", d.name)
}

// pointer semantic
func(d *data) setAge(age int){
  d.age = age
}


func main(){

	d := data{name:"Bill"}
	
	d.displayName()
	d.setAge(22)
	
	// compileres are doing this:
    data.displayName(d)
    (*data).setAge(&d, 22)

  //copy the function pointer
    // the function variable will get itsown copy of d vecuase the methos is using the value  recevier
	f1 = d.displayName
	f1()  // call the method via the variable
	d.name = "john" // change the value of d
	f1()   // print Bill,  we don't see the change, because f1 operate on the copy 

//copy the function pointer
	f2 = d.setAge
	f2(23)
	d.name = "sammy"
	f2(23) // we see the change
}
```

![[decoupling-cost.excalidraw|800]]

## When use Value or Pointer semantic ?

use the value semantics to keep the heap clean [[Correctness vs performance]]

|Type | Class |  Semantics|  Note |  Exception |
|------------ | ------------|-------------------|------| ---|
|built-in | `int` `bool` `string`| Value semantic| They are design to be copied. Also field in a struct as value | pass the pointer to get dat a from sql database |
|Reference types | `map` `slice`  `interface` `channel` `function`|  Value semantic | since they are already pointer there is no need to take the pointer of an adress | Except for `decode` and `unmarshal`
|User-defined struct| `struct` |  Value or Pointer | choose value semantics and after pointer|


Examples of struct with asemantic consistency [[Escape analysis]]

- `IP` of the standard library  https://github.com/golang/go/blob/master/src/net/ip.go#L271 
	- type: is a reference type of `byte[]` => VALUE SEMANTIC
	- semantic: the function Mask() is a `value semantic mutation` api . get a copy of the IP, mutate it and returns  a copy.
```
// Mask returns the result of masking the IP address ip with mask.

func (ip IP) Mask(mask IPMask) IP {
	if len(mask) == IPv6len && len(ip) == IPv4len && allFF(mask[:12]) {
		mask = mask[12:]
	}
	if len(mask) == IPv4len && len(ip) == IPv6len && bytealg.Equal(ip[:12], v4InV6Prefix) {
		ip = ip[12:]
	}
	n := len(ip)
	if n != len(mask) {
		return nil
	}
	out := make(IP, n)

	for i := 0; i < n; i++ {
		out[i] = ip[i] & mask[i]
	}
	return out
}		
```

- `Time` package  https://github.com/golang/go/blob/master/src/time/time.go#L1110
	- type:  use the value semantics,  The construction [[Factory Function]] tells the semantic of the value
		-```
``` 
func Now() Time {
  sec, nsec, mono := now()
}
```

	- excpetion:
		- `func (t *Time) UnmarshalBinary(data []byte) error`
		- `GobDecode(data []byte) error`
- `Files`
	- type: the `New() *File` returns a pointer 
	- behaviour: all the function use the `*Fle` the pointer 

> [!hint]  Look at the factory function [[Factory Function]] it dictates the semantics that will be used.

Questions to help decide
- If you give you a data of a type and change it,  the result is a new value or it is the same ? In two different points in time, after modification it is the same data or is mutate ?
	- time: if I add a 5 seconds of time the result is a new data => value semantic
	- user struct: if change the name of the user data, the result is not a new user but it is the same user with a different name => pointer semantic.