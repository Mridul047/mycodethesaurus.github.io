---
title: "Filter vs Interceptor in Java"
date: 2023-12-19 22:30
categories: [java,spring security]
tags: [java,spring security]
---

# Overview

Filters and interceptors are two mechanisms used in Java for intercepting and manipulating requests and responses in a web application. While they share a common goal, they differ in their implementation and use cases. In this post, we'll explore the general differences between filters and interceptors and then delve into their usage in a Spring Boot application.

## Filters

Filters are a part of the Java Servlet API and are used to perform filtering tasks on either the request to a resource, the response from a resource, or both. Filters are configured in the `web.xml` file of a Java web application and operate at the Servlet API level. They provide a way to perform tasks such as logging, authentication, authorization, input validation, and more.

### Key Characteristics of Filters:

1. **Configuration**: Filters are configured in the `web.xml` file, and their order of execution is determined by their placement in the configuration.

2. **Servlet API Level**: Filters operate at the Servlet API level and can intercept requests and responses before they reach the servlet or after the servlet has processed them.

3. **Global Scope**: Filters are applied globally to all requests or specific URL patterns, making them suitable for tasks that need to be performed across the entire application.

## Interceptors

Interceptors, on the other hand, are specific to certain web frameworks, and their usage may vary depending on the framework being used. In the context of Spring Boot, interceptors are part of the Spring MVC framework and are used to perform operations before and after the execution of a controller method.

### Key Characteristics of Interceptors:

1. **Spring MVC Level**: Interceptors operate at the Spring MVC level, which means they are aware of Spring MVC components and can be used to intercept requests and responses around controller execution.

2. **Controller-Centric**: Interceptors are often tied to specific controllers or controller methods, allowing for more fine-grained control over the interception process.

3. **Spring Boot Integration**: In a Spring Boot application, interceptors are configured as beans and can be easily integrated into the application's context.

## Usage in Spring Boot

Now, let's explore the use of filters and interceptors in a Spring Boot application, along with some use cases.

### Filters in Spring Boot

To create a filter in Spring Boot, you can implement the `javax.servlet.Filter` interface and then register it in the `FilterRegistrationBean` class. Here's an example of a logging filter:

```java
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.io.IOException;

import org.springframework.stereotype.Component;

@Component
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        // Log request details
        // ...

        // Continue with the filter chain
        chain.doFilter(request, response);

        // Log response details
        // ...
    }

    // Other filter lifecycle methods
}
```

### Interceptor in Spring Boot

```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

@Component
public class TimingInterceptor implements HandlerInterceptor {

    private long startTime;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        // Record the start time before the controller is executed
        startTime = System.currentTimeMillis();
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                           ModelAndView modelAndView) throws Exception {
        // Calculate and log the execution time after the controller has been executed
        long endTime = System.currentTimeMillis();
        long executionTime = endTime - startTime;
        // Log executionTime
    }

    // Other interceptor methods
}
```

## Filter Use Case

A common use case for a filter in a Spring Boot application is authentication. You can create a custom authentication filter that intercepts incoming requests, checks for valid authentication tokens, and handles authentication logic before the request reaches the controller.

## Interceptor Use Case

Imagine a scenario where you want to log the execution time of specific controller methods. In this case, you can use an interceptor to record the start time before the controller is executed and calculate the execution time after the controller has completed its processing.


## Request Execution Flow with Use Case

1. **A user attempts to access a secured resource in the Spring Boot application by sending an HTTP request.**

2. **The request enters the Spring Boot application and passes through the filter chain:**
    - The authentication filter checks for a valid authentication token in the request header.
    - If the token is present and valid, the filter allows the request to proceed; otherwise, it may redirect the user to the login page or return an authentication error.

3. **After passing through the filter chain, the request reaches the DispatcherServlet:**
    - The DispatcherServlet maps the request to the corresponding controller method responsible for handling the secured resource.

4. **Before the controller method is invoked, the request enters the interceptor chain:**
    - The timing interceptor records the start time before the controller is executed.
    - Other interceptors in the chain may perform additional checks or modifications.

5. **The controller method is invoked to process the request and generate a response.**

6. **After the controller method execution, the response passes back through the interceptor chain:**
    - The timing interceptor calculates and logs the execution time.
    - Other interceptors may perform post-processing tasks.

7. **The response, now modified by the interceptors, continues its journey:**
    - It passes back through the filter chain.
    - Filters in the chain may modify the response further based on application-specific logic.

8. **Finally, the modified response is sent back to the user:**
    - If the user was successfully authenticated and authorized, they receive the secured resource.
    - If there were authentication issues or authorization failures, an appropriate response is sent, and the user may be redirected to an error page or prompted to log in again.

This detailed execution flow demonstrates how filters and interceptors work together to handle various aspects of request processing in a Spring Boot application, from authentication and authorization to logging and performance monitoring.


## Conclusion

In summary, filters and interceptors serve as powerful mechanisms in a Java web application, each with its own strengths and use cases. Filters are more global and operate at the Servlet API level, while interceptors are controller-centric and operate at the Spring MVC level. Understanding when to use filters and interceptors in a Spring Boot application allows developers to implement efficient and modular request and response processing.
