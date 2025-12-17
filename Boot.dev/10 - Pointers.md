
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

# Pointer Receivers

A receiver type on a method can be a pointer.

Methods with pointer receivers can modify the value to which the receiver points. Since methods often need to modify their receiver, pointer receivers are _more common_ than value receivers. However, methods with pointer receivers don't require that a pointer is used to call the method. The pointer will automatically be derived from the value.

## Pointer Receiver

```go
type car struct {
	color string
}

func (c *car) setColor(color string) {
	c.color = color
}

func main() {
	c := car{
		color: "white",
	}
	c.setColor("blue")
	fmt.Println(c.color)
	// prints "blue"
}
```
## Non-Pointer Receiver

```go
type car struct {
	color string
}

func (c car) setColor(color string) {
	c.color = color
}

func main() {
	c := car{
		color: "white",
	}
	c.setColor("blue")
	fmt.Println(c.color)
	// prints "white"
}
```
# Pointer Receiver Code

Methods with pointer receivers don't require that a pointer is used to call the method. The pointer will automatically be derived from the value.

```go
type circle struct {
	x int
	y int
    radius int
}

func (c *circle) grow() {
    c.radius *= 2
}

func main() {
    c := circle{
        x: 1,
        y: 2,
        radius: 4,
    }

    // notice c is not a pointer in the calling function
    // but the method still gains access to a pointer to c
    c.grow()
    fmt.Println(c.radius)
    // prints 8
}
```
# Pointer Performance

Occasionally, new Go developers hear "pointers don't pass copies" and take that to a logical extreme, concluding:

> Pointers are always faster because copying is slow. I'll always use pointers!

_No. Bad. Stop._

**Here are my rules of thumb:**

1. First, worry about writing clear, correct, maintainable code.
2. If you have a performance problem, fix it.

Before even thinking about using pointers to optimize your code, use pointers when you need a shared reference to a value; otherwise, just use values.

**If you _do_ have a performance problem, consider:**

1. [Stack vs. Heap](https://go.dev/doc/faq#stack_or_heap)
2. Copying

Interestingly, local non-pointer variables are generally faster to pass around than pointers because they're stored on the [stack](https://computersciencewiki.org/index.php?title=Stack_memory), which is faster to access than the [heap](https://computersciencewiki.org/index.php/Heap_memory). Even though copying is involved, the stack is so fast that it's no big deal.

Once the value becomes large enough that copying is the greater problem, it can be worth using a pointer to avoid copying. That value will probably go to the heap, so the gain from avoiding copying needs to be greater than the loss from moving to the heap.

_One of the reasons Go programs tend to use less memory than Java and C# programs is that Go tends to allocate more on the stack._

If you're curious to dig deeper, I ran [a benchmark and wrote about it](https://blog.boot.dev/golang/pointers-faster-than-values).