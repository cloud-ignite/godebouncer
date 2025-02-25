# Go Debouncer

[![Go Reference](https://pkg.go.dev/badge/github.com/vnteamopen/godebouncer.svg)](https://pkg.go.dev/github.com/vnteamopen/godebouncer) [![build](https://github.com/vnteamopen/godebouncer/actions/workflows/build.yml/badge.svg?branch=main)](https://github.com/vnteamopen/godebouncer/actions/workflows/build.yml) [![Go Report Card](https://goreportcard.com/badge/github.com/vnteamopen/godebouncer)](https://goreportcard.com/report/github.com/vnteamopen/godebouncer)
[![Built with WeBuild](https://raw.githubusercontent.com/webuild-community/badge/master/svg/WeBuild.svg)](https://webuild.community) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/vnteamopen/godebouncer/blob/main/LICENSE)

Go Debouncer is a Go language library. It makes sure that the pre-defined function is only triggered once per client's signals during a fixed duration.

It allows creating a debouncer that delays invoking a triggered function until after the duration has elapsed since the last time the SendSingal was invoked.

- Official page: https://godebouncer.vnteamopen.com
- A product from https://vnteamopen.com

![GoDebouncer_drawio](https://user-images.githubusercontent.com/1828895/164943072-093b22e6-6471-4d2e-93bb-8fd08f2e4953.png)

# Quickstart

Import library to your project

```bash
go get -u github.com/vnteamopen/godebouncer
```

From your code, you can try to create debouncer.

```go
package main

import (
	"fmt"
	"time"

	"github.com/vnteamopen/godebouncer"
)

func main() {
	debouncer := godebouncer.New(5 * time.Second).WithTriggered(func() {
		fmt.Println("Trigger") // Triggered func will be called after 5 seconds from last SendSignal().
	})

	fmt.Println("Action 1")
	debouncer.SendSignal()

	time.Sleep(1 * time.Second)

	fmt.Println("Action 2")
	debouncer.SendSignal()

	// After 5 seconds, the trigger will be called.
	// Previous `SendSignal()` will be ignored to trigger the triggered function.
	<-debouncer.Done()
}
```

# Anything else?

## Do

Allows defining actions before calling SendSignal(). They are synchronous.

```go
debouncer := godebouncer.New(10 * time.Second).WithTriggered(func() {
	fmt.Println("Trigger") // Triggered func will be called after 10 seconds from last SendSignal().
})

debouncer.Do(func() {
	fmt.Println("Action 1")
})
// Debouncer run the argument function of Do() then SendSignal(). They run sequentially.
// After 10 seconds from finishing Do(), the triggered function will be called.
```

## Cancel

Allows cancelling the timer from the last function SendSignal(). The scheduled triggered function is cancelled and doesn't invoke.

```go
debouncer := godebouncer.New(10 * time.Second).WithTriggered(func() {
	fmt.Println("Trigger") // Triggered func will be called after 10 seconds from last SendSignal().
})

debouncer.SendSignal()
debouncer.Cancel() // No triggered function is called
```

## Update triggered function

Allows replacing triggered function.

```go
debouncer := godebouncer.New(10 * time.Second).WithTriggered(func() {
	fmt.Println("Trigger 1") // Triggered func will be called after 10 seconds from last SendSignal().
})

debouncer.SendSignal()
debouncer.UpdateTriggeredFunc(func() {
	fmt.Println("Trigger 2")
})

// Output: "Trigger 2" after 10 seconds
```

## Update waiting time duration

Allows replacing the waiting time duration. You need to call a SendSignal() again to trigger a new timer with a new waiting time duration.

```go
debouncer := godebouncer.New(10 * time.Second).WithTriggered(func() {
	fmt.Println("Trigger") // Triggered func will be called after 10 seconds from last SendSignal().
})

debouncer.UpdateTimeDuration(20 * time.Millisecond)
debouncer.SendSignal()
// Output: "Trigger" after 20 seconds
```

## Let the caller knows when the triggered function has been invoked

Allows the caller of godebouncer knows when the triggered function is done invoking to synchronize execution across goroutines.

```go
debouncer := godebouncer.New(1 * time.Second).WithTriggered(func() {
	fmt.Println("Fetching...")
	time.Sleep(2 * time.Second)
	fmt.Println("Done")
})

debouncer.SendSignal()

<-debouncer.Done() // The current goroutine will wait until the triggered func finish its execution.

fmt.Println("After done")
```

## Pass any to your function

```go
package main

import (
	"fmt"
	"time"

	"github.com/DanielRenne/GoCore/core/debouncer"
)

func main() {
	type myStruct struct {
		Boolean bool
		Integer int
		String string
	}
	d := debouncer.New(5 * time.Second).WithAny(func(myData any) {
		fmt.Printf("%#v", myData) // Triggered func will be called after 5 seconds from last SendSignal().
	})

	fmt.Println("Action 1")
	d.SendSignalWithData(&myStruct{
		Boolean: false,
		Integer: 5,
		String: "Will not show",
	})

	time.Sleep(1 * time.Second)

	fmt.Println("Action 2")
	d.SendSignalWithData(&myStruct{
		Boolean: true,
		Integer: 5,
		String: "Will show because last one in wins!",
	})

	// After 5 seconds, the trigger will be called.
	// Previous `SendSignal()` will be ignored to trigger the triggered function.
	<-d.Done()
}
```

# License

MIT

# Contribution

All your contributions to project and make it better, they are welcome. Feel free to start an [issue](https://github.com/vnteamopen/godebouncer/issues).

Core contributors:

- https://github.com/huyvohcmc
- https://github.com/rnvo
- https://github.com/ledongthuc

# Thanks! 🙌

- Viet Nam We Build group https://webuild.community for discussion.

[![Stargazers repo roster for @vnteamopen/godebouncer](https://reporoster.com/stars/vnteamopen/godebouncer)](https://github.com/vnteamopen/godebouncer/stargazers)
