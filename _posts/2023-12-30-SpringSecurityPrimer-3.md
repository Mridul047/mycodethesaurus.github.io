---
title: "User Authentication using Spring Security"
date: 2024-01-01 15:00
categories: [java, spring security, authentication]
tags: [java, spring security]
---

## Overview

Now that we have an idea of how spring security is configured in spring boot application. Lets take a look on some other helper classes which will be used in authentication using spring security.

## SecurityContextHolder

`SecurityContextHolder` class is responsible for storing information about the authenticated user such as username, user authorities and other user identification details.
As default setting, `SecurityContextHolder` uses a `ThreadLocal` to store user details.This means that the `SecurityContext` is always available to methods in the same thread, even if the `SecurityContext` is not explicitly passed around as an argument to those methods.

## SecurityContext

`SecurityContextImpl` implements `SecurityContext` interface.
The instance can be obtained from the `SecurityContextHolder` which contains the `Authentication` of the currently authenticated user. 

Spring Security’s `FilterChainProxy` ensures that the `SecurityContext` is always cleared.

## Authentication

`AbstractAuthenticationToken` implements `Authentication` interface.
An instance of  `Authentication` is given as input to AuthenticationManager to provide the credentials provided by the user has  to authenticate or the current user from the `SecurityContext`.

The `Authentication` contains:

`principal`: Used for identifiying the user. When authenticating with a username/password this is often an instance of UserDetails.

`credentials`: Often a password. In many cases, this is cleared after the user is authenticated, to ensure that it is not leaked.

`authorities`: The `GrantedAuthority` instances are high-level permissions the user is granted. Two examples are roles and scopes.

`GrantedAuthority`  instances can be obtained from the `Authentication.getAuthorities()`. This method provides a `Collection of GrantedAuthority` objects.

## AuthenticationManager

`AuthenticationManager` instance is responsible for authenticating the http request.
The most common implementation of `AuthenticationManager` is  `ProviderManager`.


## ProviderManager

`ProviderManager` contains List of `AuthenticationProvider` which is used for authentication.

## AuthenticationProvider

`AuthenticationProvider` has various implementations. Instance of this class is responsible of performing the actual authentication. 
One of the most commonly used implementation is `DaoAuthenticationProvider`.


## Conclusion

