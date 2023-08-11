### Java Persistence API (JPA)

#### Persistence Context

- Describes the relationship between all the Entity instances in our program and their representations in the underlying database.

There are 4 states of an entity object:

- *Transient*: not associated with the persistence context. Often has not yet had an ID assigned
- *Managed* or *persistent*: persistent. Managed by the current persistence context. **Changes** to the entity will **be reflected in the backing db**.
- *Detached*: previously managed. Occurs to all managed entities when persistence context ends.
- *Removed*: scheduled to be removed from teh db. Java object still exists and has ID.

#### Entity Manager

- EntityManager has the role of **creating and deleting persistent entities**, and they can also find entities in the database or **execute other queries**.

**Changing Persistence States**

- **Persist**: Takes an Entity not yet managed. **The** Entity becomes managed and will be saved to the database. Note that, **the entity** changed the state from _transient_ to _managed_.
- **Find**: Looks up an id in the database and returns a **managed** Entity.
- **Merge**: Updates an Entity that is in the **detached** state. Returns **an instance** of that Entity that is now managed. If Entity was not found in the database to update, persists Entity as a new row. Note that, _Merge_ is different from _Persist_, _merge_ will create a new instance while _persist_ updates the same instance. Besides, _Merge_ takes time to look up the existing object in the database first.
- **Remove**: Detaches an entity from the persistence context and deletes it from the database. This works on entities that are **currently managed** by the persistence context.

**Example:**

```
@PersistenceContext
EntityManager entityManager;

public void persistExample(Person p) {
   entityManager.persist(p); //write p to the database
   p.setFavoriteComposer("Johann Strauss II"); //will update database
}

public void findExample(Long id) {
   Person p = entityManager.find(Person.class, id); //retrieve an instance by its key
   p.setFavoriteComposer("Sir Malcolm Arnold"); // will update database
}

public void getReferenceExample(Long personId, Long outfitId) {
   Person p = entityManager.find(Person.class, personId);

   /*
   * returns a proxy object that doesn't load the values of any attributes util they're directly referenced
   */
   Outfit outfitReference = entityManager.getReference(Outfit.class, outfitId); 
   p.getOutfits().add(outfitReference);
}

public void mergeExample(Person detachedPerson){
   detachedPerson.setFavoriteComposer("Rimsky Korsakov");
   Person managedPerson = entityManager.merge(detachedPerson);
   managedPerson.setFavoriteComposer("C.P.E. Bach");        //will overwrite Korsakov
   detachedPerson.setFavoriteComposer("Antonio Salieri");   //will have no effect on database
}

public void deleteExample(Long id) {
   Person p = entityManager.find(Person.class, id); //retrieve an instance by its key
   entityManager.remove(p); //will delete row from database
}
 ```


***Remember:*** Hibernate exposes some methods beyond those specified in the JPA spec. These methods can be accessed through an object called **Session**, which is the Hibernate implementation of EntityManager.

```
entityManger.unwrap(Session.class);
 ```

