---
title: "Spring Security Basics"
date: 2023-12-16 13:30
categories: [java,spring security]
tags: [java,spring security]
---

## Spring Security Evolution:

Originally named the "Acegi Security System for Spring," Spring Security emerged as a standalone project outside the Spring ecosystem. Initially focused on authorization and relying on container-provided authentication, its popularity soared over time. Driven by public demand and a growing contributor base, it transitioned to become an integral part of the Spring Framework portfolio, eventually rebranded as Spring Security.

## Understanding Spring Security:

Spring Security stands as a robust framework offering configurable Authentication and Authorization capabilities for securing Spring-based applications. Some key advantages include:

- **Open Source:** Spring Security is an open-source project.
- **Spring Best Practices:** Developed on top of the Spring framework, adhering to its best practices.
- **Versatile Authentication:** Out-of-the-box support for various authentication methods, including OpenID, LDAP, SAML 2.0, OAuth 2.0, Form Authentication, Database Authentication, and Cert. X.509 Authentication.

## When to Leverage Spring Security:

Consider using Spring Security in the following scenarios:

- **Securing Resources:** Restricting access to resources by securing URLs and enabling communication over HTTPS.
- **Role-Based Authentication/Authorization:** Enforcing role-based authentication and authorization.
- **Integration:** Integrating with OpenID, LDAP, and databases as security providers.
- **Certificate Authentication:** Enabling authentication using public/private key certificates.

## What's New in Spring Security 6:

With the release of Spring Security 6, several notable changes have been introduced:

- **Java 17 Baseline:** Spring Boot 3 and Spring Security 6 now have a baseline of Java 17.
- **WebSecurityConfigurerAdapter Deprecation:** The deprecated WebSecurityConfigurerAdapter class has been removed; now, you must create a bean of type SecurityFilterChain.
- **authorizeRequests Deprecated:** The deprecated authorizeRequests has been replaced by authorizeHttpRequests in the HttpSecurity configuration, offering fine-grained request matching for access control.
- **Matcher Deprecation:** AntMatcher, MvcMatcher, and RegexMatcher are deprecated and replaced by requestMatchers or securityMatchers for path-based access control, providing more flexibility in matching requests.

## Summary:

Spring Security has evolved from its origins as Acegi Security to becoming a pivotal part of the Spring Framework. With its rich feature set and continuous improvements, it remains a go-to choice for securing Java-based applications. In Spring Security 6, the framework embraces Java 17 and introduces changes aimed at enhancing configurability and flexibility in access control.