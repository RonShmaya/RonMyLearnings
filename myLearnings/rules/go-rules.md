# Go - Essential Rules for Writing Code

## Top Critical Points - Must Know ⚠️

1. **Unused variables/imports = compile error** - Go enforces code cleanliness
2. **`:=` is declare + assign** - only for new variables, inside functions only
3. **Multiple return values are NOT tuples** - must handle all or use `_` to ignore
4. **No pointer arithmetic** - unlike C, pointers are safe
5. **Interfaces are implicit** - no "implements" keyword needed
6. **Zero values are safe** - all types have sensible defaults (0, "", false, nil)
7. **Exported names start with capital letter** - lowercase = private
8. **Slices are views, not copies** - modifying slice modifies underlying array
9. **Range creates copies** - modifying range variable doesn't affect original
10. **Close channels only from sender** - receiver closing causes panic

---

## Common Mistakes Summary

1. **Forgetting to use variables** - `x := 5` without using x = compile error
2. **Using `:=` outside functions** - use `var x = 5` at package level
3. **Not handling all return values** - `x := f()` fails if f returns `(int, error)`, use `x, _ := f()`
4. **Modifying slice and expecting copy** - `s[0] = 99` changes underlying array!
5. **Modifying range variable** - `for _, v := range arr { v.x = 1 }` doesn't affect original
6. **Adding to nil map** - `m[key] = val` panics if m is nil, use `m = make(map[K]V)` first
7. **Returning large structs by value** - `func f() BigStruct` copies, use `func f() *BigStruct`
8. **Not checking error** - always `if err != nil { ... }`
9. **Closing channel from receiver** - only sender should `close(ch)`
10. **Sending to closed channel** - `ch <- x` panics if ch is closed

---

## General

### Be Careful
- **Unused variables = compile error** - `x := 5` without using x fails
- **Unused imports = compile error** - `import "fmt"` without using fmt fails
- **No implicit conversions** - `var f float64 = i` fails, use `float64(i)`

### Zero Values (Safe Defaults)
- `int` → `0`
- `float` → `0.0`
- `bool` → `false`
- `string` → `""` (empty string, NOT nil)
- `pointer` → `nil`
- `slice` → `nil` (length 0, capacity 0)
- `map` → `nil` (cannot add keys to nil map!)
- `interface` → `nil`

### Unique to Go
- **Exported vs unexported** - `Name` is public, `name` is private
- **No exceptions** - use multiple return values with error instead - `func f() (int, error)` 
- **Garbage collected** - automatic memory management

---

## Variables & Declaration

### Be Careful
- **`:=` only inside functions** - use `var` at package level
- **`:=` for new variables only** - use `=` for reassignment
- **No implicit type conversion** - `int` → `float64` requires `float64(i)`

### Remember
- **Multiple declarations** - `var i, j int = 1, 2` or `i, j := 1, 2`
- **Different types in one line** - `var c, python = true, "no!"` (type inference)
- **Var blocks** - `var ( x int; y string )` group related variables
- **Type alias** - `type MyInt = int` (same type)
- **New type** - `type MyInt int` (different type, needs conversion)

### Unique to Go
- **`:=` operator** - short declaration, not in most languages
- **No default values needed** - zero values are safe

---

## Constants

### Be Careful
- **Cannot use `:=`** - must use `const` keyword
- **Must be compile-time values** - no runtime expressions

### Critical
- **Numeric constants are high-precision** - untyped constants take type from context
- **Can overflow at runtime** - `const Big = 1 << 100` works in constants, not variables
- **Context-aware** - `const c = 1` can be int or float depending on usage

---

## Functions

### Be Careful
- **Multiple returns must be handled** - `x := f()` fails if f returns `(int, error)`
- **Named returns are initialized** - `func f() (x int) { return }` x starts at 0

