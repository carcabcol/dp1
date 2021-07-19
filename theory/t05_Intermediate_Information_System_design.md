# T5 - Intermediate Information Systems design
## How to perform Input Data Validation

### Syntactic vs Semantic (Bussiness Rule)

**Sytactic validations** are simple constraints validation (`@NotNull`, `@Past`, etc).

**Semantic validations or Business Rules** are constraints with a more complex condition. “_Pets of a same owner cannot have the same name_”

### Validator: Spring built-in vs Custom

**Validation Tool 1 (Spring built-int Validator)**: Spring provides a Validator contract usable in every layer. And a DataBinder to dynamically bind user input data with domain model entities. Using the `@Valid` annotation, the simple constraints for each of their attributes.

```java
@PostMapping(value = "/owners/new")
public String processCreationForm(@Valid Owner owner, BindingResult result) {
    if (result.hasErrors()) {
        return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
    }
    else {
        //creating owner, user and authorities
        this.ownerService.saveOwner(owner);
        return "redirect:/owners/" + owner.getId();
    }
}
```

**Validation Tool 2 (Custom Validator Class)**: We can hook additional custom validators.

**Validation Tool 3 (Exceptions)**: Throwing an exception if a constraint is not followed, then, catching the exception and sending the binding error to views.

**Validation Tool 4 (Custom Validation Annotation)**: Creating a custom and reusable annotation that we can add to each of the relevant attributes.

```java
@Documented
@Constraint(validatedBy = ContactNumberValidator.class)
@Target( { ElementType.METHOD, ElementType.FIELD })
@Retention(RetentionPolicy.RUNTIME)
public @interface ContactNumberConstraint {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

```java
public class ContactNumberValidator implements ConstraintValidator<ContactNumberConstraint, String> {
    @Override
    public void initialize(ContactNumberConstraint contactNumber) {
    }
    @Override
    public boolean isValid(String contactField, ConstraintValidatorContext cxt) {
    return contactField != null && contactField.matches("[0-9]+") && (contactField.length() > 8)
    && (contactField.length() < 14);
    }
}
```

```java
@ContactNumberConstraint
Private String phone;
```

### Exceptions

### Accessing Validation Errors in Views

## Where to perform the Input Data Validation

### Anti-Patterns

An Anti-Pattern is just like a pattern, except instead of a solution it gives something that looks superficially
like a solution, but isn't one.

Intuitive Idea: "_When you find this problem, DO NOT apply this solution__”.

1. Anti-Pattern 1: Input Data **Validation Only in Business Logic** and Resource.
2. Anti-Pattern 2: Input Data **Validation Only in Presentation Layer**.
3. Anti-Pattern 3: Input Data **Validation in every Layer “with a Shotgun”**.

### General rule to Validate in Spring

**Input Data Validation in every layer but consciously**.

* Syntactic Validation in Presentation layer.
* Syntactic Validation as additional safety net in Resource Layer; but also Semantic validation or Business Rules in Business Logic Layer.

## Type conversion and Formatting

The project uses a `GenericIdToEntityConverter`.

Configuration is in `WebConfig`.

