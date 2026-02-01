# Scala - Essential Rules for Writing Code

## Top Critical Points - Must Know ⚠️

1. **Everything is an expression** - returns a value, not statements
2. **Use recursion, not loops** - prefer recursive functions over for/while loops
3. **Immutability by default** - use val, avoid var
4. **All functions are objects** - Function1, Function2... up to Function22
5. **Case class params are auto-val** - accessible without explicit val keyword
6. **Regular class params need val** - otherwise only accessible inside class
7. **Never use null** - use Option[T] instead (Some/None)
8. **Pattern matching over if/else** - more powerful and idiomatic
9. **Enum value 0 must be unspecified/default** - first enum value
10. **Code blocks return last expression** - the value of the block

---

## Constructor Parameters

### Regular Classes
- `name: String` → private, only inside class
- `val name: String` → public immutable field
- `var name: String` → public mutable field (avoid!)

### Case Classes
- `name: String` → automatically public immutable (auto-val)
- `val name: String` → same as above (explicit val redundant)
- `var name: String` → public mutable field (only if needed)

## Values and Variables
- **Always use val** (immutable) - default choice
- **Avoid var** (mutable) - breaks immutability
- **Lazy val** - evaluated on first access (use for expensive computations)
- **Type inference works** - explicit types optional but useful for API boundaries

## Functions
- Use recursion instead of loops
- Last expression in block is return value
- No return keyword needed (implicit)
- Multiple parameter lists for currying: `def func(a: A)(b: B)`
- Unit type = void (side effects only)

## Case Classes
- Auto-generates: apply, unapply, equals, hashCode, copy, toString
- Perfect for immutable data structures
- No `new` keyword needed (auto-generated apply)
- All parameters automatically public immutable
- Use for pattern matching

## Collections

### List Operations
- `::` prepend element
- `+:` prepend
- `:+` append
- `head` first element
- `tail` all but first
- `headOption` safe head (returns Option)

### Prefer
- `mapValues()` over `map()` on Maps - avoids repartitioning
- `filter()` before `map()` - reduces processing
- `flatMap()` for nested transformations

## Pattern Matching
- Always include default case `_` - avoids MatchError
- More powerful than switch/if-else
- Pattern match is expression (returns value)
- Decompose case classes: `case Person(n, a) =>`
- Use guards: `case x if x > 0 =>`

## Option Type
- **Never null** - use `Option[T]` (Some/None)
- `Option(nullable)` converts null to None
- `getOrElse(default)` provide fallback
- `map/flatMap/filter` chain operations
- Use in for comprehensions
- `isDefined/isEmpty` check presence

## Try Type
- For operations that may throw exceptions
- `Try { risky() }` returns Success/Failure
- `map/flatMap/filter` like Option
- `recover/recoverWith` handle failures
- `getOrElse(default)` provide fallback
- Convert to Option: `.toOption`

## Future & Promise

### Future
- Async computation - starts immediately on creation
- Always need ExecutionContext (thread pool)
- `map/flatMap/filter` chain async ops
- Use for comprehensions for multiple Futures
- `onComplete` for callbacks
- **Avoid Await.result** - blocking defeats async purpose

### Promise
- Writable Future - complete from outside
- `promise.success(value)` complete with value
- `promise.failure(exception)` complete with error
- `promise.future` get the Future
- Use for callback-based API bridges
- Can only be completed once

## For Comprehensions
- **With yield** → transforms collection (returns new collection)
- **Without yield** → side effects only (returns Unit)
- Use `if` for filters (guards)
- Define intermediate values with `=`
- Works with Option, Try, Future, List, etc.

## Implicits (Scala 2) / Context Abstractions (Scala 3)

### Scala 2
- `implicit val` - implicit values
- `implicit def` - implicit conversions
- `implicit` parameter - receives implicit
- `implicit class` - extension methods

### Scala 3 (Prefer)
- `given` replaces implicit val/def
- `using` replaces implicit parameter
- `extension` replaces implicit class
- `summon[T]` replaces implicitly[T]
- Context bounds: `[A : Combinator]`

## Multiple Parameter Lists (Currying)
- `def func(a: A)(b: B)` - multiple parameter lists
- Use for partial application
- Better type inference in later lists
- Separate implicit/using parameters: `def func(x: X)(using ctx: Context)`
- Creates specialized functions

## Object-Oriented Rules
- Traits for multiple inheritance (like interfaces)
- Objects are singletons
- Companion objects for factory methods/static members
- `apply()` method for function-like syntax
- Multiple `apply()` overloads allowed
- No `override` keyword for apply (not inherited)

## Error Handling
- **Never use null** - use Option
- **Avoid try/catch in logic** - use Try
- Pattern match on Success/Failure
- Use recover/recoverWith for fallback
- Future failures propagate through chain

## Best Practices
- Prefer map/flatMap over explicit loops
- Use for comprehensions for readability
- Pattern match over type checks
- Return Option instead of throwing exceptions (when appropriate)
- Return Try when operation may fail
- Return Future for async operations
- Always handle None/Failure cases
- Avoid blocking operations
- Use lazy val for expensive computations
- Extension methods for type enrichment

## Common Pitfalls

### AVOID
- Using null (use Option)
- Using var (use val)
- Using loops (use recursion/map/fold)
- Blocking Futures with Await
- Modifying collections (use immutable)
- Plain constructor params in case class (redundant val)
- Forgetting ExecutionContext for Future

### DO
- Use Option for nullable values
- Use Try for exception handling
- Use Future for async
- Use val for immutability
- Pattern match over if/else
- Use recursion over loops
- Chain operations with map/flatMap
- For comprehensions for multiple operations