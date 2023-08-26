## Testing and Logging

**Note:** Here is just some basic information. Need to investigate more!

### Testing

![](/imgs/java_web/testing-1.png)
_Types of Testing_

- **UAT** : User acceptance testing - to make sure that the requirements are met with the solution provided.
- **Sanity testing**: the basic test cases or the basic used cases that is set as per the requirement is met.
- **Regression testing**: is usually used in the maintenance phase. Particularly, testing the old features work well as before adding new features to the product.

#### Some keywords for Searching

ExpectedException, Mockito, PowerMock, TestNg

### Logging

#### By default
- By default, Spring Boot already includes the **SLF4J** library and **Logback**. 
- **SLF4J** serves as an **abstraction layer** for various logging frameworks. It means you can choose the logging implementation that best suits your needs.
- **Logback** is the **default** logging implementation in Spring Boot.
- So, if you are using Spring Boot, you can directly use SLF4J for logging in your app without any additional configuration.

#### Logging - Log4J

- Add package ```slf4j-log4j12``` to ```pom.xml``` to configure Log4J as the logging implementation for the app.
- The package ```slf4j-log4j12``` bridges the SLF4J API with the Log4j implementation.

```
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>2.0.7</version>
    <type>pom</type>
    <scope>test</scope>
</dependency>
 ```

**Note:** you can remove ```<scope>test</scope>``` to use Log4j in the scope of your app.

#### Log Levels

- *Trace*: Logs the fine-grained information. This is a high (most detailed) level of logging.
- *Debug*: Logs the information necessary for debugging.
- *Info*: Used for logging the status messages or any desirable field value
- *Warn*: Used for logging potentially unexpected/dangerous situations
- *Error*: Used for logging Exception and minor Error events
- *Fatal*: Used for logging very severe Error events that might lead the application to collapse.