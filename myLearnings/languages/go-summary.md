# Go Language Summary

## Variables and Declaration

### Basic Declaration Syntax

Go offers multiple ways to declare variables:

```go
// Method 1: Full declaration with type and value
var a int = 5

// Method 2: Type inference with var
var a = 5

// Method 3: Short declaration (most common, inside functions only)
a := 5

// Multiple declarations with same type
var c, python, java bool

// Multiple declarations with values
var i, j int = 1, 2

// Different types in one line (type inference)
var c, python, java = true, false, "no!"
c, python, java := true, false, "no!"
```

### Variable Blocks

```go
var (
    ToBe   bool       = false
    MaxInt uint64     = 1<<64 - 1
    z      complex128 = cmplx.Sqrt(-5 + 12i)
)
```

### Important Rules
- **`:=` declares AND assigns** - only for new variables
- **`:=` only works inside functions** - use `var` at package level
- **Unused variables = compile error** - Go enforces clean code
- **No implicit type conversion** - must explicitly convert

---

## Types

### Built-in Types

```go
bool
string
int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr
byte // alias for uint8
rune // alias for int32 (Unicode code point)
float32 float64
complex64 complex128
```

### Type Definitions

```go
// Type alias - same underlying type
type MyInt = int  // MyInt IS int

// New type - different type (requires conversion)
type MyInt int    // MyInt is NOT int
```

### Zero Values (Defaults)

All types have safe default values when declared without initialization:

```go
var i int      // 0
var f float64  // 0.0
var p bool     // false
var s string   // "" (empty string, NOT nil!)
```

### Type Conversions

Go requires explicit type conversion between different types:

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)

// Simpler syntax
i := 42
f := float64(i)
u := uint(f)
```

**Unlike C**: Go requires explicit conversion - no implicit conversion between types.

### Type Inference

When declaring without explicit type, the variable's type is inferred:

```go
var i int
j := i // j is an int

// Untyped numeric constants infer type based on precision
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

---

## Functions

### Function Declaration

```go
// Basic function with parameters
func add(x int, y int) int {
    return x + y
}

// Consecutive parameters of same type - shorthand
func add(x, y int) int {
    return x + y
}
```

### Multiple Return Values

Functions can return multiple values (NOT tuples):

```go
func add(x, y int) (int, int) {
    return x + y, x
}

// Must handle all return values
a, b := add(5, 3)

// Use _ to ignore values
a, _ := add(5, 3)
_, b := add(5, 3)

// ERROR: x := add(5, 3) - must handle all returns
```

### Named Return Values

Functions can name return values for cleaner code:

```go
func add(x, y int) (r1, r2 int) {
    r1 = x + y
    r2 = x
    return // naked return - returns r1 and r2
}
```

**Important**: Named returns are initialized to zero values.

---

## Constants

### Declaring Constants

```go
const Pi = 3.14
const (
    StatusOK = 200
    StatusNotFound = 404
)
```

### Rules
- **Cannot use `:=` syntax** - must use `const` keyword
- **Can be**: character, string, boolean, or numeric values
- **Must be compile-time values** - cannot be runtime expressions

### Numeric Constants - Special Behavior ⚠️

**CRITICAL**: Numeric constants are high-precision and context-aware:

```go
const (
    // Create a huge number by shifting a 1 bit left 100 places
    Big = 1 << 100
    // Shift it right again 99 places, so we end up with 1<<1, or 2
    Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 { return x * 0.1 }

func main() {
    fmt.Println(needInt(Small))    // Works! 21
    fmt.Println(needFloat(Small))  // Works! 0.2
    fmt.Println(needFloat(Big))    // Works! Huge float
    // fmt.Println(needInt(Big))   // ERROR: overflows int
}
```

**Why this matters**:
- Untyped constants take the type needed by their context
- This is DANGEROUS with variables (runtime overflow)
- An `int` can store max 64-bit integer, constants exceed this at compile-time

**Rule**: Prefer constants for large numbers used in different contexts.

---

## Printing and Formatting

### Common Print Functions

```go
fmt.Print("hello")    // Print without newline
fmt.Println("hello")  // Print with newline
fmt.Printf("hello %s\n", "world") // Print with format
s := fmt.Sprintf("hello %s", "world") // Returns formatted string
```

### Format Verbs with Examples

