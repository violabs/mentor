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
4. A **`GreetingController`** to expose a `/hello` endpoint

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

**Mocking** is a technique where you create substitutes (or "mocks") for real objects that your code depends on. These mock objects:

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

### 1. Create the `Greeting` Class

This simple data class holds our greeting message and additional details:

```kotlin
package com.mentor.helloUniverse

data class Greeting(
    val message: String,
    val details: Map<String, String> = emptyMap()
)
```

### 2. Create the `GreetingService`

Our service returns personalized greetings:

```kotlin
package com.mentor.helloUniverse

import org.springframework.stereotype.Service

@Service
class GreetingService {

    // Additional details about our greeting
    private val details = mapOf(
        "version" to "1.0",
        "mode" to "dev"
    )

    fun getGreeting(name: String = "Universe", mood: String = "neutral"): Greeting {
        val message = when(mood) {
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

### 3. Create a Controller

Let's expose our greeting through a REST endpoint:

```kotlin
package com.mentor.helloUniverse

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RequestParam
import org.springframework.web.bind.annotation.RestController

@RestController
class GreetingController(
    private val greetingService: GreetingService
) {
    @GetMapping("/hello")
    fun getGreeting(
        @RequestParam(required = false, defaultValue = "Universe") name: String,
        @RequestParam(required = false, defaultValue = "neutral") mood: String
    ): Greeting {
        return greetingService.getGreeting(name, mood)
    }
}
```

## Setting Up MockK in Your Project

To use MockK for mocking in your tests, add these dependencies to your `build.gradle.kts` file:

```kotlin
dependencies {
    // Existing Spring Boot dependencies...
    
    // MockK
    testImplementation("io.mockk:mockk:1.13.10")
    testImplementation("com.ninja-squad:springmockk:4.0.2")
    
    // For BDD-style assertions
    testImplementation("org.assertj:assertj-core:3.25.3")
}
```

## TDD-Style Test with MockK

Here's how we test the `GreetingController` using TDD and mocking the `GreetingService` with MockK:

```kotlin
package com.mentor.helloUniverse

import io.mockk.every
import io.mockk.mockk
import io.mockk.verify
import org.junit.jupiter.api.Assertions.*
import org.junit.jupiter.api.Test
import org.springframework.http.MediaType
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get
import org.springframework.test.web.servlet.result.MockMvcResultMatchers.*
import org.springframework.test.web.servlet.setup.MockMvcBuilders

class GreetingControllerTDDTest {

    private val greetingService = mockk<GreetingService>()
    private val controller = GreetingController(greetingService)
    private val mockMvc = MockMvcBuilders.standaloneSetup(controller).build()

    @Test
    fun shouldReturnGreetingFromService() {
        // Arrange
        val greeting = Greeting(
            message = "Hello, Test Universe!",
            details = mapOf("version" to "1.0", "mood" to "happy")
        )
        every { greetingService.getGreeting(any(), any()) } returns greeting

        // Act & Assert
        mockMvc.perform(
            get("/hello")
                .param("name", "Test Universe")
                .param("mood", "happy")
                .contentType(MediaType.APPLICATION_JSON)
        )
            .andExpect(status().isOk)
            .andExpect(jsonPath("$.message").value("Hello, Test Universe!"))
            .andExpect(jsonPath("$.details.version").value("1.0"))
            .andExpect(jsonPath("$.details.mood").value("happy"))

        // Verify that the service was called with the right parameters
        verify { greetingService.getGreeting("Test Universe", "happy") }
    }
}
```

## BDD-Style Test with MockK

BDD tests can also use MockK, but with more descriptive language:

```kotlin
package com.mentor.helloUniverse

import io.mockk.every
import io.mockk.mockk
import io.mockk.verify
import org.assertj.core.api.Assertions.assertThat
import org.junit.jupiter.api.Test

class GreetingServiceBDDTest {

    /*
     Feature: Personalized Greeting
       In order to feel welcomed appropriately,
       As a user,
       I want to receive a customized greeting that reflects my mood.
    */

