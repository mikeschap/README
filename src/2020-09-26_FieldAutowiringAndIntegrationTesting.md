# Spring Field Autowiring and Integration Testing

TL;DR: use constructor or setter autowiring instead.

If you have, let's say, a repository class that you want to write integration tests for, but you inject all its dependencies using field autowiring, you're set up for pain.

```
@Repository
public class ExampleRepository {

  @Autowired
  private ExampleDependency dependency;
  
  ...
```

Whether your repository class integrates with your database is not logically intertwined with whether its dependencies can be injected, but if you use field autowiring, you have to add Spring into the mix just to get your tests to run.

```
@ExtendWith(SpringExtension.class)
public class ExampleRepositoryIT {
  
  // Pain

  ...
```

Why add the overhead? Just use constructor or setter autowiring instead.


```
@Repository
public class ExampleRepository {

  private final ConstructorDependency constructorDependency;
  private SetterDependency setterDependency;
  
  @Autowired
  public ExampleRepository(ConstructorDependency constructorDependency) {
    this.constructorDependency = constructorDependency;
  }
  
  @Autowired
  public void setSetterDependency(SetterDependency setterDependency) {
    this.setterDependnecy = setterDependency;
  }
  
  ...
```

Then your integration tests can look like this:

```
@ExtendWith(MockitoExtension.class)
public class ExampleRepositoryIT {

  private final ConstructorDependency constructorDependency;
  private final SetterDependency setterDependency;
  private final ExampleRepository exampleRepository;
  
  public ExampleRepositoryIT() {
    constructorDependency = mock(ConstructorDependency.class);
    setterDependency = mock(SetterDependency.class);
    exampleRepository = new ExampleRepository(constructorDependency);
    exampleRepository.setSetterDependency(setterDependency);
  }

  // Less pain
  
  ...
```

There are [other reasons](https://www.vojtechruzicka.com/field-dependency-injection-considered-harmful/
) to avoid field autowiring, too - check 'em out.

👋
