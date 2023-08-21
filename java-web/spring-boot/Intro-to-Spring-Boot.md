## Introduction to Spring Boot

- **Spring framework** is an umbrella project that encompasses many powerful Java libraries for **web development**. Spring includes many high-level industry standard solutions for **essential components of web development**, such as database access, security, cloud deployment, web services, and more.
- **Spring Boot** is a part of Spring framework. 
- **Spring Boot** helps in rapid application development. 
- **Spring Boot** provides developers the way of getting started with Spring's core libraries with a minimal configuration setup.
- We have to learn **the Spring way of solving problems**.

#### Three main components of a Web App

3 main components in a standard web application:

- **Data Storage:** How the data is stored?
- **Application Logic:** What is the service of your application providing for users?
- **Client Access:** What type of client accesses to the service?

In **plain Java**, you have to do a lot of tasks to handle fully requirements of 3 above main components in a web application. With Spring, the solution to all of these is actually just **a Spring Boot application**. 

**How to build and manage each of 3 main components in Java using Spring Boot?**

- Write many **small Spring components** called **beans** or sometimes services, which are just Java classes that Spring **knows** about.
- These beans can take advantage of the Spring's vast array of libraries to perform tasks like database access or HTML template rendering.
- Spring allows us to create networks of **dependencies between beans**, which means we can write **single-purpose components** that rely on each other to perform complex tasks.

![Web Application Architecture](/imgs/java_web/l0-10-intro-to-web-development.jpg "The Architecture of a Web Application")

#### Business Stakeholders

- **Developers** who write the code.
- **Testers** who write and run tests.
- **Designers** who create prototypes for the developers to implement.
- **Project Architects** who choose the technologies best suited to a project's requirements
- **Business Analysts** who write technical specifications for the developers to follow.
- **Project Managers** who plan development efforts.

#### Spring vs Spring Boot

Internally, Spring Boot is built on top of the Spring. 

Spring takes care of the actions:
  
  1. Inversion of control (IoC)
  2. Dependency injection
  3. Common annotations
  4. Implements and provides the **foundation** for other frameworks, such as Thymeleaf, MyBatis, Spring MVC, and many others.

Spring Boot brings all the functionality of Spring and some additional advantages, such as:

  1. It can automatically configure Spring and third-party libraries
  2. It provides necessary dependencies to **ease the build configuration**

#### Covered Topics in the Course
![Course Outline](/imgs/java_web/l0-30-course-outline.jpg "Course Outine")

![Overview of Curriculum Goals](/imgs/java_web/l0-42-recap.jpg "Overview of Curriculum Goals")