```go
// General format verbs
fmt.Printf("%v", 42)           // Output: 42 (default format)
fmt.Printf("%T", 42)           // Output: int (type)
fmt.Printf("%%")               // Output: % (literal percent)

// Boolean
fmt.Printf("%t", true)         // Output: true

// Integer formats
fmt.Printf("%d", 42)           // Output: 42 (decimal)
fmt.Printf("%b", 42)           // Output: 101010 (binary)
fmt.Printf("%o", 42)           // Output: 52 (octal)
fmt.Printf("%x", 255)          // Output: ff (hex lowercase)
fmt.Printf("%X", 255)          // Output: FF (hex uppercase)
fmt.Printf("%c", 65)           // Output: A (character from Unicode)

// Float formats
fmt.Printf("%f", 3.14159)      // Output: 3.141590 (decimal)
fmt.Printf("%.2f", 3.14159)    // Output: 3.14 (2 decimal places)
fmt.Printf("%e", 123.456)      // Output: 1.234560e+02 (scientific)
fmt.Printf("%E", 123.456)      // Output: 1.234560E+02 (scientific uppercase)

// String formats
fmt.Printf("%s", "hello")      // Output: hello (plain string)
fmt.Printf("%q", "hello")      // Output: "hello" (quoted string)

// Pointer
x := 42
fmt.Printf("%p", &x)           // Output: 0xc000012345 (memory address)

// Width and precision
fmt.Printf("%5d", 42)          // Output:    42 (width 5, right-aligned)
fmt.Printf("%-5d", 42)         // Output: 42    (width 5, left-aligned)
fmt.Printf("%05d", 42)         // Output: 00042 (zero-padded)
fmt.Printf("%5.2f", 3.14159)   // Output:  3.14 (width 5, 2 decimals)

// Struct formatting
type Person struct {
    Name string
    Age  int
}
p := Person{"Alice", 30}
fmt.Printf("%v", p)            // Output: {Alice 30}
fmt.Printf("%+v", p)           // Output: {Name:Alice Age:30} (with field names)
fmt.Printf("%#v", p)           // Output: main.Person{Name:"Alice", Age:30} (Go syntax)
```

---

## Flow Control

### For Loop

Go has **only one looping construct**: the `for` loop.

#### Standard For Loop

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

**Three components** (all optional):
1. **init statement** - executed before first iteration
2. **condition** - evaluated before every iteration
3. **post statement** - executed at end of every iteration

**Note**: No parentheses `()` around components, braces `{}` are always required.

#### For as While

```go
sum := 1
for sum < 1000 {
    sum += sum
}
```

#### Infinite Loop

```go
for {
    // infinite loop
    // use break to exit
}
```

#### Do-While Pattern

```go
for {
    // ... do something
    if condition {
        break
    }
}
```

#### For Range (Foreach)

```go
numbers := []int{1, 2, 3, 4, 5}

// With index and value
for index, value := range numbers {
    fmt.Println(index, value)
}

// Value only (ignore index)
for _, v := range numbers {
    fmt.Println(v)
}

// Index only
for i := range numbers {
    fmt.Println(i)
}
```

**Important**: Can use three syntaxes:
- `for init; condition; post {}` - standard
- `for condition {}` - while-like
- `for {}` - infinite

**Cannot mix**: You can't use two components like `for init; condition {}` - must use all three or just condition!

---

### If Statements

#### Basic If

```go
if x < 0 {
    return -x
}
```

**Note**: No parentheses `()` around condition, braces `{}` are required.

#### If with Short Statement

Variables can be declared in the if statement:

```go
func pow(x, n, lim float64) float64 {
    if v := math.Pow(x, n); v < lim {
        return v
    }
    return lim
}
```

**Scope**: Variables declared in the `if` are only available until the end of the `if` (including `else`).

#### If-Else

```go
func pow(x, n, lim float64) float64 {
    if v := math.Pow(x, n); v < lim {
        return v
    } else {
        // v is available in else block too
        fmt.Printf("%g >= %g\n", v, lim)
    }
    // v is NOT available here
    return lim
}
```

---

### Switch

#### Basic Switch

```go
switch os := runtime.GOOS; os {
case "darwin":
    fmt.Println("OS X.")
case "linux":
    fmt.Println("Linux.")
default:
    fmt.Printf("%s.\n", os)
}
```

**Key differences from C/Java/JavaScript**:
- **Automatic break** - only runs selected case, not all following cases
- **Cases need not be constants** - can be any expression
- **Values need not be integers** - any comparable type works
- **Can have init statement** - like `if` and `for`

#### Switch with No Condition

Switch without condition is same as `switch true`:

