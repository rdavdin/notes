### Security

#### Authentication vs Authorization

- **Authentication**: Authentication is about validating the identity of a client attempting to call a web service. Typically, identity is validated with user credentials, such as a user name and password.
- **Authorization**: Authorization is the next step after authentication. So once a client is authenticated (they have proven who they are), what do they have access to? For example, what data can they view, are they allowed to change that data, etc.

##### Basic Authentication (Basic Auth)

- This is the simplest protocol available for performing web service authentication **over HTTP** protocol. 
- Basic Auth requires a username and password. The client calling the web service takes these two credentials, converts them to **a single Base 64-encoded value** and passes the value along in the **Authentication HTTP header**.
```
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
```

##### API Key Authentication

- API key authentication is another technique **used to secure Web Services**, which requires the API to be accessed with a unique key.

#### Spring Security

- **Spring Security** is a part of the Spring Framework and provides authentication, authorization and other security features for Spring-based applications.

![](../../imgs/java_web/spring-security-01.png)

##### Security configurations

- We can do security configuration entirely in a Java class. 

**Have a look at** [an example here](https://github.com/rdavdin/securedogapi)