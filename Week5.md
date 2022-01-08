# Week 5

## Type-Directed Programming
### Context Parameters
#### Using Clause

We let the compiler know that we expect it to pass the ord argument by making it a **context parameter**. Then we can omit the ord argument to calls to sort. The compiler infers the argument based on its expected type.

`def sort[A](xs: List[A])(using ord: Ordering[A]): List[A] = ...`

It is possible to pass the argument explicitly with the using clause for non-default values
`sort(xs)(using Ordering.Int.reverse)`

##### Syntax Reference

```scala
// Multiple in a single clause
def f(x: Int)(using a: A, b: B) = ...
f(x)(using a, b)
// or several in a row
def f(x: Int)(using a: A)(using b: B) = ...
// using clauses can be mixed with regular parameters
def f(x: Int)(using a: A)(y: Boolean)(using b: B) = ...
```
##### Anonymous Using Clauses
Useful if the body of sort does not mention ord at all, but simply passes it on as a context argument

##### Context Bounds
```scala
def sort[A](xs: List[A])(using ord: Ordering[A]): List[A]
// can be rewritten in the short syntax
def sort[A: Ordering](xs: List[A]): List[A]
```
Parameter A has one **context bound**

### Given Definitions
For the previous example to work, `Ordering.Int` must be a given definition:
```scala
object Ordering:
    given Int: Ordering[Int] with
        def compare(x: Int, y: Int): Int =
            if x < y then -1 else if x > y then 1 else 0
```
This code defines a given instance of type Ordering[Int], named Int. The instance is initialised the first time it is used.

We can also define a given instance that is equal to some expression, using an alias

```scala
object IntOrdering extends Ordering[Int]:
    def compare(x: Int, y: Int): Int =
        if x < y then -1 else if x > y then 1 else 0

given intOrdering: Ordering[Int] = IntOrdering
```
RHS of alias only evaluated once and reused.

Can be anonymous, just omit the name
```scala
given Ordering[Double] with
    def compare(x: Double, y: Double): Int =
        if x < y then -1 else if x > y then 1 else 0
```
The compiler will synthesize a name for an anonymous definition

Summoning an instance - works for named and anonymous, refer to an instance by its type. Summon is a predefined method `def summon[T](using arg: T): T = arg`

Visibility of Given Instances

Importing Given Instances
```scala
import scala.math.Ordering.Int // by name

import scala.math.Ordering.{given Ordering[Int]} // by type
import scala.math.Ordering.{given Ordering[?]} // by type

import scala.math.Ordering.given // within a given selector (imports all given instances of a path)
```
since names don't matter the by type is preferred.

##### Search Scope for Given Instances
for a given instance of type T includes:
* first, all th given instances that are visible (inherited, imported, or defined in any enclosing scope)
* then, the given instances found in any companion object *associated* with T

The definition of *associated* is quite general. Beside the companion object of the type T itself, the compiler will also consider:
* companion objects associated with any of T's inherited types
* companion objects associated with any type argument in T
* if T is an inner class, the outer objects in which it is embedded

If no given instances then compilation error `error: no implicit argument of type Int was found for parameter x of method ...`

if more than one given instance is eligible, an **ambiguity** is reported `error: ambiguous implicit arguments: both...`

### Priorities Between Given Definitions
If one instance is **more specific** than another then the ambiguity is not generated.

A definition `given a: A` is more specific than a defintion `given b: B` if:
* a is in a closer lexical scope than b
* a is defined in a class or object which is a subclass of the class defining b
* type A is a subtype of type B
* type A has more "fixed" parts than B

### Type Classes
Type classes support *retroactive* extension: the ability to extend a data type with new operations without changing the original definition of the data type.

Type classes provide another form of polymorphism: the behaviour of sort varies according to the type of elements in the list.
At compiliation time, the compiler resolves the specific Ordering implementation that matches the type of the list elements.

Type classes classify types by the operations they support.

In Scala, this mechanism is often preferred over subtyping to achieve polymorphism.

One reason for this is that type classes support retroactive extension.

### Conditional Given Definitions



## Extension Methods and Implicit Conversions

### Type Classes and Extension Methods



### Implicit Conversions


---
Go to [week 6](Week6.md)