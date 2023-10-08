
> [!cite]
A piece of code change is behaviour based on the concrete data it is opeating on.


A good reason to have behaviour [[Behaviour]]  associated to data is when we want to implements polimorphism that give the ability to decoupling [[Decoupling]]


Example

``` go 
type reader interface {
   read([]byte)
}


```


Interface `interface` is a type with the following considerations:
- is not real (it is nothing concrete).
- they define a set of common methods.


> [!tip]
> The name of the interface must be verb (e.g reader, writer) not nouns (e.g., animal, dog) because the interface define behaviour.





