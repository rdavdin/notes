### Web Development in java

#### Java application server

**Java application server** is a _pluggable architecture_. It's a program that is capable of running other programs. It accomplishes this by **providing a common interface**, the **servlet**, for each program to communicate with and then runs each of the programs over the interface in parallel. In other words, it hosts many **specialized apps** in parallel.

A Java application server provides **utilities** like multi-threading, request filtering, and resource sharing to each application.

In the context of Java EE, an application server is a Java EE server.

To develop a web app with Java, we need to use a Java application server to operate fundamental tasks such:

- process HTTP requests and respond to them
- manage concurrent requests, many client connections in parallel

Which Java application server should you choose for your app?

- GlassFish
- TomCat
- Netty
- WildFly
- etc.

**Web Servers vs Application Servers**

- The core difference is that application servers have **full** support for the **Java EE spec**, while web servers support a **small subset** of that functionality.

![Web server vs application server](/imgs/java_web/javaee-spec-supp-2-1-768x381.webp) *Source: https://www.baeldung.com/java-servers*

#### Java Servlets

**Servlets** are the interface or the main connection between the apps you develop and the application server they run on.

By implementing one or more servlets in your application, in particular extending ```Servlet``` class, you provide **endpoints** that handle incoming requests in a way specific to your app needs, and you can map specific request URLs to specialized servlets by adding entries to a ```web.xml``` file. The server uses this configuration to instantiate and manage your servlets.

The server interacts with each servlet through 3 standard methods:
- ```service``` is where requests are handled, and the server will call this method when a request is routed to the servlet it's called on.
- ```init``` is where initialization of the servlet is handled, and the server will call this method directly after instantiating the servlet.
- ```destroy``` is where servlet resource cleanup is handled, and is called directly before the server terminates the servlet instance.

![The life cycle of a servlet](/imgs/java_web/screen-shot-2020-06-03-at-4.51.26-pm.png) 
*The Lifecycle of a Servlet in an Application Server*

![](/imgs/java_web/javaeett_dt_013.png)
Source [Java Web App Request Handling](https://javaee.github.io/tutorial/webapp001.html#GEYSJ)

  1. The client sends an HTTP request to the web server.
  2. A web server that implements Java Servlet and JavaServer Pages technology **converts the request** into an ```HTTPServletRequest``` object.
  3. This object is delivered to a web component, which can interact with JavaBeans components 
  4. or a database to generate dynamic content.
  5. The web component can then generate an ```HTTPServletResponse``` or can pass the request to another web component. A web component eventually generates a ```HTTPServletResponse``` object.
  6. The web server **converts** this object **to an HTTP response** and returns it to the client.

- [Java reflection API](https://www.baeldung.com/java-reflection)
- **JAR**: **J**ava **AR**chive
- **WAR**: **W**eb application **AR**chive

#### Spring Applications
###### Spring's place in an application server?
- Servlets and application severs together **form** a powerful platform. Spring is something built on top of that platform.
- Spring application components as classes are instantiated and managed by **the Spring IoC container** just like an application server instantiates and manages servlets.
- The Spring container is piggybacked on a servlet instance, meaning that the overall lifecycle of the Spring application components **is bounded** by the lifecycle of the servlet they're running on.
- In addition, Spring can re-route requests from the Servlet layer to our application components according to whatever logic we dictate.

![](/imgs/java_web/spring-place-in-app-server.png)
*Spring's place in an application server*

###### So, Why Spring? What advantages?
- Spring framework helps **automatically link** Spring application components thanks to the implementation of Spring, the dependency injection (DI) mechanism.
- So, developers' work is just to define **specialized, reusable** code components

###### So, What's Spring Boot for?
- Without Spring Boot, a developer has to do a lot of tasks related to configuration like creating a servlet for Spring to live on an application server. 
- Spring Boot is created to help developers reduce these kinds of tasks. It provides an **a-la-cart** Spring experience. Based on the application needs, a developer just picks corresponding modules, like ordering individual dishes from a menu. That's it! Spring Boot will do the rest. 

Use [Spring Initializr](https://start.spring.io/), an official project generator, to configure metadata and build properties of a project as well as what starter dependencies you want to include.

#### Summary
- Apps and app servers communicate with each other using servlets
- The server instantiates and manages each of an app's servlets, routing requests to them as configured, calling lifecycle methods when appropriate
- Why Spring? and What's Spring Boot for?
- How to use Spring Initialzr to start a web app?
 

### Quizzes

1. What some services does Spring help manage for us?
   - [x] Login Security
   - [x] HTML Generation
   - [ ] Responding to Browser Requests
   - [x] Database Access
   - [ ] Running Multiple Java Programs in Parallel

2. Which benefits of a java application server?
   - [x] It automatically handles multiple client connections simultaneously
   - [x] It can be configured to forward requests with custom logic
   - [x] It can share heavyweight or universal components with each of its applications
   - [ ] It can display web pages by itself
   - [ ] It can respond to HTTP requests with **application data** by itself

- _heavyweight_ or _universal_ components refer to **resources** that are shared across multiple apps running on the server. These resources can include things like database connections, thread pools.

3. Spring provides a lot of additional features that can help use to receive requests and create responses. What are some things Spring can help us do?
      - [x] Use templates to generate webpages as responses to HTTP requests
      - [x] Manage security contexts adn authenticate requests
      - [x] Route HTTP requests to specific application code
      - [x] Inject object instances into services that need to use them

4. Spring Starter Packs Quiz
Pretend it's time to make a new project having a basic user interface, using the Spring Initialzr and you must decide _what requirements to include_. Assume that we would include at least _Spirng MVC, Thymeleaf, Spring Security, and MyBatis.

Which of the following dependencies do you think we should include in a project that wants to use these tools to support REST web services and writes to a PostgreSQL database?
  - [x] Spring Boot DevTools
  - [x] Spring Web
  - [ ] Spring Web Services
  - [x] Thymeleaf
  - [x] Spring Security
  - [ ] JDBC API
  - [x] MyBatis Framework
  - [x] PostgreSQL Driver
  - [ ] Spring Integration
  - [ ] Spring Boot Actuator

**Why are these chosen?**

- Of course, **Thymeleaf**, **Spring Security** and **MyBatis**.
- Thymeleaf requires **Spring Web** to function
- MyBatis requires a database driver, and the only one listed is **PostgreSQL Driver**
- **Spring Boot DevTools** provides **valuable utilities** for development on **any Spring Boot project**
- That's enough!
- JDBC is covered by MyBatis - both are APIs for database access
- Spring Integration and Spring Actuator are high-level libraries for coordinating multiple servers or services, and in the project that doesn't need them.