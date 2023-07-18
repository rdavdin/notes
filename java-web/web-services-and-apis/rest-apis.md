### Web APIs

#### Architecture Style

**REST** stands for **RE**presentational **S**tate **T**ransfer. It's **a set of guidelines** application developers use to design APIs.

There are 4 principles that APIs follow:

1. Data and functionality in the API are considered “resources” and identified through something called the URI, or Uniform Resource Identifier. These are accessed by web links.
2. Resources are manipulated using a fixed set of operations. 

   - GET retrieves a resource. 
   - POST creates one. 
   - Use PUT to update the resource, 
   - and of course, DELETE will remove it.

3. Resources can be represented in multiple formats, such as HTML, XML, plain text, and other formats defined by a media type.
4. Communication between the client and server (i.e. endpoint) is stateless. This means the server will not remember or store any state about the client that made the call.

#### Dependencies are necessary for developing a restful app in Spring Boot

1. **Spring Web** (spring-boot-starter-web): This starter pack is used for web application development and automatically adds libraries like **Spring MVC**, **REST**, **Tomcat**, and **Jackson**.
2. **H2 Database** (com.h2database): This starter pack is used for the H2 in-memory database.
3. **Spring Data JPA** (spring-boot-starter-data-jpa): This starter pack includes **JDBC**, **Hibernate**, **Transactions API**, **JPA**, and **Aspects**. Mainly features that we need to interact with a database

#### Annotations
- Entities are simple objects that limit the database and they have the ability to be **mapped** from the database table to a Java object.

- **@Entity**: This marks a **Java class** as an entity. Typically, an entity maps to a database table adn the table contains rows that represent that given entity.

- **@RestController**: This marks a class as a REST API. @RestController is a convenience annotation that combines @Controller and @ResponseBody.
- **@GetMapping**

**CrudRepository**

- It greatly reduces the amount of boilerplate code needed to implement a data access layer
- It uses the spring data JPA repository, and it **automatically** creates **simple queries behind the scenes** using method names