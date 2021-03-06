# How to organize the go struct, in order to save memory.

In this article I want to explain you how the memory works internally in a 64 bit architecture, when you instantiate a struct. (You can play with the code https://play.golang.org/p/cUgB54yCpL )

## How memory works internally :

When you have a struct like this one :
```golang
type myStruct struct {
 myBool   bool    // 1 byte
 myFloat float64  // 8 bytes
 myInt  int32     // 4 bytes
}
```

As you see a boolean takes 1 byte, a float64 8 bytes, and an int32 4 bytes.
But the memory allocates consecutive packet of 8 bytes. So instead of taking 1 + 8 + 4 = 13 bytes. This struct will takes : 24 bytes
```golang
a := myStruct{}
fmt.Println(unsafe.Sizeof(a)) // 24 bytes
```
Because in memory we will have :

![Struct in memory](/images/memorystruct1.png)

So 8 + 8 + 8 = 24 bytes

## How to optimize :
It’s possible to optimize, ordering the struct by bytes taken :
```golang
type myStructOptimized struct {
 myFloat float64 // 8 bytes
 myBool  bool    // 1 byte
 myInt   int32   // 4 bytes
}
```
This new struct ordered will take now :
```golang
b := myStructOptimized{}
fmt.Println(unsafe.Sizeof(b)) // ordered 16 bytes
```
16 bytes, because in memory it will be allocated like that (https://play.golang.org/p/gmkrt6X7aM) :

![Struct in memory](/images/memorystruct2.png)

8 + 8 = 16 bytes

As you can see, there are a padding to respect the data alignment, (https://en.wikipedia.org/wiki/Data_structure_alignment), So it means that in some case the data is not optimally word-aligned (depending on many things processor, cache memory, virtual memory) and it can sometimes slower the application, so benchmark everything :D

### Links :
https://play.golang.org/p/cUgB54yCpL
https://github.com/Tkanos/optimizing-memory-by-ordering-struct-by-type
http://www.catb.org/esr/structure-packing/
https://en.wikipedia.org/wiki/Data_structure_alignment
