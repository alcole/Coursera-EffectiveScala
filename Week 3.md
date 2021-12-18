# Week 3

## Organise Code
worksheets are a single file

projects with multiple files

definitions introduce names
* collisions - 2 definitions with same names
* coupling with parts of the code that refer to the names

**Packages** allow you to give a prefix to your definitions so they don't collide. 

To place a definition inside a package, use a package clause at the top of your source file

```scala
package effective.example

object Hello:
    val foo = ...
```
This would place the object `Hello` in the package `effective.example`. 
You can refer to it with the fully qualified name `effective.example.Hello`.

It is good practice to put the source files in the subdirectories mirroring the packages structure. So for the example `effective/example/Hello.scala` which is normally all under `src/main/scala`.

Can also use an import clause `import effective.example.Hello` and then refer directly `println(Hello.foo)`

There are several forms of imports
```scala
import effective.example.Hello // imports just Hello
import effective.example.Hello.foo // imports just foo
import effective.example.{Hello, Bar} // imports both Hello and Bar
import effective.example.* // imports everything from the package
```
first 3 are named imports, last one is a wildcard import.

Some are imported automatically in any Scala program, all members of the
* package scala
* package java.lang
* singleton object scala.Predef

## Build Tools
how they manage the workflow, when you program you need to
* compile
* run
* test
* deploy
  
and some of these tasks 
* can be done by a CI server
* may be triggered on source changes

indicate where source files, and libraries it is dependent on and it goes away and simplify the coordination of these tasks.

### sbt
others include Maven, Gradle, Mill

An sbt project is a directory with the following 2 files
* project/build.properties
* build.sbt

properties defines sbt version
build.sbt defines config of project, including the scala version

by default compiles file that are in src/main/scala

sbt shell allows you to issue commands,
useful tasks:
* compile
* run - compiles and runs, as depends on compile so does this if needed
* console - compiles program and opens a REPL, so you can refer to definitions in the program
* test - compiles program and then compiles the tests and runs

2 concepts 
* settings - parameterise the build
* taks - perform actions, they are evalutated at each invocation

Tests - can use framework munit. First declare a dependency, mention that should be part of the class path for running the tests, but not for running the main program. Trailing `% test` to do so. By default looks for test sources in src/text/scala directory

sbt plugings in project/plugins.sbt help define the build.

Scala index to search for plugins and other components.examples sbt-site makes it easier to build a website, often used for documentation libraries. Defines a task makeSite.

Build definition is written in scala.

#### sbt - keys and scopes

sourceDirectory

task scoping

## Program Entry Point
Worksheets eval top to bottom, projects need an entry point. Annotated with `@main`, can't be a method in a class must be top level method, or a method in an object definition. Can add parameters, so need to supply when you run. Program execution is aborted if not supplied or they are the wrong type. Top level defintions `def, val, var, object, trait and class` are valid, can't have top level statements.

## Modules
### Encapsulation
Constructor parameters of "simple" classes are private they can only be accessed from the class body. One difference  - case classes achieve aggregation and classes achieve encapsulation.
Class members are public by default, can qualify them as private
`private def...`
#### Traits
Can define an interface in Scala by writing a trait definition
```scala
trait DatabaseAcces:
    def readData(): List[Data] // abstract method

class PersistentDatabase(connection: Connection) extends DatabaseAccess:
    def readData(): List[Data] = ...

class InMemoryDatabase extends DatabaseAccess:
    def readData(): List[Data] = ...    
```
trait defines a type but no constructor, this example has one abstract method. Unlike sealed traits, "simple" traits can have an unbounded number of implementation. Classes that extend must implement the abstract method. Good to depend on interfaces not implementations.
#### Protected Members
Public members are visible from outside the class or trait, private members are visible from the inside of the the class or trait only.

**Protected** members are visible from the inside of a trait or class, and from the inside of its descendants(but not from the outside). 

### Extending and Refining Classes


### Case Classes vs Simple Classes
and sealed traits and simple traits

* Case classes achieve aggregation - constructor parameters are public
* classes achieve encapsulation - constructor parameters are by default private

Case class, the compiler creates a class and customises parts of it
* constructor parameters are promoted to public members (aka **fields**)
* an **extractor** enables pattern matching
* **equality** operator between instances compares values of the case class fields - this is how case classes behave differently

both case and simple classses:
* define a new type along with a constructor
* can have public and private members
* can extend traits and override members
* create abstraction levels

Sealed vs simple traits

subclasses of a sealed trait have to be defined in the same file as the sealed trait, and have a fixed number of concrete subclasses. Exhaustively checking in pattern matching is only possible with sealed traits. 

Both sealed traits and simple traits:
* define a new type along with no constructor
* can have concrete and abstract members
* can have public and private members
* create abstraction levels

Case classes and sealed traits make you think of types as **sets of possible values**. 

Alternatively, "simple" traits and classes make you think of types as **interfaces** that provide a specific set of instructions.

Summary:
* Types can be seen as sets of operations (with unbounded number of possible values), or sets of possible values (with an unbounded number of operations)
* Classes conveniently group operations together
* If the set of possible values of a type is bounded, then it is probably a good idea to model this type with a case class
* If the set of possible operations on values of some type is bounded, then it is probably a good idea to model this type with a class

### Opaque Types

### Extension Methods

