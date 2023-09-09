### Connecting to Data Sources

#### Terms

**Data Source:** A source of data, such as a database

**DataSource:** Java utility class (interface) that manages connections to physical or logical databases

#### Summary the article: [JDBC - a short guide](https://www.marcobehler.com/guides/jdbc)

- We can **manually** make connections to database through JDBC driver.
- Why needs JDBC **connection pooling**? Connection pooling is a pool containing already open connections. With these open connections they can be re-used. It is more efficient in terms of performance because it takes time to open a connection to database.
- **DataSource** is an interface that represents a connection pool. There are many implementations for DataSource interface, such as: **[HikariCP](https://github.com/brettwooldridge/HikariCP)**, **[Tomcat JDBC Connection Pool](https://tomcat.apache.org/tomcat-9.0-doc/jdbc-pool.html)**, **[Oracle's UCP](https://docs.oracle.com/database/121/JJUCP/intro.htm#JJUCP8109)**, etc. Spring uses HikariCP by default.

### Add Connector dependency 

- To use external database, we must add connector dependency to ```pom.xml```. For example:

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
 ```

### Ways to configure DataSources in Spring

##### Way 1: From ```application.properties``` file

- Spring **automatically** builds a DataSource for us **on startup**. 
- We can configure to override all the properties of the datasource by setting values in the ```application.properties``` file.
- [List of Sprign Data Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#data-properties)


**Example:**

```
spring.datasource.url=jdbc:mysql://localhost:3306/exampledb
spring.datasource.username=sa
spring.datasource.password=sa1234
 ```

##### Way 2: Create ```@Bean``` that return DataSource instances

```
@Configuration
public class DatasourceConfig {

   @Bean
   public DataSource getDatasource() {
       DataSourceBuilder dsb = DataSourceBuilder.create();
       dsb.username("sa2");
       dsb.password(securePasswordService());
       dsb.url("jdbc:mysql://localhost:3306/exercise1");
       return dsb.build();
   }

   private String securePasswordService() {
       return "sa1234";
   }
}
 ```

##### Way 3: Combine both ways 1 and 2 using ```@ConfigurationProperties```

```
@Configuration
public class DatasourceConfig {

   @Bean
   @ConfigurationProperties(prefix="foo.datasource")
   public DataSource getDatasource() {
       DataSourceBuilder dsb = DataSourceBuilder.create();
       dsb.password(securePasswordService());
       return dsb.build();
   }

   private String securePasswordService() {
       return "sa1234";
   }
}
 ```

*```application.properties```* file

```
//other properties
foo.datasource.username=sa3
foo.datasource.password=1234
 ```

In this case, the properties with prefix *'foo.datasource'* will be overrode by values in the properties file on returned object. It means the database instance with the last value of password is *1234* instead of *sa1234*.

##### Further reading the article [Guide to ```@ConfigurationProperties``` in Spring Boot](https://www.baeldung.com/configuration-properties-in-spring-boot)

#### DataSourceProperties

- **Situation:** Different DataSource providers sometimes have **different names** for their properties. 
- **Question:** How to manage converting between the standard ```spring.datasource``` properties and your desired DataSource type?

- **Answer:** Using **DataSourceProperties** object

    - Create a ```@Bean``` returning a ```DataSourceProperties``` object you populate from your properties file
    - Create a ```@Bean``` returning a ```DataSource``` with a parameter input is the ```DataSourceProperties``` object
    - add ```@Primary``` to both beans so that Spring knows which beans to use by default of that type

```
@Bean
@Primary
@ConfigurationProperties("spring.datasource")
public DataSourceProperties getDataSourceProperties(){
   return new DataSourceProperties();
}

@Bean
@Primary
@ConfigurationProperties(prefix = "spring.datasource.configuration")
public DataSource getDatasource(DataSourceProperties properties) {
   return properties.initializeDataSourceBuilder().type(HikariDataSource.class).build();
}
 ```

#### Multiple DataSources

- **Situation:** Data is stored in multiple locations. it means different entities from different physical datasources. For example, if our Person entities were stored in our main database, but Outfit entities were stored in a remote database.

- **Question:** How to configure a separate datasource bean for each of the two entities and Spring would manage our connections to retrieve them from the appropriate locations?

- **Answer:** If your data is stored in multiple locations, you can access it by creating multiple DataSource ```@Beans```. You will have to provide beans for an ```EntityManagerFactory``` and a ```TransactionManager``` as well. To support Spring Data repositories, we also use the ```@EnableJpaRepositories``` annotation to reference the specific classes.

*DataSourceConfig.java*

```
@Configuration
@EnableJpaRepositories(basePackageClasses = Humanoid.class, entityManagerFactoryRef = "humanoidFactory")
public class DatasourceConfig {

   @Bean
   @Primary
   @ConfigurationProperties("spring.datasource")
   public DataSourceProperties getDataSourceProperties(){
       return new DataSourceProperties();
   }

   @Bean
   @Primary
   @ConfigurationProperties(prefix = "spring.datasource.configuration")
   public DataSource getDatasource(DataSourceProperties properties) {
       return properties.initializeDataSourceBuilder().type(HikariDataSource.class).build();
   }

   @Bean(name = "humanoidFactory")
   @Primary
   public LocalContainerEntityManagerFactoryBean humanoidEntityManagerFactory(
           EntityManagerFactoryBuilder entityManagerFactoryBuilder) {
       return entityManagerFactoryBuilder
               .dataSource(getDatasource(getDataSourceProperties()))
               .packages(Humanoid.class)
               .persistenceUnit("humanoid")
               .build();
   }

   @Bean(name = "humanoidTx")
   @Primary
   public PlatformTransactionManager humanoidTransactionManager(@Qualifier("humanoidFactory")EntityManagerFactory entityManagerFactory){
       return new JpaTransactionManager(entityManagerFactory);
   }
}
 ```

*DataSourceConfig2.java*

```
@Configuration
@EnableJpaRepositories(basePackageClasses = {Outfit.class, OutfitRepository.class}, entityManagerFactoryRef = "outfitFactory")
public class Datasource2Config {

   @Bean
   @ConfigurationProperties("spring.datasource2")
   public DataSourceProperties getDataSource2Properties(){
       return new DataSourceProperties();
   }

   @Bean
   @ConfigurationProperties(prefix = "spring.datasource2.configuration")
   public DataSource getDatasource2(DataSourceProperties properties) {
       return properties.initializeDataSourceBuilder().type(HikariDataSource.class).build();
   }

   @Bean(name = "outfitFactory")
   public LocalContainerEntityManagerFactoryBean outfitEntityManagerFactory(
           EntityManagerFactoryBuilder entityManagerFactoryBuilder) {
       return entityManagerFactoryBuilder
               .dataSource(getDatasource2(getDataSource2Properties()))
               .packages(Outfit.class)
               .persistenceUnit("outfit")
               .build();
   }

   @Bean(name = "outfitTx")
   public PlatformTransactionManager outfitTransactionManager(@Qualifier("outfitFactory")EntityManagerFactory entityManagerFactory){
       return new JpaTransactionManager(entityManagerFactory);
   }
}
 ```

**Further Reading** 

- The article [Configure and Use Multiple DataSources in Spring Boot - Baeldung](https://www.baeldung.com/spring-boot-configure-multiple-datasources) and [Source code here](https://github.com/eugenp/tutorials/tree/master/persistence-modules/spring-data-jdbc)

- [Spring Boot documentation on custom and multiple DataSources](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.data-access)

### Database Initialization

##### Initializing databases using SQL scripts (```data.sql```, ```schema.sql```)

- ```spring.datasource.initialization-mode``` (for Spring Boot under 2.5) or ```spring.sql.init.mode``` (for Spring Boot 2.5 and above): tell Spring whether or not attempts to **generate schema** for our database automatically from ```schema.sql``` for creating schema (DDL) and ```data.sql``` for importing data (DML).

- 3 options for the property:
  - **Embedded**: Default. Initialization performed on embedded databases only.
  - **Always**: Initialization for both embedded and external databases.
  - **Never**: No initialization for either embedded or external databases.

**Platform-specific Initialization**

You may provide additional initialization scripts for specific platforms:

- ```schema-${platform}.sql``` and ```data-${platform}.sql```
- By setting property ```spring.datasource.platform={platform}```
- For example: ```spring.datasource.platform=foo``` tells Spring to try and execute ```schema-foo.sql``` and ```data-foo.sql```.

**Good Practice:** 

- In development: set **Always**
- In production: set **Never**

***defer-datasource-initialization***

- By default, ```data.sql``` scripts get executed before the Hibernate is initialized. We need to set ```spring.jpa.defer-datasource-initialization``` to ```true``` so that Hibernate creates our tables before inserting the data into them.
- ```defer-datasource-initialization=true```: 
  
  - ```JPA schema create from Entities``` > ```import.sql``` > ```schema.sql``` > ```data.sql```

- ```defer-datasource-initialization=false```: 
  
  - ```schema.sql``` > ```data.sql``` > ```JPA schema create from Entities``` > ```import.sql``` 

**Remind: deprecated properties**

- In Spring Boot 2.5, ```spring.datasource.*``` is being mapped to ```DataSourceProperties``` beans automatically and some fields are deprecated and migrated to ```spring.sql.init.*```, such as:

  - ```spring.datasource.initialization-mode``` --> ```spring.sql.init.mode```

  - ```spring.datasource.platform``` --> ```spring.sql.init.platform```

##### Initializing databases using Hibernate

JPA has features for DDL generation, and these can be set up to run on startup against the database. This is controlled through two external properties:

- ```spring.jpa.generate-ddl``` (boolean): default ```false```, is vendor independent
- ```spring.jpa.hibernate.ddl-auto``` (enum: ```none```, ```validate```, ```update```, ```create``` and ```create-drop```)

    - Default value: ```create-drop``` for embedded databases, ```none``` for non-embedded databases
    - Be careful when switching from in-memory to a ‘real’ database that you do not make assumptions about the existence of the tables and data in the new platform. You **either** have to **set ```ddl-auto```** explicitly **or** use one of **the other mechanisms** to initialize the database.
    - In addition, ```import.sql``` file is executed on startup if Hibernate creates the schema (ddl) from scratch (that is ```ddl-auto``` is set ```create``` or ```create-drop```). This is a **Hibernate feature** (and has nothing to do with Spring)

    ![](/imgs/java_web/initialize-db-properties.png)

    - **```create```**: Drop all tables for defined Entities, then create them.
    - **```create-drop```**: Create tables, drop them when application stops.
    - **```update```**: Attempt to migrate previous version of tables to match current Entities.
    - **```validate```**: Throw an exception if tables or columns are missing.
    - **```none```**: Do not initialize tables.

[Spring documentation on Database Initialization](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.data-initialization)

##### Additional properties

```
 # print all generated sql commands to the console
 spring.jpa.show-sql=true
 # formatting the sql output that makes it easier to read
 spring.jpa.properties.hibernate.format_sql=true
 ```

### Unit Test Data Source

#### Configuring Unit Tests with ```application.properties```

- Create a new ```application.properties``` file and put it to ```[project root]/src/test/resources```

```
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:db
spring.datasource.username=sa
spring.datasource.password=sa

# Optional for the properties following with embedded databases
spring.sql.init.mode=embedded
spring.jpa.generate-ddl=true
spring.jpa.hibernate.auto-dll=create-drop

# The properties for generating sql commands to the console
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
 ```

**@SpringBootTest**

- This annotation goes on your unit test class.
- Creates an **entire Spring ApplicationContex**t when running unit tests.
- It is used if you need to **test controller or service classes**, or perform **integration tests** spanning multiple layers.

**@DataJpaTest**

- Test your **data layer** without providing an ```application.properties``` file.
- It disables Spring auto-configuration and automatically uses an in-memory database if available.
-  It **only** loads Entities and Spring Data JPA repositories, but **not** your Services or Controllers.

**@AutoConfigureTestDatabase**

- This annotation can be used with either ```@SpringBootTest``` or ```@DataJpaTest```.
- Use it to **customize Spring’s behavior** for replacing the normal datasource. 

Example: ```@AutoConfigureTestDatabase(replace=Replace.NONE)``` tells Spring that it should **NOT replace** the datasource with an in-memory datasource


#### Errors 

##### Unable to find @SpringBootTest

```
java.lang.IllegalStateException: Unable to find a @SpringBootConfiguration, you need to use @ContextConfiguration or @SpringBootTest(classes=...) with your test
 ```

**Reason:** ApplicationTest has a different package structure from Application.

![](/imgs/java_web/test-errors-1.png)

**Solution:** 

1. Restructure the package structure 
2. Or tell Spring where to find the ```@SpringBootConfiguration```

```
@SpringBootTest(classes = EntitiesApplication.class)
 ```

##### Failed to load ApplicationContext

```
java.lang.IllegalStateException: Failed to load ApplicationContext
 ```

In this case, we should consider the reason from the ```@Bean``` that configures DataSource in a programmatic way. Maybe you configure it with an external url, and this property cannot be overrode from the url of ```application.properties``` file for testing.