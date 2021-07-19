# T6 – Introduction to Information System testing
## Unit Testing concepts

**Bug**: An error in the way the code is written. 

**Failure**: An unexpected behavior of the code.

**Debugging**: The process of diagnosing a failure and identifying the bug that is causing it.

The **Subject Under Test (SUT)** is the unit.

* If we are using a functional language: The functions
* If we are using object-oriented programming: The *public interface of each
  production class* (In DP1; **Service classes,  Validators, Custom Queries** interface in Repositories.

## Unit Testing

**Positive** unit test: Check the happy path, the normal behaviour.

**Negative** unit test: Test “abnormal behaviour”.

### FIRST unit test features

* Fast: Easy and quick to run.
* Independent: No test should rely on preconditions created by other tests.
* Repeatable: Test behavior should not depend on external factors.
* Self-checking: each test should be able to determine on its own whether it passed or failed
* Timely: tests should be created or updated at the same time as the code being tested.

### Test structure

1. Set up the test data. **Arrange / Fixture**
2. Run your subject under test. **Act**
3. Assert that the expected results are returned. **Assert**



Testing negative cases

```java
class AssertionsDemo {
	private final Calculator calculator = new Calculator();

    @Test
    void exceptionTesting() {
    Exception exception = assertThrows(ArithmeticException.class, ()
    	-> calculator.divide(1, 0));
    assertEquals("/ by zero", exception.getMessage());
    }
}
```

Testing validators

```java
class ValidatorTests {
    private Validator createValidator() {
        LocalValidatorFactoryBean localValidatorFactoryBean =
            new LocalValidatorFactoryBean();
        localValidatorFactoryBean.afterPropertiesSet();
        return localValidatorFactoryBean;
	}
  
@Test
void shouldNotValidateWhenFirstNameEmpty() {
    //Fixture
    LocaleContextHolder.setLocale(Locale.ENGLISH);
    Person person = new Person();
    person.setFirstName("");
    person.setLastName("smith");
    
    //Act
 	Validator validator = createValidator();
    Set<ConstraintViolation<Person>> constraintViolations =
    validator.validate(person);
    
    //Assert
    assertThat(constraintViolations.size()).isEqualTo(1);
    ConstraintViolation<Person> violation =
    	constraintViolations.iterator().next();
    	assertThat(violation.getPropertyPath().toString())
    	.isEqualTo("firstName");
    assertThat(violation.getMessage()).isEqualTo("must not be empty");
    }
}
...
}
```

Target > site > jacoco. Allows to see the coverage of our tests.

## Testing Framework

## How many Unit Tests?

## Some best testing practices

### Parameterizing Unit Tests

### Fluent Assertions

AssertJ is automatically imported with the spring-boot-starter-test.

```java
assertThat(getString(notification,EXTRA_BIG_TEXT)).isEqualTo("Message has been sent");
```



### Keep Unit Tests Focused

If the scenario sounds a little hard to track, it is: They are probably testing different scenarios, not one.

### Keep Cause and Effect Clear

Write tests where the **effects immediately follow the causes**.

### DRY principle, DAMP approach

The test below follows the **DRY principle (“Don’t Repeat Yourself”),** a best practice that encourages code reuse rather than duplication.

**Use the DAMP principle (“Descriptive and Meaningful Phrases”), which emphasizes readability over uniqueness**. It can introduce code redundancy (e.g., by repeating similar code), but it makes tests more obviously correct.

`@DataJpaTest`-> Carga la base de datos