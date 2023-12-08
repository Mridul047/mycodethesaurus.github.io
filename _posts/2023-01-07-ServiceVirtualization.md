---
title: "Service Virtualization"
date: 2023-01-07 14:22
categories: [java,wiremock]
tags: [java,wiremock]
---

# What is Service Virtualization ?

Service virtualization refers to the practice of creating a virtual version of a service, such as an API or a database, for the purpose of testing or development.

This can be useful under some scenarios listed below:
 
* When you want to test how your application handles different types of input or responses.
* When you want to build an application which depends on 3rd party APIs which may or maynot be available 24*7.
* The real service is unavailable or too slow.

# Tools for Service Virtualization

There are several tools available for service virtualization in Java, including:

* Mountebank:
 An open-source tool that allows you to create virtual services using a simple DSL (domain-specific language).

* Hoverfly:
 An open-source tool that allows you to create virtual services for a variety of protocols, including HTTP, HTTPS, and AMQP.

* Microcks:
 An open-source platform for mocking and testing APIs, which supports service virtualization for a variety of protocols including HTTP, HTTPS, and JMS.

* WireMock:
 A Java library for creating virtual HTTP services, which can be useful for testing HTTP clients.

# Service Virtualization using Wiremock 

WireMock is a library for creating virtual HTTP services in Java.
It can be used to create a stub server, which listens for incoming HTTP requests and returns pre-configured responses.
This can be useful for testing HTTP clients, as you can use WireMock to simulate different server behaviors and test how your client handles them.

For example, you can use WireMock to:

* Create a stub server that always returns a specific HTTP response, regardless of the request.
* Create a stub server that returns different responses depending on the request.
* Record and replay HTTP requests and responses for use in testing.

Let us take a look how we can integrate wiremock with springboot to create a virtualization service.

1. List of Dependecies required in pom.xml is listed below:
    ```xml
        <dependency>
            <groupId>com.github.tomakehurst</groupId>
            <artifactId>wiremock-jre8-standalone</artifactId>
            <version>2.35.0</version>
            <scope>compile</scope>
        </dependency>
    ```

2. Configure wiremock:
    ```java
    @Configuration
    public class CustomWiremockConfig {

        @Bean
        public WireMockServer wireMockServer(){
            WireMockConfiguration config = WireMockConfiguration.options()
                    .withRootDirectory("src/main/resources/wiremock")
                    .mappingSource(new JsonFileMappingsSource(new ClasspathFileSource("src/main/resources/wiremock/mocks")))
                    .port(9000);
            WireMockServer wireMockServer = new WireMockServer(config);
            wireMockServer.start();
            return  wireMockServer;
        }
    }
    ```
3. Create folder structure as highlighted below:

 ![Directory Strucutre](/assets/images/ServiceVirtualization/Project_Dir_Structure.jpg){: width="700" height="200" }

    * Role of "/wiremock/__file" :
        It's used to save the ResponseDefinition.

    * Role of "/wiremock/mocks" :
        It's used to store the RequestPattern.

4. Create sample request or RequestPattern using json file:
    ```json
        {
            "request": {
            "method": "GET",
            "url": "/api/products"
             },
            "response": {
            "status": 200,
            "headers": {
                "Content-Type": "application/json"
                 },
            "bodyFileName": "response.json"
             }
        }
    ```

5. Create a sample response or ResponseDefinition usign json file:
    ```json
        [
            {
                "id": 1,
                "title": "Fjallraven - Foldsack No. 1 Backpack, Fits 15 Laptops",
                "price": 109.95,
                "description": "Your perfect pack for everyday use and walks in the forest. Stash your laptop (up to 15 inches) in the padded sleeve, your everyday",
                "category": "men's clothing",
                "image": "https://fakestoreapi.com/img/81fPKd-2AYL._AC_SL1500_.jpg",
                "rating": {
                "rate": 3.9,
                "count": 120
                }
            },
            {
                "id": 2,
                "title": "Mens Casual Premium Slim Fit T-Shirts ",
                "price": 22.3,
                "description": "Slim-fitting style, contrast raglan long sleeve, three-button henley placket, light weight & soft fabric for breathable and comfortable wearing. And Solid stitched shirts with round neck made for durability and a great fit for casual fashion wear and diehard baseball fans. The Henley style round neckline includes a three-button placket.",
                "category": "men's clothing",
                "image": "https://fakestoreapi.com/img/71-3HjGNDUL._AC_SY879._SX._UX._SY._UY_.jpg",
                "rating": {
                "rate": 4.1,
                "count": 259
                }
            },
            {
                "id": 3,
                "title": "Mens Cotton Jacket",
                "price": 55.99,
                "description": "great outerwear jackets for Spring/Autumn/Winter, suitable for many occasions, such as working, hiking, camping, mountain/rock climbing, cycling, traveling or other outdoors. Good gift choice for you or your family member. A warm hearted love to Father, husband or son in this thanksgiving or Christmas Day.",
                "category": "men's clothing",
                "image": "https://fakestoreapi.com/img/71li-ujtlUL._AC_UX679_.jpg",
                "rating": {
                "rate": 4.7,
                "count": 500
                }
            },
            {
                "id": 4,
                "title": "Mens Casual Slim Fit",
                "price": 15.99,
                "description": "The color could be slightly different between on the screen and in practice. / Please note that body builds vary by person, therefore, detailed size information should be reviewed below on the product description.",
                "category": "men's clothing",
                "image": "https://fakestoreapi.com/img/71YXzeOuslL._AC_UY879_.jpg",
                "rating": {
                "rate": 2.1,
                "count": 430
                }
            },
            {
                "id": 5,
                "title": "John Hardy Women's Legends Naga Gold & Silver Dragon Station Chain Bracelet",
                "price": 695,
                "description": "From our Legends Collection, the Naga was inspired by the mythical water dragon that protects the ocean's pearl. Wear facing inward to be bestowed with love and abundance, or outward for protection.",
                "category": "jewelery",
                "image": "https://fakestoreapi.com/img/71pWzhdJNwL._AC_UL640_QL65_ML3_.jpg",
                "rating": {
                "rate": 4.6,
                "count": 400
                }
            }
        ]
    ```

6. All setup is done w.r.t virtualization service. Once you start the application hit following link to get 
    the corresponding response:

    ```
        http://localhost:9000/api/products
    ```
