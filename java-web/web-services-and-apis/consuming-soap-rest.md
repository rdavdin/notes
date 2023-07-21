### Consuming SOAP & REST

#### Consuming REST

- Create a POJO class with fields corresponding to key names in JSON document returned from calling API
- **Bind** the JSON data to the POJO class using **Jackson**

- Jackson dependency ```jackson-databind```, which is a library that serializes or maps Java objects to JSON and vice versa.

- Have a look at an example [here](https://github.com/rdavdin/consumerest)

#### Consuming SOAP & Spring Web Services

- Consuming SOAP APIs is a bit more complicated.

- Spring Web Services package provides functionality **to automatically generate the Java files** you need to consume a web service from your application. Using the WSDL (Web Services Description Language)