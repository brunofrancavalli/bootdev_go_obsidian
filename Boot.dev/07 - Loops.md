 
### L1 Loops in Go

The basic loop in Go is written in standard C-like syntax:

```go
for INITIAL; CONDITION; AFTER{
  // do something
}
```
INITIAL is run once at the beginning of the loop and can create
variables within the scope of the loop.

CONDITION is checked before each iteration. If the condition doesn't pass
then the loop breaks.

AFTER is run after each iteration.

For example:

```go
for i := 0; i < 10; i++ {
  fmt.Println(i)
}
// Prints 0 through 9
```

### L2 Can omit parts of the for loop


### L3 No while

Most programming languages have a concept of a while loop. Because Go allows for the omission of sections of a for loop, a while loop is just a for loop that only has a CONDITION.

for CONDITION {
  // do some stuff while CONDITION is true
}

For example:

```
plantHeight := 1
for plantHeight < 5 {
  fmt.Println("still growing! current height:", plantHeight)
  plantHeight++
}
fmt.Println("plant has grown to ", plantHeight, "inches")
```
Which prints:

still growing! current height: 1
still growing! current height: 2
still growing! current height: 3
still growing! current height: 4
plant has grown to 5 inches

### L5 Continue & Break
Whenever we want to change the control flow of a loop we can use the continue and break keywords.

continue
The continue keyword stops the current iteration of a loop and continues to the next iteration. continue is a powerful way to use the guard clause pattern within loops.

```go
for i := 0; i < 10; i++ {
  if i % 2 == 0 {
    continue
  }
  fmt.Println(i)
}
// 1
// 3
// 5
// 7
// 9
```

break
The break keyword stops the current iteration of a loop and exits the loop.

```go
for i := 0; i < 10; i++ {
  if i == 5 {
    break
  }
  fmt.Println(i)
}
// 0
// 1
// 2
// 3
// 4
```

## Range

``` go
for index, person := range people {
    fmt.Println(person.Name)
}
```