### Remember
- **Parameters** - `func add(x, y int) int` type after name
- **Shorthand for same type** - `(x int, y int)` → `(x, y int)`
- **Multiple returns** - `func f() (int, error)` NOT tuples
- **Named returns** - `func f() (r int) { r = 5; return }` can omit return value
- **Must handle all returns** - use `_` to ignore: `result, _ := f()`

### Unique to Go
- **Multiple return values** - not tuples, truly separate values
- **Named return values** - declared in function signature

---

## Flow Control - For

### Critical
- **Only one loop construct** - `for` is the only loop in Go
- **Three syntaxes only** - `for init; cond; post {}`, `for cond {}`, `for {}`
- **Cannot mix** - can't use `for init; cond {}` (two components)

### Remember
- **No parentheses** - `for i := 0; i < 10; i++` (no parens around condition)
- **Braces required** - `{}` are always mandatory
- **While equivalent** - `for condition {}`
- **Infinite loop** - `for {}`
- **Do-while pattern** - `for { ... break }`

---

## Flow Control - If/Switch

### Remember - If
- **No parentheses** - `if x < 0 {}` (no parens)
- **Braces required** - `{}` always mandatory
- **Short statement** - `if v := f(); v < 10 {}` - v scoped to if/else only

### Remember - Switch
- **Automatic break** - no fallthrough (unlike C/Java/JS)
- **Cases need not be constants** - any expression works
- **Values need not be integers** - any comparable type
- **No condition = switch true** - `switch { case x > 0: ... }`
- **Can have init statement** - `switch os := runtime.GOOS; os { ... }`

---

## Flow Control - Defer

### Critical
- **Arguments evaluated immediately** - `defer fmt.Println(x)` evaluates x now, prints later (until function returns)
- **LIFO execution order** - Last-In-First-Out (stack)

### Remember
- **Use for cleanup** - `defer file.Close()` or `defer mu.Unlock()`
- **Executes even on panic** - ensures cleanup happens

---

## Pointers

### Be Careful
- **Nil pointer dereference = panic** - `*p` panics if p is nil - check for nil before dereferencing

### Remember
- **`*T`** - `var p *int` pointer to type T
- **`&`** - `p = &x` generates pointer to operand
- **`*`** - `val := *p` dereferences pointer (accesses value)
- **Zero value is `nil`** - `var p *int` starts as nil

### Unique to Go
- **No pointer arithmetic** - cannot do `p++` like C
- **Automatic dereference** - `p.X` works, no need for `(*p).X`

---

## Structs

### Be Careful
- **Large structs are expensive to copy** - return pointers for large structs
- **Functions return copies** - use pointers to avoid copying

### Remember
- **Access with dot** - `v.X` or `p.X` even for pointers
- **Anonymous fields** - `type A struct { B }` struct embedding
- **Struct literal** - `Vertex{X: 1}` can omit fields, get zero values
- **Pointer to struct** - `p := &Vertex{1, 2}` returns `*Vertex`

### Critical
- **Return pointers for large structs** - `func f() *Big` avoid expensive copy
- **Small structs can be by value** - `func f() Point` few fields, no arrays
- **Pointer vs Value decision** - If struct is large, use pointer. If it must be mutated, use pointer. Otherwise value is fine

---

## Arrays

### Critical
- **Size is part of type** - `[4]int` ≠ `[5]int` (different types!)
- **Fixed size** - cannot change size after declaration

### Remember
- **Rarely used** - `[]int{1,2,3}` slices are more common than `[3]int{1,2,3}`
- **Passed by value** - `func f(a [5]int)` -  function receives copy of entire array 

---

## Slices

### Critical ⚠️
- **Slices are views** - NOT copies, modify underlying array
- **Changing slice changes array** - and all other slices referencing it
- **Append may reallocate** - if capacity exceeded, new array allocated
- **Nil slice is valid** - length 0, capacity 0, safe to use
- **Out of bounds = panic** - `s[10]` when len=5 panics