[Hibernate documentation on Persistence Context](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#pc)

#### Fetching 

How do the operations of EntityManger (persist, find, merge, remove) interact with associated entities (in relationships)?

Setting a fetch strategy can prevent your Entities from loading associated values until those values are referenced.

**FetchType.EAGER**: Always retrieve the associated values as part of the Entity retrieval. This means the initial query for the entity retrieves this data.

- Default for: @Many**ToOne**, @One**ToOne**

**FetchType.LAZY**: Wait to retrieve associated values until they are referenced. Lazy-loaded attributes are Hibernate proxy objects whose specific values are retrieved from the database only if they’re accessed. The initial query for the entity will NOT retrieve this data.

- Default for: @One**ToMany**, @Many**ToMany**

**An example**

```
@Entity
public class Person {

   @Id
   @GeneratedValue
   Long id;

   @OneToMany(mappedBy = "person", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
   List<Outfit> outfits;

   private String name;
   private int age;
   private String favoriteComposer;

   / *rest of class* /
}
 ```

[Hibernate documentation on Fetching](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#fetching)

#### Cascading

[Hibernate Documentation on Cascading](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#pc-cascade)

#### Queries

- All we have to do is provide the query string to the EntityManger to ask it to create query

```
private static final String FIND_PERSON_BY_COMPOSER =
       "select p from Person p " +
       "where p.favoriteComposer like :favoriteComposer";
public Person findPersonByFavoriteComposer(String favoriteComposer){
   TypedQuery<Person> query = entityManager.createQuery(FIND_PERSON_BY_COMPOSER, Person.class);
   query.setParameter("favoriteComposer", favoriteComposer);
   return query.getSingleResult();
}
 ```

##### Referencing Associated Entities

- In SQL, you will often join tables together to search for results by related Entities.
- In JPQL, you can reference the **value** of associated Entities by accessing them **directly as Entity attributes** in the query

**Example:** The unidirectional ```@ManyToMany``` from Humanoid to Outfit

```
@Entity
public class Humanoid {
   @Id
   @GeneratedValue
   private Long id;

   @ManyToMany
   private List<Outfit> outfits;

   / *rest of class* /
}

@Entity
public class Outfit {
   @Id
   @GeneratedValue
   private Long id;

   / *rest of class* /
}
 ```

**How to query a specific outfit belongs to which humanoid in JPQL?**

```
private static final String FIND_HUMANOID_BY_OUTFIT =
       "select h from Humanoid h " +
       "where :outfit member of h.outfits";

List<Humanoid> findHumanoidByOutfit(Outfit o){
   TypedQuery<Humanoid> query = entityManager.createQuery(FIND_HUMANOID_BY_OUTFIT, Humanoid.class);
   query.setParameter("outfit", o);
   return query.getResultList();
}
 ```

[Hibernate document on Queries](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#hql)


#### Named Queries

##### Approach 1: Static Query String Constants

As the above example, we can declare constant query strings along with your class attributes as below:

```
private static final String FIND_HUMANOID_BY_OUTFIT = 
            "select h from Humanoid h where :outfit member of h.outfits";
 ```

- There is **no validation** of your queries, and query strings defined in this way can **fail without warning at runtime**

##### Approach 2: ```@NamedQuery``` and ```@NamedQueries``` at the class level

- Define a query in advance
- and give it a **unique name**

- This approach, named queries will be checked at build time for validity. Any named queries that reference **invalid** entities will **throw exceptions**, helping ensure that we don't commit invalid query strings.

***Remember:*** The scope of these unique names are global across the **whole persistence unit**, which means you should **avoid using duplicate** query names **across different entities**

**Example:**
```
@NamedQuery(
name = "Outfit.findByHat", 
query = "select o from Outfit o where o.hat = :hat")

TypedQuery<Outfit> query = entityManager.createNamedQuey("Outfit.findByHat", Outfit.class);
 ```
Or we can use the annotation ```@NamedQueries``` to store all of our named queries as below:

```
@NamedQueries({
 @NamedQuery(
  name = "Outfit.findByHat", 
  query = "select o from Outfit o where o.hat = :hat"),
 @NamedQuery(
  name = "Outfit.findBySock", 
  query = "select o from Outfit o where o.sock = :sock")
})
 ```

#### Criteria Queries

```
CriteriaBuilder builder = entityManager.getCriteriaBuilder();

CriteriaQuery<Person> criteria = builder.createQuery(Person.class);
Root<Person> root = criteria.from(Person.class);
criteria.select(root);
criteria.where(builder.equal(root.get(Person_.name), "John Doe"));

List<Person> persons = entityManager.createQuery(criteria).getResultList();
 ```
[Hibernate document on Criteria](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#criteria)

#### Projections

**Projecting into a Value Type**

```
private static final String LIST_FAVORITE_COMPOSERS = "select distinct p.favoriteComposer from Person p";

List<String> listFavoriteComposers() {
   TypedQuery<String> query = entityManager.createQuery(LIST_FAVORITE_COMPOSERS, String.class);
   return query.getResultList();
}
 ```

**Projecting into a Non-Entity Object**

*PersonComposerDTO.java* 

```
public class PersonComposerDTO {
   private String name;
   private String composer;

   public PersonComposerDTO(String name, String composer) {
       this.name = name;
       this.composer = composer;
   }
   / *getters and setters* /
}
 ```

```
private static final String GET_PERSON_AND_COMPOSER =
       "select new com.udacity.jdnd.course3.controller.PersonComposerDTO(p.name, p.favoriteComposer) " +
       "from Person p " +
       "where p.id = :id";

PersonComposerDTO getPersonComposer(Long id) {
   TypedQuery<PersonComposerDTO> query = entityManager.createQuery(GET_PERSON_AND_COMPOSER, PersonComposerDTO.class);
   query.setParameter("id", id);
   return query.getSingleResult();
}
 ```

See, ```com.udacity.jdnd.course3.controller.PersonComposerDTO```: the fully path required.

***Remember:*** The returned object is not an instance of an entity so it will **not be managed** by the EntityManager and is simply a POJO.

**Hibernate does recommend** projections for **read-only** transactions because it's faster.

#### Repository Pattern

So far, we see that each repository has a interface similar to a collection in Java. It means that these repositories have similar methods such ```save, delete, findById, etc.```.

![](/imgs/java_web/screen-shot-2020-03-19-at-3.13.32-pm.png)
_A repository has a similar interface to a collection_

**So, how to manage these repositories properly?**

3 ways for repository management:

**Unique Interface, Unique Implementation**

This way, each entity has a unique interface and a unique implementation.

- Pros: More customizable, able to limit methods per Entity
- Cons: Lots of very similar interfaces and classes

**Generic Interface, Unique Implementation**

This way, entities have a common interface and each implementation for one entity.

- Pros & Cons: Fewer Interfaces, but implement unused methods.

**Generic Interface, Generic Implementation**

This way, entities have a common interface and a common implementation.

- Most work up front and least redundant code.

**This is the way applied by Spring Data JPA**

#### Spring Data JPA

**Remind:**

**JPA**: Defines the API for using Java objects to map to database tables

**Hibernate**: Implement the JPA API

**Spring Data JPA**: **code** and **code generation tools** that help use JPA without writing quite as much of our own boilerplate.

- Spring Data JPA already provides the implementation for default methods.

```
@Repository
public interface OutfitRepository extends CrudRepository<Outfit, Long> {
}
 ```

- Besides, it also provides code generation tools that generates implementation of JPQL automatically when we add **new method signatures** to our interface. The implementation is generated based on the **words** used on the **method name**.

```
@Repository
public interface OutfitRepository extends CrudRepository<Outfit, Long> {
   //finds a single outfit by attribute
   Outfit findByHat(String hat);
   //you can use Operators like And/Or, Lessthan/greaterthan, null/notnull
   Outfit findByHatAndShoes(String hat, String shoes);
}
 ```

- Generation tools are powerful, but in case we need to perform **more complex queries**, we also provide our own JPQL strings or named queries to our repository methods directly.

**Referencing Associations, Providing JPQL, and using Named Queries**

```
@Repository
public interface HumanoidRepository extends JpaRepository<Humanoid, Long> {
   //you can reference associations and attributes by chaining
   //attribute names. Here we reference Humanoid.outfits.hat
   List<Humanoid> findAllByOutfitsHat(String hat);

   //you can provide specific JPQL Queries
   @Query("select h from Humanoid h where :outfit member of h.outfits ")
   List<Humanoid> findAllByOutfit(@Param("outfit") Outfit outfit);

   //does the same as above
   List<Humanoid> findAllByOutfitsContaining(Outfit outfit);

   //automatically uses query named Humanoid.findAllNamedQuery
   List<Humanoid> findAllNamedQuery(Outfit outfit);
}
 ```

[Spring Data JPA Documentation on Spring](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories)

#### Flushing

**Flushing:** The process of synchronizing the state of the persistence context with the underlying database.

**Transaction:** A set of operations that either succeed or fail as a group

**Level 1 Cache:** The persistence context functions as a Level 1 Cache, because it does not write changes to the database until **Flushing** occurs.

##### Flushing Triggers

1. Transactions ends
2. Query overlaps with queued Entity actions: The situation where a query is executed while there are pending changes to the entities in the persistence context, and the persistence context flushes the changes to the database before executing the query to provide **accurate** query result.
3. Native SQL Query executes without registering affected Entities

##### A good practice

- Start one transaction for each **request that interacts with the database**.
- Using ```@Transactional``` to do that at the **Service layer**.

[Hibernate documentation on Flushing](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#flushing)