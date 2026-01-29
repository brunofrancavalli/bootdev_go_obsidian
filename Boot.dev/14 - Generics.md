# Generics in Go

As we've mentioned, Go does _not_ support classes. For a long time, that meant that Go code couldn't easily be reused in many cases. For example, imagine some code that splits a slice into 2 equal parts. The code that splits the slice doesn't care about the _types of values_ stored in the slice. Before generics, we needed to write the same code for each type, which is a very un-[DRY](https://blog.boot.dev/clean-code/dry-code/) thing to do.

```go
func splitIntSlice(s []int) ([]int, []int) {
    mid := len(s)/2
    return s[:mid], s[mid:]
}
```

```go
func splitStringSlice(s []string) ([]string, []string) {
    mid := len(s)/2
    return s[:mid], s[mid:]
}
```

In Go 1.18 however, support for [generics](https://blog.boot.dev/golang/how-to-use-golangs-generics/) was released, effectively solving this problem!

## Type Parameters

Put simply, generics allow us to use variables to refer to specific types. This is an amazing feature because it allows us to write abstract functions that drastically reduce code duplication.

```go
func splitAnySlice[T any](s []T) ([]T, []T) {
    mid := len(s)/2
    return s[:mid], s[mid:]
}
```

In the example above, `T` is the name of the type parameter for the `splitAnySlice` function, and we've said that it must match the `any` constraint, which means it can be anything. This makes sense because the body of the function _doesn't care_ about the types of things stored in the slice.

```go
firstInts, secondInts := splitAnySlice([]int{0, 1, 2, 3})
fmt.Println(firstInts, secondInts)
```

## Assignment

At Textio we store all the emails for a campaign in memory as a slice. We store payments for a single user in the same way.

Complete the `getLast()` function. It should be a generic function that returns the last element from a slice, no matter the types stored in the slice. If the slice is empty, it should return the zero value of the type.

## Tip: Zero Value of a Type

Creating a variable that's the zero value of a type is easy:

```go
var myZeroInt int
```

It's the same with generics, we just have a variable that represents the type:

```go
var myZero T
```

# Why Generics?

## Generics Reduce Repetitive Code

You should care about generics because they mean you don't have to write as much code! It can be frustrating to write the same logic over and over again, just because you have some underlying data types that are slightly different.

## Generics Are Used More Often in Libraries and Packages

Generics give Go developers an elegant way to write amazing utility packages. While you will see and use generics in application code, I think it will be much more common to see generics used in libraries and packages. Libraries and packages contain importable code intended to be used in _many_ applications, so it makes sense to write them in a more abstract way. Generics are often the way to do just that!

## Why Did It Take So Long to Get Generics?

Go places an emphasis on simplicity. In other words, Go has purposefully left out many features to provide its best feature: being simple and easy to work with.

According to [historical data from Go surveys](https://go.dev/blog/survey2020-results), Go’s lack of generics has always been listed as one of the top three biggest issues with the language. At a certain point, the drawbacks associated with the lack of a feature like generics justify adding complexity to the language.

# Constraints

Sometimes you need your generic function to know _something_ about the types it operates on. The example we used in the first exercise didn't need to know _anything_ about the types in the slice, so we used the built-in `any` constraint:

```go
func splitAnySlice[T any](s []T) ([]T, []T) {
    mid := len(s)/2
    return s[:mid], s[mid:]
}
```

Constraints are just interfaces that allow us to write generics that only operate within the constraints of a given interface type. In the example above, the `any` constraint is the same as the empty interface because it means the type in question can be _anything_.

## Creating a Custom Constraint

Let's take a look at the example of a `concat` function. It takes a slice of values and concatenates the values into a string. This should work with _any type that can represent itself as a string_, even if it's not a string under the hood. For example, a `user` struct can have a `.String()` that returns a string with the user's name and age.

```go
type stringer interface {
    String() string
}

func concat[T stringer](vals []T) string {
    result := ""
    for _, val := range vals {
        // this is where the .String() method
        // is used. That's why we need a more specific
        // constraint instead of the any constraint
        result += val.String()
    }
    return result
}
```

# Interface Type Lists

When generics were released, a new way of writing interfaces was also released at the same time!

Traditional interfaces in Go are **method-based**: a type satisfies an interface if it has the required methods.

With generics, we also got **type-set (type-list) interfaces**. Instead of listing methods, they list the concrete (or underlying) types that are allowed. These are mostly used as **constraints on type parameters**.

For example, to use `<` and `>` on a type parameter `T`, the compiler must know `T` is ordered. A type-list interface spells out exactly which types count as ordered:

```go
// Ordered matches any type that supports <, <=, >, and >=.
type Ordered interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~uintptr |
        ~float32 | ~float64 |
        ~string
}

// Because T is constrained by Ordered, the compiler knows
// that < is valid for any T used with this function.
func Min[T Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}
```

# Parametric Constraints

Your interface definitions, which can later be used as constraints, can accept [type parameters](https://go.dev/tour/generics/1) as well.

```go
// The store interface represents a store that sells products.
// It takes a type parameter P that represents the type of products the store sells.
type store[P product] interface {
	Sell(P)
}

type product interface {
	Price() float64
	Name() string
}

type book struct {
	title  string
	author string
	price  float64
}

func (b book) Price() float64 {
	return b.price
}

func (b book) Name() string {
	return fmt.Sprintf("%s by %s", b.title, b.author)
}

type toy struct {
	name  string
	price float64
}

func (t toy) Price() float64 {
	return t.price
}

func (t toy) Name() string {
	return t.name
}

// The bookStore struct represents a store that sells books.
type bookStore struct {
	booksSold []book
}

// Sell adds a book to the bookStore's inventory.
func (bs *bookStore) Sell(b book) {
	bs.booksSold = append(bs.booksSold, b)
}

// The toyStore struct represents a store that sells toys.
type toyStore struct {
	toysSold []toy
}

// Sell adds a toy to the toyStore's inventory.
func (ts *toyStore) Sell(t toy) {
	ts.toysSold = append(ts.toysSold, t)
}

// sellProducts takes a store and a slice of products and sells
// each product one by one.
func sellProducts[P product](s store[P], products []P) {
	for _, p := range products {
		s.Sell(p)
	}
}

func main() {
	bs := bookStore{
		booksSold: []book{},
	}

    // By passing in "book" as a type parameter, we can use the sellProducts function to sell books in a bookStore
	sellProducts[book](&bs, []book{
		{
			title:  "The Hobbit",
			author: "J.R.R. Tolkien",
			price:  10.0,
		},
		{
			title:  "The Lord of the Rings",
			author: "J.R.R. Tolkien",
			price:  20.0,
		},
	})
	fmt.Println(bs.booksSold)

    // We can then do the same for toys
	ts := toyStore{
		toysSold: []toy{},
	}
	sellProducts[toy](&ts, []toy{
		{
			name:  "LEGO bricks",
			price: 10.0,
		},
		{
			name:  "Barbie",
			price: 20.0,
		},
	})
	fmt.Println(ts.toysSold)
}
```

# Naming Generic Types

Let's look at this simple example again:

```go
func splitAnySlice[T any](s []T) ([]T, []T) {
    mid := len(s)/2
    return s[:mid], s[mid:]
}
```

Remember, `T` is just a variable name, We could have named the type parameter _anything_. `T` happens to be a fairly common convention for a type variable, similar to how `i` is a convention for index variables in loops.

This is just as valid:

```go
func splitAnySlice[MyAnyType any](s []MyAnyType) ([]MyAnyType, []MyAnyType) {
    mid := len(s)/2
    return s[:mid], s[mid:]
}
```