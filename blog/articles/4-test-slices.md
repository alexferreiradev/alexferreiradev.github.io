# Create fast integration tests

The developers need to create tests to achieve better quality for systems. There are some challenges to creating tests. The developers need to know the libraries and frameworks for testing. The company needs to create an environment like production. One way to create an environment like production is by using Testcontainers.  This increases the difficulty of creating tests and can increase the pipeline time. One solution for that is to create tests only for system slices and not for the entire system. This article shows how Spring helps us to create these types of tests.

## Spring Tests

Tests are one part of development that is difficult for a lot of developers. The developers need to know the library and frameworks to run the tests. The libraries help to integrate the language and the framework. The framework web mocks the dependencies for each infrastructure. So the developers tend to skip the test phase due to these challenges. Spring-Boot came to help with libraries for that, as the spring-boot-tests library. The library helps developers create simple tests faster.

## Slices tests on Spring Boot

The first Spring-Boot versions only help to create integration tests. These tests set up all the configurations of the system with Spring and waste a lot of time on pipelines. Also, it wastes a lot of computer resources to run. So, Spring-Boot 2.4 added Slices tests in the testing library. This type of test configures specific layers of an application, like the web layer (MVC layer). It does not configure the database and other infrastructures. So, the test uses fewer resources and runs faster than conventional Spring tests. Also, since the first version, the community has added new Slice Tests. The last added was for Cassandra databases.

## Slice tests Types

There are a lot of slice tests on Spring-boot-test library. The list of types is in the official document. The main types are:

* MockMVC tests for Web applications

* JPATest for repository

* AMQPTest for listeners/senders

### MockMVC Tests The Mock

McvTest will start in the Spring only for the web application layer. It won't configure the database or other beans to test this layer. An example of a controller test is below:

```java
@ContextConfiguration(classes = {SpringApplicationLight.class})
@Import({JacksonAutoConfiguration.class, LoggerBeanFactory.class, ResourceExceptionHandler.class})
@WebMvcTest(AuthorizationResource.class)
class AuthorizationResourceIT {

	private final CreateAuthorizationRequest request = RequestFixtures.createAuthorizationRequest();

	@Autowired MockMvc mockMvc;
	@Autowired ObjectMapper objectMapper;

	@MockBean CreateAuthorization useCase;

	private final String baseUrl = "/authorizations";

	@Test
	void shouldReturn200_whenRequestIsValid() throws Exception {
		String authorizationId = "authorizationId";
		ArgumentCaptor<CreateAuthorizationInput> captor = ArgumentCaptor.forClass(CreateAuthorizationInput.class);
		Mockito.when(useCase.execute(captor.capture())).thenReturn(authorizationId);
		
		mockMvc.perform(post(baseUrl).contentType(MediaType.APPLICATION_JSON)
						.content(objectMapper.writeValueAsString(request))).andDo(MockMvcResultHandlers.print())
				.andExpect(status().is2xxSuccessful())
				.andExpect(jsonPath("$.authorizationId").value(authorizationId))
		;

	}

```

This code uses mockMvc to create a server with the controller. It creates an object of your controller configured by the `@WebMvcTest` annotation. After Junit initialization, Spring creates an object to inject into the field `mockMvc`. The object will create an HTTP server that receives a request and calls the controller. So, in the test method, MockMvc handles the request to the controller object. The controller will process the request and return a response object. After, MockMvc will return to us the response to make assertions. We can use MockMvc to assert the status, the body content, and others with expected values.

### JPA Tests

One example is:

```java
@ActiveProfiles("db")
@DataMongoTest(excludeAutoConfiguration = {
		EmbeddedMongoAutoConfiguration.class,
})
@AutoConfigureDataJpa
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class BaseRepositoryIT {

	private static final DynamicPropertyConfigurableContainer noSqlContainer = new CustomNoSqlContainer();
	private static final DynamicPropertyConfigurableContainer sqlContainer = new CustomDataBaseContainer();

	@DynamicPropertySource
	static void datasourceProperties(DynamicPropertyRegistry dynamicPropertyRegistry) {
		noSqlContainer.configure(dynamicPropertyRegistry);
		sqlContainer.configure(dynamicPropertyRegistry);
	}

}

```

This is a JPA-based class. We need this base class to create the containers to create the databases. This is a way to create a lot of repository tests with the same setup. The class is using two libraries, one for NoSQL and the other for SQL. The `DataMongoTest` annotation sets up the NoSQL beans. You can use embedded Mongo. We are using the Testcontainers library to create containers for us. So, we exclude the embedded configuration. We did the same for SQL. The annotation `AutoConfigureTestDatabase` removes the embedded database. After that, you can set up the properties from each container to Spring properties. Use the `DynamicPropertySource` annotation to set properties for each database. The Spring needs the port from the container to connect to the databases. We used the dynamic properties to set the ports.

Now, we can create all the repository tests extending from this base class. An example of tests for the repository can be: 

```java

class AuthorizationRepositoryIT extends BaseRepositoryIT {

	@Autowired AuthorizationRepository repository;

	private final Authorization authorization = DomainFixtures.createAuthorization();

	@Test

	void shouldSaveAuthorization() {
		Authorization savedAuth = repository.save(authorization);
		assertNotNull(repository.findById(savedAuth.id).orElseThrow());
	}

}

```

This test uses the repository to save an entity. The database created by TestContainers is the database container. The base configuration doesn't create any other bean. The controllers, HTTP clients, and other types are not created.

## Conclusion

In this article, we showed how to create integration tests with fewer resources.  We showed the power of tests by layers with the Spring Boot framework. You can talk more about tests, call me. You have a special chance to book on [my calendly] this week. It will be a pleasure to help you solve your problem.

[article for Quarkus]: https://dev.to/alexferreiradev/stop-suffering-with-tests-use-testcontainers-4m3k
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's Github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]