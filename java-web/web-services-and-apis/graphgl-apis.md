### GraphQL APIs

#### What is GraphQL?

- GraphQL is a **query language for APIs** or a syntax that describes **how to ask for data** and is generally used **to load data from a server to a client**.
- GraphQL allows for the aggregation of data from multiple sources on the back-end and offers maximum efficiency and flexibility because it allows the calling client to **specify exactly what data it needs**

#### Dependencies are necessary

- **Spring for GraphQL** (spring-boot-starter-graphql): This starter is very helpful because:

  - it will add and automatically configure a GraphQL Servlet that you can access at /graphql . 
  - it will also use a GraphQL schema library to parse all schema files found on the classpath. 
  - it will also set up an endpoint that can access POST requests.

- ```graphql-java-tools```: a helper library to parse the GraphQL schema. **Note:** is no longer actively maintained. The GraphQL Java community has moved towards using the ```graphql-java``` library directly. **This dependency is not necessary**.

#### Schemas

- The GraphQL schema defines **the data points** offered via an API. 
- The schema contains **the data types** and **relationships** between them and the set of operations available.
- **Operations** like **queries for retrieving data** and/or **mutations for creating, updating, and deleting data**.
- See, **queries** are **read-only** operations; **mutations** are operations used to **modify data** on the server.

- Each schema file will contain all of the query and mutation operations for the given type

**What is a data point?** 

- Data points refer to the **individual pieces of data that can be queried and retrieved from your GraphQL API**. These data points are defined by the types in your GraphQL schema.
- For example, there is a schema that defines a ```User``` type with fields: ```id```, ```name```, and ```email```. Each of these fields represents a data point that can be queried and returned as part of a GraphQL query response.

The following schema as an example:

```
type Location {
 id: ID!
 name: String!
 address: String!
}

type Query {
 findAllLocations: [Location]!
}

type Mutation {
 newLocation(name: String!, address: String) : Location!
 deleteLocation(id:ID!) : Boolean
 updateLocationName(newName: String!, id:ID!) : Location!
}
```

An example: [Building a GraphQL service](https://spring.io/guides/gs/graphql-server/)