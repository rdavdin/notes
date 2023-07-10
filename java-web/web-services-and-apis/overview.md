### Overview

#### What is a Web Service?

- A web service is a way to share data between **two separate systems** or a way to retrieve data to display in your application.
- Web services are designed **to communicate with other programs** or applications rather than **directly with users** and are often used to integrate systems allowing data to flow seamlessly between the two.
- The means of communication between the client and server is via a **standard web protocol** like HTTP/HTTPs on the world wide web, that uses a **common language** or message format like JSON or XML.

**Some terms:**

- Client: The system requesting information
- Service Provider: The organization or person that created the web service
- Server: The system providing the information

#### Web Services vs APIs vs Microservices?

- APIs and microservices are an evolution
- An API allows for data sharing between two different applications or systems. Wha...t? Sounds a lot like a web service? Well... APIs are more dynamic.

**SOAP-based web services vs REST-based APIs?**

- SOAP complexities led to REST
- SOAP (Simple Object Access Protocol)

  - a messaging protocol using XML to allow applications running on different systems or platforms to communicate.

- APIs are more lightweight and streamlined than a web service. 

  - Web services typically carry more **overhead** to work with them. Web services are often dependent on the SOAP protocol to send and receive data over a network, which is not a lightweight architecture.
  - APIs typically have a more lightweight architecture and are good for devices with limited bandwidth like mobile devices
  - So, when comparing web services to APIs, the significance lies in **the amount of work required by consumer and provider to package and un-package the data**

- **Remember:** All web services are APIs but not all APIs are web services.

**How about microservices?**

- Microservices are similar to APIs but are **fully contained individual components** that communicate with each other in calling clients and are modeled around a **specific business domain**.



[A list of publicly accessible APIs](https://github.com/public-apis/public-apis/blob/master/README.md)