```go
t := time.Now()
switch {
case t.Hour() < 12:
    fmt.Println("Good morning!")
case t.Hour() < 17:
    fmt.Println("Good afternoon.")
default:
    fmt.Println("Good evening.")
}
```

**Use case**: Clean way to write long if-then-else chains.

---

### Defer

#### What is Defer?

Defers execution of a function until the surrounding function returns.

```go
func main() {
    defer fmt.Println("world")
    fmt.Println("hello")
}
// Output:
// hello
// world
```

#### Defer Behavior

- **Arguments evaluated immediately** - but function call waits
- **Execution order: LIFO** (Last-In-First-Out) - deferred calls pushed onto stack

```go
func main() {
    for i := 0; i < 5; i++ {
        defer fmt.Println(i)
    }
}
// Output:
// 4
// 3
// 2
// 1
// 0
```

**Common use**: Cleanup operations (close files, unlock mutexes, etc.)

---

## Pointers

### Pointer Basics

Go has pointers but **no pointer arithmetic** (unlike C).

```go
var p *int  // pointer to an int, zero value is nil

i := 42
p = &i      // & generates a pointer to operand

fmt.Println(*p)  // * denotes pointer's underlying value (dereference)
*p = 21          // set value through pointer
fmt.Println(i)   // i is now 21
```

### Pointer Operations

```go
i, j := 42, 2701

p := &i         // point to i
fmt.Println(*p) // read i through the pointer (42)
*p = 21         // set i through the pointer
fmt.Println(i)  // see the new value of i (21)

p = &j          // point to j
*p = *p / 37    // divide j through the pointer
fmt.Println(j)  // see the new value of j (73)
```

**Key points**:
- `*T` is a pointer to type T
- `&` operator generates pointer to operand
- `*` operator dereferences pointer (accesses underlying value)
- Zero value is `nil`
- **No pointer arithmetic** - safer than C

---

## Structs

### Defining and Using Structs

```go
type Vertex struct {
    X int
    Y int
}

// Create struct instance
v := Vertex{1, 2}
fmt.Println(v)  // {1 2}

// Access fields with dot notation
v.X = 4
fmt.Println(v.X)  // 4
```

### Struct Pointers

```go
v := Vertex{1, 2}
p := &v

// Access via pointer - no need to dereference!
p.X = 1e9  // same as (*p).X = 1e9

fmt.Println(v)  // {1000000000 2}
```

**Important**: Go allows `p.X` instead of `(*p).X` for convenience.

### Struct Literals

```go
var (
    v1 = Vertex{1, 2}   // has type Vertex
    v2 = Vertex{X: 1}   // Y:0 is implicit (zero value)
    v3 = Vertex{}       // X:0 and Y:0 (all zero values)
    p  = &Vertex{1, 2}  // has type *Vertex (pointer)
)
```

**Named field syntax**: Can list subset of fields, order doesn't matter.

### Returning Structs vs Pointers from Functions

**Critical**: When returning structs from functions, Go makes a **copy**. For large structs, this is expensive.

```go
type BigStruct struct {
    Data [1000]int
    Name string
}

// BAD: Returns copy of entire struct (expensive!)
func getBigStruct() BigStruct {
    return BigStruct{Name: "test"}
}

// GOOD: Returns pointer (only copies pointer address)
func getBigStructPtr() *BigStruct {
    return &BigStruct{Name: "test"}
}

func main() {
    // Copy entire struct (4000+ bytes)
    s1 := getBigStruct()

    // Copy only pointer (8 bytes on 64-bit)
    s2 := getBigStructPtr()
}
```

**Best practices**:
- **Small structs** (few fields, no arrays) - return by value
- **Large structs** - return pointer to avoid expensive copy
- **Need to modify** - return pointer
- **Immutability desired** - return by value

```go
// Small struct - OK to return by value
type Point struct {
    X, Y int
}

func newPoint(x, y int) Point {
    return Point{X: x, Y: y}
}

// Large struct - return pointer
type User struct {
    ID       int
    Name     string
    Email    string
    Settings map[string]interface{}
    Data     []byte
}

func newUser(id int, name string) *User {
    return &User{
        ID:   id,
        Name: name,
    }
}
```

---

## Arrays

### Array Basics

Arrays have **fixed size** - size is part of the type.

```go
var a [2]string
a[0] = "Hello"
a[1] = "World"
fmt.Println(a[0], a[1])  // Hello World
fmt.Println(a)           // [Hello World]

// Array literal
primes := [6]int{2, 3, 5, 7, 11, 13}
fmt.Println(primes)
```

### Arrays in Functions

