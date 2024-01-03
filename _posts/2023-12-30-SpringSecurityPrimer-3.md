---
title: "User Authentication using Spring Security"
date: 2024-01-01 15:00
categories: [java, spring security, authentication]
tags: [java, spring security]
---

## Overview

In this post, we delve into the core aspects of Spring Security authentication within a Spring Boot application. The focus lies on the fundamental helper classes that play a pivotal role in the authentication process.

## SecurityContextHolder

![Security Context Holder Class](/assets/images/SpringSecurity/SecurityContextHolderClass.png)


The `SecurityContextHolder` class serves as a repository for storing authenticated user information, encompassing the username, user authorities, and other identification details. It leverages a `ThreadLocal` by default, ensuring that the `SecurityContext` remains consistently accessible to methods within the same thread.

## SecurityContext

Derived from `SecurityContextHolder`, the `SecurityContextImpl` class implements the `SecurityContext` interface. It encapsulates the `Authentication` of the presently authenticated user. Proper clearing of the `SecurityContext` is managed by Spring Security’s `FilterChainProxy`.

## Authentication

Implementing the `Authentication` interface, the `AbstractAuthenticationToken` class serves as input to the `AuthenticationManager` for either authenticating user credentials or retrieving the current user from the `SecurityContext`. Key elements of `Authentication` include:

- `principal`: Identifies the user, often represented by an instance of `UserDetails` during username/password authentication.
- `credentials`: Typically a password, frequently cleared post-authentication to prevent any potential leakage.
- `authorities`: Represents high-level permissions granted to the user, obtainable from `Authentication.getAuthorities()`.

## AuthenticationManager

The `AuthenticationManager` instance bears the responsibility of authenticating HTTP requests, with the widely-used implementation being `ProviderManager`.

## ProviderManager

`ProviderManager` maintains a list of `AuthenticationProvider` instances for handling authentication.

## AuthenticationProvider

Diverse implementations of `AuthenticationProvider` exist, with `DaoAuthenticationProvider` being a commonly used option. This provider is responsible for the actual authentication process.

## Authentication Flow using Spring Security

![Spring security authN flow](/assets/images/SpringSecurity/SpringSecurityAuthNFlow.png){: width="700" height="200" }

1. The initial request is processed by authentication filters, each handling the request based on the configured security strategy. For example, the `BasicAuthenticationFilter` processes requests with HTTP basic authentication.

2. The authentication filter generates an authentication token from the incoming request.

3. The `AuthenticationManager` is invoked to authenticate the request.

4. The `AuthenticationManager` contains a list of `AuthenticationProvider` instances.

   - An `AuthenticationProvider` has two methods: `supports(..)` and `authenticate(..)`.
   
   - The `supports(..)` method determines whether the `AuthenticationProvider` supports the authentication type.
   
   - The `authenticate(..)` method performs the actual authentication.

5. The `AuthenticationProvider` utilizes the `UserDetailsService` implementation to execute authentication. The `UserDetailsService` loads the `UserDetails` from an identity store containing user account details, such as user authorities, username, password, and other relevant statistics.

   - The `AuthenticationProvider` leverages the loaded `UserDetails` instance to carry out the actual authentication.
   
   - The authenticated principal is then returned to the `AuthenticationManager`, and the resulting `Authentication` object is stored in the `SecurityContext` for subsequent use by other filters.

## Conclusion

In summary, this post sheds light on the essential components crucial for user authentication using Spring Security. We've explored the storage of user details in `SecurityContextHolder`, the pivotal role of `AuthenticationManager` in authenticating HTTP requests, and diverse implementations of `AuthenticationProvider`, including the widely-used `DaoAuthenticationProvider`.
