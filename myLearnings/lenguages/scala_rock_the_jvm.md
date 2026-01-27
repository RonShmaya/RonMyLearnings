# Scala at Light Speed - Quick Reference Guide

A fast reference guide for Scala beginners with practical examples to remember core concepts.

---

## Table of Contents
1. [Basics](#basics)
    - [Values and Types](#values-and-types)
    - [Strings](#strings)
    - [Expressions](#expressions)
    - [Code Blocks](#code-blocks)
    - [Functions](#functions)
    - [Unit Type](#unit-type)
2. [Object Orientation](#object-orientation)
    - [Classes and Instances](#classes-and-instances)
    - [Constructor Parameters: val, var, or nothing?](#constructor-parameters-val-var-or-nothing)
    - [Inheritance](#inheritance)
    - [Abstract Classes](#abstract-classes)
    - [Traits (Interfaces)](#traits-interfaces)
    - [Infix Notation](#infix-notation)
    - [Anonymous Classes](#anonymous-classes)
    - [Singleton Objects](#singleton-objects)
    - [The apply Method - Deep Dive](#the-apply-method---deep-dive)
    - [Companion Objects](#companion-objects)
    - [Case Classes](#case-classes)
        - [Case Class Constructor Parameters - Special Behavior!](#case-class-constructor-parameters---special-behavior)
        - [Explicit val in Case Classes](#explicit-val-in-case-classes)
        - [Using var in Case Classes](#using-var-in-case-classes)
    - [Exception Handling](#exception-handling)
    - [Generics](#generics)
    - [Key Principles](#key-principles)
3. [Functional Programming](#functional-programming)
    - [Functions as Objects](#functions-as-objects)
    - [Function Syntax Sugar](#function-syntax-sugar)
    - [Multiple Parameter Lists / Currying](#multiple-parameter-lists--currying)
    - [Higher-Order Functions (HOF)](#higher-order-functions-hof)
    - [For Comprehensions](#for-comprehensions)
    - [Collections](#collections)
        - [Lists](#lists)
        - [Sequences](#sequences)
        - [Vectors](#vectors)
        - [Sets](#sets)
        - [Ranges](#ranges)
        - [Tuples](#tuples)
        - [Maps](#maps)
4. [Pattern Matching](#pattern-matching)
    - [Basic Pattern Matching](#basic-pattern-matching)
    - [Case Class Decomposition](#case-class-decomposition)
    - [Deconstructing Tuples](#deconstructing-tuples)
    - [Decomposing Lists](#decomposing-lists)
    - [Pattern Matching with Option (Some/None)](#pattern-matching-with-option-somenone)
    - [Pattern Matching with null](#pattern-matching-with-null)
5. [Advanced Topics](#advanced-topics)
    - [Lazy Evaluation](#lazy-evaluation)
    - [Option Type](#option-type)
        - [Basic Option Usage](#basic-option-usage)
        - [Option as a Pseudo-Collection](#option-as-a-pseudo-collection)
        - [Chaining Option Operations](#chaining-option-operations)
        - [Option with Collections](#option-with-collections)
    - [Try Type](#try-type)
        - [Basic Try Usage](#basic-try-usage)
        - [Try as a Pseudo-Collection](#try-as-a-pseudo-collection)
        - [Chaining Try Operations](#chaining-try-operations)
        - [Try with Collections](#try-with-collections)
        - [Option vs Try](#option-vs-try)
    - [Future (Asynchronous Programming)](#future-asynchronous-programming)
    - [Implicits Basics (Scala 2)](#implicits-basics-scala-2)
        - [Implicit Arguments](#implicit-arguments)
        - [Implicit Conversions (Enrichment)](#implicit-conversions-enrichment)
    - [Contextual Abstractions (Scala 3)](#contextual-abstractions-scala-3)
        - [Context Parameters and Arguments (given/using)](#context-parameters-and-arguments-givenusing)
        - [Type Classes Pattern](#type-classes-pattern)
        - [Where Scala Looks for Given Instances](#where-scala-looks-for-given-instances)
        - [Context Bounds](#context-bounds)
        - [Extension Methods](#extension-methods)
        - [Multiple Extension Methods](#multiple-extension-methods)
        - [Extension Methods with Type Parameters](#extension-methods-with-type-parameters)
        - [Use Cases for Context Parameters](#use-cases-for-context-parameters)
        - [Scala 2 Implicits vs Scala 3 Contextual Abstractions](#scala-2-implicits-vs-scala-3-contextual-abstractions)
6. [Quick Cheat Sheet](#quick-cheat-sheet)
7. [Practice Tips](#practice-tips)

---

## Basics

### Values and Types

```scala
// Defining values (immutable)
val meaningOfLife: Int = 42  // const in other languages
val aBoolean = false         // type inference

// Basic types: Int, Boolean, Char, Double, Float, String
```

### Strings

```scala
val aString = "I love Scala"
val aComposedString = "I" + " " + "love" + " " + "Scala"
val anInterpolatedString = s"The meaning of life is $meaningOfLife"
```

### Expressions

```scala
// Everything is an expression (returns a value)
val anExpression = 2 + 3

// If-expressions (not statements!)
val ifExpression = if (meaningOfLife > 43) 56 else 999

// Chained if-expressions
val chainedIfExpression =
  if (meaningOfLife > 43) 56
  else if (meaningOfLife < 0) -2
  else if (meaningOfLife > 999) 78
  else 0
```

### Code Blocks

```scala
// Code blocks are expressions
val aCodeBlock = {
  val aLocalValue = 67
  aLocalValue + 3  // last expression is the value
} // aCodeBlock = 70
```

### Functions

```scala
// Function definition
def myFunction(x: Int, y: String): String = {
  y + " " + x
}

// Recursive functions (preferred over loops in Scala!)
def factorial(n: Int): Int =
  if (n <= 1) 1
  else n * factorial(n - 1)

// factorial(5) = 120
```

### Unit Type

```scala
// Unit = no meaningful value (like void)
// Used for side effects
println("I love Scala")

def myUnitReturningFunction(): Unit = {
  println("I don't love returning Unit")
}

val theUnit = ()
```

**Key takeaway**: In Scala, use RECURSION instead of loops!

---

## Object Orientation

### Classes and Instances

```scala
// Basic class
class Animal {
  val age: Int = 0
  def eat() = println("I'm eating")
}

val anAnimal = new Animal
```

### Constructor Parameters: val, var, or nothing?

**IMPORTANT**: Understanding constructor parameters is crucial in Scala!

```scala
// Without val/var - just a constructor parameter
class Person(name: String, age: Int) {
  // name and age are accessible ONLY inside the class
  def greet(): String = s"Hi, I'm $name and I'm $age years old"
}

val person = new Person("Alice", 25)
person.greet()      // ✓ Works - returns "Hi, I'm Alice and I'm 25 years old"
// person.name      // ✗ ERROR - not accessible from outside!
// person.age       // ✗ ERROR - not accessible from outside!


// With val - becomes a public immutable field
class Dog(val name: String, val breed: String) {
  def bark(): String = s"$name the $breed says Woof!"
}

val dog = new Dog("Buddy", "Golden Retriever")
dog.name            // ✓ "Buddy" - accessible from outside
dog.breed           // ✓ "Golden Retriever" - accessible from outside
dog.bark()          // ✓ "Buddy the Golden Retriever says Woof!"
// dog.name = "Max" // ✗ ERROR - val is immutable!


// With var - becomes a public mutable field
class Cat(var name: String, var age: Int) {
  def meow(): String = s"$name says Meow!"
}

val cat = new Cat("Whiskers", 3)
cat.name            // ✓ "Whiskers" - accessible
cat.age             // ✓ 3 - accessible
cat.name = "Fluffy" // ✓ Works - var is mutable!
cat.age = 4         // ✓ Works
cat.meow()          // ✓ "Fluffy says Meow!"


// Mixed parameters - some with val, some without
class Student(val id: Int, name: String, var grade: Double) {
  // id - public immutable field (val)
  // name - only accessible inside class (no val/var)
  // grade - public mutable field (var)

  def info(): String = s"Student $id: $name has grade $grade"
}

val student = new Student(101, "John", 85.5)
student.id          // ✓ 101 - accessible (val)
// student.name     // ✗ ERROR - not accessible from outside!
student.grade       // ✓ 85.5 - accessible (var)
student.grade = 90.0 // ✓ Works - can modify
student.info()      // ✓ "Student 101: John has grade 90.0"
```

**Summary Table**:

| Syntax | Accessible Outside? | Accessible Inside? | Mutable? |
|--------|-------------------|------------------|----------|
| `name: String` | ✗ No | ✓ Yes | ✗ No |
| `val name: String` | ✓ Yes | ✓ Yes | ✗ No |
| `var name: String` | ✓ Yes | ✓ Yes | ✓ Yes |

**Rule of thumb**:
- No `val`/`var` → constructor parameter only (private)
- `val` → public immutable field (prefer this!)
- `var` → public mutable field (avoid if possible - breaks immutability)

### Inheritance

```scala
// Subtype polymorphism
val aDeclaredAnimal: Animal = new Dog("Hachi")
aDeclaredAnimal.eat()  // most derived method called at runtime
```

### Abstract Classes

```scala
abstract class WalkingAnimal { //only one extends - like Java
  val hasLegs = true  // public by default, can restrict by adding protected or private
  def walk(): Unit    // abstract method
}
```

### Traits (Interfaces)

```scala
trait Carnivore {
  def eat(animal: Animal): Unit
}

trait Philosopher {
  def ?!(thought: String): Unit  // valid method name!
}

// Single-class inheritance, multi-trait mixing
class Crocodile extends Animal with Carnivore with Philosopher {
  override def eat(animal: Animal): Unit =
    println("I am eating you, animal!")

  override def ?!(thought: String): Unit =
    println(s"I was thinking: $thought")
}
```

### Infix Notation

```scala
val aCroc = new Crocodile
aCroc.eat(aDog)     // normal call
aCroc eat aDog      // infix notation (methods with ONE argument)
aCroc ?! "What if we could fly?"

// Operators are methods!
val basicMath = 1 + 2
val anotherBasicMath = 1.+(2)  // equivalent
```

### Anonymous Classes

```scala
val dinosaur = new Carnivore {
  override def eat(animal: Animal): Unit =
    println("I am a dinosaur so I can eat pretty much anything")
}

// Compiler creates: class Carnivore_Anonymous_35728 extends Carnivore
```

### Singleton Objects

```scala
object MySingleton {  // only instance of MySingleton type
  val mySpecialValue = 53278
  def mySpecialMethod(): Int = 5327
  def apply(x: Int): Int = x + 1
}

MySingleton.mySpecialMethod()
MySingleton.apply(65)
MySingleton(65)  // equivalent to apply(65)
```

### The apply Method - Deep Dive

**IMPORTANT**: Understanding `apply` is key to writing idiomatic Scala!

#### What is apply?

`apply` is a special method name in Scala. When you call an object like a function, Scala automatically looks for an `apply` method.

```scala
object MyObject {
  def apply(x: Int): String = s"You called with $x"
}

MyObject.apply(5)  // explicit call
MyObject(5)        // syntactic sugar - same as MyObject.apply(5)
```

#### Can you have multiple apply methods? YES!

You can overload `apply` with different signatures (different parameters).

```scala
object Calculator {
  // Multiple apply methods with different signatures
  def apply(x: Int): Int = x * 2

  def apply(x: Int, y: Int): Int = x + y

  def apply(x: String): String = x.toUpperCase
}

Calculator(5)           // 10 - calls apply(x: Int)
Calculator(3, 4)        // 7 - calls apply(x: Int, y: Int)
Calculator("hello")     // "HELLO" - calls apply(x: String)
```

#### Where does apply come from?

`apply` is **NOT inherited** from anywhere - you're not overriding anything!

```scala
// In objects - you DEFINE apply, not override
object Factory {
  def apply(): String = "Created!"  // NO override keyword
}

// In classes - same thing
class Person(name: String) {
  def apply(age: Int): String = s"$name is $age years old"  // NO override
}

val alice = new Person("Alice")
alice(25)  // "Alice is 25 years old"
```

**Why no `override` keyword?**
- `override` is only used when you're overriding a method from a parent class/trait
- `apply` is just a regular method with a special syntactic sugar
- You're not overriding anything - you're defining a new method

#### apply in Classes vs Objects

```scala
// In a regular class - instance method
class Greeter(name: String) {
  def apply(greeting: String): String = s"$greeting, $name!"
}

val greeter = new Greeter("Bob")
greeter("Hello")  // "Hello, Bob!"


// In an object - static-like method
object StringUtils {
  def apply(s: String): String = s.trim.toLowerCase
}

StringUtils("  HELLO  ")  // "hello"


// In a companion object - often used as factory
class Person(val name: String, val age: Int)

object Person {
  // Factory method using apply
  def apply(name: String, age: Int): Person = new Person(name, age)

  // Multiple factory methods
  def apply(name: String): Person = new Person(name, 0)
}

val person1 = Person("Alice", 30)  // calls apply(String, Int)
val person2 = Person("Bob")        // calls apply(String)
// No 'new' keyword needed!
```

#### Case Classes and apply

Case classes automatically get an `apply` method in their companion object:

```scala
case class User(name: String, age: Int)

// Compiler generates this for you:
/*
object User {
  def apply(name: String, age: Int): User = new User(name, age)
}
*/

val user = User("Charlie", 25)  // Uses auto-generated apply
```

#### Summary

| Question | Answer |
|----------|--------|
| Can you have multiple `apply` methods? | ✓ Yes - overload with different parameters |
| Do you need `override` keyword? | ✗ No - you're not overriding anything |
| Where is it defined? | You define it - it's not inherited |
| Works in classes? | ✓ Yes - instance method |
| Works in objects? | ✓ Yes - "static" method |
| Why is it special? | Syntactic sugar: `obj(args)` → `obj.apply(args)` |

### Companion Objects

```scala
object Animal {  // companion to class Animal
  // Can access each other's private fields/methods
  val canLiveIndefinitely = false
}

val animalsCanLiveForever = Animal.canLiveIndefinitely  // "static" access
```

### Case Classes

```scala
// Lightweight data structures with boilerplate
case class Person(name: String, age: Int)

// Benefits:
// - sensible equals and hashCode
// - serialization
// - companion with apply
// - pattern matching

val bob = Person("Bob", 54)  // no 'new' needed!
```

#### Case Class Constructor Parameters - Special Behavior!

**IMPORTANT**: Case class constructor parameters behave differently from regular classes!

**In regular classes:**
- Without `val`/`var` → parameter is private, only accessible inside class
- With `val` → public immutable field
- With `var` → public mutable field

**In case classes:**
- Parameters are AUTOMATICALLY `val` (public immutable) even without the keyword!

```scala
// Case class - parameters are automatically val
case class Student(name: String, id: Int, grade: Double)

val student = Student("Alice", 101, 95.5)
student.name     // ✓ "Alice" - accessible! (automatically val)
student.id       // ✓ 101 - accessible! (automatically val)
student.grade    // ✓ 95.5 - accessible! (automatically val)
// student.name = "Bob"  // ✗ ERROR - immutable!


// Compare with regular class - parameters NOT accessible without val
class RegularStudent(name: String, id: Int, grade: Double)

val regular = new RegularStudent("Bob", 102, 88.0)
// regular.name   // ✗ ERROR - not accessible!
// regular.id     // ✗ ERROR - not accessible!
```

#### Explicit val in Case Classes

You can explicitly write `val` but it doesn't change anything - they're already `val`:

```scala
case class Book(val title: String, val author: String)
// Same as: case class Book(title: String, author: String)

val book = Book("1984", "Orwell")
book.title   // ✓ "1984"
book.author  // ✓ "Orwell"
```

#### Using var in Case Classes

If you need a mutable field, use `var` explicitly:

```scala
case class Counter(var count: Int)

val counter = Counter(0)
counter.count        // ✓ 0 - accessible
counter.count = 5    // ✓ Works - mutable because of var
counter.count        // ✓ 5


// Mixed: some val, some var
case class User(name: String, var age: Int, var score: Double)
// name is immutable (default val)
// age and score are mutable (explicit var)

val user = User("Charlie", 25, 100.0)
user.name           // ✓ "Charlie"
// user.name = "X"  // ✗ ERROR - immutable
user.age = 26       // ✓ Works - mutable
user.score = 150.0  // ✓ Works - mutable
```

#### Summary Table

| Class Type | Parameter Syntax | Accessible? | Mutable? |
|------------|-----------------|-------------|----------|
| Regular class | `name: String` | ✗ No | ✗ No |
| Regular class | `val name: String` | ✓ Yes | ✗ No |
| Regular class | `var name: String` | ✓ Yes | ✓ Yes |
| **Case class** | `name: String` | ✓ Yes (auto val) | ✗ No |
| **Case class** | `val name: String` | ✓ Yes (explicit val) | ✗ No |
| **Case class** | `var name: String` | ✓ Yes | ✓ Yes |

**Key Takeaway**: In case classes, all constructor parameters are automatically public and immutable (`val`) unless you explicitly use `var` to make them mutable!

### Exception Handling

```scala
try {
  val x: String = null
  x.length
} catch {
  case e: Exception => "some faulty error message"
} finally {
  // execute no matter what
}
```

### Generics

```scala
abstract class MyList[T] {
  def head: T
  def tail: MyList[T]
}

val aList: List[Int] = List(1,2,3)
val first = aList.head  // Int
val rest = aList.tail
val aStringList = List("hello", "Scala")
val firstString = aStringList.head  // String
```

### Key Principles

1. **Immutability**: Operate with immutable values/objects
    - Any modification returns ANOTHER object
    - Benefits: works great in multithreaded/distributed environments

```scala
val reversedList = aList.reverse  // returns NEW list
```

2. **Scala is closest to the OO ideal**

---

## Functional Programming

### Functions as Objects

```scala
// Scala runs on JVM - functions are objects
// FunctionX = Function1, Function2, ... Function22

val simpleIncrementer = new Function1[Int, Int] {
  override def apply(arg: Int): Int = arg + 1
}

simpleIncrementer.apply(23)  // 24
simpleIncrementer(23)        // same - syntactic sugar

// Function with 2 arguments
val stringConcatenator = new Function2[String, String, String] {
  override def apply(arg1: String, arg2: String): String = arg1 + arg2
}

stringConcatenator("I love", " Scala")
```

### Function Syntax Sugar

```scala
// Syntactic sugar for functions
val doubler: Int => Int = (x: Int) => 2 * x
doubler(4)  // 8

// Equivalent to:
val doubler: Function1[Int, Int] = new Function1[Int, Int] {
  override def apply(x: Int) = 2 * x
}
```

**Remember**: ALL SCALA FUNCTIONS ARE INSTANCES OF FUNCTION_X TYPES

### Multiple Parameter Lists / Currying

**Currying** is the technique of transforming a function with multiple arguments into a sequence of functions, each taking a single argument. Scala supports this natively with multiple parameter lists.

#### Basic Syntax

```scala
// Single parameter list (normal)
def add(x: Int, y: Int): Int = x + y
add(3, 4)  // 7

// Multiple parameter lists (curried)
def addCurried(x: Int)(y: Int): Int = x + y
addCurried(3)(4)  // 7
```

#### Why Use Multiple Parameter Lists?

**1. Partial Application** - Create specialized functions by providing some arguments:

```scala
def multiply(x: Int)(y: Int): Int = x * y

val double = multiply(2) _    // partially applied function
val triple = multiply(3) _

double(5)   // 10
triple(5)   // 15


// Practical example: custom formatters
def formatMessage(prefix: String)(message: String): String =
  s"$prefix: $message"

val errorFormatter = formatMessage("ERROR") _
val infoFormatter = formatMessage("INFO") _

errorFormatter("Something went wrong")  // "ERROR: Something went wrong"
infoFormatter("Process completed")      // "INFO: Process completed"
```

**2. Type Inference** - Later parameter lists can use types from earlier ones:

```scala
def foldLeft[A, B](list: List[A], initial: B)(f: (B, A) => B): B =
  list match {
    case Nil => initial
    case head :: tail => foldLeft(tail, f(initial, head))(f)
  }

// Scala can infer types in the function f
val numbers = List(1, 2, 3, 4)
foldLeft(numbers, 0)((acc, x) => acc + x)  // types inferred!
// Compare with single parameter list - would need explicit types
```

**3. Implicit/Using Parameters** - Separate implicit/contextual parameters:

```scala
// Scala 2 style
def greet(name: String)(implicit greeting: String): String =
  s"$greeting, $name!"

implicit val defaultGreeting: String = "Hello"
greet("Alice")  // "Hello, Alice!"

// Scala 3 style
def greet(name: String)(using greeting: String): String =
  s"$greeting, $name!"

given defaultGreeting: String = "Hello"
greet("Bob")  // "Hello, Bob!"
```

**4. Custom Control Structures** - Create DSL-like syntax:

```scala
def transaction[A](db: Database)(code: Database => A): A = {
  db.beginTransaction()
  try {
    val result = code(db)
    db.commit()
    result
  } catch {
    case e: Exception =>
      db.rollback()
      throw e
  }
}

// Usage looks like a control structure
transaction(myDatabase) { db =>
  db.insert("users", user)
  db.update("accounts", account)
}
```

#### Currying Regular Functions

You can convert regular functions to curried form:

```scala
// Regular function
def add(x: Int, y: Int, z: Int): Int = x + y + z

// Manual currying
def addCurried(x: Int)(y: Int)(z: Int): Int = x + y + z

// Or curry an existing function
val curriedAdd = (add _).curried
curriedAdd(1)(2)(3)  // 6

// Partial application
val add5 = curriedAdd(5) _
val add5and10 = add5(10) _
add5and10(3)  // 18
```

#### Real-World Examples

```scala
// Example 1: Configuration-based processing
def processData(config: Config)(data: List[String]): List[String] = {
  data.map(s => if (config.uppercase) s.toUpperCase else s.toLowerCase)
}

val prodConfig = Config(uppercase = true)
val devConfig = Config(uppercase = false)

val prodProcessor = processData(prodConfig) _
val devProcessor = processData(devConfig) _

prodProcessor(List("hello", "world"))  // List("HELLO", "WORLD")
devProcessor(List("hello", "world"))   // List("hello", "world")


// Example 2: Dependency injection
trait UserRepository {
  def findById(id: Int): Option[User]
}

def getUserEmail(repo: UserRepository)(userId: Int): Option[String] =
  repo.findById(userId).map(_.email)

val emailFinder = getUserEmail(myRepository) _
emailFinder(123)  // Some("user@example.com")


// Example 3: Builder pattern
def buildQuery(table: String)(columns: String*)(where: String): String =
  s"SELECT ${columns.mkString(", ")} FROM $table WHERE $where"

val userQuery = buildQuery("users")("id", "name", "email") _
userQuery("age > 18")  // "SELECT id, name, email FROM users WHERE age > 18"
userQuery("active = true")  // "SELECT id, name, email FROM users WHERE active = true"
```

#### Multiple Parameter Lists vs Tuples

```scala
// Using tuple (NOT recommended)
def add(params: (Int, Int)): Int = params._1 + params._2
add((3, 4))  // awkward syntax

// Using multiple parameters (better)
def add(x: Int, y: Int): Int = x + y
add(3, 4)  // cleaner

// Using currying (best for partial application)
def add(x: Int)(y: Int): Int = x + y
val add5 = add(5) _
add5(10)  // 15
```

#### Key Points

- **Multiple parameter lists** = `def func(a: A)(b: B): C`
- **Currying** = transforming multi-arg function into chain of single-arg functions
- **Partial application** = providing some arguments to get a new function
- Use currying for:
    - Partial application
    - Better type inference
    - Separating implicit/using parameters
    - Building DSLs
    - Dependency injection

**Common pattern**: `def method(normalParams)(implicitParams)` or `def method(normalParams)(usingParams)` in Scala 3

### Higher-Order Functions (HOF)

Functions that take functions as arguments or return functions.

```scala
// map: transform each element
val aMappedList = List(1,2,3).map(x => x + 1)
// Result: List(2,3,4)

// flatMap: transform and flatten
val aFlatMappedList = List(1,2,3).flatMap { x =>
  List(x, 2 * x)
}
// Result: List(1,2,2,4,3,6)

// filter: keep only elements matching predicate
val aFilteredList = List(1,2,3,4,5).filter(_ <= 3)
// Result: List(1,2,3)
// _ is shorthand for x => x

// Complex example: all pairs
val allPairs = List(1,2,3).flatMap(number =>
  List('a', 'b', 'c').map(letter => s"$number-$letter")
)
// Result: List("1-a", "1-b", "1-c", "2-a", "2-b", "2-c", "3-a", "3-b", "3-c")
```

### For Comprehensions

**For comprehensions** provide a more readable syntax for working with collections. They can be used in two ways:
- **With `yield`** - transforms and returns a new collection
- **Without `yield`** - performs side effects (iteration only)

#### Basic For with yield

```scala
// Syntactic sugar for map/flatMap/filter chains
val alternativePairs = for {
  number <- List(1,2,3)
  letter <- List('a', 'b', 'c')
} yield s"$number-$letter"
// Result: List("1-a", "1-b", "1-c", "2-a", "2-b", "2-c", "3-a", "3-b", "3-c")

// Equivalent to flatMap/map chain above
```

#### For without yield (Iteration - Side Effects)

When you omit `yield`, the for loop performs side effects and returns `Unit`:

```scala
// Simple iteration - prints each element
for (i <- 1 to 5) {
  println(i)
}
// Prints: 1, 2, 3, 4, 5


// Iterate over a list
val names = List("Alice", "Bob", "Charlie")
for (name <- names) {
  println(s"Hello, $name!")
}
// Prints:
// Hello, Alice!
// Hello, Bob!
// Hello, Charlie!


// Nested iteration
for {
  i <- 1 to 3
  j <- 1 to 3
} {
  println(s"$i x $j = ${i * j}")
}
// Prints all multiplication results (1x1, 1x2, 1x3, 2x1, ...)
```

#### For with Filters (Guards)

```scala
// With single filter
val evenNumbers = for {
  i <- 1 to 10
  if i % 2 == 0
} yield i
// Result: Vector(2, 4, 6, 8, 10)


// With multiple filters
val result = for {
  i <- 1 to 20
  if i % 2 == 0      // only even numbers
  if i % 3 == 0      // and divisible by 3
} yield i
// Result: Vector(6, 12, 18)


// Without yield - just print filtered values
for {
  i <- 1 to 10
  if i % 2 == 0
} {
  println(s"Even number: $i")
}
```

#### For with Intermediate Variables

```scala
// You can define intermediate values in for comprehensions
val result = for {
  x <- 1 to 3
  y <- 1 to 3
  sum = x + y        // intermediate value
  if sum % 2 == 0    // filter using intermediate value
} yield (x, y, sum)
// Result: Vector((1,1,2), (1,3,4), (2,2,4), (3,1,4), (3,3,6))


// Another example
val names = List("alice", "bob", "charlie")
val greetings = for {
  name <- names
  upperName = name.toUpperCase  // intermediate value
  if upperName.length > 3
} yield s"Hello, $upperName!"
// Result: List("Hello, ALICE!", "Hello, CHARLIE!")
```

#### Practical Examples

```scala
// Example 1: Working with Option
val maybeX = Some(5)
val maybeY = Some(10)

val result = for {
  x <- maybeX
  y <- maybeY
} yield x + y
// Result: Some(15)


// Example 2: Reading multiple optional values
case class User(name: String, age: Option[Int], email: Option[String])

val users = List(
  User("Alice", Some(25), Some("alice@example.com")),
  User("Bob", None, Some("bob@example.com")),
  User("Charlie", Some(30), None)
)

// Get all users with both age and email
val completeUsers = for {
  user <- users
  age <- user.age
  email <- user.email
} yield (user.name, age, email)
// Result: List(("Alice", 25, "alice@example.com"))


// Example 3: Cartesian product
val colors = List("Red", "Green", "Blue")
val sizes = List("S", "M", "L")

val products = for {
  color <- colors
  size <- sizes
} yield s"$color-$size"
// Result: List("Red-S", "Red-M", "Red-L", "Green-S", ...)


// Example 4: Processing nested structures
val matrix = List(
  List(1, 2, 3),
  List(4, 5, 6),
  List(7, 8, 9)
)

val flattenedDoubled = for {
  row <- matrix
  element <- row
} yield element * 2
// Result: List(2, 4, 6, 8, 10, 12, 14, 16, 18)


// Example 5: With range and filter
val pythagoras = for {
  a <- 1 to 20
  b <- a to 20      // b starts from a (avoid duplicates)
  c <- b to 20      // c starts from b
  if a*a + b*b == c*c  // Pythagorean theorem
} yield (a, b, c)
// Result: Vector((3,4,5), (5,12,13), (6,8,10), ...)
```

#### Key Points

- **With `yield`** → returns a new collection (transformation)
- **Without `yield`** → performs side effects, returns Unit (iteration)
- Filters use `if` (called guards)
- Can define intermediate variables with `=`
- Works with any type that has `map`, `flatMap`, `filter` methods (Option, List, Future, etc.)

**Equivalent transformations**:
```scala
// For comprehension
for {
  x <- List(1,2,3)
  y <- List(10,20)
} yield x + y

// Is the same as:
List(1,2,3).flatMap(x =>
  List(10,20).map(y => x + y)
)
```

### Collections

#### Lists

```scala
val aList = List(1,2,3,4,5)
val firstElement = aList.head          // 1
val rest = aList.tail                  // List(2,3,4,5)
val aPrependedList = 0 :: aList        // List(0,1,2,3,4,5)
val anExtendedList = 0 +: aList :+ 6   // List(0,1,2,3,4,5,6)
```

#### Sequences

```scala
val aSequence: Seq[Int] = Seq(1,2,3)
val accessedElement = aSequence(1)  // 2 (element at index 1)
```

#### Vectors

```scala
val aVector = Vector(1,2,3,4,5)  // fast Seq implementation
```

#### Sets

```scala
val aSet = Set(1,2,3,4,1,2,3)  // Set(1,2,3,4) - no duplicates!
val setHas5 = aSet.contains(5)  // false
val anAddedSet = aSet + 5       // Set(1,2,3,4,5)
val aRemovedSet = aSet - 3      // Set(1,2,4)
```

#### Ranges

```scala
val aRange = 1 to 1000
val twoByTwo = aRange.map(x => 2 * x).toList  // List(2,4,6,...,2000)
```

#### Tuples

```scala
// Groups of values under the same value
val aTuple = ("Bon Jovi", "Rock", 1982)
```

#### Maps

```scala
val aPhonebook: Map[String, Int] = Map(
  ("Daniel", 6437812),
  "Jane" -> 327285  // same as ("Jane", 327285)
)
```

---

## Pattern Matching

### Basic Pattern Matching

```scala
// Like switch but much more powerful!
val anInteger = 55
val order = anInteger match {
  case 1 => "first"
  case 2 => "second"
  case 3 => "third"
  case _ => anInteger + "th"  // default case
}

// Pattern matching is an EXPRESSION (returns a value)
```

### Case Class Decomposition

```scala
case class Person(name: String, age: Int)
val bob = Person("Bob", 43)

val personGreeting = bob match {
  case Person(n, a) => s"Hi, my name is $n and I am $a years old."
  case _ => "Something else"
}
```

### Deconstructing Tuples

```scala
val aTuple = ("Bon Jovi", "Rock")
val bandDescription = aTuple match {
  case (band, genre) => s"$band belongs to the genre $genre"
  case _ => "I don't know what you're talking about"
}
```

### Decomposing Lists

```scala
val aList = List(1,2,3)
val listDescription = aList match {
  case List(_, 2, _) => "List containing 2 on its second position"
  case _ => "unknown list"
}
```

### Pattern Matching with Option (Some/None)

**Option** is Scala's way to handle nullable values safely. Pattern matching is perfect for handling Options:

```scala
// Basic Option pattern matching
val maybeValue: Option[Int] = Some(42)

val result = maybeValue match {
  case Some(value) => s"Got a value: $value"
  case None => "Got nothing"
}
// Result: "Got a value: 42"


// With None
val emptyValue: Option[String] = None

val result2 = emptyValue match {
  case Some(str) => s"String length: ${str.length}"
  case None => "No string available"
}
// Result: "No string available"


// Pattern matching inside case classes with Option
case class User(name: String, age: Option[Int], email: Option[String])

val user1 = User("Alice", Some(25), Some("alice@example.com"))
val user2 = User("Bob", None, Some("bob@example.com"))

def describeUser(user: User): String = user match {
  case User(name, Some(age), Some(email)) =>
    s"$name is $age years old, email: $email"

  case User(name, Some(age), None) =>
    s"$name is $age years old, no email"

  case User(name, None, Some(email)) =>
    s"$name's age unknown, email: $email"

  case User(name, None, None) =>
    s"$name - no details available"
}

describeUser(user1)  // "Alice is 25 years old, email: alice@example.com"
describeUser(user2)  // "Bob's age unknown, email: bob@example.com"


// Nested Option pattern matching
case class Address(city: String, zipCode: Option[String])
case class Person(name: String, address: Option[Address])

val person = Person("John", Some(Address("NYC", Some("10001"))))

person.address match {
  case Some(Address(city, Some(zip))) =>
    s"Lives in $city, ZIP: $zip"
  case Some(Address(city, None)) =>
    s"Lives in $city, no ZIP code"
  case None =>
    "Address unknown"
}
// Result: "Lives in NYC, ZIP: 10001"
```

### Pattern Matching with null

**IMPORTANT**: Avoid using `null` in Scala! Use `Option` instead. However, when dealing with Java libraries, you may encounter null:

```scala
// Matching null (not recommended, but sometimes necessary)
val possiblyNull: String = null

val result = possiblyNull match {
  case null => "It's null!"
  case s => s"Got a string: $s"
}
// Result: "It's null!"


// Better approach: convert null to Option immediately
def safeName(name: String): String = Option(name) match {
  case Some(n) => s"Hello, $n"
  case None => "Hello, stranger"
}

safeName("Alice")  // "Hello, Alice"
safeName(null)     // "Hello, stranger"


// Pattern matching with guards to handle null in complex cases
case class Data(value: String, metadata: String)

def processData(data: Data): String = data match {
  case Data(null, _) => "Value is null"
  case Data(v, null) => s"Value: $v, no metadata"
  case Data(v, m) if v.isEmpty => "Value is empty"
  case Data(v, m) => s"Value: $v, Metadata: $m"
}

processData(Data(null, "meta"))    // "Value is null"
processData(Data("test", null))    // "Value: test, no metadata"
processData(Data("", "meta"))      // "Value is empty"
processData(Data("hello", "info")) // "Value: hello, Metadata: info"
```

**Best Practice**:
```scala
// DON'T do this:
val name: String = null

// DO this instead:
val name: Option[String] = None

// When receiving from Java:
val javaValue: String = someJavaMethod()  // might be null
val safeValue: Option[String] = Option(javaValue)  // converts null to None
```

**Important**:
- PM tries all cases in sequence
- If no match found, throws MatchError
- Always include a default case (_) to avoid errors
- Use `Option` instead of `null` for Scala code
- Pattern match on `Some/None` for safe null handling

---

## Advanced Topics

### Lazy Evaluation

```scala
lazy val aLazyValue = 2

lazy val lazyValueWithSideEffect = {
  println("I am so very lazy!")
  43
}

val eagerValue = lazyValueWithSideEffect + 1
// "I am so very lazy!" printed only when lazyValueWithSideEffect is accessed

// Useful in infinite collections
```

### Option Type

**Option** is a pseudo-collection with at most one element: `Some(value)` or `None`. It's Scala's safe way to handle nullable values.

#### Basic Option Usage

```scala
// Handle null values safely
def methodWhichCanReturnNull(): String = "hello, Scala"

val anOption = Option(methodWhichCanReturnNull())  // Some("hello, Scala")
// Option = collection with at most one element: Some(value) or None

val stringProcessing = anOption match {
  case Some(string) => s"I have obtained a valid string: $string"
  case None => "I obtained nothing"
}
```

#### Option as a Pseudo-Collection

Option behaves like a collection with map, flatMap, filter, and more:

```scala
// map: transform the value if present
val someValue: Option[Int] = Some(5)
val mappedValue = someValue.map(_ * 2)  // Some(10)

val noneValue: Option[Int] = None
val mappedNone = noneValue.map(_ * 2)   // None


// flatMap: transform and flatten
def divide(a: Int, b: Int): Option[Int] =
  if (b == 0) None else Some(a / b)

val result1 = Some(10).flatMap(x => divide(x, 2))  // Some(5)
val result2 = Some(10).flatMap(x => divide(x, 0))  // None
val result3 = None.flatMap(x => divide(10, 2))     // None


// filter: keep value only if predicate is true
val someNumber: Option[Int] = Some(42)
val filtered1 = someNumber.filter(_ > 40)   // Some(42)
val filtered2 = someNumber.filter(_ > 50)   // None


// getOrElse: provide default value if None
val value1 = Some(5).getOrElse(0)     // 5
val value2 = None.getOrElse(0)        // 0


// orElse: provide alternative Option if None
val opt1: Option[Int] = Some(5)
val opt2: Option[Int] = None
val opt3: Option[Int] = Some(10)

opt1.orElse(opt3)  // Some(5)
opt2.orElse(opt3)  // Some(10)


// fold: like getOrElse but with transformation
val result = Some(5).fold(0)(_ * 2)   // 10
val result2 = None.fold(0)(_ * 2)     // 0


// foreach: execute side effect if value present
Some(42).foreach(x => println(s"Value: $x"))  // prints "Value: 42"
None.foreach(x => println(s"Value: $x"))      // does nothing


// isDefined and isEmpty
val opt: Option[Int] = Some(42)
opt.isDefined  // true
opt.isEmpty    // false
None.isEmpty   // true
```

#### Chaining Option Operations

```scala
// Example: Safe user lookup and processing
case class User(id: Int, name: String, age: Int)

def findUser(id: Int): Option[User] = {
  if (id == 1) Some(User(1, "Alice", 25))
  else None
}

def validateAge(user: User): Option[User] = {
  if (user.age >= 18) Some(user)
  else None
}

// Chaining with flatMap
val result = findUser(1)
  .flatMap(validateAge)
  .map(user => s"Welcome, ${user.name}!")
  .getOrElse("Access denied")
// Result: "Welcome, Alice!"


// Using for comprehension (cleaner syntax)
val greeting = for {
  user <- findUser(1)
  validUser <- validateAge(user)
} yield s"Welcome, ${validUser.name}!"

println(greeting.getOrElse("Access denied"))
```

#### Option with Collections

```scala
// Filtering Options from a list
val values: List[Option[Int]] = List(Some(1), None, Some(3), None, Some(5))

// Extract all present values
val allValues = values.flatten  // List(1, 3, 5)

// Or using flatMap
val extracted = values.flatMap(opt => opt)  // List(1, 3, 5)


// Safe head operation
val list = List(1, 2, 3)
val head = list.headOption  // Some(1)
val emptyHead = List().headOption  // None
```

### Try Type

**Try** is a pseudo-collection that represents a computation that may succeed (Success) or fail (Failure). It's used for exception handling in a functional way.

#### Basic Try Usage

```scala
import scala.util.{Try, Success, Failure}

def methodWhichCanThrowException(): String = throw new RuntimeException

val aTry = Try(methodWhichCanThrowException())
// Try = collection with either a value or an exception

val anotherStringProcessing = aTry match {
  case Success(validValue) => s"I have obtained a valid string: $validValue"
  case Failure(ex) => s"I have obtained an exception: $ex"
}
```

#### Try as a Pseudo-Collection

Try behaves like a collection with map, flatMap, filter:

```scala
import scala.util.{Try, Success, Failure}

// map: transform the value if successful
val tryValue: Try[Int] = Try(10 / 2)
val mapped = tryValue.map(_ * 2)  // Success(10)

val tryFailed: Try[Int] = Try(10 / 0)
val mappedFailed = tryFailed.map(_ * 2)  // Failure(ArithmeticException)


// flatMap: chain operations that may fail
def divide(a: Int, b: Int): Try[Int] = Try(a / b)

val result1 = Try(10).flatMap(x => divide(x, 2))  // Success(5)
val result2 = Try(10).flatMap(x => divide(x, 0))  // Failure(ArithmeticException)


// filter: keep value only if predicate is true
val tryNumber = Try(42)
val filtered1 = tryNumber.filter(_ > 40)  // Success(42)
val filtered2 = tryNumber.filter(_ > 50)  // Failure(NoSuchElementException)


// getOrElse: provide default value if Failure
val value1 = Try(10 / 2).getOrElse(0)     // 5
val value2 = Try(10 / 0).getOrElse(0)     // 0


// orElse: provide alternative Try if Failure
val try1: Try[Int] = Try(10 / 0)
val try2: Try[Int] = Try(5)

try1.orElse(try2)  // Success(5)


// fold: handle both Success and Failure cases
val result = Try(10 / 2).fold(
  ex => s"Error: ${ex.getMessage}",  // Failure case
  value => s"Success: $value"        // Success case
)
// "Success: 5"


// foreach: execute side effect if successful
Try(42).foreach(x => println(s"Value: $x"))      // prints "Value: 42"
Try(10 / 0).foreach(x => println(s"Value: $x"))  // does nothing


// isSuccess and isFailure
val tryVal = Try(42)
tryVal.isSuccess  // true
tryVal.isFailure  // false


// recover: handle specific exceptions
val recovered = Try(10 / 0).recover {
  case _: ArithmeticException => 0
}
// Success(0)


// recoverWith: handle exceptions with another Try
val recoveredWith = Try(10 / 0).recoverWith {
  case _: ArithmeticException => Try(5)
}
// Success(5)
```

#### Chaining Try Operations

```scala
import scala.util.Try

// Example: Safe arithmetic operations
def parseInt(s: String): Try[Int] = Try(s.toInt)
def divide(a: Int, b: Int): Try[Int] = Try(a / b)
def sqrt(n: Int): Try[Double] = Try {
  if (n < 0) throw new IllegalArgumentException("Negative number")
  else math.sqrt(n)
}

// Chaining with flatMap
val result = parseInt("10")
  .flatMap(x => divide(x, 2))
  .flatMap(x => sqrt(x))
  .map(x => s"Result: $x")
  .getOrElse("Computation failed")
// Result: "Result: 2.23606797749979"


// Using for comprehension
val computation = for {
  x <- parseInt("10")
  y <- divide(x, 2)
  z <- sqrt(y)
} yield s"Result: $z"

println(computation.getOrElse("Computation failed"))


// Handling failures gracefully
val safeComputation = for {
  x <- parseInt("10")
  y <- divide(x, 0).recover { case _: ArithmeticException => 1 }
  z <- sqrt(y)
} yield z

println(safeComputation)  // Success(1.0)
```

#### Try with Collections

```scala
import scala.util.Try

// Parse multiple strings safely
val strings = List("1", "2", "abc", "4")

// Collect successful parses
val parsed = strings.map(s => Try(s.toInt))
// List(Success(1), Success(2), Failure(NumberFormatException), Success(4))

val successfulValues = parsed.collect {
  case Success(value) => value
}
// List(1, 2, 4)


// Alternative: filter and map
val validNumbers = parsed.filter(_.isSuccess).map(_.get)
// List(1, 2, 4)


// Convert Try to Option
val asOption = Try(10 / 2).toOption  // Some(5)
val failedOption = Try(10 / 0).toOption  // None
```

#### Option vs Try

```scala
// Option: for values that may or may not exist (absence)
def findUser(id: Int): Option[User] = ???

// Try: for computations that may fail (exceptions)
def parseJson(json: String): Try[JsonObject] = ???


// Converting between them
val opt: Option[Int] = Some(5)
val tryFromOpt: Try[Int] = opt
  .map(Success(_))
  .getOrElse(Failure(new NoSuchElementException("None")))

val tryVal: Try[Int] = Try(10 / 2)
val optFromTry: Option[Int] = tryVal.toOption
```

**Key Points**:
- Both Option and Try work as pseudo-collections with map, flatMap, filter
- Use **Option** for optional values (something may be absent)
- Use **Try** for operations that may throw exceptions
- Both support for comprehensions for cleaner chaining
- Both provide safe ways to handle edge cases without explicit null checks or try-catch blocks

### Future (Asynchronous Programming)

**Future** is Scala's way to handle asynchronous computations. A Future represents a value that may not be available yet but will be computed at some point. It's a pseudo-collection containing a value when evaluated.

#### Basic Future Usage

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global
import scala.util.{Success, Failure}

// Create a simple Future - runs asynchronously
val aFuture: Future[Int] = Future {
  println("Loading...")
  Thread.sleep(1000)
  println("I have computed a value.")
  67
}
// Future starts executing immediately on creation

// Main thread continues without waiting
println("Waiting for result...")

// IMPORTANT: You need an ExecutionContext (thread pool) to run Futures
// ExecutionContext.Implicits.global provides a default thread pool
```

#### Creating Futures

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global

// From a value - creates an already completed Future
val immediateValue: Future[Int] = Future.successful(42)

// From an exception - creates a failed Future
val failedFuture: Future[Int] = Future.failed(new RuntimeException("Oops!"))

// From computation that might throw
val computation: Future[Int] = Future {
  10 / 2  // computed asynchronously
}

val failingComputation: Future[Int] = Future {
  10 / 0  // throws ArithmeticException - Future will be failed
}
```

#### Future Callbacks

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global
import scala.util.{Success, Failure}

val myFuture: Future[Int] = Future {
  Thread.sleep(500)
  42
}

// onComplete - handles both success and failure
myFuture.onComplete {
  case Success(value) => println(s"Success: $value")
  case Failure(exception) => println(s"Failed: ${exception.getMessage}")
}

// foreach - only handles success
myFuture.foreach { value: Int =>
  println(s"Got value: $value")
}

// failed - converts to Future[Throwable] on failure
val failedResult: Future[Throwable] = myFuture.failed
failedResult.foreach { ex: Throwable =>
  println(s"Exception was: ${ex.getMessage}")
}
```

#### Transforming Futures

Futures work as pseudo-collections with map, flatMap, filter:

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global

// map: transform the result when Future completes
val future1: Future[Int] = Future { 21 }
val doubled: Future[Int] = future1.map { x: Int => x * 2 }
// Result (when complete): 42

// flatMap: chain Futures - flatten nested Futures
def fetchUserId(): Future[Int] = Future {
  Thread.sleep(100)
  123
}

def fetchUserName(id: Int): Future[String] = Future {
  Thread.sleep(100)
  s"User_$id"
}

val userName: Future[String] = fetchUserId().flatMap { id: Int =>
  fetchUserName(id)
}
// Result (when complete): "User_123"

// filter: keep value only if predicate is true
val number: Future[Int] = Future { 42 }
val filtered: Future[Int] = number.filter { n: Int => n > 40 }
// Success(42)

val filtered2: Future[Int] = number.filter { n: Int => n > 50 }
// Failure(NoSuchElementException)

// withFilter: same as filter, used in for comprehensions
val result: Future[Int] = number.withFilter { n: Int => n > 40 }

// recover: handle failure by providing a fallback value
val risky: Future[Int] = Future { 10 / 0 }
val recovered: Future[Int] = risky.recover {
  case _: ArithmeticException => 0
}
// Result: Success(0)

// recoverWith: handle failure with another Future
val recoveredWith: Future[Int] = risky.recoverWith {
  case _: ArithmeticException => Future.successful(0)
}
// Result: Success(0)

// fallbackTo: use alternative Future if this one fails
val backup: Future[Int] = Future { 99 }
val withFallback: Future[Int] = risky.fallbackTo(backup)
// Result: Success(99)
```

#### Combining Multiple Futures

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global

// zip: combine two Futures into a tuple
val future1: Future[Int] = Future { 10 }
val future2: Future[String] = Future { "Hello" }
val combined: Future[(Int, String)] = future1.zip(future2)
// Result: (10, "Hello")

// zipWith: combine two Futures with a function
val summed: Future[Int] = Future { 10 }.zipWith(Future { 20 }) {
  (a: Int, b: Int) => a + b
}
// Result: 30

// sequence: convert List[Future[T]] to Future[List[T]]
val futures: List[Future[Int]] = List(
  Future { 1 },
  Future { 2 },
  Future { 3 }
)
val sequenced: Future[List[Int]] = Future.sequence(futures)
// Result: List(1, 2, 3)

// traverse: map and sequence in one step
val numbers: List[Int] = List(1, 2, 3)
val futureResults: Future[List[Int]] = Future.traverse(numbers) { n: Int =>
  Future { n * 2 }
}
// Result: List(2, 4, 6)

// firstCompletedOf: returns first Future to complete
val slow: Future[Int] = Future { Thread.sleep(1000); 1 }
val fast: Future[Int] = Future { Thread.sleep(100); 2 }
val first: Future[Int] = Future.firstCompletedOf(List(slow, fast))
// Result: 2 (completes first)
```

#### For Comprehensions with Futures

For comprehensions make chaining Futures much more readable:

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global

// Sequential Future execution
def fetchUserId(): Future[Int] = Future {
  Thread.sleep(100)
  123
}

def fetchUserAge(id: Int): Future[Int] = Future {
  Thread.sleep(100)
  25
}

def fetchUserName(id: Int): Future[String] = Future {
  Thread.sleep(100)
  s"User_$id"
}

// Using for comprehension - much cleaner!
val userInfo: Future[String] = for {
  id <- fetchUserId()          // waits for id
  age <- fetchUserAge(id)      // then fetches age
  name <- fetchUserName(id)    // then fetches name
} yield s"$name is $age years old"
// Result: "User_123 is 25 years old"

// Equivalent to flatMap/map chain:
val userInfoVerbose: Future[String] = fetchUserId().flatMap { id: Int =>
  fetchUserAge(id).flatMap { age: Int =>
    fetchUserName(id).map { name: String =>
      s"$name is $age years old"
    }
  }
}

// With filters
val adultUserInfo: Future[String] = for {
  id <- fetchUserId()
  age <- fetchUserAge(id)
  if age >= 18                 // filter: only adults
  name <- fetchUserName(id)
} yield s"Adult: $name, age $age"

// Parallel execution - start Futures before for comprehension
val futureId: Future[Int] = fetchUserId()
val futureAge: Future[Int] = fetchUserAge(123)
val futureName: Future[String] = fetchUserName(123)

val parallelResult: Future[String] = for {
  id <- futureId      // these all started already
  age <- futureAge    // so they run in parallel
  name <- futureName
} yield s"$name is $age years old"
```

#### Promise - Creating Futures Manually

**Promise** is a writable, single-assignment container that completes a Future. Use it when you need to complete a Future from outside code (e.g., callbacks, event handlers).

```scala
import scala.concurrent.{Future, Promise}
import scala.concurrent.ExecutionContext.Implicits.global
import scala.util.{Success, Failure, Try}

// Basic Promise usage
val promise: Promise[Int] = Promise[Int]()
val future: Future[Int] = promise.future  // get Future from Promise

// Complete the Promise with a value
promise.success(42)
// Now future contains 42

// Or complete with failure
val failedPromise: Promise[Int] = Promise[Int]()
failedPromise.failure(new RuntimeException("Something went wrong"))

// complete - with Try (Success or Failure)
val promise2: Promise[Int] = Promise[Int]()
promise2.complete(Success(100))
// or
promise2.complete(Failure(new Exception("Error")))

// trySuccess/tryFailure - returns Boolean (false if already completed)
val promise3: Promise[Int] = Promise[Int]()
val wasCompleted1: Boolean = promise3.trySuccess(10)  // true
val wasCompleted2: Boolean = promise3.trySuccess(20)  // false - already completed

// completeWith - complete with result of another Future
val promise4: Promise[Int] = Promise[Int]()
val otherFuture: Future[Int] = Future { 99 }
promise4.completeWith(otherFuture)
```

#### Practical Promise Example

```scala
import scala.concurrent.{Future, Promise}
import scala.concurrent.ExecutionContext.Implicits.global

// Simulate async API with callbacks
def asyncApiCall(callback: Int => Unit): Unit = {
  // Simulates external async operation
  new Thread {
    override def run(): Unit = {
      Thread.sleep(500)
      callback(42)
    }
  }.start()
}

// Wrap callback-based API with Promise
def asyncApiCallAsFuture(): Future[Int] = {
  val promise: Promise[Int] = Promise[Int]()

  asyncApiCall { result: Int =>
    promise.success(result)
  }

  promise.future
}

// Now we can use it as a Future!
val result: Future[Int] = asyncApiCallAsFuture()
result.foreach { value: Int =>
  println(s"Got result: $value")
}

// More complex: Promise with timeout
def fetchWithTimeout(timeoutMs: Long): Future[String] = {
  val promise: Promise[String] = Promise[String]()

  // Actual work
  Future {
    Thread.sleep(1000)
    "Data loaded"
  }.onComplete { result: Try[String] =>
    promise.tryComplete(result)
  }

  // Timeout
  Future {
    Thread.sleep(timeoutMs)
    promise.tryFailure(new Exception("Timeout!"))
  }

  promise.future
}

val timedResult: Future[String] = fetchWithTimeout(500)
timedResult.onComplete {
  case Success(data) => println(s"Success: $data")
  case Failure(ex) => println(s"Failed: ${ex.getMessage}")
}
// Prints: "Failed: Timeout!" (because timeout is 500ms, work takes 1000ms)
```

#### Producer-Consumer with Promise

```scala
import scala.concurrent.{Future, Promise}
import scala.concurrent.ExecutionContext.Implicits.global

// Producer-consumer pattern
def producer(promise: Promise[Int]): Unit = {
  Future {
    println("Producer: computing...")
    Thread.sleep(500)
    val result: Int = 42
    println(s"Producer: done, sending $result")
    promise.success(result)
  }
}

def consumer(future: Future[Int]): Unit = {
  future.foreach { value: Int =>
    println(s"Consumer: received $value")
  }
}

val promise: Promise[Int] = Promise[Int]()
val future: Future[Int] = promise.future

consumer(future)   // Consumer waits for value
producer(promise)  // Producer provides value

// Output:
// Producer: computing...
// Producer: done, sending 42
// Consumer: received 42
```

#### Waiting for Futures (Blocking)

**WARNING**: Blocking defeats the purpose of async programming. Use only when absolutely necessary (e.g., end of program, tests).

```scala
import scala.concurrent.{Future, Await}
import scala.concurrent.duration._
import scala.concurrent.ExecutionContext.Implicits.global

val future: Future[Int] = Future {
  Thread.sleep(500)
  42
}

// Await.result - blocks until Future completes or timeout
val result: Int = Await.result(future, 2.seconds)  // 42

// Await.ready - blocks until complete, returns the Future itself
val readyFuture: Future[Int] = Await.ready(future, 2.seconds)

// isCompleted - check without blocking
val isDone: Boolean = future.isCompleted  // true or false

// value - returns Option[Try[T]] if completed, None otherwise
val maybeResult: Option[Try[Int]] = future.value
// Some(Success(42)) if completed successfully
// Some(Failure(exception)) if failed
// None if not yet completed
```

#### Best Practices

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global

// 1. Always use an ExecutionContext
// Don't: (will not compile)
// val future = Future { 42 }

// Do:
val future: Future[Int] = Future { 42 }(ExecutionContext.global)
// or use import: import scala.concurrent.ExecutionContext.Implicits.global

// 2. Prefer transformations over callbacks
// Don't:
val future1: Future[Int] = Future { 10 }
future1.foreach { x: Int =>
  val doubled: Int = x * 2
  println(doubled)
}

// Do:
val future2: Future[Int] = Future { 10 }
val doubled: Future[Int] = future2.map { x: Int => x * 2 }
doubled.foreach { x: Int => println(x) }

// 3. Use for comprehensions for multiple Futures
// Don't:
fetchUserId().flatMap { id: Int =>
  fetchUserAge(id).flatMap { age: Int =>
    fetchUserName(id).map { name: String =>
      (id, age, name)
    }
  }
}

// Do:
for {
  id <- fetchUserId()
  age <- fetchUserAge(id)
  name <- fetchUserName(id)
} yield (id, age, name)

// 4. Handle errors gracefully
val safe: Future[Int] = Future { 10 / 0 }.recover {
  case _: ArithmeticException => 0
}

// 5. Use Promise for bridging callback-based APIs
def callbackApi(callback: String => Unit): Unit = ???

def asFuture(): Future[String] = {
  val promise: Promise[String] = Promise[String]()
  callbackApi { result: String => promise.success(result) }
  promise.future
}
```

#### Key Points

- **Future**: read-only placeholder for async computation
- **Promise**: writable, single-assignment container that completes a Future
- Futures compose with map, flatMap, filter (like Option and Try)
- Use for comprehensions for cleaner async code
- Promises bridge callback-based APIs to Future-based code
- Avoid blocking with Await unless necessary
- Always need an ExecutionContext (thread pool)
- Futures start executing immediately on creation
- A Promise can only be completed once

### Implicits Basics (Scala 2)

#### Implicit Arguments

```scala
def aMethodWithImplicitArgs(implicit arg: Int) = arg + 1

implicit val myImplicitInt = 46

println(aMethodWithImplicitArgs)  // automatically uses myImplicitInt
```

#### Implicit Conversions (Enrichment)

```scala
implicit class MyRichInteger(n: Int) {
  def isEven() = n % 2 == 0
}

println(23.isEven())  // new MyRichInteger(23).isEven()

// Use carefully - adds methods to existing types
```

### Contextual Abstractions (Scala 3)

**Contextual Abstractions** are Scala 3's cleaner and more powerful replacement for implicits. They provide two main features: **context parameters** (given/using) and **extension methods**.

#### Context Parameters and Arguments (given/using)

Context parameters allow you to pass values implicitly based on their type.

```scala
// Basic example: custom ordering
val aList = List(2, 1, 3, 4)
val anOrderedList = aList.sorted  // uses a contextual Ordering

// Define a given instance (replaces implicit val)
given descendingOrdering: Ordering[Int] = Ordering.fromLessThan(_ > _)
// Now sorted uses descendingOrdering automatically

val descendingList = aList.sorted  // List(4, 3, 2, 1)
```

#### Type Classes Pattern

Context parameters are perfect for implementing type classes:

```scala
// Define a type class
trait Combinator[A] {
  def combine(x: A, y: A): A
}

// Method that requires a Combinator for type A
def combineAll[A](list: List[A])(using combinator: Combinator[A]): A =
  list.reduce((a, b) => combinator.combine(a, b))

// Provide a given instance for Int
given intCombinator: Combinator[Int] = new Combinator[Int] {
  override def combine(x: Int, y: Int): Int = x + y
}

val aList = List(1, 2, 3, 4)
val theSum = combineAll(aList)  // 10 - uses intCombinator automatically
```

#### Where Scala Looks for Given Instances

Scala searches for `given` instances in this order:
1. **Local scope** - givens defined in the current scope
2. **Imported scope** - givens explicitly imported
3. **Companions of all types involved** - companion objects of:
    - The type being used (e.g., companion of `List`)
    - Type parameters (e.g., companion of `Int`)

```scala
// Example: given in companion object
trait JsonSerializer[A] {
  def toJson(value: A): String
}

case class Person(name: String, age: Int)

object Person {
  // This given is automatically found when Person is used
  given personSerializer: JsonSerializer[Person] =
  new JsonSerializer[Person] {
    def toJson(p: Person): String =
      s"""{"name":"${p.name}","age":${p.age}}"""
  }
}

def serialize[A](value: A)(using serializer: JsonSerializer[A]): String =
  serializer.toJson(value)

val alice = Person("Alice", 25)
serialize(alice)  // finds personSerializer in Person companion automatically
```

#### Context Bounds

Context bounds provide a shorthand for context parameters:

```scala
// Verbose version with explicit using parameter
def combineAll_v1[A](list: List[A])(using combinator: Combinator[A]): A =
  list.reduce(combinator.combine)

// Anonymous using parameter (no name needed if not used directly)
def combineAll_v2[A](list: List[A])(using Combinator[A]): A = ???

// Context bound - shortest syntax
def combineAll_v3[A : Combinator](list: List[A]): A = ???
// The [A : Combinator] syntax means "A must have a Combinator[A] available"
```

To access the contextual instance with context bounds, use `summon`:

```scala
def combineAll_v4[A : Combinator](list: List[A]): A = {
  val combinator = summon[Combinator[A]]  // retrieves the given instance
  list.reduce(combinator.combine)
}
```

#### Extension Methods

Extension methods allow you to add methods to existing types without inheritance:

```scala
// Simple extension method
case class Person(name: String) {
  def greet(): String = s"Hi, my name is $name, I love Scala!"
}

extension (string: String)
  def greet(): String = new Person(string).greet()

val greeting = "Daniel".greet()  // "Hi, my name is Daniel, I love Scala!"


// Generic extension method
extension [A] (list: List[A])
  def combineAllValues(using combinator: Combinator[A]): A =
    list.reduce(combinator.combine)

val numbers = List(1, 2, 3, 4)
val sum = numbers.combineAllValues  // 10 - uses intCombinator
```

#### Multiple Extension Methods

You can group multiple extension methods together:

```scala
extension (s: String) {
  def toInt: Option[Int] = s.toIntOption

  def isPalindrome: Boolean = s == s.reverse

  def repeat(n: Int): String = s * n
}

"123".toInt           // Some(123)
"abc".toInt           // None
"racecar".isPalindrome  // true
"Ha".repeat(3)        // "HaHaHa"
```

#### Extension Methods with Type Parameters

```scala
extension [A] (list: List[A]) {
  // Safe second element access
  def secondOption: Option[A] =
    if (list.length >= 2) Some(list(1)) else None

  // Group into pairs
  def groupPairs: List[(A, A)] =
    list.grouped(2).collect { case List(a, b) => (a, b) }.toList
}

List(1, 2, 3).secondOption      // Some(2)
List(1).secondOption            // None
List(1, 2, 3, 4).groupPairs     // List((1,2), (3,4))
```

#### Use Cases for Context Parameters

1. **Type Classes**: Define behavior for types (like Ordering, JsonSerializer)
2. **Dependency Injection**: Pass dependencies implicitly
3. **Context-Dependent Functionality**: Behavior that depends on available context
4. **Type-Level Programming**: Advanced compile-time computation

```scala
// Example: Dependency injection
trait Database {
  def query(sql: String): List[String]
}

given testDb: Database = new Database {
  def query(sql: String) = List("test", "data")
}

def fetchUsers()(using db: Database): List[String] =
  db.query("SELECT * FROM users")

fetchUsers()  // uses testDb automatically
```

#### Scala 2 Implicits vs Scala 3 Contextual Abstractions

| Scala 2 | Scala 3 | Purpose |
|---------|---------|---------|
| `implicit val` | `given` | Define contextual values |
| `implicit def` | `given` + `using` | Define contextual conversions |
| `implicit` parameter | `using` parameter | Receive contextual values |
| `implicit class` | `extension` | Add methods to types |
| `implicitly[T]` | `summon[T]` | Access contextual values |

**Key Improvements in Scala 3**:
- More explicit intent (`given`/`using` vs overloaded `implicit`)
- Better error messages
- Cleaner syntax for extension methods
- Separation of concerns (given vs extension)

---

## Quick Cheat Sheet

### Core Principles
- Everything is an expression (returns a value)
- Prefer immutability
- Use recursion over loops
- Functions are objects (Function1, Function2, etc.)
- Pattern matching is powerful and preferred

### Common Patterns
```scala
// Function definition
(x: Int) => x + 1

// Anonymous class
new Trait { def method = implementation }

// Pattern matching
value match { case pattern => result }

// For comprehension
for { x <- collection } yield transformation

// Infix notation (one argument)
object method argument

// Apply method
object(args)  // same as object.apply(args)
```

### Collections Operations
```scala
list.map(f)           // transform each element
list.filter(pred)     // keep matching elements
list.flatMap(f)       // transform and flatten
list.reduce(op)       // combine all elements
list.fold(z)(op)      // reduce with initial value
list.head            // first element
list.tail            // all but first
list.isEmpty         // check if empty
```

### Remember
- `val` = immutable (use this!)
- `var` = mutable (avoid!)
- `def` = method/function
- `lazy val` = evaluated on first access
- `case class` = data with built-in boilerplate
- `object` = singleton
- `trait` = interface

---

## Practice Tips

1. **Think in expressions**: Everything returns a value
2. **Embrace immutability**: Don't modify, create new
3. **Pattern match often**: It's more powerful than if/else
4. **Use higher-order functions**: map, filter, flatMap are your friends
5. **Avoid null**: Use Option instead
6. **Avoid exceptions in logic**: Use Try for exception handling
7. **Think recursively**: Replace loops with recursive functions

---

**Created from the Rock the JVM "Scala at Light Speed" course materials**