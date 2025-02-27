# Setting Up Your Environment 🛠️

Welcome to our **Testing Series**! In this first segment, we'll get you set 
up with a **Kotlin-based Spring Boot project**, plus any essential tools you'll need for the rest of 
the series. By the end of this guide, you'll have a working development environment and a baseline 
project ready to test. Let's jump in!

---

## Table of Contents
1. [Overview](#1-overview)
2. [Prerequisites](#2-prerequisites)
3. [Setting Up Your Environment](#3-setting-up-your-environment)
    - [Java 17](#java-17)
    - [Docker (or Colima)](#docker-or-colima)
    - [IDE (IntelliJ IDEA)](#ide-intellij-idea)
4. [Creating a Kotlin Spring Boot Project](#4-creating-a-kotlin-spring-boot-project)
    - [Using start.spring.io](#using-startspringio)
    - [Project Structure](#project-structure)
5. [Configuring Your Build File for Testing](#5-configuring-your-build-file-for-testing)
    - [Adding MockK](#adding-mockk)
    - [Adding SpringMockK](#adding-springmockk)
6. [Next Steps](#6-next-steps)

---

## 1. Overview 📝

In this part of the series, we're focusing on two main goals:
- **Provide a brief introduction** to the testing journey we'll be taking.
- **Ensure your environment is fully set up** for Kotlin-based Spring Boot applications, so you don't 
    have to scramble for installations later.

We'll keep it beginner-friendly. If you already have an environment configured, feel free to skim the 
sections below for any helpful tips or tools you might have missed.

---

## 2. Prerequisites ✅

Before we dive into the setup, here are a few basic requirements we're assuming:

1. **Basic Programming Knowledge**  
   You've written some code before—even if it's just "Hello World" in Kotlin or another language.

2. **A Compatible Operating System**  
   This tutorial focuses primarily on macOS or Linux for Docker/Colima examples. If you're on Windows, 
   you can still follow along using Docker Desktop, WSL 2, or a similar setup, but we'll keep 
   Windows-specific instructions minimal for now.

3. **Internet Connection**  
   You'll need a stable connection to download dependencies from Maven Central, run Docker pulls, and so on.

---

## 3. Setting Up Your Environment 💻

### Java 17 ☕
This series relies on **Java 17** (the current Long-Term Support release). We'll be using that as our base 
JVM for Kotlin and Spring Boot. If you don't already have Java 17 installed:

- **macOS/Linux**:
    - Use SDKMAN, Homebrew, or your package manager of choice to install OpenJDK 17.
    - For example, on macOS with Homebrew:
      ```bash
      brew install openjdk@17
      ```
    - Then, confirm installation:
      ```bash
      java -version
      ```
- **Windows**:
    - To Fill Out
- **Further Setup**:
    - If you need more guidance, we'll provide a separate tutorial on installing and configuring Java 17 in detail.

### Docker or Colima 🐳
To run containers (for databases, microservices, etc.) later in the series, you'll want Docker or a Docker alternative:

- **Docker Desktop**
    - The simplest way on macOS and Windows. Download from 
      [Docker's official website](https://www.docker.com/products/docker-desktop).
    - Keep in mind Docker Desktop has licensing considerations for certain companies, but for individual 
      use or small businesses, it's typically free.
- **Colima (macOS/Linux)**
    - An alternative that runs Docker containers in a lightweight Linux VM.
    - Install on macOS with Homebrew:
      ```bash
      brew install colima
      colima start
      ```
    - Once started, verify Docker is working:
      ```bash
      docker run hello-world
      ```
> **Note:** For Windows, Colima isn't officially supported, so you'd likely need WSL 2 or Docker Desktop.

### IDE (IntelliJ IDEA) 🧰
We'll be using [**IntelliJ IDEA**](https://www.jetbrains.com/idea/) for Kotlin development:

1. **Download & Install**
    - Grab the Community Edition (free) or Ultimate Edition from JetBrains.
2. **Configure Kotlin Plugin**
    - IntelliJ IDEA typically comes with Kotlin support out of the box (especially the Ultimate Edition). 
      Just check **Plugins** settings if something's missing.
3. **Confirm Setup**
    - You can open IntelliJ and create a basic Kotlin file just to ensure it compiles correctly.

Alternatively you may use any IDE you would like.

---

## 4. Creating a Kotlin Spring Boot Project 🚀

Now that your environment is ready, it's time to create a **Spring Boot** project with **Kotlin**. 
We'll use [start.spring.io](https://start.spring.io/) for a fast and painless setup.

### Create Github/Gitlab repo (Optional) 📁

If you want to store your project in a centralized location, create a new repo using
Github or Gitlab. After creating a blank project, pull it down locally and add the downloaded
code from `start.spring.io` into this new blank project.

> We will provide more detail setup in a future tutorial.

### Using start.spring.io 🌱

1. **Go to [start.spring.io](https://start.spring.io/)**
    - Under **Project**, select **Gradle Project**
    - Under **Language**, select **Kotlin**.
    - Enter your **Group** (e.g., `com.myawesomeproject`). This reflects you company
      - `<company/website_top_level_domain>.<company_name>.<optional_sub_project_details>`
      - If you do not have a site in mind, just use `com.mentor`
    - Enter your **Artifact** name (e.g., `helloUniverse`).
    - Set **Spring Boot** version to the latest stable (e.g., `3.x.x`).
2. **Add Dependencies**
    - You can always add more later, but for now, it's nice to include:
        - **Spring Web** (for a simple REST endpoint if you'd like)
        - **Spring Boot DevTools** (optional but handy)
        - **Test** (Kotlin test libraries, JUnit, etc.—usually included by default)
3. **Generate & Download**
    - Click the **Generate** button, which will download a `.zip` file containing your project.
    - Move the content into your github project if you created that.

> **NOTE:** It can be a bit tricky to organize the files correctly. Typically, especially if I use git/github,
> I will pull down the project, bring the zip file into it, unzip it, and then pull out all the
> content into the root. Specifically ensuring that the project folder is the top level and I don't have a 
> nested project/project structure.

### Project Structure 📂

After unzipping and opening the project in IntelliJ, you'll see a structure similar to:

```
helloUniverse/
├─ build.gradle.kts         # Gradle build script (Kotlin DSL)
├─ settings.gradle.kts      # Gradle settings (Kotlin DSL)
├─ src/
│  ├─ main/
│  │  ├─ kotlin/
│  │  │  └─ com/mentor/hello_universe
│  │  │     └─ HelloUniverseApplication.kt
│  │  └─ resources/
│  │     └─ application.properties
│  └─ test/
│     ├─ kotlin/
│     │  └─ com/mentor/hello_universe
│     │     └─ HelloUniverseApplicationTests.kt
│     └─ resources/
└─ ...
```

> **Tip**: Make sure you see the Kotlin icon or Kotlin references in your **build.gradle.kts** file. 
> IntelliJ might prompt you to **import** or **sync** the Gradle project if it detects changes.

---

## 5. Configuring Your Build File for Testing 🧪

Now that we have our basic project set up, let's enhance it by adding some essential testing libraries that will make our testing journey much smoother. We'll focus on two powerful libraries for Kotlin testing in Spring Boot applications: **MockK** and **SpringMockK**.

### Adding MockK 🎭

MockK is a mocking library designed specifically for Kotlin. It provides first-class support for Kotlin features and is the preferred alternative to Mockito when working with Kotlin code.

To add MockK to your project, update your **build.gradle.kts** file with the following dependency:

```kotlin
dependencies {
    // Other dependencies
    
    // MockK for Kotlin-friendly mocking
    testImplementation("io.mockk:mockk:1.13.10")
}
```

MockK provides several advantages for Kotlin developers:
- Native support for Kotlin features like coroutines, extension functions, and higher-order functions
- Cleaner syntax specifically designed for Kotlin
- More powerful capabilities for handling Kotlin-specific constructs

### Adding SpringMockK 🍃

SpringMockK is an integration library that brings MockK's capabilities into the Spring testing framework. It allows you to use MockK in place of Mockito in your Spring Boot tests with minimal configuration.

Add SpringMockK to your **build.gradle.kts** file:

```kotlin
dependencies {
    // Other dependencies and MockK
    
    // SpringMockK for Spring Boot integration with MockK
    testImplementation("com.ninja-squad:springmockk:4.0.2")
}
```

SpringMockK provides:
- `@MockkBean` annotation as a replacement for Spring's `@MockBean`
- `@SpykBean` annotation as a replacement for Spring's `@SpyBean`
- Seamless integration with Spring's test context framework

With these libraries added, you'll be ready to write more idiomatic Kotlin tests for your Spring Boot application. We'll explore how to use these libraries effectively in the upcoming sections on unit testing and Spring controller testing.

> **Note**: The versions specified above (MockK 1.13.10 and SpringMockK 4.0.2) were the latest at the time of writing. Feel free to check for newer versions when setting up your project.

---

## 6. Next Steps 🔮

At this point, you're set up to:
1. Write and run **Kotlin code** in a Spring Boot app.
2. Use **Docker** or **Colima** for containerized services later on.
3. Expand your environment as needed with additional dependencies or libraries.

In the **next part** of this series, we'll dive deeper into **TDD and BDD Basics**—showing you how both testing approaches can be applied to your Kotlin Spring Boot code. We'll explore the different philosophies and practical implementations.

**Ready to dive in?** Let's move on to learning about test-driven and behavior-driven development!

---

[← BACK: Introduction](../01_outline.md) | [NEXT: TDD & BDD Basics →](../03_TDD_BDD/03_tdd_bdd_basics.md)