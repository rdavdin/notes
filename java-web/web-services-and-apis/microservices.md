### Microservices

#### Microservices Architecture (MSA)

There are several disadvantages to the n-tier monolithic application architecture:

- Tight coupling of code which makes changes hard
- A single deployment with multiple layers that causes long testing, building and deployment cycles
- A big monolithic app that makes code reuse and sharing of components difficult

The Microservices Architecture (MSA) **decomposes** systems into **discrete, individual, standalone components** that can communicate amongst themselves, working together or with external systems.

Features of MSA:

- MSA is very **flexible** because it supports any language that can communicate via a RESTful endpoint and leverages REST over HTTP.
- MSA offers agility and systems that are easier to write, test, deploy, and share.
- MSA provides systems that can **better scale** to load and demand
- MSA provides systems that are **resilient** because failures are isolated and don't cascade through the infrastructure.
- Each microservice is for a **single purpose**,
- It's simple and has a well-defined interface,
- It's **modular and independent**,
- And it has **isolated persistence**, which means the microservices don't share data, they have their own unique database.


#### Spring Cloud & Eureka

##### What is a service registry? 

- A service registry is a crucial component that allows microservices to register and discover each other.
- It acts as a centralized directory where microservices can register their availability and **provide** information about their location (IP address and port). This eliminates the need for microservices to have prior knowledge of each other's addresses.
- The service registry keeps track of all the registered microservices and their corresponding network locations. This information enables other microservices to discover and communicate with each other dynamically.
- By using the service registry, microservices can easily locate and interact with other services without hardcoding IP addresses or maintaining complex configuration files.

##### Netflix's Eureka

- Eureka is a service registry. Spring has incorporated it into **Spring Cloud**.
- Eureka consists of two components:
  
  - A server-side component is the registry in which all microservices register themselves. It means that a microservice registers itself with the Eureka server by providing its own **metadata**, such as its name, IP address, and port.
  - A client-side component that allows microservices use the Eureka client to register; once the registration is complete, it notifies the server of its existence.

##### Create a Eureka server

- Need 2 dependencies:
  -  ```Config Client``` for ```spring-cloud-starter-config```
  -  ```Eureka Server``` for ```spring-cloud-starter-netflix-eureka-server```

![](/imgs/java_web/eureka-server-dependencies.png)
_Only 2 these dependencies for creating a Eureka server_

**Note:** Should use tool [Initializr](https://start.spring.io/) to generate ```pom.xml``` file properly

- Add ```@EnableEurekaServer```:

```
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}
}
 ```

- Update properties file to avoid registering itself as a client

```
spring.application.name=eureka-server
server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

spring.cloud.config.import-check.enabled=false
 ```

##### Convert a RESTful app to a microservice 

- Add additional dependencies: 
  - ```Config Client``` for ```spring-cloud-starter-config```
  - ```Eureka Discovery Client``` for ```spring-cloud-starter-netflix-eureka-client```

![](/imgs/java_web/eureka-client-dependencies.png)
_Add more additional dependencies for Eureka client_

**Note:** Should use tool [Initializr](https://start.spring.io/) to generate ```pom.xml``` file properly

- Add ```@EnableEurekaClient```:

```
@SpringBootApplication
@EnableEurekaClient
public class PricingServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(PricingServiceApplication.class, args);
    }
}
 ```

- Update properties file to register itself as a client to Eureka server

```
spring.application.name=pricing-service
server.port=8082

eureka.client.service-url.default-zone=http://localhost:8761/eureka/
eureka.instance.prefer-ip-address=true

spring.cloud.config.import-check.enabled=false
 ```

**Check Eureka server's dashboard at [http://localhost:8761/](http://localhost:8761/)**

![](/imgs/java_web/eureka-dashboard.png)
_2 services Boogle-maps and Pricing-service are registered successfully_

**Further Reading [Introduction to Spring Cloud Netflix – Eureka](https://www.baeldung.com/spring-cloud-netflix-eureka)**