```go
func SumArray(a [4]int) int {
    sum := 0
    for _, v := range a {
        sum += v
    }
    return sum
}
```

**Critical**: `[4]int` and `[5]int` are **different types**! Size is part of array type.

---

## Slices

### What are Slices?

**Slices are dynamically-sized, flexible views into arrays**. Much more common than arrays in Go.

```go
// Slice type: []T (no size specified)
var s []int  // slice of ints
```

### Creating Slices from Arrays

```go
primes := [6]int{2, 3, 5, 7, 11, 13}

var s []int = primes[1:4]  // [3 5 7]
fmt.Println(s)
```

**Slice syntax**: `a[low:high]` - includes first element, excludes last.

```go
a[1:4]  // elements 1, 2, 3
a[:3]   // elements 0, 1, 2
a[2:]   // elements 2 to end
a[:]    // all elements
```

### Slices are References

**Critical**: Slices don't store data - they describe a section of an underlying array.

```go
names := [4]string{"John", "Paul", "George", "Ringo"}

a := names[0:2]  // [John Paul]
b := names[1:3]  // [Paul George]

// Changing slice changes underlying array
b[0] = "XXX"

fmt.Println(a)     // [John XXX]
fmt.Println(b)     // [XXX George]
fmt.Println(names) // [John XXX George Ringo]
```

### Slice Literals

```go
// Creates array, then builds slice referencing it
s := []int{2, 3, 5, 7, 11, 13}

// Slice of structs
s := []struct {
    i int
    b bool
}{
    {2, true},
    {3, false},
    {5, true},
}
```

### Length and Capacity

```go
s := []int{2, 3, 5, 7, 11, 13}

len(s)  // length: number of elements in slice
cap(s)  // capacity: number of elements in underlying array from first element
```

```go
s := []int{2, 3, 5, 7, 11, 13}
// len=6 cap=6

s = s[:0]
// len=0 cap=6 (capacity unchanged)

s = s[:4]
// len=4 cap=6

s = s[2:]
// len=2 cap=4 (capacity reduced - starts from different point)
```

### Nil Slices

```go
var s []int
fmt.Println(s, len(s), cap(s))  // [] 0 0

if s == nil {
    fmt.Println("nil!")  // prints this
}
```

**Zero value**: nil slice has length 0, capacity 0, no underlying array.

### Creating Slices with make

```go
// make([]T, length, capacity)
a := make([]int, 5)      // len=5 cap=5 [0 0 0 0 0]
b := make([]int, 0, 5)   // len=0 cap=5 []

// Slices can contain any type, including slices
board := [][]string{
    []string{"_", "_", "_"},
    []string{"_", "_", "_"},
    []string{"_", "_", "_"},
}
```

### Appending to Slices

```go
func append(s []T, vs ...T) []T
```

```go
var s []int
s = append(s, 0)        // [0]
s = append(s, 1)        // [0 1]
s = append(s, 2, 3, 4)  // [0 1 2 3 4]
```

**Important**:
- If underlying array is too small, a new larger array is allocated
- Append returns the new slice
- Original slice is unchanged

### Slice Dangers ⚠️

```go
// DANGER 1: Modifying slice modifies underlying array
s := []int{1, 2, 3}
s[0] = 999  // changes underlying array!

// DANGER 2: Append may reallocate
// Check capacity before appending: if cap(s) == len(s), append allocates new array

// DANGER 3: Out of bounds panic
s := make([]int, 5, 10)
// s[10] = 1  // panic: index out of range
```

---

## Range

### Range over Slices

```go
pow := []int{1, 2, 4, 8, 16, 32, 64, 128}

// Index and value
for i, v := range pow {
    fmt.Printf("2**%d = %d\n", i, v)
}

// Index only (omit second variable)
for i := range pow {
    fmt.Println(i)
}

// Value only (use _ for index)
for _, value := range pow {
    fmt.Println(value)
}
```

**Returns**: Two values - index and **copy** of element.

### Range Copies Elements ⚠️

**Critical**: Range always transfers a **copy** of elements!

```go
type Big struct {
    Arr [1000]int
}

bigArr := []Big{{}, {}}

// BAD: Copies entire struct on each iteration (expensive!)
for _, b := range bigArr {
    b.Arr[0] = 42  // modifies COPY, not original!
}

// GOOD: Use index to avoid copies
for i := range bigArr {
    bigArr[i].Arr[0] = 42  // modifies original
}

// GOOD: Use pointer slice
bigArr := []*Big{{}, {}}
for _, b := range bigArr {
    b.Arr[0] = 42  // modifies original through pointer
}
```

