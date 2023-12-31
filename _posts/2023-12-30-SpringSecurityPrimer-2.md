---
title: "Exploring Spring Security configuration"
date: 2023-12-30 18:30
categories: [java, spring security]
tags: [java, spring security]
---

## Overview

Having gained insights into the architecture of Spring Security for servlets, we now turn our attention to how Spring Boot configures Spring Security within a Spring Boot application.

## Spring Security Configurations

### SecurityAutoConfiguration Class

At the core of Spring Security autoconfiguration is the [SecurityAutoConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/security/servlet/SecurityAutoConfiguration.html) class. It utilizes three additional classes: `SpringBootWebSecurityConfiguration`, `WebSecurityEnablerConfiguration`, and [SecurityDataConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/security/SecurityDataConfiguration.html) for a comprehensive autoconfiguration process.

### SpringBootWebSecurityConfiguration Class

The `SpringBootWebSecurityConfiguration` class is loaded when security is available, and no custom configuration is defined. This class ensures that default configurations are applied unless overridden by user defined custom configs.

### WebSecurityEnablerConfiguration Class

The `WebSecurityEnablerConfiguration` class dynamically adds the `@EnableWebSecurity` annotation if Spring Security is present in the classpath. However, this configuration step is skipped if the annotation is explicitly added or a bean named `springSecurityFilterChain` is defined.

### @EnableWebSecurity Annotation

The [@EnableWebSecurity](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/config/annotation/web/configuration/EnableWebSecurity.html) annotation plays an important role in Spring Security configuration. It provides default instances for [WebSecurityConfiguration](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/config/annotation/web/configuration/WebSecurityConfiguration.html) and `HttpSecurityConfiguration`, enabling [@EnableGlobalAuthentication](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/config/annotation/authentication/configuration/EnableGlobalAuthentication.html). The `WebSecurityConfiguration` manages security for web components, such as images, CSS, and JS files, while the `HttpSecurityConfiguration` configures web security for HTTP requests.

### SecurityDataConfiguration Class
The [SecurityDataConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/security/SecurityDataConfiguration.html) class provides support for Spring Data integration
with Spring Security. It defines a bean called `SecurityEvaluationContextExtension`,
which allows Spring Security to be exposed as SpEL expressions to create Spring Data
queries.

### UserDetailsServiceAutoConfiguration Class

The [UserDetailsServiceAutoConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/security/servlet/UserDetailsServiceAutoConfiguration.html) class helps in configuration of an [InMemoryUserDetailsManager](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/provisioning/InMemoryUserDetailsManager.html) if no instance of [UserDetailsService](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/core/userdetails/UserDetailsService.html) is specified in the application. The default implementation includes a user with the default username 'user' and a randomly generated UUID password. Developers can provide custom implementations of the `UserDetailsService` interface to override this default configuration.

### SecurityFilterAutoConfiguration Class

The last autoconfiguration class we'll explore is [SecurityFilterAutoConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/security/servlet/SecurityFilterAutoConfiguration.html), responsible for configuring the [DelegatingFilterProxyRegistrationBean](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/web/servlet/DelegatingFilterProxyRegistrationBean.html). This class registers the Spring Security filter [DelegatingFilterProxy](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/filter/DelegatingFilterProxy.html) as a [ServletContextInitializer](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/web/servlet/ServletContextInitializer.html) and is invoked after the `SecurityAutoConfiguration`.

## Conclusion

In this exploration of Spring Security configuration, we've unveiled the internals of autoconfiguration in a Spring Boot application. Understanding these configurations is crucial for tailoring security measures to specific application needs.
