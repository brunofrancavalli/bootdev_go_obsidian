
## General

```
type shape interface {
  area() float64
  perimeter() float64
}

type rect struct {
    width, height float64
}
func (r rect) area() float64 {
    return r.width * r.height
}
func (r rect) perimeter() float64 {
    return 2*r.width + 2*r.height
}

type circle struct {
    radius float64
}
func (c circle) area() float64 {
    return math.Pi * c.radius * c.radius
}
func (c circle) perimeter() float64 {
    return 2 * math.Pi * c.radius
}
```
Usage
```
func printShapeData(s shape) {
	fmt.Printf("Area: %v - Perimeter: %v\n", s.area(), s.perimeter())
}
```

The interface association is implicit, that means I don't need to tell about the interface.



## Multiple Interfaces

A type can implement any number of interfaces in Go. For example, the empty interface, interface{}, is always implemented by every type because it has no requirements.

### Type Assertions
When working with interfaces in Go, every once-in-awhile you'll need access to the underlying type of an interface value. You can cast an interface to its underlying type using a type assertion.

The example below shows how to safely access the radius field of s when s is an unknown type:

we want to check if s is a circle in order to cast it into its underlying concrete type
we know s is an instance of the shape interface, but we do not know if it's also a circle
c is a new circle struct cast from s
ok is true if s is indeed a circle, or false if s is NOT a circle

```
type shape interface {
	area() float64
}

type circle struct {
	radius float64
}

c, ok := s.(circle)
if !ok {
	// log an error if s isn't a circle
	log.Fatal("s is not a circle")
}

radius := c.radius
```

### Type Switches

A type switch makes it easy to do several type assertions in a series.

A type switch is similar to a regular switch statement, but the cases specify types instead of values.

```
func printNumericValue(num interface{}) {
	switch v := num.(type) {
	case int:
		fmt.Printf("%T\n", v)
	case string:
		fmt.Printf("%T\n", v)
	default:
		fmt.Printf("%T\n", v)
	}
}

func main() {
	printNumericValue(1)
	// prints "int"

	printNumericValue("1")
	// prints "string"

	printNumericValue(struct{}{})
	// prints "struct {}"
}

fmt.Printf("%T\n", v) prints the type of a variable.
```

### Clean Interfaces

Writing clean interfaces is hard. Frankly, any time you’re dealing with abstractions in code, the simple can become complex very quickly if you’re not careful. Let’s go over some rules of thumb for keeping interfaces clean.

1. Keep Interfaces Small
If there is only one piece of advice that you take away from this lesson, make it this: keep interfaces small! Interfaces are meant to define the minimal behavior necessary to accurately represent an idea or concept.

Here is an example from the standard HTTP package of a larger interface that’s a good example of defining minimal behavior:

```
type File interface {
    io.Closer
    io.Reader
    io.Seeker
    Readdir(count int) ([]os.FileInfo, error)
    Stat() (os.FileInfo, error)
}
```

Any type that satisfies the interface’s behaviors can be considered by the HTTP package as a File. This is convenient because the HTTP package doesn't need to know if it’s dealing with a file on disk, a network buffer, or a simple []byte.

2. Interfaces Should Have No Knowledge of Satisfying Types
An interface should define what is necessary for other types to classify as a member of that interface. They shouldn't be aware of any types that happen to satisfy the interface at design time.

For example, let’s assume we are building an interface to describe the components necessary to define a car.

```
type car interface {
	Color() string
	Speed() int
	IsFiretruck() bool
}
```

Color() and Speed() make perfect sense, they are methods confined to the scope of a car. IsFiretruck() is an anti-pattern. We are forcing all cars to declare whether or not they are firetrucks. In order for this pattern to make any amount of sense, we would need a whole list of possible subtypes. IsPickup(), IsSedan(), IsTank()… where does it end??

Instead, the developer should have relied on the native functionality of type assertion to derive the underlying type when given an instance of the car interface. Or, if a sub-interface is needed, it can be defined as:

```
type firetruck interface {
	car
	HoseLength() int
}
```

Which inherits the required methods from car as an embedded interface and adds one additional required method to make the car a firetruck.

3. Interfaces Are Not Classes
Interfaces are not classes, they are slimmer.
Interfaces don't have constructors or deconstructors that require that data is created or destroyed.
Interfaces aren't hierarchical by nature, though there is syntactic sugar to create interfaces that happen to be supersets of other interfaces.
Interfaces define function signatures, but not underlying behavior. Making an interface often won't DRY up your code in regards to struct methods. For example, if five types satisfy the fmt.Stringer interface, they all need their own version of the String() function.
Optional: Further Reading
[Best Practices for Interfaces in Go](https://blog.boot.dev/golang/golang-interfaces/)
