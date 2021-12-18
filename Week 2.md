# Week2

## Lists
Part of Scala standard library

examples using

```Scala
case class AddressBook(contacts: List[Contact])
case class Contact(
    name: String,
    email: String,
    phoneNumbers: List[String]
    )
```
Collection types are **parameterised** by the type of their elements eg `List[Int], List[String]` can also have list of lists.

### Constructing Lists
```scala
val fruits = List("apples", "oranges", "pears")
val nums = List(1, 2, 3, 4)
val diag3 = List(List(1, 0, 0), List(0, 1, 0), List(0, 0, 1))
val empty = List()
```
### Basic manipulations
```scala
val numberOfContacts: Int = addressBook.contacts.size
val isAliceInContacts: addressBook.contacts.contains(alice)
val contactNames: List[String] = addressBook.contacts.map(contact => contact.name)
val contactsWithPhone: List[Contact] = addressBook.contacts.filter(contact => contact.phoneNumbers.nonEmpty)
```

### More operations on List
A list is:
* the empty list Nil
* a pair containing a head element and a tail that is a list
Lists are immutable
:: is right associative
`List(alice, bob) == alice :: bob :: Nil`

can take apart with pattern matching
`.head`
`.tail`
can also access by index `(0)` etc.

Constant time operations:
* ::
* head
* tail

Linear time operations:
* Random access
* size

## Functions
Can pass a function as a parameter and can be returned as a parameter or returned as a result.

rewrites function with `.apply`

* Functions are values
* define function literals with arrow syntax `(x: Int) => x + 1`
* A function that takes a parameter of type A and returns a result of type B is a value of type A => B, which has an apply method
* calling the function means calling its apply method
```scala
val increment: Int => Int = x => x + 1
val endsWithScaDotLa: Contact => Boolean =
  contact => contact.email.endsWith("@sca.la")

val increment: Int => Int = _ + 1

val endsWithScaDotLa: Contact => Boolean = _.email.endsWith("@sca.la")

val increment: Int => Int = x => x + 1
val increment = (x: Int) => x + 1
val increment = (_: Int) + 1

val add: (Int, Int) => Int = _ + _
\\ equivalent to
val add: (Int, Int) => Int = (x1, x2) => x1 + x2

```

## Collections
* construct
* query
* transform

> large library not all covered here

* List - immutable sequence
* ArrayBuffer - mutable sequence
* Map - immutable dictionary

most immutable collections available, but need to import mutable
* scala.collection.immutable
* scala.collection.mutable

convention is to import the package directly and then refer to definitions by prefixing 
```scala
import scala.collection.mutable
val buffer = mutable.ArrayBuffer()
```
### Constructing
Simplest way is to create an empty collection
```scala
List.empty // List[Nothing]
mutable.ArrayBuffer.empty // ArrayBuffer[Nothing]
Map.empty // Map[Nothing, Nothing]
//better to specify the type
List.empty[Int] // List[Int]
mutable.ArrayBuffer.empty[Double] // ArrayBuffer[Double]
Map.empty[String, Boolean] // Map[String, Boolean]
```
vararg constructor
`List(1,2,3,4)`
or `Map("a" -> true, "e" -> true, "b" -> false)`
the `a -> b` syntax constructs a tuple.

Prepend and append elements with
`+:` and `:+` methods. For maps use the `+`

#### Aside: Tuple
* collection of fixed size
* may have different types
* -> is shorthand, if size bigger then 2 then use () and separate with commas
* Pattern match to deconstruct

```scala
(10.0, "Hello") match
    case (number, greeting) => s"$greeting! The number is $number"
// or in a val
val (x, y) = (10.0, 20.0)
x // 10.0
y // 20.0
// or
val pair = ("Alice", 42)
pair(0) // : String = "Alice"
pair(1) // : Int = 42
```

### Querying
* xs.size
* xs.IsEmpty, xs.nonEmpty
* xs.contains(x)
* find returns first match of a predicate
* filter returns all matches of a predicate

**Option** is a special collection that contains zero or one element. `Some` when there is a value and `None` otw.

### Transforming
#### Map
transforms every element to a new element
```scala
list.map(x => x + 1)
buff.map(x => x % 2 == 0)
map.map((key, value) => key -> (value * 2))
```
#### FlatMap
allows change to the number of elements in the collection
```scala
mutable.ArrayBuffer(1, 2).flatMap(x => mutable.ArrayBuffer(x, x * 2))

Map(0 -> "zero", 1 -> "one").flatMap((key, _) => Map(key.toString -> key))
```
#### FoldLeft
* transform into anything else
* 2 parameters
```scala
List(1, 2, 3).foldLeft(0)((accum, elt) => accum + elt)
List(1, 2, 3).foldLeft(List.empty[Int])((accum, elt) => elt +: accum)
// true if last element is even or the list is empty
List(1, 2, 3).foldLeft(true)((accum, elt) => elt % 2 == 0)
```

#### GroupBy
groups according to a partition function

### Sequences and Maps
* Sequences have a well defined order, 
can get head and tail
* Linear sequences like list, have to go through
* Indexed sequence ArrayBuffers
* Sequences can be sorted `sortBy` method

* Map - use get method to return element associated with a key, returns option

### Option
* common practice to label with a maybe prefix
* can pattern match on Option
* map method can transform if element or nothing if None
* operation getOrElse returns optional value if defined or falls back to a given value
* zip operation combines to optional into single optional pair, defined only if two original option values are defined
* a value of type Option[A] can either be None, or Some(a: A)
* Some collection operations return optional values (eg find, headOption)


## Loops
several ways to preform loops
* iterate on the standard collections
* *imperative* loops with the control structure `while`
* *functional* loops with recursion


### Ranges
```scala
1 to 4 // 1, 2, 3, 4
0 to 10 by 2 // 0, 2, 4, 6, 8, 10
5 until 0 by -1 // 5, 4, 3, 2, 1
```
`1.to(4)` // : Range

### Iterate example with foldLeft
or with product operation
```scala
def factorial(n: Int): Int = 
    (1 to n).foldLeft(1)((result, x) => result * x)
    
def factorial(n: Int): Int = (1 to n).product
```

### Functional Loops
```scala
def factorial(n: Int): Int = 
    if n == 0 then 1
    else n * factorial(n -1)
```

### Imperative loop
```scala
def factorial(n: Int): Int = 
    var acc = 1
    var i = 1
    while i < n do
        i = i + 1
        acc = acc * 1
    acc
```

## Tail Recursion
A recursive call is in the tail position if it is the result of the recursive method
```scala
def factorial(n: Int): Int = 
    def factorialTailRec(x: Int, accumulator: Int): Int =
        if x == 0 then accumulator
        else factorialTailRec(x - 1, x * accumulator)
        
    factorialTailRec(n, 1)
end factorial
```

### for syntax



## Collections Extra

* Concatenating with ++, and can be called with dot notation .++ will not mutate mutable and immutable
* ++= mutable concatenation for mutable collection types
* prepend and append for mutable with += and +=:
* mutably remove elements -= removes first element equal to parameter


These are some symbolic and alphabetic names
* `+` is updated
* ++ is concet
* `-` is removed
* -- is removedAll
* +: is prepend
* :+ is append
* += is addOne
* ++= is addAll

> calling an object as if it was a function is a shorthand for calling the apply method on that object

Query
* forall
* exists to check at least one element matches a predicate