### Be Careful
- **Check capacity before append** - if `len(s) == cap(s)` append allocates new array
- **Slice bounds** - `a[low:high]` includes low, excludes high - `a[1:4]` includes index 1,2,3 excludes 4
- **Never define slice outbound** - causes panic - `s[:17]` panics if cap < 17

### Remember
- **`len(s)`** - number of elements
- **`cap(s)`** - capacity of underlying array from first element
- **`make([]T, len, cap)`** - create slice with length and capacity
- **`append(s, elem)`** - add elements, may reallocate
- **Slice literal** - `[]int{1, 2, 3}` (no size specified)

### Unique to Go
- **Slice structure** - [pointer, length, capacity]
- **Default slicing** - `a[:]` all, `a[:3]` first 3, `a[2:]` from 2 to end

---

## Range

### Critical ⚠️
- **Range creates copies** - always copies the element value
- **Modifying copy doesn't affect original** - use index to modify
- **For large structs, use index** - avoid expensive copy

### Remember
- **Two values** - `for i, v := range arr` index and copy of element
- **Omit second variable** - `for i := range arr` index only
- **Use `_` for index** - `for _, v := range arr` value only
- **Best practice for mutation** - `for i := range arr { arr[i].x = 1 }` or use pointer slice

### Example Patterns
```go
// BAD - copies large struct
for _, s := range bigArr {
    s.Field = 42  // modifies COPY
}

// GOOD - use index
for i := range bigArr {
    bigArr[i].Field = 42  // modifies original
}

// GOOD - use pointer slice
bigArr := []*Big{{}, {}}
for _, b := range bigArr {
    b.Field = 42  // modifies through pointer
}
```

---

## Maps

### Critical ⚠️
- **Nil map cannot add keys** - will panic
- **Must use `make`** - `m = make(map[string]int)` before use
- **Missing key returns zero value** - not error

### Be Careful
- **Test key existence** - `v, ok := m[key]` ok is false if key missing
- **Zero value vs missing** - `m["missing"]` returns 0 for int map, use `ok` to distinguish

### Remember
- **Zero value is `nil`** - `var m map[string]int` no keys, cannot add keys
- **Insert/update** - `m[key] = value`
- **Delete** - `delete(m, key)`
- **Check existence** - `if v, ok := m[key]; ok { ... }`

---

## Function Values & Closures

### Remember
- **Functions are first-class values** - `fn := func(x int) int { return x * 2 }` pass as arguments
- **Closures capture variables** - `func() { sum += x }` reference outer scope
- **Each closure has own variables** - `f1, f2 := adder(), adder()` each bound to own state

---

## Methods

### Be Careful
- **Value receiver copies** - `func (v Vertex) M()` expensive for large structs
- **Pointer receiver modifies** - `func (v *Vertex) M()` changes original struct
- **Cannot assign value to pointer-receiver interface** - `var i I = T{}` fails if method on `*T`

### Remember
- **Receiver between func and name** - `func (v Vertex) Method()`
- **Can define on any type** - `func (f MyFloat) Abs()` not just structs (in same package)
- **Cannot define on external types** - including built-in types like `int`

### Critical
- **Use pointer receivers for large structs** - `func (v *BigStruct) M()` avoid copy
- **Use pointer receivers to modify** - `func (v *Vertex) Scale() { v.X *= 2 }` changes original
- **Consistency** - if some methods use `*T`, all should
- **Pointer vs Value decision** - If struct is large, use pointer. If it must be mutated, use pointer. Otherwise value is fine

### Unique to Go
- **Automatic conversion** - `v.Method()` → `(&v).Method()` for pointer receiver
- **Methods on non-struct types** - `func (f MyFloat) Method()`

---

## Interfaces

### Critical ⚠️
- **Nil concrete value ≠ nil interface** - `(nil, *T)` is NOT nil
- **Nil interface causes panic** - no type to call method on
- **Pointer receiver = only pointer implements** - `*T` implements, not `T`

