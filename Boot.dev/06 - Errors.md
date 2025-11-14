
## 1 The Error interface

Interface for error

```
type error interface {
    Error() string
}
```

How it works:

```
// Atoi converts a stringified number to an integer
i, err := strconv.Atoi("42b")
if err != nil {
    fmt.Println("couldn't convert:", err)
    // because "42b" isn't a valid integer, we print:
    // couldn't convert: strconv.Atoi: parsing "42b": invalid syntax
    // Note:
    // 'parsing "42b": invalid syntax' is returned by the .Error() method
    return
}
// if we get here, then the
// variable i was converted successfully
```

## 3 The Error Interface
Because errors are just interfaces, you can build your own custom types that implement the error interface. Here's an example of a userError struct that implements the error interface:

```
type userError struct {
    name string
}

func (e userError) Error() string {
    return fmt.Sprintf("%v has a problem with their account", e.name)
}
```
It can then be used as an error:

```
func sendSMS(msg, userName string) error {
    if !canSendToUser(userName) {
        return userError{name: userName}
    }
    ...
}
```

### 6 The Errors Package
The Go standard library provides an "errors" package that makes it easy to deal with errors.

Read the godoc for the errors.New() function, but here's a simple example:

```
var err error = errors.New("something went wrong")
```


### 7 panic 

Avoid panic, it should never be used

```
func enrichUser(userID string) User {
    user, err := getUser(userID)
    if err != nil {
        panic(err)
    }
    return user
}

func main() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("recovered from panic:", r)
        }
    }()

    // this panics, but the defer/recover block catches it
    // a truly astonishingly bad way to handle errors
    enrichUser("123")
}
```

I use error values for all "normal" error handling, and if I have a truly unrecoverable error, I use log.Fatal to print a message and exit the program.

