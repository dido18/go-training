# ## Factory Function 
In go there are no constructors.
Ther are instead factory functions: a function that initialize a struct for use and returns it back to the caller. This is good for [[Readibility]] and [[Simplicity]]

Example

```go
// create an user value and pass a copy back to the calle
func createUserV1() user{
    u := user{
        Name:"bill"
    }
	
	fmt.Prinltn("V1", &u)
	
	return u 
}  

// create an user value and pass a pointer back to the calle
func createUserV2() *user{
    u := user{
	    Name:"bill"
	}
	
	fmt.Prinltn("v2", &u)
	
	return &u 
}  

func main(){
  u1 := createuUserV1()
  u2 := createUserV2()
  pritnln("U1", &u1, "u2"; &u2)
}
```

![[factory-constr-escape-analysis.excalidraw|600]]


See [[Escape analysis]] to show detail of the ecape analysis algorithm

The stack allocation is as follows:
- createuUserV1: using a copy the frame below the active frame can be removed safely. 
- createuUserV2: the pointer in the active frame COULD points to corrupted data if the data is maintaned into the stack!!!  [[Integrity]]  issue . The compiler performs static anaalysis to allocate the data into the heap executin the **escape analysis.** 
