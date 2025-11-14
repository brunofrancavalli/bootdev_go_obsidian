#golang #go 

# Functions

### Functions
Functions in Go can take zero or more arguments.

To make code easier to read, the variable type comes after the variable name.

For example, the following function:

```
func sub(x int, y int) int {
    return x-y
}
```

Accepts two integer parameters and returns another integer.

Here, func sub(x int, y int) int is known as the "function signature".

### Anonymous functions

```
func double(a int) int {
    return a + a
}

func main() {
    // using a named function
	newX, newY, newZ := conversions(double, 1, 2, 3)
	// newX is 2, newY is 4, newZ is 6

    // using an anonymous function
	newX, newY, newZ = conversions(func(a int) int {
	    return a + a
	}, 1, 2, 3)
	// newX is 2, newY is 4, newZ is 6
}
```

### The Benefits of Named Returns
Good for Documentation (Understanding)
Named return parameters are great for documenting a function. We know what the function is returning directly from its signature, no need for a comment.

Named return parameters are particularly important in longer functions with many return values.

```
func calculator(a, b int) (mul, div int, err error) {
    if b == 0 {
      return 0, 0, errors.New("can't divide by zero")
    }
    mul = a * b
    div = a / b
    return mul, div, nil
}
```

### Explicit Returns
Even though a function has named return values, we can still explicitly return values if we want to.

Otherwise, if we want to return the values defined in the function signature we can just use a naked return (blank return):

```
func getCoords() (x, y int) {
    return // implicitly returns x and y
}
```

### Functions As Values

Let's assume we have two simple functions:

```
func add(x, y int) int {
	return x + y
}

func mul(x, y int) int {
	return x * y
}
```

We can create a new aggregate function that accepts a function as its 4th argument:

```
func aggregate(a, b, c int, arithmetic func(int, int) int) int {
  firstResult := arithmetic(a, b)
  secondResult := arithmetic(firstResult, c)
  return secondResult
}
```

It calls the given arithmetic function (which could be add or mul, or any other function that accepts two ints and returns an int) and applies it to three inputs instead of two. It can be used like this:

```
func main() {
	sum := aggregate(2, 3, 4, add)
	// sum is 9
	product := aggregate(2, 3, 4, mul)
	// product is 24
}
```

#### Anonymous Functions
Anonymous functions are true to form in that they have no name. They're useful when defining a function that will only be used once or to create a quick closure.

Let's say we have a function conversions that accepts another function, converter as input:

```
func conversions(converter func(int) int, x, y, z int) (int, int, int) {
	convertedX := converter(x)
	convertedY := converter(y)
	convertedZ := converter(z)
	return convertedX, convertedY, convertedZ
}
```
We could define a function normally and then pass it in by name... but it's usually easier to just define it anonymously:

```
func double(a int) int {
    return a + a
}

func main() {
    // using a named function
	newX, newY, newZ := conversions(double, 1, 2, 3)
	// newX is 2, newY is 4, newZ is 6

    // using an anonymous function
	newX, newY, newZ = conversions(func(a int) int {
	    return a + a
	}, 1, 2, 3)
	// newX is 2, newY is 4, newZ is 6
}
```


### Defer

```
func GetUsername(dstName, srcName string) (username string, err error) {
	// Open a connection to a database
	conn, _ := db.Open(srcName)

	// Close the connection *anywhere* the GetUsername function returns
	defer conn.Close()

	username, err = db.FetchUser()
	if err != nil {
		// The defer statement is auto-executed if we return here
		return "", err
	}

	// The defer statement is auto-executed if we return here
	return username, nil
}
```

