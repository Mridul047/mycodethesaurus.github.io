---
title: "Spring Security Basics"
date: 2023-12-16 13:30
categories: [java,spring security]
tags: [java,spring security]
---

# Spring Security History :

Spring Security began as a non-Spring project.
It's original name was “Acegi Security System for Spring”.

Originally, it dealt only with authorization and leveraged container-provided authentication.
Over time due to public demand, the project started gaining traction, as more people started using it and contributing to its continuously growing code base.

Eventually it became a Spring Framework portfolio project and then later it was rebranded as Spring Security.


# What is Spring Security ?

Spring security is framework which provides highly configurable Authentication & Authorization capabilities for securing spring based applications.

Some Benefits of using spring security:

* It's opensource.

* Developed on top of spring framework following spring best practices.

* Provides OOTB support for various Authentication methods such as :
    1. OpenID
    2. LDAP
    3. SAML 2.0
    4. OAuth 2.0
    5. Form Authentication
    6. Database Authentication
    7. Cert. X.509 Authentication

# When to use Spring Security ?

Some of the scenarios where spring security can be used are listed below:

* Restricting access to resouces by securing URLs & enabling communication over https.

* Enforcing role based authentication/authorization.

* Enabling integration with OpenID, LDAP & database as security providers.

* Enabling authentication using public/private key certificates.

# What's new in Spring Security 6 ? 

* The Spring Boot 3 and Spring Security 6 baseline is now Java 17.

* The WebSecurityConfigurerAdapter class has been deprecated and removed in Spring Security 6, so now you must create a bean of type SecurityFilterChain.

* Instead of using authorizeRequests, which has been deprecated, you should now use authorizeHttpRequests, which is part of the HttpSecurity configuration allowing you to configure fine-grained request matching for access control.

* AntMatcher, MvcMatcher, and RegexMatcher have been deprecated and replaced by requestMatchers or securityMatchers for path-based access control, allowing you to match requests based on patterns or other criteria without relying on specific matchers.

# Summary :