---

## Maps

### Map Basics

```go
// Declare map type
var m map[string]int  // zero value is nil

// Create with make
m = make(map[string]int)

// Map literal
m := map[string]int{
    "Alice": 25,
    "Bob":   30,
}
```

### Maps with Struct Values

```go
type Vertex struct {
    Lat, Long float64
}

// Declare and create
var m map[string]Vertex
m = make(map[string]Vertex)

m["Bell Labs"] = Vertex{40.68433, -74.39967}

// Map literal
var m = map[string]Vertex{
    "Bell Labs": {40.68433, -74.39967},  // type name can be omitted
    "Google":    {37.42202, -122.08408},
}
```

### Map Operations

```go
m := make(map[string]int)

// Insert or update
m["Answer"] = 42

// Retrieve
v := m["Answer"]
fmt.Println(v)  // 42

// Delete
delete(m, "Answer")

// Test if key exists (two-value assignment)
v, ok := m["Answer"]
if ok {
    fmt.Println("Key exists:", v)
} else {
    fmt.Println("Key does not exist")
}

// Short form
if v, ok := m["Answer"]; ok {
    fmt.Println("Value:", v)
}
```

**Important**:
- Zero value of map is `nil`
- Nil map has no keys and **cannot add keys**
- `make` returns initialized map ready for use
- If key not in map, returns zero value for element type

---

## Function Values and Closures

### Functions as Values

Functions are first-class values - can be passed around like any other value.

```go
func compute(fn func(float64, float64) float64) float64 {
    return fn(3, 4)
}

func main() {
    hypot := func(x, y float64) float64 {
        return math.Sqrt(x*x + y*y)
    }

    fmt.Println(hypot(5, 12))        // Output: 13
    fmt.Println(compute(hypot))      // Output: 5
    fmt.Println(compute(math.Pow))   // Output: 81
}
```

### Function Closures

A closure is a function that references variables from outside its body.

```go
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}

func main() {
    pos, neg := adder(), adder()
    for i := 0; i < 10; i++ {
        fmt.Println(
            pos(i),      // 0, 1, 3, 6, 10, 15, 21, 28, 36, 45
            neg(-2*i),   // 0, -2, -6, -12, -20, -30, -42, -56, -72, -90
        )
    }
}
```

**Key point**: Each closure is bound to its own `sum` variable.

---

## Methods

### Defining Methods

Go doesn't have classes, but you can define methods on types.

```go
type Vertex struct {
    X, Y float64
}

// Method with receiver
func (v Vertex) Abs() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
    v := Vertex{3, 4}
    fmt.Println(v.Abs())  // Output: 5
}
```

**Syntax**: Method has a receiver argument between `func` and method name.

### Methods on Non-Struct Types

You can declare methods on any type defined in your package.

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
    if f < 0 {
        return float64(-f)
    }
    return float64(f)
}

func main() {
    f := MyFloat(-math.Sqrt2)
    fmt.Println(f.Abs())  // Output: 1.4142135623730951
}
```

**Restriction**: Can only declare method on types defined in same package (not built-in types from other packages).

### Pointer Receivers vs Value Receivers

```go
type Vertex struct {
    X, Y float64
}

// Value receiver - receives a copy
func (v Vertex) Abs() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

// Pointer receiver - can modify original
func (v *Vertex) Scale(f float64) {
    v.X = v.X * f
    v.Y = v.Y * f
}

func main() {
    v := Vertex{3, 4}
    v.Scale(10)
    fmt.Println(v.Abs())  // Output: 50
}
```

**When to use pointer receivers**:
- Need to modify the receiver
- Avoid copying large structs (performance) - same reason to return pointers from functions
- Consistency (if some methods have pointer receivers, use them for all)

**When to use value receivers**:
- Small structs
- Immutable operations
- Value semantics desired

**Important**: Value receivers and returning by value both create copies. For large structs, use pointers to avoid expensive copying.

### Automatic Pointer/Value Conversion

Go automatically converts between values and pointers for method calls:

```go
v := Vertex{3, 4}
v.Scale(5)      // Go interprets as (&v).Scale(5)

p := &Vertex{3, 4}
p.Abs()         // Go interprets as (*p).Abs()
```

**Important**: This convenience only works for method calls, not for functions!

---

## Interfaces

### Defining Interfaces

An interface is a set of method signatures.

```go
type Abser interface {
    Abs() float64
}
```

### Implicit Implementation

A type implements an interface by implementing its methods. **No explicit declaration needed**.

```go
type Vertex struct {
    X, Y float64
}

