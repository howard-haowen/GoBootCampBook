# Tour of Go

\label{cha:tour_of_go}

Go is often referred as a "simple" programming language, **a language that can be
learned in a few hours if you already know another language**.
Go was designed to feel familiar and to stay as simple as possible,
[the entire language specification](http://golang.org/ref/spec) fits
in just a few pages.

Here are the concepts we are going to explore before writing our
first application. To walk through these various concepts, we are
going to use Go's official [Tour of Go](https://tour.golang.org/) web
application.

## Basics [[02_basics]]

\label{sec:basic_concepts}

### Packages, variables, and functions

#### [[package]]

- Every Go program is made up of packages.
- Programs start running in package main.
- By convention, **the package name is the same as the last element of the import path**. For instance, the "math/rand" package comprises files that begin with the statement package rand.

[Package example](https://tour.golang.org/basics/1)

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	fmt.Println("My favorite number is", rand.Intn(10))
}
```

> The imported package `math/rand` is referred to simply as `rand`.

#### [[import]]

- [Imports example](https://tour.golang.org/basics/2)

```go
package main

import (
	"fmt"
	"math"
)

func main() {
	fmt.Printf("Now you have %g problems.\n", math.Sqrt(7))
}
```

> This code groups the imports into a parenthesized, **factored import statement**.

#### Exported names

- After importing a package, you can refer to the names it exports.
- In Go, **a name is exported if it begins with a capital letter**.

    - `Foo` is an exported name, as is `FOO`. The name `foo` is not exported.

- [Exported names example](https://tour.golang.org/basics/3)

```go
package main

import (
	"fmt"
	"math"
)

func main() {
	fmt.Println(math.pi)
}
```

> While `math.pi` will create an error, `math.Pi` will not.

#### Functions, signature, return values, named results

- A function can take zero or more typed arguments.
- **The type comes after the variable name**.
- Functions can be defined to return any number of values.
- **Return values are always typed**.

- [Function example](https://tour.golang.org/basics/4)

```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

> a function that takes two arguments
> The two arguments `x int, y int` can be shortened to `x, y int`

- [Function with multiple results](https://tour.golang.org/basics/6)

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

> a function that returns two values

- [Function with named results](https://tour.golang.org/basics/7)

```go
package main

import "fmt"

func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
```

> A return statement without arguments returns the named return values (i.e. `x `and `y` in this case). This is known as a **naked return**.

##### Resources

- [Go's declaration
    Syntax](http://blog.golang.org/gos-declaration-syntax)

#### Variables

- The [[var]] statement declares a list of variables; as in function argument lists, the type is last.
- A var declaration can include **initializers**, one per variable.
- If an initializer is present, the type can be omitted; the variable will take the type of the initializer.
- Inside a function, the `:=` short assignment statement can be used in place of a var declaration with implicit type.
- Outside a function, every construct begins with a keyword ([[var]],
- [[func]], and so on) and the `:=` construct is not available.

- [Variables example](https://tour.golang.org/basics/8)

```go
package main

import "fmt"

var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

> Output: `0 false false false`
> Initializers of the `bool` and `int` type are `false` and `0` respectively.

- [Initialization of variables](https://tour.golang.org/basics/9)

```go
package main

import "fmt"

var i, j int = 1, 2

func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

> For `i` and `j`, the type is explicitly specified.
> For `c`, `python`, and `java`, the type is inferred.

- [Short variable declaration: `:=`](https://tour.golang.org/basics/10)

```go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

> `{ArgName} := {ArgValue}`is a shorthand for `var {ArgName} {ArgType} = {ArgValue}`.

#### Basic types [[03_types]]

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

- [Basic types example](https://tour.golang.org/basics/11)

```go
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
	fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
	fmt.Printf("Type: %T Value: %v\n", z, z)
}
```

> Output: `Type: bool Value: false Type: uint64 Value: 18446744073709551615 Type: complex128 Value: (2+3i)`

#### Type conversion

- The expression `T(v)` converts the value v to the type T.
- Go assignment between items of different type requires an explicit conversion.

Some numeric conversions:

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

Or, put more simply:

```go
i := 42
f := float64(i)
u := uint(f)
```

#### Constants

- Constants are declared like variables, but with the [[const]] keyword.
- Constants can be character, string, boolean, or numeric values.
- **Constants cannot be declared using the `:=` syntax.**
- An untyped constant takes the type needed by its context.

```go
const Pi = 3.14
const (
        StatusOK                   = 200
        StatusCreated              = 201
        StatusAccepted             = 202
        StatusNonAuthoritativeInfo = 203
        StatusNoContent            = 204
        StatusResetContent         = 205
        StatusPartialContent       = 206
)
```

### Flow control statements: [[for]], [[if]], [[switch]], and [[defer]]

#### [[for]] Loop [[05_control_flow]]

- **Go has only one looping construct, the [[for]] loop**.
- The basic for loop looks as it does in C or Java, except that the ( ) are gone (they are not even optional) and the { } are required.
- There are three components in a [[for]] loop, all seperated by semicolons:
    - the **init statement**: executed before the first iteration
    - the **condition expression**: evaluated before every iteration
    - the **post statement**: executed at the end of every iteration

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```

- As in C or Java, **you can leave the pre and post statements empty**.

```go
sum := 1
for ; sum < 1000; {
    sum += sum
}
```

Or simply

```go
sum := 1
for sum < 1000 {
    sum += sum
}
```

- Without the init and post statement, a [[for]] loop in Go functions just like a `while` loop in Python.
- To create an infinite loop, [[for]] and a pair of braces are all it takes.

```go
for {
  // do something in a loop forever
}
```

#### [[if]] statement

- The [[if]] statement looks as it does in C or Java, except that the `( )` are gone and the `{ }` are required.
- Variables declared by the statement are only in scope until the end of the [[if]].

```go
if answer != 42 {
    return "Wrong answer"
}
```

- Like [[for]], **the [[if]] statement can start with a short statement to execute before the condition**.

```go
if err := foo(); err != nil {
    panic(err)
}
```

- Variables declared inside an [[if]] short statement are also available inside any of the [[else]] blocks.

#### [[switch]] statement

- A [[switch]] statement is a shorter way to write a sequence of `if - else` statements. It runs the first case whose value is equal to the condition expression.
- Keywords:
    - [[switch]]: starts a switch block
    - [[case]]: specifies a condition and an action to be executed when the condition is true, like `if` and `elif` in Python
    - [[default]]: specifies the default condition and an action to be executed when the condition is true, like `else` in Python

```go
package main

import (
 "fmt"
 "runtime"
)

func main() {
 fmt.Print("Go runs on ")
 switch os := runtime.GOOS; os {

 case "darwin":
 fmt.Println("OS X.")

 case "linux":
 fmt.Println("Linux.")

 default:
 // freebsd, openbsd,
 // plan9, windows...
 fmt.Printf("%s.\n", os)
 }
}
```

##### [[switch]] evaluation order

- [[switch]] cases evaluate cases from top to bottom, stopping when a case succeeds.

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("When's Saturday?")
	today := time.Now().Weekday()
	switch time.Saturday {
	case today + 0:
		fmt.Println("Today.")
	case today + 1:
		fmt.Println("Tomorrow.")
	case today + 2:
		fmt.Println("In two days.")
	default:
		fmt.Println("Too far away.")
	}
}
```

> The output when run on a day that's more than 3 days before Saturday:
> `> When's Saturday? Too far away.`

#### [[defer]] statement

- A defer statement defers the execution of a function until the surrounding function returns.

```go
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

> The output is `hello word` rather than `world hello` because of the [[defer]] statement.

##### stacking [[defer]]

- Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

```go
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
```

> The whole [[for]] loop is deferred, resulting `9~0` (in reverse order, due to [[LIFO]]) to be printed after `counting` and `done`.

### More types: structs, slices, and maps

#### Pointers

#### Arrays

#### Slices

#### Maps

#### Structs

#### Initializing

#### Range

#### First class functions, closures

## Methods and interfaces [[06_methods]]

\label{sec:methods_and_interfaces}

### Methods

### Interfaces

### Stringers

### Errors

### Images

## Concurrency [[08_concurrency]]

### Goroutines

### Channels

### Select
