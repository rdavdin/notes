### Data in Multi-Tier Architecture

- **Multi-tier Architecture** is a term used to refer to a form of design that **separates** various functions of the application into their own **layers**.

- **Goal**: Understand how data in our application is defined and used across these layers.

#### Lesson Outline

1. Value types vs Entity types
2. Basic Types in Java and JDBC
   A Basic Type acts as an **intermediary**   between a Java field type and a type definition provided by JDBC.
3. Identifiers
4. Relationships
5. Composition and Inheritance

#### Value Types vs Entity Types

**Entity Types**

Java classes that describe a collection of data. 

- Contain **data only**, no behavior
- Represented by **a table in the db**

**Value Types**

The data inside an Entity

- Primitives like int, boolean, char
- Classes that only represent **a single piece of data**, like BigInteger or LocalDate
- Represented by **a column in the db**

**Define an Entity type**

![](/imgs/java_web/screen-shot-2020-03-19-at-2.22.48-pm.png)
_@Entity, @Table, @Column annotations_

**_Remember_**: Entity classes must provide a public or protected 

##### Further Reading
[Hibernate Documentation on Mapping Types](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#mapping-types)

#### Basic Types

- Basic Types map a single database column to a single, non-aggregated Java type.

- [Full List of Hibernate standard Basic Types](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#basic)
- Hibernate selects default BasicTypes for you, but you can override by specifying the **@Type** annotation, as below.

![](/imgs/java_web/screen-shot-2020-03-19-at-2.24.14-pm.png)

#### Identifiers

Using the ```@Id``` annotation

```
@Entity
public class Person {
    @Id
    @GeneratedValue
    Long id;
   / *rest of class* /
}
```

```@GeneratedValue``` annotation indicates that this value to be assigned automatically.

**_Recommend_**: Hibernate recommends using the **long or integer wrapper classes** as identifiers

##### Composite Identifiers
Here are the rules governing composite identifiers, as defined by the Jakarta Persistence specification:

- The composite identifier must be represented by a "primary key class". 
- The primary key class must be public and must have a public no-arg constructor.
- The primary key class must be serializable.
- The primary key class must define equals and hashCode methods, consistent with equality for the underlying database types to which the primary key is mapped.
... [Source - detail of the documentation here](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#identifiers-composite)
- 