func (v Vertex) Abs() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
    if f < 0 {
        return float64(-f)
    }
    return float64(f)
}

func main() {
    var a Abser

    v := Vertex{3, 4}
    a = v  // Vertex implements Abser
    fmt.Println(a.Abs())  // Output: 5

    f := MyFloat(-math.Sqrt2)
    a = f  // MyFloat implements Abser
    fmt.Println(a.Abs())  // Output: 1.414...
}
```

### Interface Values

Interface values hold a tuple: `(value, type)`

```go
var a Abser
v := Vertex{3, 4}
a = v
// a holds (Vertex{3, 4}, Vertex)
```

### Nil Interface Values

```go
var a Abser
// a is (nil, nil)
// Calling a.Abs() causes runtime error - no type to indicate which method to call
```

### Interface with Nil Concrete Value

```go
type I interface {
    M()
}

type T struct {
    S string
}

func (t *T) M() {
    if t == nil {
        fmt.Println("<nil>")
        return
    }
    fmt.Println(t.S)
}

func main() {
    var i I
    var t *T
    i = t  // i is (*T, nil) - interface is NOT nil!
    i.M()  // Output: <nil> - method called with nil receiver
}
```

**Critical**: Interface holding nil concrete value is **not nil interface**.

### Empty Interface

The empty interface specifies zero methods: `interface{}`

```go
var i interface{}

i = 42
fmt.Printf("(%v, %T)\n", i, i)  // Output: (42, int)

i = "hello"
fmt.Printf("(%v, %T)\n", i, i)  // Output: (hello, string)
```

**Modern Go**: `any` is an alias for `interface{}`

```go
func PrintAnything(v any) {
    fmt.Println(v)
}
```

**Use case**: Handling values of unknown type (e.g., `fmt.Print`).

### Pointer vs Value Receivers in Interfaces

```go
type I interface {
    M()
}

type T struct{}

func (t *T) M() {}  // Method on *T

func main() {
    var i I

    t := T{}
    // i = t  // ERROR: T doesn't implement I (only *T does)
    i = &t    // OK: *T implements I
}
```

**Rule**: If method defined on pointer receiver `(*T)`, only `*T` implements interface.

### Important Interface Rules

- **Implicit implementation** - no "implements" keyword
- **Any type can implement** - not just structs
- **Empty interface** (`any`) holds any type
- **Nil concrete value ≠ nil interface** - `(nil, *T)` is not nil
- **Pointer receivers** - if method on `*T`, only `*T` implements interface
- **Polymorphism** - write functions that work with any type satisfying interface

---

## Type Assertions

### Basic Type Assertion

Access an interface value's underlying concrete value:

```go
var i interface{} = "hello"

s := i.(string)
fmt.Println(s)  // Output: hello

// If wrong type, triggers panic
// f := i.(float64)  // panic: interface conversion
```

### Testing Type Assertion

```go
var i interface{} = "hello"

s, ok := i.(string)
fmt.Println(s, ok)  // Output: hello true

f, ok := i.(float64)
fmt.Println(f, ok)  // Output: 0 false (no panic)
```

**Pattern**: Similar to map key existence check.

---

## Type Switches

A type switch compares types, not values.

```go
func do(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("Int: %v\n", v)
    case string:
        fmt.Printf("String: %q\n", v)
    case bool:
        fmt.Printf("Bool: %v\n", v)
    default:
        fmt.Printf("Unknown type: %T\n", v)
    }
}

func main() {
    do(21)        // Output: Int: 21
    do("hello")   // Output: String: "hello"
    do(true)      // Output: Bool: true
    do(3.14)      // Output: Unknown type: float64
}
```

**Syntax**: `i.(type)` replaces specific type with keyword `type`.

**Note**: `nil` values are handled in the `default` case. If there's no `default` case, nothing happens for `nil`.

---

## Error Handling

### The Error Interface

```go
type error interface {
    Error() string
}
```

**Any type that implements `Error() string` is a valid error.**

**Convention**: `nil` error = success, non-nil error = failure.

### Step 1: Define Custom Error Type

```go
// Define new type
type MyError struct {
    Code int
    Msg  string
}

