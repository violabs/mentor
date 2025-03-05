# TDD and BDD Basics with Spring & Kotlin

In this guide, we'll explore both Test-Driven Development (TDD) and Behavior-Driven Development
(BDD) approaches by creating a simple Spring project with Kotlin. We'll also introduce mocking
as a crucial technique for effective testing.

## What You'll Learn

- The differences between TDD and BDD approaches
- How to implement TDD-style tests in Kotlin
- How to implement BDD-style tests in Kotlin
- Creating a simple Spring Boot service and controller
- Using mocking to isolate components for testing
- Testing both approaches against our implementation

## Overview

We'll create:

1. A **`GreetingService`** that provides greetings based on parameters
2. A **`Greeting`** data class for our responses
3. Several test classes demonstrating TDD, BDD, and mocking techniques
4. A **`GreetingController`** to expose a `/` endpoint

## Project Structure

```
helloUniverse/
├─ build.gradle.kts
├─ settings.gradle.kts
└─ src/
   ├─ main/
   │  ├─ kotlin/
   │  │  └─ com/mentor/helloUniverse/
   │  │     ├─ GreetingService.kt
   │  │     ├─ Greeting.kt
   │  │     └─ GreetingController.kt
   └─ test/
      ├─ kotlin/
      │  └─ com/mentor/helloUniverse/
      │     ├─ GreetingServiceTDDTest.kt
      │     ├─ GreetingServiceBDDTest.kt
      │     └─ GreetingControllerTest.kt
```

## TDD vs BDD: A Brief Introduction

### Test-Driven Development (TDD)

TDD follows a cycle of:

1. Write a failing test
2. Implement just enough code to make the test pass
3. Refactor the code while keeping tests green

TDD focuses on:

- Small, incremental development steps
- Testing technical functionality
- Clear test outcomes (pass/fail)
- Code design driven by testability

### Behavior-Driven Development (BDD)

BDD extends TDD by:

- Using natural language to describe tests
- Focusing on user-visible behavior rather than implementation
- Encouraging collaboration between developers, QA, and non-technical stakeholders
- Writing tests that serve as living documentation

BDD tests help bridge the gap between technical implementation and business requirements.

## Introduction to Mocking

### What is Mocking?

**Mocking** is a technique where you create substitutes (or "mocks") for real objects that your code depends on. These
mock objects:

- Simulate specific behaviors of real dependencies
- Allow you to control the test environment
- Help isolate the component you're testing

### Why Use Mocks?

1. **Isolation**: Test components independently without requiring their dependencies
2. **Speed**: Tests run faster by avoiding slow external systems
3. **Reliability**: Tests don't fail because of external system issues
4. **Edge Case Testing**: Easily simulate rare conditions or error cases
5. **Determinism**: Tests produce consistent results regardless of environment

### Common Mocking Scenarios

- External API calls
- Database interactions
- File system operations
- Time-dependent functionality
- Resources with limited availability

## Let's Build Our Project

Now, let's implement our code and tests using both approaches.

Typically, with TDD, you would create the failing test first, and then create the
code to make that test pass (red, green, refactor).

We will start by defining our first test. This will represent a simple self-contained
unit test.

> Some practices of TDD suggest writing the test first without these classes. If that style helps,
> you can start with the test file below, and then fill it in after attempting to run the test.

[Branch with code](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/01_initial_failing_test)

> todo: add guide

The initial requirement we get is that we always want a Greeting object.

### 1. Create the `Greeting` Class

This simple data class holds our greeting message and additional details. We will keep
this open for now:

```kotlin
package com.mentor.helloUniverse

data class Greeting(
    val message: String? = null
)
```

### 2. Create the `GreetingService` without Implementation

Our service will return personalized greetings, for now add the empty values:

```kotlin
package com.mentor.helloUniverse

import org.springframework.stereotype.Service

@Service
class GreetingService {
    fun getGreeting(): Greeting? {
        return null
    }
}
```

### 3. Failing Test

Now that we have the base items, we can write our failing test. We are going to
start by just asserting it even exists.

```kotlin
package com.mentor.helloUniverse

import org.junit.jupiter.api.Test

class GreetingServiceTDDTest {
    private val greetingService = GreetingService()

    @Test
    fun `getGreeting returns the greeting with default details if inputs not set`() {
        // arrange
        val expected = Greeting()

        // act
        val result = greetingService.getGreeting()

        // assert
        assert(result == expected) {
            """
                EXPECT: $expected
                ACTUAL: $result
            """.trimIndent()
        }
    }
}
```

### 4. Test Resolution

#### Run Failing Test

You can utilize the runnable options within the IDE, or you can run them manually with `./gradlew test`.

