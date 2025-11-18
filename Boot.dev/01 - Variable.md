#go #golang #variable
# Declaration
```
var mySkillIssues int
mySkillIssues = 42
```
short hand 
```
mySkillIssues := 42
```

## Type Sizes
Integers, uints, floats, and complex numbers all have type sizes.

#### Signed Integers (No Decimal)
```
int  int8  int16  int32  int64
```

#### Unsigned Integers (Whole Numbers/No Decimal)
"uint" stands for "unsigned integer".

```
uint 
uint8 
uint16 
uint32 
uint64 
uintptr
```

#### Signed Decimal Numbers
```
float32 
float64
```

#### Complex Numbers (a Complex Number Has a Real and Imaginary Part)
```
complex64 
complex128
```

### What's the Deal With the Sizes?
The size (8, 16, 32, 64, 128, etc) represents how many bits in memory will be used to store the variable. The "default" int and uint types refer to their respective 32 or 64-bit sizes depending on the environment of the user.

The "standard" sizes that should be used unless you have a specific performance need (e.g. using less memory) are:

```
int
uint
float64
complex128
```

#### Converting Between Types
Some types can be easily converted like this:

```
temperatureFloat := 88.26
temperatureInt := int64(temperatureFloat)
```

Casting a float to an integer in this way truncates the floating point portion.

#### Same line

```
mileage, company := 80276, "Toyota"
```

## Constants
Constants are declared with the const keyword. They can't use the := short declaration syntax.

```
const pi = 3.14159
```

Constants can be primitive types like strings, integers, booleans and floats. They can not be more complex types like slices, maps and structs, which are types we will explain later.

As the name implies, the value of a constant can't be changed after it has been declared.

this is valid:

```
const firstName = "Lane"
const lastName = "Wagner"
const fullName = firstName + " " + lastName
```

## Formatting strings

Formatting Strings in Go
Go follows the printf tradition from the C language. In my opinion, string formatting/interpolation in Go is less elegant than Python's f-strings, unfortunately.

***fmt.Printf - Prints a formatted string to standard out.***
***fmt.Sprintf() - Returns the formatted string***
These following "formatting verbs" work with the formatting functions above:

#### Default Representation
The %v variant prints the Go syntax representation of a value, it's a nice default.

```
s := fmt.Sprintf("I am %v years old", 10)
// I am 10 years old
```

```
s := fmt.Sprintf("I am %v years old", "way too many")
// I am way too many years old
```
If you want to print in a more specific way, you can use the following formatting verbs:

##### String
```
s := fmt.Sprintf("I am %s years old", "way too many")
// I am way too many years old
```

##### Integer
```
s := fmt.Sprintf("I am %d years old", 10)
// I am 10 years old
```
##### Float
```
s := fmt.Sprintf("I am %f years old", 10.523)
// I am 10.523000 years old
```

```
// The ".2" rounds the number to 2 decimal places
s := fmt.Sprintf("I am %.2f years old", 10.523)
// I am 10.52 years old
```

### Runes and String Encoding
In many programming languages (cough, C, cough), a "character" is a single byte. Using ASCII encoding, the standard for the C programming language, we can represent 128 characters with 7 bits. This is enough for the English alphabet, numbers, and some special characters.

In Go, strings are just sequences of bytes: they can hold arbitrary data. However, Go also has a special type, rune, which is an alias for int32. This means that a rune is a 32-bit integer, which is large enough to hold any Unicode code point.

When you're working with strings, you need to be aware of the encoding (bytes -> representation). Go uses UTF-8 encoding, which is a variable-length encoding.

What Does This Mean?
There are 2 main takeaways:

When you need to work with individual characters in a string, you should use the rune type. It breaks strings up into their individual characters, which can be more than one byte long.
We can include a wide variety of Unicode characters in our strings, such as emojis and Chinese characters, and Go will handle them just fine.

### Formatting strings

```
import "fmt"
fmt.Printf(`Message: "%s" Cost: %v cents`, message, cost)
```

```go
name := "Hello"
firstRuneOfName = []rune(name)[0]
fmt.printLn firstRuneOfName
// H
```
## Basic Variables

| Variable Name | Description                           |
| ------------- | ------------------------------------- |
| bool          | a boolean value, either true or false |
| string        |                                       |
| int           |                                       |
| float64       |                                       |
| byte          |                                       |


