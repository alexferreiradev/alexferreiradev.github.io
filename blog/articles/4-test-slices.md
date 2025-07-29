# Create fast integration tests

The developers need to create tests to achieve better quality for systems. There are some challenges to create tests. The developers need to know the libraries and frameworks for testing and the company need to create some environment similar to production. One way to create an environment similar to production is using Testcontainers, but increase the difficult to create tests and can increase the pipeline time. One solution for that is to create tests only for system slices and not for all the system. This article show how to Spring help us to create these types of tests.

## Spring Tests

Tests are one part of development that is dificult for a lot of developers. The developers need to know the library to run the tests, the framework web that are using to mock the dependencies for each infra structure and others behaviors to each platform. So the developers tendem to skip the test fase. Spring-Boot came to help with libraries for that as spring-boot-tests library. The library help developers to create tests faster and easyly. 

## Slices tests on Spring Boot

The first Spring-Boot versions only help to create tests that setup all the configurations of all the system with spring but this type of tests waste a lot of time of pipelines and waste a lot of computer resources to run. So, on Spring-Boot 2.4 was added Slices tests in testing library. This type of tests configure specific layers of an application like web layer (MVC layer), don't configure database for example. So, the test use less resources and run faster than conventional spring tests. In addition, since first version, each new Spring-Boot version new Slice Tests are added as the last for Cassandra databases.

## Slice tests Types

There are a lot of slice tests on Spring-boot-test library. The list of types can be accessed by official document. The main types are: 
* MockMVC tests for Web applications
* JPATest for repository
* AMQPTest for listeners / senders

### MockMVC Tests

The MockMcvTest is a test that will start the Spring just for web application layer, don't need database or others beans to tests this layer. An example of controller test is below:
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
This code use mockMvc to create a server with the controller and simulate a http request to that server and the response. On the test we can use the library from mockMvc to make assert on the response, the status, the body content and others.

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
This is a JPA base class. An example of tests of one repository can be: 
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
This test use the repository to save and entity. The database create by TestContainers the database container. But the base configuration don't create any other bean as Controllers, Http Clients, and others types.

## Conclusion
In this article, we showed how to create integration tests with the Spring Boot framework for layers. If you like this content and want to talk more about a problem, call me. You have a special chance to book on [my calendly] this week. It will be a pleasure to help you solve your problem.

[article for Quarkus]: https://dev.to/alexferreiradev/stop-suffering-with-tests-use-testcontainers-4m3k
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's Github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]