You should see the output of:

```
EXPECT: Greeting(message=null)
ACTUAL: null
java.lang.AssertionError: EXPECT: Greeting(message=null)
ACTUAL: null
	at com.mentor.helloUniverse.GreetingServiceTDDTest.getGreeting returns the greeting with default details if inputs not set(GreetingServiceTDDTest.kt:17)
	at java.base/java.lang.reflect.Method.invoke(Method.java:580)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1596)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1596)
```

#### Write code to pass test

At this point you may try to make the test pass on its own. The solution below:

```kotlin
package com.mentor.helloUniverse

import org.springframework.stereotype.Service

@Service
class GreetingService {
    fun getGreeting(): Greeting? {
        return Greeting()
    }
}
```

This should lead to the passing test.

[Branch Code Reference](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/02_initial_failing_test_fix)

### 5. Add Additional Requirements

We want to be able to add any name and have the greeting return as 'Hello, ' + word + '!'.
If no input provided, we want to default it to 'Universe'.
Additionally, we want to get details about our app. We will add a temp storage in the
form of a `Map` to return metadata for our `Greeting` object. This metadata should have key of 'version' with '1.0'
and 'mode' with value 'dev'.

With these requirements we can update our tests to start with the case where we expect the default.

#### Failed Tests

```kotlin
package com.mentor.helloUniverse

import org.junit.jupiter.api.Test

class GreetingServiceTDDTest {
    private val greetingService = GreetingService()

    @Test
    fun `getGreeting returns the greeting with default details if inputs not set`() {
        // arrange
        val expected = Greeting(
            message = "Hello, Universe!",
            metadata = mapOf(
                "version" to "1.0",
                "mode" to "dev"
            )
        )

        // act
        val result = greetingService.getGreeting()

        // assert
        assert(result == expected) {
            """
                EXPECT: $expected
                ACTUAL: $result
            """.trimIndent()
        }
    }
}
```

[Branch Code Reference](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/03_additional_test_fail)

#### Update the GreetingService

You can try to get the tests to pass yourself. You can find a solution below:

```kotlin
package com.mentor.helloUniverse

import org.springframework.stereotype.Service

private val METADATA = mapOf(
    "version" to "1.0",
    "mode" to "dev"
)

@Service
class GreetingService {
    fun getGreeting(): Greeting? {
        return Greeting(
            message = "Hello, Universe!",
            metadata = METADATA
        )
    }
}
```

[Branch Code Reference](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/04_additional_test_pass)

### 6. Final test coverage

The requirements state that we need to be able to add any custom name, so we will create a second test for now to cover
that. We will need to modify the method signature of `getGreeting` and add that to the test.

#### Failed Test

##### Service File Update

```kotlin
    fun getGreeting(name: String? = null): Greeting? {
    return Greeting(
        message = "Hello, Universe!",
        metadata = METADATA
    )
}
```

##### Test File Update

```kotlin
   ...

@Test
fun `getGreeting returns the greeting with custom name if inputs set`() {
    // arrange
    val expected = Greeting(
        message = "Hello, World!",
        metadata = mapOf(
            "version" to "1.0",
            "mode" to "dev"
        )
    )

    // act
    val result = greetingService.getGreeting(name = "World")

    // assert
    assert(result == expected) {
        """
                EXPECT: $expected
                ACTUAL: $result
            """.trimIndent()
    }
}

...
```

[Branch Code Reference](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/05_final_test_fail)

#### Updating code to pass test

We can update the service to do this:

```kotlin
    fun getGreeting(name: String? = null): Greeting? {
    return Greeting(
        message = "Hello, $name!",
        metadata = METADATA
    )
}
```

If you run `./gradlew test` you will see that our new test passes! But then we also see that our first test
fails. The code above removed the default! So we must modify the signature. On top of reducing the scope
of our nullability, we can apply that to the response, since we ALWAYS return the `Greeting` object.

```kotlin
    fun getGreeting(name: String = "Universe"): Greeting {
    return Greeting(
        message = "Hello, $name!",
        metadata = METADATA
    )
}
```

We can run the `./gradlew test` again and all of the test should pass!

[Branch Code Reference](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/05_final_test_pass)

---

## Behavior Driven Development

We can enhance the way we code by focusing on the behavior. Instead of `arrange`, `act`, `assert`, it can
prove more helpful to think of it as `given`, `when`, `then`. We can modify the tests to reflect this using
Kotlin's extensibility.

Add the following to the same level as your test classes called `TestStyle.kt`

