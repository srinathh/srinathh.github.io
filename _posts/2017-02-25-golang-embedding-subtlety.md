---
layout: post
title: "How type embedding intersects with Pointer and Value method receivers in Go" 
categories: [Programming, Go]
permalink: /blog/golang-embedding-subtlety/
author: srinath
image: assets/images/generic/Go-Logo_Blue.png  
---
I recently encountered a subtlety in Go language in how methods exposed by
an embedded type are resolved vs. those with the same name defined by the 
container types depending on whether they are defined on
[Pointer or Value](https://golang.org/doc/effective_go.html#pointers_vs_values) receivers.
A quick trip to the language documentation however helped me understand subtlety and fix
the unexpected result I was getting. It left me with a greater appreciation for how different
features of the Go Language are designed to interact in cleanly defined manners.

Specifically, I wanted to embed [time.Time](https://golang.org/pkg/time/#Time) into a
type called `ISODate` which would have a [json.Marshaler](https://golang.org/pkg/encoding/json/#Marshaler)
and [json.Unmarshaler](https://golang.org/pkg/encoding/json/#Unmarshaler) defined to handle
dates specified in the format `2016-01-02`. As a convenience, I wanted to implement
the [Stringer](https://golang.org/pkg/fmt/#Stringer) interface on `ISODate` to return 
dates in the same format, over-riding how [time.Time](https://golang.org/pkg/time/#Time.String)
handles it. The code snippet below shows the `Stringer` portion. 

```go
type ISODate struct {
	time.Time
}

func (t *ISODate) String() string {
	return t.Format("2006-01-02")
}
```

By of force of habit I had defined the `Stringer` with a pointer receiver. However, when I 
tried to use the `String()` method above, I saw the [time.Time.String](https://golang.org/pkg/time/#Time.String)
formatting of `2006-01-02 15:04:05.999999999 -0700 MST` fomat instead of the `2006-01-02`
fomrat that I wanted. Digging into the problem a bit, I found that calling `String()` on
`&ISODate` produced the correct formatting. Clearly I was missing something in my understanding
of Pointer and Value receivers for methods.

```go
package main

import (
	"fmt"
	"time"
)

type ISODate struct {
	time.Time
}

func (t *ISODate) String() string {
	return t.Format("2006-01-02")
}

func main() {
	t := ISODate{time.Date(2006, 1, 2, 3, 4, 5, 7, time.UTC)}
	fmt.Println(t)
	fmt.Println(&t)
}

Output:
{2006-01-02 03:04:05.000000007 +0000 UTC}
2006-01-02
```


Two of the real gems in the Go language documentation are the [Language Specification](https://golang.org/ref/spec)
which is remarkably compact and readable for a technical document and the [Effective Go](https://golang.org/doc/effective_go.html)
article which contains somewhat friendlier explanations of the concepts.

Sure enought, in Effective Go's section on [Pointer vs Value receivers](https://golang.org/doc/effective_go.html#pointers_vs_values),
I found the following explanation:

> The rule about pointers vs. values for receivers is that value methods can be invoked on 
> pointers and values, but pointer methods can only be invoked on pointers.

I had defined my `ISODate.String()` method with a pointer receiver in the code above and so clearly the
second `fmt.Println(&t)` called it as expected. However, where did the first `fmt.Println(t)` output
come from?

Looking at the code for [time.Time.String()](https://golang.org/src/time/format.go?s=13317:13346#L417)
we see that the `String()` function is defined on a value receiver. Since `time.Time` is embedded
in `ISODate` and its methods are therefore promoted, it turns out that `ISODate` in this case 
had two `String()` methods - one with a pointer receiver and the other with a value receiver.

```go
// String returns the time formatted using the format string
//	"2006-01-02 15:04:05.999999999 -0700 MST"
func (t Time) String() string {
    return t.Format("2006-01-02 15:04:05.999999999 -0700 MST")
}
```

The fix was simply to define the `String()` method on a value receiver 
so that the underlying `String()` of the `time.Time` could be overridden.

```go
package main

import (
	"fmt"
	"time"
)

type ISODate struct {
	time.Time
}

func (t ISODate) String() string {
	return t.Format("2006-01-02")
}

func main() {
	t := ISODate{time.Date(2006, 1, 2, 3, 4, 5, 7, time.UTC)}
	fmt.Println(t)
	fmt.Println(&t)
}

Output:
2006-01-02
2006-01-02

```