### Remember
- **Implicit implementation** - no "implements" keyword needed
- **Interface value = (value, type)** - tuple of concrete value and type - `var i I = T{x:1}` stores `(T{x:1}, T)`
- **Empty interface `any`** - `var x any = 42` accepts any type
- **Type assertion** - `v := i.(T)` panics or `v, ok := i.(T)` safe

### Unique to Go
- **Implicit interfaces** - no explicit declaration needed
- **Empty interface** - `interface{}` or `any`

---

## Type Assertions & Switches

### Be Careful
- **Wrong type assertion = panic** - use two-value form for safety
- **Nil in default case** - type switch handles nil in default

### Remember
- **Type assertion** - `t := i.(T)` panics on wrong type
- **Safe assertion** - `t, ok := i.(T)` ok is false on wrong type
- **Type switch** - `switch v := i.(type) { case int: ...; case string: ... }`
- **Nil handling** - `nil` goes to `default:` case in type switch

---

## Error Handling

### Critical ⚠️
- **Always check `err != nil`** - errors must be handled
- **Return error as last value** - `func f() (result T, err error)`

### Remember
- **Error is an interface** - any type with `Error() string` method
- **Nil error = success** - non-nil = failure
- **Custom errors** - implement `Error() string`
- **Use pointer receiver** - `func (e *MyError) Error() string`
- **Type assertion for details** - `if myErr, ok := err.(MyError); ok { ... }`

### Quick Creation
- **Simple error** - `errors.New("message")`
- **Formatted error** - `fmt.Errorf("format %v", value)`

---

## Generics

### Critical
- **Must specify constraint** - `[T any]` or `[T comparable]`
- **No constraint = compile error** - cannot use `[T]` alone

### Remember
- **Type parameters in brackets** - `func f[T any](s []T)`
- **`comparable` constraint** - allows `==` and `!=`
- **`any` constraint** - no restrictions

---

## Concurrency - Goroutines

### Remember
- **Lightweight threads** - managed by Go runtime
- **Syntax** - `go f(x, y)`
- **Arguments evaluated in current goroutine** - execution in new goroutine
- **Shared memory needs sync** - use channels or mutexes

---

## Concurrency - Channels

### Critical ⚠️
- **Unbuffered channels block** - until other side is ready
- **Deadlock when no goroutines can help** - not just "send without receive"
- **Send on closed channel = panic** - never send to closed channel
- **Only sender should close** - receiver closing causes issues

### Be Careful
- **Must create with `make`** - `ch := make(chan int)`
- **Buffered vs unbuffered** - different blocking behavior
- **Buffered full = blocks send** - until space available
- **Buffered empty = blocks receive** - until data available

### Remember
- **Send** - `ch <- v`
- **Receive** - `v := <-ch`
- **Close** - `close(ch)` (sender only!)
- **Check closed** - `v, ok := <-ch` - ok false if closed
- **Range over channel** - `for v := range ch` - continues until closed

### Deadlock Rules
- **Deadlock occurs when** - no goroutines exist to relieve blocking
- **If other goroutines running** - no deadlock, operation waits
- **Unbuffered channels** - every send/receive blocks until other side ready

---

## Concurrency - Select

### Remember
- **Wait on multiple channels** - `select { case <-ch1: ... case <-ch2: ... }`
- **Random selection** - if multiple cases ready, chooses randomly
- **`default` case** - runs if no other case ready (non-blocking)
- **Case not ready** - `case ch <- x` not ready if no receiver

---

## Concurrency - Mutex

### Remember
- **Use for shared memory** - when channels aren't appropriate
- **`Lock()`** - `mu.Lock()` acquire lock
- **`Unlock()`** - `mu.Unlock()` release lock
- **Use `defer`** - `defer mu.Unlock()` ensures unlock even if panic