# Lack of Enums

My _least_ favorite part of Go? Glad you asked. It's Go's lack of [enums](https://en.wikipedia.org/wiki/Enumerated_type), [sum types](https://en.wikipedia.org/wiki/Algebraic_data_type), [tagged unions](https://en.wikipedia.org/wiki/Tagged_union), etc. Compared to other statically typed languages like:

- [Rust](https://www.rust-lang.org/)
- [TypeScript](https://www.typescriptlang.org/)
- [OCaml](https://ocaml.org/)

Go's type system just isn't as powerful. It's more similar to C's type system than it is to Rust's. It's more concerned with simplicity than it is with expressiveness.

## Error Handling

In Rust, like Go, errors are just values. In Go, we write something like this:

```go
user, err := getUser()
if err != nil {
    return fmt.Errorf("failed to get user: %w", err)
}
// do something with user
```

In Rust, we can do something like this:

```clike
let user_result = get_user();
let user = match user_result {
    Ok(user) => user,
    Err(error) => return Err(format!("failed to get user: {}", error)),
};
```

In Rust, the `get_user` function returns a [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html) type: a type that is either an `Ok` or an `Err`. The compiler _forces_ the developer to handle the error case before they can continue with the happy path (using the user data).

In Go, the developer can choose to happily ignore the error value if they choose and use the user data, even if it's invalid (probably `nil` or an empty struct).

The support for enums in Rust makes it easier to write bug-free code.