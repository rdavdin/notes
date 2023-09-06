### Documentation

#### Swagger

- OpenAPI (formerly known as the Swagger Spec) is one of the most popular API documentation spec.
- Swagger is the most well-known and widely used tool for implementing the OpenAPI spec.
- Swagger helps developers **design, build, document, consume REST APIs**.
- **SpringFox** is a swagger integration for the Spring framework.

  - Swagger Editor - A browser-based editor where you can **write OpenAPI specs**.
  - Swagger UI - A web application that **renders OpenAPI specs** as interactive API documentation.
  - Swagger Codegen - A tool that **generates server stubs** and client libraries from an OpenAPI spec.

- The documentation files (HTML, Javascript, CSS files) are bundled by Swagger UI to display the API in a web browser. And users can interact/test with the API's resources without having any of their implementation logic in place.

**Dependencies**
```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>3.0.0</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>3.0.0</version>
</dependency>
```

**Integrating Swagger 2 Into the Project**

The configuration of Swagger mainly centers around the Docket bean.

```
@Configuration
@EnableSwagger2
public class SwaggerConfig {
   @Bean
   public Docket api() {
       return new Docket(DocumentationType.SWAGGER_2)
               .select()
               .apis(RequestHandlerSelectors.any())
               .paths(PathSelectors.any())
               .build();
   }
}
```

- After defining the Docket bean, its select() method returns an instance of ApiSelectorBuilder, which provides a way to control the endpoints exposed by Swagger.

- We can configure predicates for **selecting RequestHandlers** with the help of RequestHandlerSelectors and PathSelectors. Using ```any()``` for both will make documentation for our entire API available through Swagger.

**Customizing Swagger**
- adjust predicates for selecting RequestHandlers under methods ```apis``` and ```paths```
- ```useDefaultResponseMessages(false)``` - to turn off the default response messages
- ```@ApiResponses``` - to customize error messages with Swagger

**Test your APIs**

At http://localhost:8080/swagger-ui.html

##### Further Reading
[Setting Up Swagger 2 with a Spring REST API Using Springfox](https://www.baeldung.com/swagger-2-documentation-for-spring-rest-api)

### Updated to Spring Boot 3.x

**Dependency** 

```
<!-- https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-starter-webmvc-ui -->
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.2.0</version>
</dependency>
 ```

**Change *SwaggerConfig.java***

```
@Configuration
public class SwaggerConfig {
    @Bean
    public OpenAPI api(){
        return new OpenAPI().info(new Info().title("This is a title").description("Description here").version("0.1"));
    }
}
 ```
**Configure to bypass Spring Security?**

```
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests((authReqs) -> {
            authReqs.requestMatchers(AntPathRequestMatcher.antMatcher("/swagger-ui.html")).permitAll();
            authReqs.requestMatchers(AntPathRequestMatcher.antMatcher("/swagger-ui/**")).permitAll();
            authReqs.requestMatchers(AntPathRequestMatcher.antMatcher("/v3/api-docs/**")).permitAll();
            authReqs.requestMatchers(AntPathRequestMatcher.antMatcher("/hello")).permitAll();
            authReqs.anyRequest().authenticated();
        }
    );
    http.httpBasic(Customizer.withDefaults());
    return http.build();
}
 ```

**Custom properties**

- Specify the path of the OpenAPI documentation, *default: /v3/api-docs*

```
springdoc.api-docs.path=/api-docs
 ```
- Specify the path of the Swagger UI, *default: /swagger-ui.html*
```
springdoc.swagger-ui.path=/swagger-ui.html
 ```
- Enable or disable Swagger UI

```
springdoc.swagger-ui.enabled=true
 ```