the methods defined to a data i used to assign behaviour to it.


>[!note]
> A data with behaviour must be the exception, not the rule.
> Function are the first choice.
 

Go provides separation of data and behaviour because the methods are defined **outside** the data (not inside the data as OOP).


The method are syntattic sugar around data.
underneth we have function that go adjust by passing value or pointer.

Example of data with behviour:
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
	f1 = d.displayName
	f1()
	d.name = "dummy"
}

```


