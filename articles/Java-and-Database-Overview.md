## **The article: [Java & Database: Libraries and APIs](https://www.marcobehler.com/guides/java-databases)**

### Noted main points

#### JDBC Summary

- When using JDBC, you are basically working with **bare metal**. You have the full power and speed of SQL nad JDBC at your hand, but you need to make sure to somehow **convert back and forth between your Java objects and the SQL code yourself.**
- You also have to make sure to be a good developer and open/close database connections yourself.
- That's where more convenient, light-weight frameworks come in.

#### Java ORM Frameworks: Hibernate, JPA and more

#### Hibernate

- It lets you (relatively) easily **convert between database tables and java classes** without having to do much apart from an **initial mapping**
- It allows you to **not have to write any SQL code** for (basic) CRUD operations. Think: _creating a user_, _deleting a user_, _updating a user_.

```
Session session = sessionFactory.openSession();
User user = new User();
user.setFirstName("Hans");
user.setLastName("Dampf");
// this line will generate and execute the "insert into users" sql for you!
session.save( user );
```
- It offers a **couple query mechanisms (HQL - Hibernate's Query Language, Criteria API)** on top of SQL to query your databases to **deal with more complex SQL statements**. 
_HQL_ looks similar to SQL, but is focused on your Java objects and actually **independent of the underlying SQL database**. That means, in theory, the same _HQL_ statements work for **all databases (MySQL, Oracle, Postgres, etc.)**, with the drawback that you lose access to all database specific features.

```
List<User> users = session.createQuery("select from User u where u.firstName = 'hans'", User.class).list();

session.createQuery("update User u set u.lastName = :newName where u.lastName = :oldName").executeUpdate();
```

See, both queries look very much like their SQL equivalents, but not that you are **not accessing SQL tables or columns (_first_name_)** in these queries. Instead, you are **accessing properties (_u.firstName_)** of your mapped ```User.java``` class. _Hibernate_ will then **make sure to convert** these _HQL_ statements to proper, database-specific SQL statements.

- In addition, Hibernate offers a ton of other _convenient features_, like _cascading_, _lazy loading_, _caching_, and much more. It **truly** is a complex piece of software, that you cannot fully grasp by just copy-and-pasting some online tutorial.
- To use Hibernate efficiently, you need to get good knowledge of **both Hibernate and SQL**.

**Suggested Reading**
1. Book [Java Persistence with Hibernate](https://www.manning.com/books/java-persistence-with-hibernate-second-edition)
2. [Vlad Mihalcea’s](https://vladmihalcea.com/) or [Thorben Janssen’s Sites](https://thoughts-on-java.org/)

#### Java Persistence API (JPA)?

- JPA is merely a specification, not an implementation or library. So, JPA defines a **standard** what features a library must support to be _JPA-compliant_. _Hibernate_, _EclipseLink_, or _TopLink_ that all implement the JPA spec.
- So, instead of writing Hibernate-specific code or EclipseLink-specific code, you write JPA-specific code. And then it is just a matter of **adding some libraries (Hibernate) and configuration file to your JPA project**, and you can access your database.
- Generally speaking, **JPA is another abstraction** on top of Hibernate.

**Remember:**
- In theory, JPA should describe its spec first, then implementations follow.
- But, in practice, as Hibernate is **by far the most popular JPA implementation**, features in JPA are often "a heavily-inspired-**subset**" of Hibernate features.

**JPQL - JPA's Query Language**

- JPA comes with its own query language, JPQL. 
- JPQL is essentially a heavily-inspired-**subset** of HQL, with JPQL queries always being valid HQL queries - **but not the other way around**.

**Main Takeaway**
- Make sure you get a good foundation on how ORM (e.g. Hibernate) works **AND** how SQL and your database work. Then you are going to be on the right way. If you have a lack of basic knowledge about how they work, you will run into **major performance** and maintenance challenges later in your project.

#### Hibernate vs MyBatis

| Hibernate | MyBatis |
|:----:|:----:|
| full-blown | lightweight |
| Java-first approach | database-first approach |