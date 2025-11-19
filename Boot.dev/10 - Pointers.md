
# Introduction to Pointers

As we have learned, a variable is a named location in memory that stores a value. We can manipulate the value of a variable by assigning a new value to it or by performing operations on it. When we assign a value to a variable, we are storing that value in a specific location in memory.

```go
x := 42
// "x" is the name of a location in memory. That location is storing the integer value of 42
```

## A Pointer Is a Variable

A pointer is a variable that stores the _memory address_ of another variable. This means that a pointer "points to" the _location_ of where the data is stored, **_not_** the actual data itself.

The `*` syntax defines a pointer:

```go
var p *int
```

The `&` operator generates a pointer to its operand.

```go
myString := "hello"
myStringPtr := &myString
```

# References

It's possible to define an empty pointer. For example, an empty pointer to an integer:

```go
var p *int

fmt.Printf("value of p: %v\n", p)
// value of p: <nil>
```

Its zero value is `nil`, which means it doesn't point to any memory address. Empty pointers are also called "nil pointers".

Instead of starting with a nil pointer, it's common to use the `&` operator to get a pointer to its operand:

```go
myString := "hello"      // myString is just a string
myStringPtr := &myString // myStringPtr is a pointer to myString's address

fmt.Printf("value of myStringPtr: %v\n", myStringPtr)
// value of myStringPtr: 0x140c050
```

## Dereference

The `*` operator dereferences a pointer to get the original value.

```go
*myStringPtr = "world"                              // set myString through the pointer
fmt.Printf("value of myString: %s\n", *myStringPtr) // read myString through the pointer
// value of myString: world
```

Unlike C, Go has no pointer arithmetic (which is covered in our [Learn Memory Management course](https://www.boot.dev/courses/learn-memory-management) if you haven't taken it already).
