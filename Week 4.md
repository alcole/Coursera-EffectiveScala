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

### Property Based Testing

### Mocking

### Integration Testing

### Testing the Tests

### Debugging Programs