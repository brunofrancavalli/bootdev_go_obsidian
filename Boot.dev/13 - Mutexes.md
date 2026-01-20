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

Boots

Spellbook

Lessons

![Boots](https://www.boot.dev/_nuxt/new_boots_profile.DriFHGho.webp)

**Need help?** I, Boots the Gormless Glutton, can assist... _for a price_.

Start Voice Chat

- main.go

- main_test.go

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

package main

  

import (

"fmt"

"sync"

"testing"

)

  

func Test(t *testing.T) {

type testCase struct {

email string

count int

}

  

runCases := []testCase{

{"norman@bates.com", 23},

{"marion@bates.com", 67},

}

  

submitCases := append(runCases, []testCase{

{"lila@bates.com", 31},

{"sam@bates.com", 453},

}...)

  

testCases := runCases

if withSubmit {

testCases = submitCases

}

  

skipped := len(submitCases) - len(testCases)

  

passCount := 0

failCount := 0

  

for _, test := range testCases {

sc := safeCounter{

  

Submit

Run

Solution