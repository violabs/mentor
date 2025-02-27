# TDD and BDD Basics with Spring & Kotlin

In this guide, we'll explore both Test-Driven Development (TDD) and Behavior-Driven Development (BDD) approaches by creating a simple Spring project with Kotlin.

## What You'll Learn

- The fundamental concepts behind TDD and BDD
- How to implement TDD-style tests in Kotlin
- How to implement BDD-style tests in Kotlin 
- Creating a simple Spring Boot service and controller
- Testing both approaches against our implementation

## Overview

We'll create:

1. A **`GreetingService`** that stores a local map of details
2. A **`Greeting`** data class to hold our message and details
3. A **TDD test** class with standard assertions
4. A **BDD test** class with descriptive, behavior-focused naming
5. A **`GreetingController`** to expose a `/hello` endpoint

## Project Structure

Our sample Spring Boot project will have this directory structure:

```plaintext
helloUniverse/
└─ src/
   ├─ main/
   │  ├─ kotlin/
   │  │  └─ com/myawesomeproject/hello_universe/
   │  │     ├─ GreetingService.kt
   │  │     ├─ Greeting.kt
   │  │     └─ GreetingController.kt
   └─ test/
      ├─ kotlin/
      │  └─ com/myawesomeproject/hello_universe/
      │     ├─ GreetingServiceTDDTest.kt
      │     └─ GreetingServiceBDDTest.kt
```

## TDD vs BDD: A Brief Introduction

### Test-Driven Development (TDD)

TDD follows a "Red-Green-Refactor" cycle:

1. **Red**: Write a failing test
2. **Green**: Write the minimum code to make the test pass
3. **Refactor**: Improve the code while keeping tests passing

TDD focuses on verifying that individual components work as expected from a technical perspective.

### Behavior-Driven Development (BDD)

BDD extends TDD by:

- Using human-readable language to describe tests
- Focusing on the behavior of the system from a user's perspective
- Often following the "Given-When-Then" format to clearly express scenarios

BDD tests help bridge the gap between technical implementation and business requirements.

## Let's Build Our Project

Now, let's implement our code and tests using both approaches.

### 1. Create the `Greeting` Data Class

This simple data class holds our greeting message and additional details:

```kotlin
package com.myawesomeproject.hello_universe

data class Greeting(
    val message: String,
    val details: Map<String, String>
)
```

### 2. Create the `GreetingService`

Our service returns a greeting with a fixed message and details:

```kotlin
package com.myawesomeproject.hello_universe

import org.springframework.stereotype.Service

@Service
class GreetingService {

    // Local map storing additional details
    private val details = mapOf(
        "version" to "1.0",
        "mode" to "dev"
    )

    fun getGreeting(): Greeting {
        return Greeting(
            message = "Hello, Universe",
            details = details
        )
    }
}
```

### 3. TDD-Style Test

Traditional TDD tests focus on clear arrange-act-assert patterns:

```kotlin
package com.myawesomeproject.hello_universe

import org.junit.jupiter.api.Assertions.*
import org.junit.jupiter.api.Test

class GreetingServiceTDDTest {

    @Test
    fun shouldReturnHelloUniverseWithDetails() {
        // Arrange
        val service = GreetingService()

        // Act
        val result = service.getGreeting()

        // Assert
        assertEquals("Hello, Universe", result.message)
        assertEquals("1.0", result.details["version"])
        assertEquals("dev", result.details["mode"])
    }
}
```

**Key TDD Characteristics:**
- Method name describes the technical expectation
- Follows the "Arrange, Act, Assert" pattern
- Uses standard JUnit assertions
- Focuses on verifying specific values

### 4. BDD-Style Test

BDD tests use more descriptive language and often document user scenarios:

```kotlin
package com.myawesomeproject.hello_universe

import org.assertj.core.api.Assertions.assertThat
import org.junit.jupiter.api.Test

class GreetingServiceBDDTest {

    /*
     Feature: Greeting
       In order to feel welcomed by the system,
       As a user,
       I want to receive a friendly greeting message with additional details.
    */

    @Test
    fun `Given a GreetingService, When the user requests a greeting, Then a "Hello, Universe" message with details is returned`() {
        // Given
        val service = GreetingService()

        // When
        val result = service.getGreeting()

        // Then
        assertThat(result.message).isEqualTo("Hello, Universe")
        assertThat(result.details["version"]).isEqualTo("1.0")
        assertThat(result.details["mode"]).isEqualTo("dev")
    }
}
```

**Key BDD Characteristics:**
- Test name reads like a natural language sentence
- Uses backticks to allow spaces in function names
- Follows the "Given, When, Then" pattern from Gherkin language
- Often includes feature descriptions from the user's perspective
- Typically uses more fluent assertion libraries like AssertJ

### 5. Create a Controller

Finally, let's expose our greeting through a REST endpoint:

```kotlin
package com.myawesomeproject.hello_universe

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RestController

@RestController
class GreetingController(
    private val greetingService: GreetingService
) {
    @GetMapping("/hello")
    fun hello(): Greeting {
        return greetingService.getGreeting()
    }
}
```

When you run your Spring Boot app and hit the `/hello` endpoint, you'll get a response like:

```json
{
  "message": "Hello, Universe",
  "details": {
    "version": "1.0",
    "mode": "dev"
  }
}
```

## Breaking and Fixing Tests

A key benefit of automated tests is detecting when changes break expected behavior. Let's see how this works:

1. **Change the Service**:
   ```kotlin
   // Update the details map
   private val details = mapOf(
       "version" to "2.0",  // Changed from 1.0
       "mode" to "dev"
   )
   ```

2. **Run the Tests**:
   Both tests will now fail because they expect version "1.0"

3. **Fix the Tests**:
   Update assertions in both test classes to expect "2.0"

This demonstrates how tests help catch potential regressions when code changes.

## Practical TDD

While "pure" TDD involves writing tests before implementation, many developers use a more flexible approach:

1. Define interfaces and class structures first
2. Write tests to specify behavior
3. Implement the functionality
4. Refactor as needed

This "partial TDD" approach maintains most benefits while fitting better into some development workflows.

## Key Takeaways

- **TDD** focuses on verifying technical implementation
- **BDD** emphasizes behavior from a user's perspective
- Both approaches help ensure code reliability and catch regressions
- Choose the style that best fits your team and project
- Tests act as living documentation of your system's behavior

## Next Steps

In future guides, we'll explore:

1. Testing controllers with MockMvc
2. Integration testing with Spring Test
3. Mocking dependencies in tests
4. Advanced testing patterns

[NEXT: Testing Spring Controllers](/testing/01_Beginner/04_Spring_Controllers/04_testing_controllers.md)