    @Test
    fun `Given a user is happy, When requesting a greeting, Then a happy greeting is returned`() {
        // Given
        val greetingService = GreetingService()

        // When
        val result = greetingService.getGreeting(name = "Universe", mood = "happy")

        // Then
        assertThat(result.message).isEqualTo("Hello, Wonderful Universe!")
        assertThat(result.details["mood"]).isEqualTo("happy")
    }

    @Test
    fun `Given a user is relaxed, When requesting a greeting, Then a peaceful greeting is returned`() {
        // Given
        val greetingService = GreetingService()

        // When
        val result = greetingService.getGreeting(name = "Universe", mood = "relaxed")

        // Then
        assertThat(result.message).isEqualTo("Hello, Peaceful Universe!")
        assertThat(result.details["mood"]).isEqualTo("relaxed")
    }
}
```

## Spring Integration Test with SpringMockK

For testing the complete integration with Spring's testing framework:

```kotlin
package com.mentor.helloUniverse

import com.ninjasquad.springmockk.MockkBean
import io.mockk.every
import io.mockk.verify
import org.junit.jupiter.api.Test
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest
import org.springframework.http.MediaType
import org.springframework.test.web.servlet.MockMvc
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get
import org.springframework.test.web.servlet.result.MockMvcResultMatchers.*

@WebMvcTest(GreetingController::class)
class GreetingControllerSpringTest {

    @Autowired
    lateinit var mockMvc: MockMvc

    @MockkBean
    lateinit var greetingService: GreetingService

    @Test
    fun shouldReturnGreetingFromService() {
        // Arrange
        val greeting = Greeting(
            message = "Hello, Spring Universe!",
            details = mapOf("version" to "1.0", "mood" to "excited")
        )
        every { greetingService.getGreeting(any(), any()) } returns greeting

        // Act & Assert
        mockMvc.perform(
            get("/hello")
                .param("name", "Spring Universe")
                .param("mood", "excited")
                .contentType(MediaType.APPLICATION_JSON)
        )
            .andExpect(status().isOk)
            .andExpect(jsonPath("$.message").value("Hello, Spring Universe!"))
            .andExpect(jsonPath("$.details.version").value("1.0"))
            .andExpect(jsonPath("$.details.mood").value("excited"))

        // Verify that the service was called with the right parameters
        verify { greetingService.getGreeting("Spring Universe", "excited") }
    }
}
```

## The Differences Between MockK and Mockito

While Mockito is the most popular Java mocking library, MockK is designed specifically for Kotlin and offers several advantages:

### MockK Advantages:

1. **Kotlin-first**: Designed for Kotlin with coroutine support and idiomatic syntax
2. **Relaxed mocks**: Can create mocks without specifying every behavior
3. **Powerful matchers**: Has a wide range of matchers for verifying method calls
4. **DSL syntax**: Uses Kotlin's DSL features for a cleaner, more readable syntax

### Key MockK Syntax:

| Operation | Mockito | MockK |
|-----------|---------|-------|
| Create a mock | `mock(Class)` | `mockk<Class>()` |
| Define behavior | `when(x.method()).thenReturn(value)` | `every { x.method() } returns value` |
| Verify calls | `verify(x).method()` | `verify { x.method() }` |
| Argument matchers | `any()` | `any()` |
| Relaxed mocks | `mock(Class, RETURNS_DEFAULTS)` | `mockk<Class>(relaxed = true)` |

## Breaking and Fixing Tests

A key benefit of automated tests is detecting when changes break expected behavior. Let's see how this works:

1. **Change the Service**:
   ```kotlin
   // Update the GreetingService's message for happy mood
   val message = when(mood) {
       "happy" -> "Hello, Fantastic $name!" // Changed from "Hello, Wonderful $name!"
       "excited" -> "Hello, Amazing $name!"
       // ...
   }
   ```

2. **Run the Tests**:
   The tests expecting "Hello, Wonderful Universe!" will now fail

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