
#### Install
[Refer to this](https://code.visualstudio.com/docs/java/java-spring-boot)
To develop a Spring Boot application in Visual Studio Code, you need to install the following:

- [Java Development Kit (JDK)](https://www.microsoft.com/openjdk)
- [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
- [Spring Boot Extension Pack](https://marketplace.visualstudio.com/items?itemName=vmware.vscode-boot-dev-pack)

#### Git clone an example to test
[Refer to this](https://code.visualstudio.com/docs/java/java-webapp)

- Git clone the example from [this link](https://github.com/spring-guides/gs-spring-boot.git)
- Open the ```complete``` folder under cloned repo in VS Code

```
cd complete
code .
```
- Open a java file under ```src/main``` 
- Then ```F5``` to run the project
- Test in the browser with url: [localhost:8080](localhost:8080)

#### Create a Spring Boot app
1. Open Command Palette: _Ctrl+Shift+P_
2. Type: _Spring Initializr_
3. Select dependencies for your project
4. ...