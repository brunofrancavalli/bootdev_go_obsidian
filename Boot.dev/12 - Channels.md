# Concurrency

Click to hide video

## What Is Concurrency?

Concurrency is the ability to perform multiple tasks at the same time. Typically, our code is executed one line at a time, one after the other. This is called _sequential execution_ or _synchronous execution_.

![](https://storage.googleapis.com/qvault-webapp-dynamic-assets/course_assets/1pQKFgw-1280x700.png)

If the computer we're running our code on has multiple cores, we can even execute multiple tasks at _exactly_ the same time. If we're running on a single core, a single core executes code at _almost_ the same time by switching between tasks very quickly. Either way, the code we write looks the same in Go and takes advantage of whatever resources are available.

## How Does Concurrency Work in Go?

Go was designed to be concurrent, which is a trait _fairly_ unique to Go. It excels at performing many tasks simultaneously safely using a simple syntax.

There isn't a popular programming language in existence where spawning concurrent execution is quite as elegant, at least in my opinion.

Concurrency is as simple as using the `go` keyword when calling a function:

```go
go doSomething()
```

In the example above, `doSomething()` will be executed concurrently with the rest of the code in the function. The `go` keyword is used to spawn a new _[goroutine](https://gobyexample.com/goroutines)_.

## Assignment

At Textio we send _a lot_ of network requests. Each email we send must go out over the internet. To serve our millions of customers, we need a single Go program to be capable of sending _thousands_ of emails at once.

Edit the `sendEmail()` function to execute its anonymous function concurrently so that the "received" message prints _after_ the "sent" message.

