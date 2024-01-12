---
title: "Unit Testing with Junit 5"
date: 2024-01-12 14:00
categories: [java, junit]
tags: [java, junit]
---

# Overview

In this post, we delve into the core aspects of unit testing using JUnit 5.

## Understanding Unit Testing

1. **What is Unit Testing?**

   Unit testing involves testing individual units or components of a software independently to validate that each unit functions as intended. In Java, a unit typically refers to a method or function.

2. **Why Unit Testing?**
   - **Early Bug Detection:** Identifying and fixing bugs during the development phase is more cost-effective than discovering them later in the process.
   - **Code Maintainability:** Unit tests act as documentation, providing insights into the expected behavior of each unit, which aids in code maintenance.
   - **Refactoring Support:** Unit tests ensure that existing functionalities aren't broken when you make changes, encouraging code refactoring.

## Writing Effective Unit Tests

1. **Test Structure:**
   - Follow the AAA pattern (Arrange, Act, Assert) to structure your tests clearly.
   - **Arrange (Given):** Set up the necessary preconditions.
   - **Act (When):** Execute the unit under test.
   - **Assert (Then):** Verify the expected results.

2. **Test Coverage:**
   - Aim for comprehensive test coverage to ensure that all code paths are exercised.
   - Use code coverage tools to identify areas that need additional testing.

3. **Isolation of Tests:**
   - Ensure that tests are independent of each other, as one test should not affect the outcome of another.
   - Utilize mocking frameworks (e.g., Mockito) to isolate units and simulate external dependencies.

## Running and Analyzing Tests

1. **Continuous Integration:**
   - Integrate unit tests into your continuous integration (CI) pipeline to ensure that tests are run automatically with each code change.

2. **Test Reports:**
   - Leverage the reporting capabilities of your testing framework to generate detailed reports, helping you identify failing tests and their causes.

3. **Test Debugging:**
   - Use debugging tools to analyze failing tests and identify the root cause of issues.

## Best Practices for Unit Testing in Java

1. **Keep Tests Simple and Readable:**
   - Write tests that are easy to understand, making it simpler for other developers to maintain and extend them.

2. **Regular Maintenance:**
   - Update tests as the codebase evolves to ensure they remain relevant and accurate.

3. **Test Naming Conventions:**
   - Follow consistent and descriptive naming conventions for your test methods.

4. **Focus on Critical Paths:**
   - Prioritize testing of critical and frequently used paths in your application.

5. **Balance Test Speed and Realism:**
   - Aim for a balance between test execution speed and realism to create efficient yet meaningful tests.

## Junit 5 Common Annotations

All core annotations are located in `org.junit.jupiter.api` package in the `junit-jupiter-api` module.


| Annotation   | Description  |
| -----------  | -----------  |
| @Test        | Marks method as a Test method |
| @BeforeAll   | Runs method before all test methods   |
| @AfterAll    | Runs method after all test methods have executed  |
| @BeforeEach  | Runs method before each Test method  |
| @AfterEach   | Runs method after each Test method  |
| @Nested      | Indicates the class is a nested class  |

## Junit 5 Utility Classes 

### Assertions

This class provides overloaded static utility methods that are used for asserting conditions while performing unit testing.

- **Assertion method parameter1:** expectedValue.
- **Assertion method parameter2:** actualValue.

## Conventions for writing test methods in JUnit 5

1. Method Signature:

Test methods must be annotated with `@Test`.
They should be public, void-returning, and take no parameters.

``` java
@Test
public void myTestMethod() {
    // Test logic
}
```

2. Test Names:

Test method names should be descriptive, concise, and follow a consistent naming convention.
Use camelCase or underscores to separate words.

``` java
@Test
public void calculateTotalCost() {
    // Test logic
}
```

3. Assertions:
Use JUnit's assertion methods for validating expected outcomes.
Common assertion methods include assertEquals, assertTrue, assertFalse, etc. 

```java 
@Test
public void testAddition() {
    int result = Calculator.add(2, 3);
    assertEquals(5, result);
}
```

4. Exception Testing:

To test methods that are expected to throw exceptions, use the assertThrows method.

```java 
@Test
public void testDivisionByZero() {
    assertThrows(ArithmeticException.class, () -> Calculator.divide(5, 0));
}
```

5. Test Annotations:

JUnit 5 provides various annotations to enhance test behavior, such as @BeforeEach, @AfterEach, @BeforeAll, and @AfterAll.

@BeforeEach and @AfterEach are particularly useful for setting up and tearing down resources before and after each test method.

```java
@BeforeEach
void setUp() {
    // Initialization logic before each test
}

@AfterEach
void tearDown() {
    // Cleanup logic after each test
}
```

6. Test Groups and Tags:

Use @Tag to categorize tests and run specific groups of tests.
Helpful for organizing and selectively running tests based on their characteristics.

```java
@Test
@Tag("integration")
void testIntegrationScenario() {
    // Integration test logic
}
```


7. Conditional Execution:

Use @Disabled to temporarily disable a test.
Conditional test execution based on specific conditions can be achieved using @EnabledOnOs, @EnabledIfSystemProperty, etc.

```java
@Test
@Disabled("Temporary disabled due to a bug")
void temporarilyDisabledTest() {
    // Test logic
}
```

8. Parameterized Tests:

JUnit 5 supports parameterized tests using @ParameterizedTest and various sources.
Allows you to run the same test with different inputs.

```java
@ParameterizedTest
@ValueSource(ints = {1, 2, 3})
void testWithParameter(int value) {
    // Test logic with parameterized input
}
```

## Conclusion

Mastering unit testing in Java with JUnit 5 is essential for ensuring the reliability and correctness of your code. By following best practices, leveraging JUnit 5 features, and adhering to conventions, you can build robust test suites that contribute to the overall quality of your software.