// Implement error interface
func (e MyError) Error() string {
    return fmt.Sprintf("code %d: %s", e.Code, e.Msg)
}
```

### Step 2: Function Returning Custom Error

```go
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, MyError{
            Code: 400,
            Msg:  "division by zero",
        }
    }
    return a / b, nil
}
```

### Step 3: Using the Error

```go
func main() {
    res, err := divide(10, 0)
    if err != nil {
        fmt.Println("error:", err)  // Output: error: code 400: division by zero
        return
    }
    fmt.Println("result:", res)
}
```

### Step 4: Extract Error Details with Type Assertion

```go
if err != nil {
    if myErr, ok := err.(MyError); ok {
        fmt.Println("Error code:", myErr.Code)  // Output: Error code: 400
        fmt.Println("Message:", myErr.Msg)      // Output: Message: division by zero
    }
}
```

### Pointer Receiver Version (More Common)

```go
type MyError struct {
    Code int
    Msg  string
}

func (e *MyError) Error() string {
    return fmt.Sprintf("code %d: %s", e.Code, e.Msg)
}

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, &MyError{  // Return pointer
            Code: 400,
            Msg:  "division by zero",
        }
    }
    return a / b, nil
}
```

### Real-World Example

```go
type NotFoundError struct {
    Resource string
    ID       int
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s with id %d not found", e.Resource, e.ID)
}

func findUser(id int) (string, error) {
    if id != 1 {
        return "", &NotFoundError{"User", id}
    }
    return "Moshe", nil
}

func main() {
    user, err := findUser(5)
    if err != nil {
        fmt.Println(err)  // Output: User with id 5 not found
        return
    }
    fmt.Println("Found:", user)
}
```

### Quick Error Creation

```go
import "errors"

// Simple error
return errors.New("something went wrong")

// Formatted error
return fmt.Errorf("user %d not found", userID)
```

---

## Common Interfaces

### Stringer Interface

```go
type Stringer interface {
    String() string
}
```

Used by `fmt` package to print values.

```go
type Person struct {
    Name string
    Age  int
}

func (p Person) String() string {
    return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
    a := Person{"Arthur Dent", 42}
    fmt.Println(a)  // Output: Arthur Dent (42 years)
}
```

### Error Interface

The `error` interface is one of the most commonly used interfaces in Go. See the **Error Handling** section for detailed implementation and usage examples.

```go
type error interface {
    Error() string
}
```

### Reader Interface

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

Represents the read end of a stream of data (files, network, etc.).

```go
r := strings.NewReader("Hello, Reader!")
b := make([]byte, 8)

for {
    n, err := r.Read(b)
    fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
    fmt.Printf("b[:n] = %q\n", b[:n])
    if err == io.EOF {
        break
    }
}
```

**Returns**: Number of bytes read and error value (`io.EOF` when stream ends).

---

## Generics

### Type Parameters in Functions

Go functions can work on multiple types using type parameters.

```go
func Index[T comparable](s []T, x T) int {
    for i, v := range s {
        if v == x {
            return i
        }
    }
    return -1
}

func main() {
    si := []int{10, 20, 15, -10}
    fmt.Println(Index(si, 15))  // Output: 2

    ss := []string{"foo", "bar", "baz"}
    fmt.Println(Index(ss, "hello"))  // Output: -1
}
```

**Syntax**: Type parameters appear in brackets `[T comparable]` before function arguments.

**Constraints**:
- `comparable` - allows `==` and `!=` operators
- `any` - no constraints (accepts any type)
- Must specify constraint - no constraint means compile error

### Generic Types

Types can also be parameterized with type parameters.

```go
// Generic linked list
type List[T any] struct {
    next *List[T]
    val  T
}

func main() {
    // List of ints
    intList := List[int]{val: 1}
    intList.next = &List[int]{val: 2}

    // List of strings
    strList := List[string]{val: "hello"}
    strList.next = &List[string]{val: "world"}
}
```

**Use case**: Generic data structures (lists, trees, stacks, queues, etc.).

---

## Concurrency

### Goroutines

A goroutine is a lightweight thread managed by the Go runtime.

```go
func say(s string) {
    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}

func main() {
    go say("world")  // Start goroutine
    say("hello")     // Run in main goroutine
}
```

**Syntax**: `go f(x, y, z)` starts new goroutine running `f(x, y, z)`

**Important**:
- Arguments evaluated in current goroutine
- Execution happens in new goroutine
- Goroutines run in same address space - shared memory must be synchronized

---

### Channels

Channels are typed conduits for sending/receiving values.

```go
ch := make(chan int)  // Create channel

ch <- v     // Send v to channel ch
v := <-ch   // Receive from ch, assign to v
```

**Default behavior**: Sends and receives block until the other side is ready (synchronization without locks).

### Channel Example

```go
func sum(s []int, c chan int) {
    sum := 0
    for _, v := range s {
        sum += v
    }
    c <- sum  // Send sum to channel
}

