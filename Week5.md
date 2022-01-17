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
Can also take context parameters. Take a type and a context parameter
`given orderingList[A](using ord: Ordering[A]): Ordering[List[A]] with ...`
 this is a *conditional* definition, an ordering of lists with elements of type A exists only if there is an ordering for A.
 
 This sort of conditional behaviour is best implemented with type classes. Subtyping and inheritance cannot express this: a class either inherits a trait or doesn't.
 
 For reference here is the implementation
 ```scala
 given orderingList[A](using ord: Ordering[A]): Ordering[List[A]] with 
    def compare(xs0: List[A], ys0: List[A]) =
        (xs, ys) match
            case (Nil, Nil) => 0
            case (Nil, _)   => -1
            case (_, Nil)   => 1
            case (x :: xs, y :: ys1) =>
                val c = ord.compare(x, y)
                if c != 0 then c else compare(xs1, ys1)
 ```
 
 * given definitions can also take type parameters and context parameters
 * an arbitrary number of given definitions can be chained until a terminal definition is reached
 * this allows the compiler to summon complex pieces of logic based on type information, eg:
 `summon[Ordering[(Int, List[String])]]`

## Extension Methods and Implicit Conversions
### Type Classes and Extension Methods
Recap:
* Type classes can be used to retroactively add operations to existing data types - but they can'be be called like methods on the instances
* Extension methods make it possible to add methods to a type, outside the type definition
* we define the extension method in the trait to add an operation to a type
```scala
trait Ordering[A]:
    def compare(x: A, y: A): Int
    extension (lhs: A)
        def < (rhs: A): Boolean = compare(lhs, rhs) < 0
        
// and then:
...
... if x < y then ...
...
```
Applicability of Extension Methods

Extension methods on an expression of type T are applicable if
1. they are visible (by being defined, inherited or imported) in a scope enclosing the point of the application, or
2. they are defined in an object associated with the type T, or
3. they are defined in a given instance associated with the type T

Summary: 
Leverage extension methods to provide a nice syntax to work with your type classes.

Extension methods are applicable on values of type T if there is a given instance
* that is visible at the point of application
* or that is defined in a companion object associated with T

### Implicit Conversions
make it possible to convert an expression to a different type.

Aside: Repeated Parameters
```scala
def printSquares(xs: Int*) = println(xs.map(x => x * x))
printSquares(1, 2, 3)
```
* xs is a **repeated** parameter
* at call site, we can supply several arguments
* in the method body, xs has type `Seq[Int]`
* repeated parameters can only appear at the end of a parameter list

The compiler looks for implicit conversions on an expression e of Type T if T does not conform to the expression's expected type S.

In such a case, the compiler looks in the the context for a given instance of type Conversion[T, S]

Note: at most one implicit conversion can be applied to a given expression

Warning:
Implicit conversions are silently applied by the compiler, and they change the type of expressions.

Therefore, they can confuse developers reading code (hence the import).

Before defining an implicit conversion, make sure to weigh the pros and cons. Reducing boilerplate is a good purpose, but this should be balanced with the possible drawbacks of not seeing pieces of code that are yet part of the program.

Summary:
* Implicit conversions can improve the ergonomics of an API but should be used *sparingly*


---
Go to [week 6](Week6.md)