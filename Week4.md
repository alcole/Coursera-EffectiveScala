# Week 4
## Reasoning About Code
### Refactoring Proof Programs
Referentially transparent operations are not subject to these problems because they do nothing more than returning a result that only depends on their input parameters

Operations that have side-effects open the door to a class of issues that do not exist with referentially transparent operations.

Side-effecting operations should be used with extra care.

### A Case for Side-Effects
"Pure" alternatives to side-effects introduce accidental complexity by requiring developers to explicitly carry over the "context" they operate on.

### Mutable Objects
we need to use a `var` definition
```scala
object Generator:
    var previous: Int = 42
    
    def nextInt(): Int =
        val result = previous * 22_695_477 + 1
        previous = result
end Generator
```
Plain classes equality is checked by comparing the "identity" of their instances, whereas case classes equality is checked by comparing the values carried by their instances.

Mutable objects are not "refactoring-proof". Therefore, it is a good practice to prefer immutable data types (for instance, immutable collections)

## Testing
### Unit Testing
In sbt projects, tests go in the `src/test/scala` directory and is common practice to mirror the names of the source files.
Several testing libraries, here we use MUnit

Add the following to the build.sbt file
```scala
libraryDependencies =+ "org.scalameta" %% "munit" % "0.7.19" % Test
testFrameworks += new TestFramework("munit.Framework")
```
A test suite is a class that extends munit.FunSuite
```scala
// File src/test/scala/testing/ProgramSuite.scala
package testing

class ProgramSuite extends munit.FunSuite:
    test("add") {
        // TODO write test spec here
    }
    
        test("fibonacci") {
        // TODO write test spec here
    }
end ProgramSuite
```
Think about corner cases to check

### Property Based Testing
Different approach with random inputs, we need to specify general properties that must be correct.

ScalaCheck library for property-based testing, integrates with munit so add the following
```scala
libraryDependencies =+ "org.scalameta" %% "munit-scalacheck" % "0.7.19" % Test
// same as for unit testing
testFrameworks += new TestFramework("munit.Framework")
```
A test suite containing properties is a class that extends munit.ScalaCheckSuite:
```scala
// File src/test/scala/testing/ProgramProperties.scala
package testing

import org.scalacheck.Prop.forAll

class ProgramProperties extends munit.ScalaCheckSuite:
    property("fibonacci(n) == fibonacci(n-1) + fibonacci(n-2) "){
        forAll { (n: Int) =>
            fibonacci(n) == fibonacci(n-1) + fibonacci(n-2)
        }
    }
```
forAll takes a function as a parameter and returns a Boolean value.

Good properties may be hard to find or formulate. You should look for **invariants** and **identities** (such as invertibility, idempotence, transformation relations etc).

See more: "Much ado about testing" talk by Nicolas Rinaudo, 2019

### Mocking
How do we test just one component when it depends on other components. Provide a fake implementation of a component to use.

Can be hard if the operations are too numerous or complicated.

You can use mocking libraries such as ScalaMock - Out of scope for this course.

### Integration Testing
Integration testing is the practice of testing a complete system, with no stubs or mocks.
MUnit allows you to set up and shut down a resource for the lifetime of a single test.
Http server:
```scala
class HttpServerSuite extends munit.FunSuite:
    val withHttpServer = FunFixture[HttpServer](
    setup = test => {
        val httpServer = HttpServer()
        httpServer.start(8888)
        httpServer
    },
    teardown = httpServer => httpServer.stop()
)
// call

withHttpServer.test("server is running") { httpServer => 
    // perform http request here
    }
```
or run all tests and then shutdown
```scala
class HttpServerSuite extends munit.FunSuite:

    val httpServer = HttpServer()
    override def beforeAll(): Unit = httpServer.start(8888)
    override def afterAll(): Unit = httpServer.stop()
```

### Testing the Tests
Test coverage tells you the paths that are untested, can use a compiler plugin scoverage.
* Tests are code too
* They need to be maintained
* They may have bugs

---
Go to [week 5](Week5.md)