func main() {
    s := []int{7, 2, 8, -9, 4, 0}

    c := make(chan int)
    go sum(s[:len(s)/2], c)
    go sum(s[len(s)/2:], c)
    x, y := <-c, <-c  // Receive from channel

    fmt.Println(x, y, x+y)  // Output: -5 17 12
}
```

**Important**: Must specify channel type. Deadlock occurs only when no goroutines can relieve the blocking (e.g., sending with no receiver and no other goroutines running).

---

### Buffered Channels

Channels can be buffered with a capacity.

```go
ch := make(chan int, 100)  // Buffered channel, capacity 100
```

**Behavior**:
- Sends block only when buffer is **full**
- Receives block only when buffer is **empty**

```go
func main() {
    ch := make(chan int, 2)
    ch <- 1
    ch <- 2
    // ch <- 3  // Would block! Buffer is full
    fmt.Println(<-ch)  // Output: 1
    fmt.Println(<-ch)  // Output: 2
}
```

**Deadlock occurs when**:
- No goroutines exist to relieve the blocking
- If other goroutines are performing send/receive, everything works fine - no error
- For unbuffered channels, every send or receive blocks until the other side is ready

---

### Range and Close

A sender can close a channel to indicate no more values will be sent.

```go
func fibonacci(n int, c chan int) {
    x, y := 0, 1
    for i := 0; i < n; i++ {
        c <- x
        x, y = y, x+y
    }
    close(c)  // Close channel
}

func main() {
    c := make(chan int, 10)
    go fibonacci(cap(c), c)
    for i := range c {  // Receives until channel closed
        fmt.Println(i)
    }
}
```

**Testing if channel is closed**:

```go
v, ok := <-ch
// ok is false if no more values and channel is closed
```

**Important rules**:
- **Only sender should close** - never the receiver
- Sending on closed channel causes panic
- Channels don't need closing usually - only when receiver needs to know there are no more values (like terminating `range` loop)
- **Range without close on sender causes error**

---

### Select

The `select` statement lets a goroutine wait on multiple communication operations.

```go
func fibonacci(c, quit chan int) {
    x, y := 0, 1
    for {
        select {
        case c <- x:  // Ready when receiver is ready
            x, y = y, x+y
        case <-quit:
            fmt.Println("quit")
            return
        }
    }
}

func main() {
    c := make(chan int)
    quit := make(chan int)
    go func() {
        for i := 0; i < 10; i++ {
            fmt.Println(<-c)
        }
        quit <- 0
    }()
    fibonacci(c, quit)
}
```

**Behavior**:
- Blocks until one case can run
- Chooses one at random if multiple are ready
- `case c <- x` not ready if there's no receiver

### Default Selection

The `default` case runs if no other case is ready.

```go
select {
case i := <-c:
    // use i
default:
    // receiving from c would block
}
```

**Use case**: Try send/receive without blocking.

```go
func main() {
    tick := time.Tick(100 * time.Millisecond)
    boom := time.After(500 * time.Millisecond)

    for {
        select {
        case <-tick:
            fmt.Println("tick.")
        case <-boom:
            fmt.Println("BOOM!")
            return
        default:
            fmt.Println("    .")
            time.Sleep(50 * time.Millisecond)
        }
    }
}
// Output:
//     .
// tick.
//     .
// tick.
//     .
// tick.
//     .
// tick.
//     .
// BOOM!
```

---

### sync.Mutex

Mutex provides mutual exclusion for safe concurrent access.

```go
import "sync"

type SafeCounter struct {
    mu sync.Mutex
    v  map[string]int
}

// Inc increments the counter for the given key
func (c *SafeCounter) Inc(key string) {
    c.mu.Lock()
    // Lock so only one goroutine can access map at a time
    c.v[key]++
    c.mu.Unlock()
}

// Value returns the current value of the counter
func (c *SafeCounter) Value(key string) int {
    c.mu.Lock()
    defer c.mu.Unlock()  // Unlock when function returns
    return c.v[key]
}

func main() {
    c := SafeCounter{v: make(map[string]int)}
    for i := 0; i < 1000; i++ {
        go c.Inc("somekey")
    }

    time.Sleep(time.Second)
    fmt.Println(c.Value("somekey"))  // Output: 1000
}
```

**Methods**:
- `Lock()` - acquire lock
- `Unlock()` - release lock

**Best practice**: Use `defer` to ensure unlock happens even if panic occurs.