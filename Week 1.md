# Week 1
## Types

## Methods and Parameters
`val `

`def`
optional can add `end`

### Explicit Result Type
```scala
def house(facade: Double, window: Double): Double = 
    val door = 2 * 1
    val subtractedArea = door + window * 2 
    facade - subtractedArea
```
Scala 3 still supports braces eg `Double = {...}``
### Methods
are introduced with the `def` keyword

### Lexical Scope
definitions that are inside a block are not visible outside of the block
### Named Parameters
`marathonDuration(speed = 12)`

### Conditions
An `if` expression takes a condition followed by the keyword `then`
`if/then` construct is an expression: it evaluates to a value
a condition must be of type Boolean, otw it's an error
`else if` to define more conditions
Scala 2 had condition in brackets with no then

### Evaluating Definitions
```scala
val tenSquared = 10 * 10
def tenSquared = 10 * 10
```
* body of def definitions is evaluated each time we use their name (if we never use it is never evaluated)
* val definitions are evaluated once, and their result is reused each time their name is used
#### Case for Parameterless def Definitions
delay evaluation until we need it
* def reevaluated, use mainly for operations that take parameters
* val used mainly for intermediate expressions

### Failing on purpose with ???
Definition can be left unimplemented

## Case Classes
`case class Rectangle(width: Int, height: Int)`
definition introduces a **type** and a **constructor**, both named rectangle. Type has two members and constructor takes two parameters.
#### adding an operation
```scala
case class Rectangle(width: Int, height: Int):
    val area = width * height
```

A case class aggregates several concepts together in a new type.

Case classes define immutable data types.

### Sealed Traits
we can define types that accept only one of several alternative values.

```scala
sealed trait Shape
case class Rectangle(width: Int, height: Int) extends Shape
case class Circle(radius: Int) extends Shape
```
Unlike case class definitions sealed traits don't introduce constructors, they are **abstract** types
`val someShape: Shape = Circle(5)`
Rectangle and circle are **subtypes** of Shape.

We can recover its concrete type using a **match** expression. 

```scala
val someShapeArea = 
    someShape match
        case Rectangle(width, height) => width * height
        case Circle(radius)           => radius * radius * 3.14
```
Scala 2 needed braces after the word match
* the **wildcard pattern** matches everything

A *case class* aggregates several concepts together, whereas a *sealed* trait represents one of several alternatives.

These two building blocks can be used to *model* business domains.

*Match* expressions can be used to define alternative *branches* of a program according to the concrete class of a sealed trait, and to *extract* data from this class at the same time.

### Enumerations
alternatives not classes but singleton values, special constructor for these in Scala 3
```scala
enum PrimaryColour:
    case Red, Blue, Green
```
#### Pattern Matching on Enumerations
```scala
def isProblematicForColorBlindPeople(colour: PrimaryColour): Boolean =
    colour match
        case PrimaryColour.Red  => true
        case PrimaryColour.Blue => false
        case PrimaryColour.Green=> true
```
value operation to enumerate all possible values
`PrimaryColour.values`

You can find an enumeration value from its String label:
`PrimaryColour.valueOf("Green") // : PrimaryColour = PrimaryColour.Green`
if argument is not a valid enumeration label you get a runtime error.

Enum definition is syntatic sugar for a sealed trait.

Note the same name can refer to either a type of a value depending on where it is used.
* object when used on RHS of a definition or when passed as argument to an operation
* type when used on RHS of a type annotation