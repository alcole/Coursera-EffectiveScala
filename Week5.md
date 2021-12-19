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

### Type Classes

## Extension Methods and Implicit Conversions

### Type Classes and Extension Methods

### Implicit Conversions


---
Go to [week 6](Week6.md)