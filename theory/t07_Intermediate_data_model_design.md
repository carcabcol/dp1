# T7 Intermediate data model design
1. Create Conceptual Models
2. Create Domain Models from Conceptual Models
3. Impose simple constraints and Use Resource/Persistence Layer

## Complete Mapping from Conceptual to Domain Models

1. Assume **every concept in the conceptual model is a Java class**
2. Map generalizations as inheritance relationships
3. Map **associations as Java attributes** (decide their navegability) and **association classes as regular classes**
4. Adapt the domain model to the l**imitations/features of the technology**
5. Add classes and methods related to the technology.

## Mapping from Domain Model to Resource/Persistence Layer

### Generalizations

`@MappedSuperclass`

### Associations

One to one: `@OneToOne(optional=false)` (mandatory) or `@OneToOne(optional=true)`

Many to one: `@ManyToOne(optional=false)`

Many to many: `@ManyToMany`

Compositions: `@OneToMany(cascade =CascadeType.ALL)`

Bidirecctional relationships: `@OneToMany(CascadeType.ALL,
mappedBy=“owner”)`

### Not “so simple” Constraints

```java
@Size(min = 3, max = 50)
@Column(unique = true)
String authority;
```

Multiple unique attributes

```java
@Entity
@Table(uniqueConstraints =
	@UniqueConstraint(
	columnNames={"year","serial"}) )
public class Invoice
	extends BaseEntity {
	...
}
```

Time related

```java
@Past
@Temporal(TemporalType.DATE)
private Date birthDate;
```

Not persisted in the database. Useful for derived attributes.

```java
@Transient@Transient
public String getFullName() {
	return firstName+” “+lastName;
}
```

## Repositories

Soruce: https://docs.spring.io/spring-data/jpa/docs/1.5.0.RELEASE/reference/html/jpa.repositories.html

### Query creation from method name

When adding additional queries to the repositories, the usual way of doing it is with the `@Query` annotations and JPQL language.

**Naming the method with a certain pattern, will result in the query generated automatically** .

```java
// Queries by attributes
List<Person> findByAddressZipCode(ZipCode zipCode);

// Paging and Sorting
Page<User> findByLastname(String lastname, Pageable pageable);
Slice<User> findByLastname(String lastname, Pageable pageable);
List<User> findByLastname(String lastname, Sort sort);
List<User> findByLastname(String lastname, Pageable pageable);

// Limiting Query Results
User findFirstByOrderByLastnameAsc();
User findTopByOrderByAgeDesc();
Page<User> queryFirst10ByLastname(String lastname, Pageable pageable);
Slice<User> findTop3ByLastname(String lastname, Pageable pageable);
List<User> findFirst10ByLastname(String lastname, Sort sort);
List<User> findTop10ByLastname(String lastname, Pageable pageable);
```

Query methods that return multiple results can use standard Java Iterable, List, and Set. Beyond that, Spring supports returning Spring Data’s Streamable, a custom extension of Iterable.

```java
interface PersonRepository extends Repository<Person, Long> {
    Streamable<Person> findByFirstnameContaining(String firstname);
    Streamable<Person> findByLastnameContaining(String lastname);
}
Streamable<Person> result = repository.findByFirstnameContaining("av")  .and(repository.findByLastnameContaining("ea"));
```

Null Handling of Repository Methods

```java
@Nullable
User findByEmailAddress(@Nullable EmailAddress emailAddress)
```

### Custom Queries (@Query)

JPQL syntax: https://eclipse-ee4j.github.io/jakartaee-tutorial/persistence-querylanguage005.html

Usar queries nativas SQL

```java
@Query(value = "", nativeQuery= true)
```

### Transactions

Is possible to declare the entire class as transactional.

