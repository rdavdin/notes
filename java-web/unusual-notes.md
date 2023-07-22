#### 1. ```@Controller``` vs ```@RestController```

- Spring 4.0 introduced the **@RestController** annotation in order to simplify the creation of RESTful web services. It's a convenient annotation that combines **@Controller and @ResponseBody**, which eliminates the need to annotate every request handling method of the controller class with the **@ResponseBody** annotation. - [Source](https://www.baeldung.com/spring-controller-vs-restcontroller)

```
@Controller
public class TestController {
    @RequestMapping("/test")
    public @ResponseBody String test(){
        return "Data Structures";
    }
}
```

**Equivalent to**

```
@RestController
public class TestController {
    @RequestMapping("/test")
    public String test(){
        return "Data Structures";
    }
}
```