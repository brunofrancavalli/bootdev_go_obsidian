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

# Channels

Channels are a typed, [thread-safe](https://en.wikipedia.org/wiki/Thread_safety) queue. Channels allow different goroutines to communicate with each other.

## Create a Channel

Like maps and slices, channels must be created _before_ use. They also use the same `make` keyword:

```go
ch := make(chan int)
```

## Send Data to a Channel

```go
ch <- 69
```

The `<-` operator is called the _channel operator_. Data flows in the direction of the arrow. This operation will [_block_](https://en.wikipedia.org/wiki/Blocking_\(computing\)) until another goroutine is ready to receive the value.

## Receive Data from a Channel

```go
v := <-ch
```

This reads and removes a value from the channel and saves it into the variable `v`. This operation will _block_ until there is a value in the channel to be read.

## Reference Type

Like maps and slices, channels are reference types, meaning they are passed by reference by default.

```go
func send(ch chan int) {
    ch <- 99
}

func main() {
    ch := make(chan int)
    go send(ch)
    fmt.Println(<-ch) // 99
}
```

## Blocking and Deadlocks

A [deadlock](https://yourbasic.org/golang/detect-deadlock/#:~:text=yourbasic.org%2Fgolang,look%20at%20this%20simple%20example.) is when a group of goroutines are all blocking so none of them can continue. This is a common bug that you need to watch out for in concurrent programming.

# Channels

In the previous lesson, we saw how we can receive values from channels like this:

```go
v := <-ch
```

However, sometimes we don't care _what_ is passed through a channel. We only care _when_ and _if_ something is passed. In that situation, we can block and wait until something is sent on a channel using the following syntax.

```go
<-ch
```

This will block until it pops a single item off the channel, then continue, discarding the item.

In cases like this, [Empty structs](https://dave.cheney.net/2014/03/25/the-empty-struct) are often used as a [unary](https://en.wikipedia.org/wiki/Unary_operation) value so that the sender communicates that this is only a "signal" and not some data that is meant to be captured and used by the receiver.

Here is an example:

```go
func downloadData() chan struct{} {
	downloadDoneCh := make(chan struct{})

	go func() {
		fmt.Println("Downloading data file...")
		time.Sleep(2 * time.Second) // simulate download time

		// after the download is done, send a "signal" to the channel
		downloadDoneCh <- struct{}{}
	}()

	return downloadDoneCh
}

func processData(downloadDoneCh chan struct{}) {
	// any code here can run normally
	fmt.Println("Preparing to process data...")

	// block until `downloadData` sends the signal that it's done
	<-downloadDoneCh

	// any code here can assume that data download is complete
	fmt.Println("Data download complete, starting data processing...")
}

processData(downloadData())
// Preparing to process data...
// Downloading data file...
// Data download complete, starting data processing...
```