```kotlin
package com.mentor.helloUniverse

object TestStyle {
    fun tdd(tddScope: TDD.() -> Unit) {
        TDD().tddScope()
    }

    fun bdd(bddScope: BDD.() -> Unit) {
        BDD().bddScope()
    }

    /**
     * use AAA - not specific to TDD
     */
    class TDD {
        fun arrange(arrangeScope: Arrange.() -> Unit) {
            Arrange().arrangeScope()
        }

        class Arrange {
            fun act(actScope: Act.() -> Unit) {
                Act().actScope()
            }
        }

        class Act() {
            fun assertion(assertScope: () -> Unit) {
                assertScope()
            }
        }
    }

    class BDD {
        fun given(givenScope: Given.() -> Unit) {
            Given().givenScope()
        }

        class Given {
            fun whenever(whenScope: When.() -> Unit) {
                When().whenScope()
            }
        }

        class When {
            fun then(thenScope: () -> Unit) {
                thenScope()
            }
        }
    }
}
```

See the code for a more detailed explanation of this class. For now you just need to know,
we can use it to wrap our sections in a scope, or a block of execution. We can then change
our test code to look like:

```kotlin
class GreetingServiceTDDTest {
    private val greetingService = GreetingService()

    @Test
    fun `getGreeting returns the greeting with default details if inputs not set`() = TestStyle.tdd {
        arrange {
            val expected = Greeting(
                message = "Hello, Universe!",
                metadata = mapOf(
                    "version" to "1.0",
                    "mode" to "dev"
                )
            )

            act {
                val result = greetingService.getGreeting()

                assertion {
                    // assert
                    assert(result == expected) {
                        """
                            EXPECT: $expected
                            ACTUAL: $result
                        """.trimIndent()
                    }
                }
            }
        }
    }

    @Test
    fun `getGreeting returns the greeting with custom name if inputs set`() = TestStyle.tdd {
        arrange {
            val expected = Greeting(
                message = "Hello, World!",
                metadata = mapOf(
                    "version" to "1.0",
                    "mode" to "dev"
                )
            )

            act {
                val result = greetingService.getGreeting(name = "World")

                assertion {
                    // assert
                    assert(result == expected) {
                        """
                        EXPECT: $expected
                        ACTUAL: $result
                        """.trimIndent()
                    }
                }
            }
        }
    }
}
```

You can see how each section is nested inside the other block. We use `assertion` instead of `assert` since
that is already used for the actual check.

Now that we have this example, we can copy the code into a new test file `GreetingServiceBDDTest.kt` at the same
level of our TDD one. Then we can just modify the code to use the BDD way.

```kotlin
package com.mentor.helloUniverse

import org.junit.jupiter.api.Test

class GreetingServiceBDDTest {
    private val greetingService = GreetingService()

   @Test
   fun `getGreeting returns the greeting with default details if inputs not set`() = TestStyle.bdd {
      given {
         whenever {
            val result = greetingService.getGreeting()

            then {
               val expected = Greeting(
                  message = "Hello, Universe!",
                  metadata = mapOf(
                     "version" to "1.0",
                     "mode" to "dev"
                  )
               )

               assert(result == expected) {
                  """
                      EXPECT: $expected
                      ACTUAL: $result
                  """.trimIndent()
               }
            }
         }
      }
   }

   @Test
   fun `getGreeting returns the greeting with custom name if inputs set`() = TestStyle.bdd {
      given {
         whenever {
            val result = greetingService.getGreeting(name = "World")

            then {
               val expected = Greeting(
                  message = "Hello, World!",
                  metadata = mapOf(
                     "version" to "1.0",
                     "mode" to "dev"
                  )
               )

               assert(result == expected) {
                  """
                  EXPECT: $expected
                  ACTUAL: $result
                  """.trimIndent()
               }
            }
         }
      }
   }
}
```

> Note. We will use `whenever` since `when` is a keyword.

While it has a similar setup to the TDD setup, we can modify it a bit to add additional clarity and context. For this,
we will modify the `TestStyle` class.

```kotlin
   class BDD {
      fun given(details: String, givenScope: Given.() -> Unit) {
         println("given $details")
         Given().givenScope()
      }
   
      class Given {
         fun whenever(details: String, whenScope: When.() -> Unit) {
            println("when $details")
            When().whenScope()
         }
      }
   
      class When {
         fun then(details: String, thenScope: () -> Unit) {
            println("then $details")
            thenScope()
         }
      }
   }
```

Then we can add details to the tests:

