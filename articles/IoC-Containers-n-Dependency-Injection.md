### Inversion of Control Containers and Dependency Injection pattern
###### [Source: martinfowler.com](https://www.martinfowler.com/articles/injection.html)

**A common issue:** 
**How to wire together different elements?** How do you fit together this web controller architecture with that database interface backing when they were built by different teams with little knowledge of each other?
**A common solution:**
A number of frameworks provide a general capability to assemble components from **different layers**. These frameworks are often referred to as **lightweight containers**. Underlying these containers are a number of **interesting design principles**. Some of these principles are explored here.

#### Components and Services

- Component: a glob of software that's intended to be used, **without change**, by an application that is out of the control of the writers of the component. The using application doesn't change the source code of the component, although they may **alter the component's behavior** by extending it in ways allowed by the component writers.
- A service is similar to a component in that it's used by **foreign applications** through some remote interface, either sync or async like web service, messaging system, RPC or socket.

