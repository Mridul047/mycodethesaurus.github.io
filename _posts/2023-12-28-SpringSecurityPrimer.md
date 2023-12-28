---
title: "Spring Security Servlet Architecture"
date: 2023-12-28 13:30
categories: [java, spring security]
tags: [java, spring security]
---

# Overview

In our journey through foundational concepts like Filters and Interceptors, coupled with essential security principles, we are now set to unravel the intricacies of Spring Security's operational mechanisms.

## Spring Security Servlet Architecture

![Spring security servlet architecture](/assets/images/SpringSecurity/SpringSecurityServletArchitecture.png){: width="700" height="200" }

## DelegatingFilterProxy

Spring introduces a vital Filter implementation known as `DelegatingFilterProxy`, bridging the gap between the Servlet container’s lifecycle and Spring’s ApplicationContext.

While the Servlet container registers Filter instances using its own standards, it remains oblivious to Spring-defined Beans.

`DelegatingFilterProxy` seamlessly integrates by registering through standard Servlet container mechanisms and then deferring all the operational responsibilities to a Spring Bean that implements Filter.

Here's a simplified breakdown of how it works:

1. **Registration:** `DelegatingFilterProxy` is registered using standard Servlet container methods.
2. **Delegation:** It looks up a Spring Bean (e.g., `Filter0`) from the ApplicationContext.
3. **Invocation:** Calls to `DelegatingFilterProxy`'s `doFilter()` method are delegated to the specified Spring bean (`Filter0`).

This mechanism empowers us to leverage all Spring features within our designated `Bean Filter0`.

## FilterChainProxy

Another noteworthy Filter implementation provided by Spring is `FilterChainProxy`, utilized by `DelegatingFilterProxy`. It encompasses one or more `SecurityFilterChain` instances responsible for processing HTTP requests.

The `SecurityFilterChain` interface includes two key methods: `matches(..)` and `getFilters(..)`.

- The `matches(..)` method evaluates whether the current `SecurityFilterChain` matches the incoming request. Spring Security offers the `RequestMatcher` interface with various implementations for matching, such as `AnyRequestMatcher` (matching all HTTP requests) and `AntPathRequestMatcher` (matching URL paths).

- If a match occurs, the `getFilters(..)` method returns the list of filters to be applied to the incoming request.

When following Spring Security's default configurations, it sets up a default `SecurityFilterChain` named `DefaultSecurityFilterChain`, configuring a predefined list of required filters. This guarantees that all HTTP requests traverse through this filter chain.

## Security Filters

The `FilterChainProxy` has a list of `SecurityFilterChain` which inturn has a list of `Security Filters` associated with it.
Depending upon the usecases those filters can be used for a number of different purposes ex. authentication, authorization, etc.

The filters are executed in a specific order to guarantee that they are invoked at the right time, 
ex. The Filter that performs authentication should be invoked before the Filter that performs authorization.

If you want to know more about filter ordering, you can check the `FilterOrderRegistration` code.

## Conclusion

In unraveling Spring Security's Servlet Architecture, we explored `DelegatingFilterProxy` and `FilterChainProxy`, seamlessly integrating Spring's capabilities into the Servlet container.

The heart of Spring Security lies in its `Security Filters`, strategically inserted into the `FilterChainProxy`. These filters handle authentication, authorization, and more, executed in a specific order for optimal functionality.

In summary, Spring Security's default configurations, exemplified by `DefaultSecurityFilterChain`, provide a robust and standardized security foundation. Adhering to these defaults ensures well-defined filter chains, enhancing overall application security.

This exploration equips you with essential insights for customizing security configurations within Spring Security's Servlet Architecture. Consider these concepts as invaluable tools for fortifying your web applications.