```kotlin
    @Test
    fun `getGreeting returns the greeting with default details if inputs not set`() = TestStyle.bdd {
      given("a request without a name") {
         whenever("we call for a greeting") {
            val result = greetingService.getGreeting()

            then("expect default 'Hello Universe!' with metadata") {
               val expected = Greeting(
                  message = "Hello, Universe!",
                  metadata = mapOf(
                     "version" to "1.0",
                     "mode" to "dev"
                  )
               )

               assert(result == expected) {
                  """
                      EXPECT: $expected
                      ACTUAL: $result
                  """.trimIndent()
               }
            }
         }
      }
   }

   @Test
   fun `getGreeting returns the greeting with custom name if inputs set`() = TestStyle.bdd {
      given("a request with a name 'World'") {
         whenever("we call for a greeting") {
            val result = greetingService.getGreeting(name = "World")
   
            then("expect returned 'Hello World!' with metadata") {
               val expected = Greeting(
                  message = "Hello, World!",
                  metadata = mapOf(
                     "version" to "1.0",
                     "mode" to "dev"
                  )
               )
   
               assert(result == expected) {
                  """
                  EXPECT: $expected
                  ACTUAL: $result
                  """.trimIndent()
               }
            }
         }
      }
   }
```

As you can see, we added context messages to help inform others working on the code, what the purpose
of this test is. Remember, tests are ways to inform others on the functionality, so think about how
you want to communicate that to future developers!

Run your tests and you should be able to see it print out these messages within that specific test.

[Branch Code Reference](https://github.com/violabs/mentor-code/tree/beginner/testing/tdd/07_tdd_bdd_example)

---

## Testing with Mocks and Spies

WORK IN PROGRESS

## The Differences Between MockK and Mockito

While Mockito is the most popular Java mocking library, MockK is designed specifically for Kotlin and offers several
advantages:

### MockK Advantages:

1. **Kotlin-first**: Designed for Kotlin with coroutine support and idiomatic syntax
2. **Relaxed mocks**: Can create mocks without specifying every behavior
3. **Powerful matchers**: Has a wide range of matchers for verifying method calls
4. **DSL syntax**: Uses Kotlin's DSL features for a cleaner, more readable syntax

### Key MockK Syntax:

| Operation         | Mockito                              | MockK                                |
|-------------------|--------------------------------------|--------------------------------------|
| Create a mock     | `mock(Class)`                        | `mockk<Class>()`                     |
| Define behavior   | `when(x.method()).thenReturn(value)` | `every { x.method() } returns value` |
| Verify calls      | `verify(x).method()`                 | `verify { x.method() }`              |
| Argument matchers | `any()`                              | `any()`                              |
| Relaxed mocks     | `mock(Class, RETURNS_DEFAULTS)`      | `mockk<Class>(relaxed = true)`       |

## Breaking and Fixing Tests

A key benefit of automated tests is detecting when changes break expected behavior. Let's see how this works:

1. **Change the Service**:
   ```kotlin
   private val METADATA = mapOf(
       "version" to "2.0",
       "mode" to "dev"
   )
   ```

2. **Run the Tests**:
   The tests expecting version "1.0" will fail

3. **Fix the Tests**:
   Update the test expectations to match the new message

This demonstrates how tests help catch potential regressions when code changes.

## Practical TDD with Mocking

Here's a practical approach to TDD with mocking:

1. **Define interfaces** for your components
2. **Write tests** using mocks for dependencies
3. **Implement** the component being tested
4. **Refactor** while keeping tests passing
5. Repeat for the next component

This approach ensures each component is testable in isolation.

## Key Takeaways

- **TDD and BDD** provide different approaches to testing, with complementary benefits
- **Mocking** is essential for testing components with dependencies
- **MockK** provides a Kotlin-first approach to mocking that integrates well with Spring
- **Isolation** through mocking makes tests more focused and reliable
- Tests serve as **living documentation** of system behavior
- A good test suite **catches regressions** when code changes

## Next Steps

In future guides, we'll explore:

1. More advanced mocking techniques
2. Integration testing with Spring Test
3. Testing database interactions
4. Testing asynchronous code
5. Advanced testing patterns

[← BACK: Introduction](../01_outline.md) | [NEXT: Testing Spring Controllers →](/testing/01_Beginner/04_Spring_Controllers/04_testing_controllers.md)

```kotlin
```kotlin
package com.mentor.helloUniverse

import org . springframework . stereotype . Service

    @Service
    class GreetingService {

        // Additional details about our greeting
        private val details = mapOf(
            "version" to "1.0",
            "mode" to "dev"
        )

        fun getGreeting(name: String = "Universe", mood: String = "neutral"): Greeting {
            val message = when (mood) {
                "happy" -> "Hello, Wonderful $name!"
                "excited" -> "Hello, Amazing $name!"
                "relaxed" -> "Hello, Peaceful $name!"
                else -> "Hello, $name!"
            }

            return Greeting(
                message = message,
                details = details + ("mood" to mood)
            )
        }
    }
```

```