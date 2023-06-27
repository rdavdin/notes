### Testing

#### Test-Driven Development (TDD)

The **red then green** philosophy!!! **Write tests before the code to be tested!!!**

![](/imgs/java_web/l5-07-big-picture.png)
_The Test-Driven Development_

- First, there is an idea for **a new feature**
- That feature idea is turned into a series of **user stories**, which are then turned into **tests**, which fail because the feature doesn't exist yet
- The developers then work on the **feature's code** requirements until all of the **tests pass**
- That's it! Back with a new feature

**User Stories**

A **user story** describes the functionality a feature should have from **the perspective of a user** interacting with the application.

**Format:**

**```As a user, I can```** _```take some action```_ **```in order to```** _```achieve some goal.```_

**Examples:**
1. **```As a user, I can```** _```click on the shopping card icon```_ **```in order to```** _```view my cart contents.```_
2. **```As a user, I can```** _```click on the ADD TO CARD button```_ **```in order to```** _```add it to my cart contents.```_
3. **```As a user, I can```** _```click on the REMOVE FROM CARD button of an inventory item in my cart```_ **```in order to```** _```remove it from my cart contents.```_

Often a feature will be broken up into many user stories, each of which should correspond to **at least on test** to be implemented for that feature.

If all the tests pass, it means that all of the user stories are successfully implemented, and the feature is complete.

#### Testing with JUnit

[JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)

[Annotations list](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations)

![](/imgs/java_web/junit-lifecycle.png)
_The JUnit Lifecycle_

- _@BeforeALL_ and _@AfterAll_ are used to manage **static resources** to be shared by all tests, like lookup tables, drivers, and web connections.
- _@BeforeEach_ and _@AfterEach_ are used to manage anything with state to be updated by a test.

- Assertions are the static methods defined in the Assertion class. 
  - [Assertions methods](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html)
  - [Example on how to use assertions in a unit test](https://junit.org/junit5/docs/current/user-guide/#writing-tests-assertions)

#### Code Coverage Reprots

- When we run code in an IDE like IntelliJ, we can choose to have the IDE track which lines of our code were visited, and how many times. This can be useful when trying to track down why a branch of a condition isn't being reached, as well as when determining how much the entire code base is covered by the currently-implemented tests.

#### Selenium/WebDriver

**Selenium** is a cross-platform tool for browser automation and scripting, and we're going to use it to write tests that **simulate a user's actions in a browser**.

![](/imgs/java_web/l5-53-selenium-webdriver.png)
_The Architecture of Selenium_


##### How Does Selenium Automate the Browser?

- Selenium uses the browser like a user would - based on the page that's displayed
- Selenium can **query the HTML of a web page** to read data and trigger events like button clicks in the browser.


[Official Selenium](https://www.selenium.dev/)


#### JUnit + Selenium

- JUnit and Selenium are the natural fit for one another. Both are **plain Java libraries**, and don't require any special syntax or approach to integrate with one another.
- But, **JUnit + Selenium -> Automated User Testing!**
- _@BeforeAll_: is perfect for writing a method to initialize the web driver that Selenium requires
- _@BeforeEach_: is perfect for writing a method to navigate to a common starting URL for all tests in the class
- _@AfterAll_: is perfect for defining a method to quit the web driver once our tests finished.

**Selenium's Page Object**

- **Goal**: test **high level** user actions
- **Problem**: Selenium driver is too **low level**. In addition, a test should be easily understandable and clear, but it is written too much code with Selenium driver like using css selector to find a proper element.
- **Solution**: Selenium Page Object is a built-in tool to solve the problem. It provides methods to call for our purposes instead of writing a lot of code with Selenium driver.
  
  - We can define a class with **Web Element fields** that can be injected by the web driver.
  - We can configure how the web driver searches for the elements with **annotations**.
  - Then we can add utility methods that perform common actions on those elements.

**An Example**

![](/imgs/java_web/selenium-page-object-1.png)
_The Functionality of Counter Page_

**CounterPage has three main sections**
**1. Defining Element Selectors**

```
    @FindBy(id = "count-display")
    private WebElement countDisplay;

    @FindBy(id = "increment-button")
    private WebElement incrementButton;

    @FindBy(id = "reset-value-field")
    private WebElement resetValueField;

    @FindBy(id = "reset-button")
    private WebElement resetButton;
```

**2. Telling Selenium to use the given driver to initialize the _@FindBy_-annotated fields in the class**

```
    public CounterPage(WebDriver driver) {
        PageFactory.initElements(driver, this);
    }
```

**3. Creating Helper Methods**

```
    public int getDisplayedCount() {
        return Integer.parseInt(countDisplay.getText());
    }

    public void incrementCount() {
        incrementButton.click();
    }

    public void resetCount(int value) {
        resetValueField.clear();
        resetValueField.sendKeys(String.valueOf(value));
        resetButton.click();
    }
```

**Writing tests**

```
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserTestingApplicationTests {
    @LocalServerPort
    private Integer port;

    private static WebDriver driver;
    private CounterPage counter;

    @BeforeAll
    public static void beforeAll() {
        WebDriverManager.chromedriver().setup();
        driver = new ChromeDriver();
    }

    @AfterAll
    public static void afterAll() {
        driver.quit();
    }

    @BeforeEach
    public void beforeEach() {
        driver.get("http://localhost:" + port + "/counter");
        counter = new CounterPage(driver);
    }

    @Test
    public void testReset() {
        counter.resetCount(10);
        assertEquals(10, counter.getDisplayedCount());
        counter.resetCount(0);
        assertEquals(0, counter.getDisplayedCount());
    }
}
```

**```@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)```** : This tells JUnit to run the application before any tests are executed, with a random port number. It means the server has to be running before the tests start.

**```@LocalServerPort```**: Spring will inject the current port into a filed annotated with this annotation.

#### Page Load Issues

In the cases, because of the delay of loading heavier resources like images, we need to make sure **the driver waits** before searching for elements that might not yet exist on the page. If not, the driver will throw an exception ```StaleElementReferenceException``` or ```NullpointerException```. 

[Read more Exception ```StaleElementReferenceException```](https://developer.mozilla.org/en-US/docs/Web/WebDriver/Errors/StaleElementReference#:~:text=When%20an%20element%20is%20no,it%20was%20retrieved%20from%20navigates.)

**Question:** So how do we make sure an element is on the page before we ask Selenium to look for it?
**Answer:** Using ```WebDriverWait``` class.

```
WebDriverWait wait = new WebDriverWait(driver, 10);  //timeout: 10s
WebElement marker = wait.until(webDriver -> webDriver.findElement(By.id("page-load-marker")));
```

If the element **_page-load-maker_** loaded, it makes sure that the **full** page loaded. So, you should consider choosing which element is **page-load-maker**.

[Official Selenium Guide to **Waits**](https://www.selenium.dev/documentation/webdriver/waits/)

#### Quiz

```
<form class=”simpleForm” action="#" th:action="@{'/simplehome'}" method="POST">
   <input class=”submitButton” name=”submit” type="submit" value="Visit me">
</form>
```

Which of the following statements can still reference this input element?

- [x] ```driver.findElement(By.name("submit")).click();```
- [x] ```driver.findElement(By.className("submitButton")).click();```
- [x] ```driver.findElement(By.tagName("input")).click();```
- [x] ```driver.findElement(By.cssSelector("input.submitButton")).click();```
- [x] ```driver.findElement(By.xpath("//input[@value='Visit me']")).click();```

**Further Reading**

- [Official Java Tutorial on Java 8's Lambda Expression Syntax](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)