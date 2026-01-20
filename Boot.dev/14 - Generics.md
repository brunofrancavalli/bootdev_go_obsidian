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