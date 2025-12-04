
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

# Pass by Reference

Functions in Go generally pass variables by value, meaning that functions receive a copy of most non-composite types:

```go
func increment(x int) {
    x++
    fmt.Println(x)
    // 6
}


func main() {
    x := 5
    increment(x)
    fmt.Println(x)
    // 5
}
```

The `main` function still prints `5` because the `increment` function received a _copy_ of `x`.

One of the most common use cases for pointers in Go is to pass variables by reference, meaning that the function receives the _address_ of the original variable, not a copy of the value. This allows the function to **update the original variable's value**.

```go
func increment(x *int) {
    *x++
    fmt.Println(*x)
    // 6
}

func main() {
    x := 5
    increment(&x)
    fmt.Println(x)
    // 6
}
```

## Fields of Pointers

When your function receives a pointer to a struct, you might try to access a field like this and encounter an error:

```go
msgTotal := *analytics.MessagesTotal
```

Instead, access it – like you'd normally do — using a [selector expression](https://go.dev/ref/spec#Selectors).

```go
msgTotal := analytics.MessagesTotal
```

This approach is the recommended, simplest way to access struct fields in Go, and is shorthand for:

```go
(*analytics).MessagesTotal
```

## Assignment

Boots

Spellbook

Community

![Boots](https://www.boot.dev/_nuxt/new_boots_profile.DriFHGho.webp)

**Need help?** I, Boots the Bane of End-Users, can assist... _for a price_.

Start Voice Chat

- main.go

- main_test.go

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

package main

  

type Analytics struct {

MessagesTotal int

MessagesFailed int

MessagesSucceeded int

}

  

type Message struct {

Recipient string

Success bool

}

  

// don't touch above this line

  

// ?

  

Submit

Run

Solution