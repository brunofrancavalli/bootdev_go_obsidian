
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

# Packages

Every Go program is made up of packages.

You have probably noticed the `package main` at the top of all the programs you have been writing.

A package named "main" has an entrypoint at the `main()` function. A `main` package is compiled into an executable program.

A package by any other name is a "library package". Libraries have no entry point. Libraries simply export functionality that can be used by other packages. For example:

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	fmt.Println("My favorite number is", rand.Intn(10))
}
```

This program is an executable. It is a "main" package and _imports_ from the `fmt` and `math/rand` library packages.

# Package Naming

By _convention_, a package's name is the same as the last element of its import path. For instance, the `math/rand` package comprises files that begin with:

```go
package rand
```

That said, package names aren't _required_ to match their import path. For example, I could write a new package with the path `github.com/textio/rand` and name the package `random`:

```go
package random
```

While the above is possible, it is discouraged for the sake of consistency.

## One Package / Directory

A directory of Go code can have **at most** one package. All `.go` files in a single directory must all belong to the same package. If they don't, an error will be thrown by the compiler. This is true for main and library packages alike.

# Modules

Go programs are organized into _packages_. A package is a directory of Go code that's all compiled together. Functions, types, variables, and constants defined in one source file are visible to **all other source files within the same package (directory)**.

A _repository_ contains one or more _modules_. A module is a collection of Go packages that are released together.

![](https://storage.googleapis.com/qvault-webapp-dynamic-assets/course_assets/IyRpjCm-1280x544.png)

## One Module Per Repo (Usually)

A file named `go.mod` at the root of a project declares the module. It contains:

- The module path
- The version of the Go language your project requires
- Optionally, any external package dependencies your project has

The module path is just the import path prefix for all packages within the module. Here's an example of a `go.mod` file:

```
module github.com/bootdotdev/exampleproject

go 1.25.1

require github.com/google/examplepackage v1.3.0
```

Each module's path not only serves as an import path prefix for the packages within but _also indicates where the go command should look to download it_. For example, to download the module `golang.org/x/tools`, the go command would consult the repository located at [https://golang.org/x/tools](https://golang.org/x/tools).

> An "import path" is a string used to import a package. A package's import path is its module path joined with its subdirectory within the module. For example, the module `github.com/google/go-cmp` contains a package in the directory `cmp/`. That package's import path is `github.com/google/go-cmp/cmp`. Packages in the standard library do not have a module path prefix.

-- Paraphrased from [Golang.org's code organization](https://golang.org/doc/code#Organization)

## Only GitHub?

You don't _need_ to publish your code to a remote repository before you can build it. A module can be defined locally without belonging to a remote repository. However, it's a good habit to keep a copy of all your projects on _some_ remote server, like GitHub.

# Go Environment

Let's clear up a few points before you start writing Go locally.

## Directory Structure

To recap how packages and modules work in your project directory structure:

- You will have many _git repositories_ on your machine (typically one per project).
- Each repository is typically a single _module_.
- Each module contains one or more _packages_
- Each package consists of one or more _Go source files_ in a single directory.

The path to a package's directory determines its _import path_ and where it can be downloaded from if you decide to host it on a remote version control system like [GitHub](https://github.com/) or [GitLab](https://gitlab.com/).

## A Note About GOPATH

The `$GOPATH` environment variable will be set by default somewhere on your machine (typically in the home directory, `~/go`). Since we will be working in the new "Go modules" setup, you _don't need to worry about that_. If you read something online about setting up your `GOPATH`, that documentation is probably out of date.

These days you should _avoid_ working in the `$GOPATH/src` directory. Again, that's the old way of doing things and can cause unexpected issues, so better to just avoid it.

## Get Into Your Workspace

Navigate to a location on your machine where you want to store some code. For example, I store all my code in `~/workspace`, then organize it into subfolders based on the remote location. For example,

`~/workspace/github.com/wagslane/go-password-validator` = [https://github.com/wagslane/go-password-validator](https://github.com/wagslane/go-password-validator)

That said, you can put your code wherever you want.

# Go Run

The [`go run` command](https://pkg.go.dev/cmd/go#hdr-Compile_and_run_Go_program) quickly compiles and runs a Go package. The compiled binary is _not_ saved in your working directory.

I typically use `go run` to do local testing, scripting and debugging.

## Assignment

1. [ ] Inside `hellogo`, create a new file called `main.go`.

Conventionally, the file in the `main` package that contains the `main()` function is called `main.go`.

2. [ ] Paste the following code into your file:

```go
package main

import "fmt"

func main() {
	fmt.Println("hello world")
}
```

3. [ ] Run the code

```bash
go run main.go
```

**Run and submit** the CLI tests from the **root of the `main` package**.

## Tips

_You can execute `go help run` in your shell to rtfm._

# Go Build

The [`go build` command](https://pkg.go.dev/cmd/go#hdr-Compile_packages_and_dependencies) compiles go code into a single, statically linked executable program. One of the beauties of Go is that you always `go build` for production, and because the output is a statically compiled binary, you can ship it to production or end users without them needing the Go toolchain installed.

Some new Go devs use `go run` on a server in production, which is a _huge_ mistake.

## Assignment

1. [ ] Ensure you are in your `hellogo` repo, then run:

```bash
go build
```

2. [ ] Run the new program:

```bash
./hellogo
```

**Run and submit** the CLI tests from the **root of the repo**.