# Mutexes in Go

Mutexes allow us to _lock_ access to data. This ensures that we can control which goroutines can access certain data at which time.

Go's standard library provides a built-in implementation of a mutex with the [sync.Mutex](https://pkg.go.dev/sync#Mutex) type and its two methods:

- [.Lock()](https://golang.org/pkg/sync/#Mutex.Lock)
- [.Unlock()](https://golang.org/pkg/sync/#Mutex.Unlock)

We can protect a block of code by surrounding it with a call to `Lock` and `Unlock` as shown on the `protected()` function below.

It's good practice to structure the protected code within a function so that `defer` can be used to ensure that we never forget to unlock the mutex.

```go
func protected(){
    mu.Lock()
    defer mu.Unlock()
    // the rest of the function is protected
    // any other calls to `mu.Lock()` will block
}
```

Mutexes are powerful. Like most powerful things, they can also cause many bugs if used carelessly.

## Maps Are Not [Thread-Safe](https://en.wikipedia.org/wiki/Thread_safety)

Maps are **not** safe for concurrent use! If you have multiple goroutines accessing the same map, and at least one of them is writing to the map, you must [lock](https://en.wikipedia.org/wiki/Readers%E2%80%93writer_lock) your maps with a mutex.

## Assignment

We send emails across many different goroutines at Textio. To keep track of how many we've sent to a given email address, we use an in-memory map.

Our `safeCounter` struct is unsafe! Update the `inc()` and `val()` methods so that they utilize the safeCounter's mutex to ensure that the map is not accessed by multiple goroutines at the same time.

## Note: Wasm Is Single-Threaded

Now, it's worth pointing out that our execution engine on Boot.dev uses web assembly to run the code you write in your browser. Web assembly is [single-threaded](https://en.wikipedia.org/wiki/Thread_\(computing\)), which awkwardly means that maps _are_ thread-safe in web assembly. I've _simulated_ a [multi-threaded](https://en.wikipedia.org/wiki/Multithreading_\(computer_architecture\)) environment with the `slowIncrement` and `slowVal` functions.

In reality, any Go code you write _may or may not_ run on a single-core machine, so it's always best to write your code so that it is safe no matter which hardware it runs on.

# Mutex Review

The principal problem that mutexes help us avoid is the _concurrent read/write problem_. This problem arises when one thread is writing to a variable while another thread is reading from that same variable _at the same time_.

When this happens, a Go program will panic because the reader could be reading bad data while it's being mutated in place.

![](https://storage.googleapis.com/qvault-webapp-dynamic-assets/course_assets/bwBI11n-700x235.png)

## Mutex Example

```go
package main

import (
	"fmt"
)

func main() {
	m := map[int]int{}
	go writeLoop(m)
	go readLoop(m)

	// stop program from exiting, must be killed
	block := make(chan struct{})
	<-block
}

func writeLoop(m map[int]int) {
	for {
		for i := 0; i < 100; i++ {
			m[i] = i
		}
	}
}

func readLoop(m map[int]int) {
	for {
		for k, v := range m {
			fmt.Println(k, "-", v)
		}
	}
}
```

The example above creates a map, then starts two goroutines which each have access to the map. One goroutine continuously mutates the values stored in the map, while the other prints the values it finds in the map.

If we run the program on a multi-core machine, we get the following output: `fatal error: concurrent map iteration and map write`

In Go, it isn't safe to read from and write to a map at the same time.

## Mutexes to the Rescue

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	m := map[int]int{}

	mu := &sync.Mutex{}

	go writeLoop(m, mu)
	go readLoop(m, mu)

	// stop program from exiting, must be killed
	block := make(chan struct{})
	<-block
}

func writeLoop(m map[int]int, mu *sync.Mutex) {
	for {
		for i := 0; i < 100; i++ {
			mu.Lock()
			m[i] = i
			mu.Unlock()
		}
	}
}

func readLoop(m map[int]int, mu *sync.Mutex) {
	for {
		mu.Lock()
		for k, v := range m {
			fmt.Println(k, "-", v)
		}
		mu.Unlock()
	}
}
```

In this example, we added a `sync.Mutex{}` and named it `mu`. In the write loop, the `Lock()` method is called before writing, and then the `Unlock()` is called when we're done. This Lock/Unlock sequence ensures that no other threads can `Lock()` the mutex while _we_ have it locked – any other threads attempting to `Lock()` will block and wait until we `Unlock()`.

In the reader, we `Lock()` before iterating over the map, and likewise `Unlock()` when we're done. Now the threads share the memory safely!

# RW Mutex

The standard library also exposes a [sync.RWMutex](https://golang.org/pkg/sync/#RWMutex)

In addition to these methods:

- [Lock()](https://golang.org/pkg/sync/#Mutex.Lock)
- [Unlock()](https://golang.org/pkg/sync/#Mutex.Unlock)

The `sync.RWMutex` also has these methods for concurrent reads:

- [RLock()](https://golang.org/pkg/sync/#RWMutex.RLock)
- [RUnlock()](https://golang.org/pkg/sync/#RWMutex.RUnlock)

The `sync.RWMutex` improves performance in read-intensive processes. Multiple goroutines can safely read from the map simultaneously, as many `RLock()` calls can occur at the same time. However, only one goroutine can hold a `Lock()`, and during this time, all `RLock()` operations are blocked.

# Read/Write Mutex Review

Maps are safe for concurrent _read_ access, just not concurrent read/write or write/write access. A read/write mutex allows all the readers to access the map at the same time, but a writer will still lock out all other readers and writers.

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	m := map[int]int{}

	mu := &sync.RWMutex{}

	go writeLoop(m, mu)
	go readLoop(m, mu)
	go readLoop(m, mu)
	go readLoop(m, mu)
	go readLoop(m, mu)

	// stop program from exiting, must be killed
	block := make(chan struct{})
	<-block
}

func writeLoop(m map[int]int, mu *sync.RWMutex) {
	for {
		for i := 0; i < 100; i++ {
			mu.Lock()
			m[i] = i
			mu.Unlock()
		}
	}
}

func readLoop(m map[int]int, mu *sync.RWMutex) {
	for {
		mu.RLock()
		for k, v := range m {
			fmt.Println(k, "-", v)
		}
		mu.RUnlock()
	}
}
```

By using a `sync.RWMutex`, our program becomes _more efficient_. We can have as many `readLoop()` threads as we want, while still ensuring that the writers have exclusive access.