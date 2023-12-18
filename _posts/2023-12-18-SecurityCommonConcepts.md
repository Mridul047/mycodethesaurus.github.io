---
title: "Common Security Concepts"
date: 2023-12-18 13:46
categories: [security]
tags: [security]
---

## User:
The first step in securing a system from malicious attackers is identifying legitimate users and allowing access to them alone. User abstractions are created in the system and given their own identity. They are the users that are later allowed to use the system.

### Real-life Example:
Consider a banking application that employs a robust user authentication system. Users create unique usernames and passwords during registration. These credentials act as the key to their accounts. Only users with the correct combination of a valid username and password are granted access to the banking system, ensuring that only legitimate users can perform transactions and access sensitive financial information.

## Credentials:
Credentials are the way a user proves who they are. Normally, in the shape of passwords (certificates are also a common way of presenting credentials), they are data that only the owner of it knows.

### Real-life Example:
In the case of online email services like Gmail, users need to enter a combination of a unique username and a password to access their accounts. This ensures that only individuals who possess the correct credentials can read and send emails from that particular account.

## Authentication:
Authentication is the process of verifying the identity of a user, system, or application.

### Real-life Example:
In a two-factor authentication system, users not only enter a password but also receive a one-time code on their mobile device. This additional step enhances security by requiring something the user knows (password) and something the user has (mobile device).

## Types of Authentications:
Authentication methods vary to accommodate different security needs. Here are some common types:

### 1. **Password-Based Authentication**
   - **Description:** Users authenticate by providing a password.
   - **Real-life Example:** Online banking platforms often rely on password-based authentication, where users enter a secret password associated with their account.

### 2. **Biometric Authentication**
   - **Description:** Users authenticate using their unique biological traits, such as fingerprints, iris scans, or facial recognition.
   - **Real-life Example:** Many modern smartphones use fingerprint or facial recognition as a form of biometric authentication to unlock the device.

### 3. **Two-Factor Authentication (2FA)**
   - **Description:** Users authenticate using two independent factors, typically something they know (password) and something they have (e.g., a mobile device).
   - **Real-life Example:** Online services often implement 2FA, where users enter a password and receive a temporary code on their mobile device.

### 4. **Multi-Factor Authentication (MFA)**
   - **Description:** Similar to 2FA but involves more than two authentication factors, adding an extra layer of security.
   - **Real-life Example:** Secure corporate systems may employ MFA, combining factors like a password, smart card, and biometric scan for enhanced security.

### 5. **Token-Based Authentication**
   - **Description:** Users authenticate using a physical or virtual token, which generates a one-time code.
   - **Real-life Example:** Secure key fobs or mobile apps generating time-sensitive codes for access to sensitive systems.

### 6. **Certificate-Based Authentication**
   - **Description:** Users authenticate by presenting a digital certificate, often issued by a certificate authority.
   - **Real-life Example:** SSL/TLS connections use certificate-based authentication to secure communication between web browsers and servers.


## Authorization:
Authorization is the process of determining what actions a user or system is allowed to perform after successful authentication.

### Real-life Example
In a file-sharing application, a user who is authenticated may have the authorization to view and edit their own files, but they may not have permission to access or modify files owned by other users.

## Types of Authorizations:
Authorization methods determine what actions a user or system is allowed to perform after successful authentication. Here are some common types:

### 1. **Role-Based Authorization**
   - **Description:** Users are assigned roles, and each role has a predefined set of permissions.
   - **Real-life Example:** In a healthcare system, a doctor role might have authorization to view patient records, while a nurse role may only have permission to update patient information.

### 2. **Attribute-Based Authorization**
   - **Description:** Access is determined by evaluating user attributes or characteristics.
   - **Real-life Example:** In a financial system, access to high-value transactions may require additional attributes such as a specific job title or authorization level.

### 3. **Rule-Based Authorization**
   - **Description:** Access is granted based on predefined rules or conditions.
   - **Real-life Example:** In an educational portal, a rule might be set to grant access to course materials only to users who have completed a prerequisite course.

### 4. **Policy-Based Authorization**
   - **Description:** Access decisions are based on a set of policies defined by administrators.
   - **Real-life Example:** In a government database, a policy might dictate that only users with security clearance level X can access certain classified information.

### 5. **Time-Based Authorization**
   - **Description:** Access permissions are granted or revoked based on specific time intervals.
   - **Real-life Example:** In a corporate environment, employees may have access to sensitive documents only during regular working hours.

## Resource:
Any part of the application you want to access that needs to be properly secured against unauthorized access—for example, a URL, a business method, or a particular business object.

### Real-life Example:
In an e-commerce website, product pages, customer profiles, and order processing functionalities are distinct resources. Each of these resources must be protected to prevent unauthorized access. For instance, only authenticated users should be able to view and update their own profiles, while only administrators may have permission to manage product listings.

## Permissions:
The access level needed to access a particular resource. For example, two users may be allowed to read a particular document, but only one can write to it. Permissions can apply to individual users or users that share a particular role.

### Real-life Example:
In a collaborative document editing platform like Google Docs, users can be assigned different permissions for a shared document. One user might have read-only access, allowing them to view the document but not make changes, while another user with editing permissions can make modifications to the content.

## Encryption
It allows you to encrypt sensible information (normally passwords, but it can be something else, like cookies) to make it incomprehensible to attackers even if they get access to the encrypted version. The idea is that you never store the plain text version of a password but instead store an encrypted version so that nobody but the owner knows the original one.

### Real-life Example
When you log in to a secure website, your password is often transmitted and stored in an encrypted form. If a hacker intercepts this data, they would only see the encrypted version of the password, making it extremely difficult to decipher without the proper encryption key. This adds an extra layer of security, ensuring that even if there's a data breach, the exposed passwords remain cryptographically protected.


## Summary:

In summary, a robust security framework encompasses user identification, authentication, authorization, resource protection, permissions, and encryption. Collectively, these elements form a comprehensive defense against malicious activities, ensuring the integrity and confidentiality of systems and data. This multi-layered approach establishes a secure environment by controlling access, verifying identities, and safeguarding sensitive information.