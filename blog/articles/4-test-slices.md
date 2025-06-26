# Create fast pipelines for tests

The developers create in addition to code for production the code for test for better quality. There are some challenges to create tests, the developer need to know the libraries and frameworks for testing, the company need to create some environment similar to production increasing the cost to delivery new things, and others. 

## Spring Tests

Tests are one part of development that is dificult for a lot of developers. The developers need to know the library to run the tests, the framework web that are using to mock the dependencies for each infra structure and others behaviors to each platform. So the developers tendem to skip the test fase. Spring-Boot came to help with libraries for that as spring-boot-tests library. The library help developers to create tests faster and easyly. 

## Slices tests on Spring Boot

The first Spring-Boot versions only help to create tests that setup all the configurations of all the system with spring but this type of tests waste a lot of time of pipelines and waste a lot of computer resources to run. So, on Spring-Boot 2.4 was added Slices tests in testing library. This type of tests configure specific layers of an application like web layer (MVC layer), don't configure database for example. So, the test use less resources and run faster than conventional spring tests. In addition, since first version, each new Spring-Boot version new Slice Tests are added as the last for Cassandra databases.

## Slice tests Types

There are a lot of slice tests on Spring-boot-test library. The list of types can be accessed by official document. The main types are: 
* MockMVC tests for Web applications
* JPATest for repository
* AMQPTest for listeners / senders

### MOckMVC Tests

The MockMcvTest is a test that will start the Spring just for web application layer, don't need database or others beans to tests this layer. One example is: 
```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = {
		JsonConverterConfiguration.class,
		LoggerBeanFactory.class,
		ResourceExceptionHandler.class,
		ConfigurationMockBean.class,
}) 
public abstract class BaseRest {

	@Autowired
	protected ResourceExceptionHandler resourceHandler;

	@Autowired
	protected ObjectMapper objectMapper;

}
```
This is a base class to create controller tests. The below is a example of controller test:
```java
public class AuthorizationResourceIT extends BaseRest {
	private final CreateAuthorizationRequest request = RequestFixtures.createAuthorizationRequest();

	MockMvc mockMvc;

	@Autowired
	CreateAuthorization useCase;
	private final String baseUrl = "/authorizations";

	@BeforeEach
	void setUp() {
		this.mockMvc = MockMvcBuilders.standaloneSetup(new AuthorizationResource(useCase))
				.setControllerAdvice(resourceHandler)
				.setMessageConverters(new MappingJackson2HttpMessageConverter())
				.build();
	}

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
}
```
This code use mockMvc to create a server with the controller and simulate a http request to that server and the response. On the test we use the library from mockMvc to make assert on the response, the status, the body content and others.

### JPA Tests

One example is:
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
		classes = {
				EntityManagerConfiguration.class,
		}
)
public class BaseRepositoryIT { }
```
This is a JPA base class. An example of tests of one repository can be: 
```java
public class ClientRepositoryIT extends BaseRepositoryIT {

    @Autowired
    EntityManager entityManager;

    UsuarioDaoImp usuarioDaoImp;

    @Test
    public void shouldSaveUsuarioWhenEntityIsValid() {
        usuarioDaoImp = new UsuarioDaoImp();
        usuarioDaoImp.setEntityManager(entityManager);
        Usuario entity = UsuarioMock.create();

        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        usuarioDaoImp.save(entity);

        transaction.commit();
    }
}
```
This test use the entity manager create by configuration and this configuration use TestContainers to create the database container. But the configuration don't create any other bean as Controllers, Http Clients, and others types.

## Test Enabler

All the slices tests create tests using default infrastructure in memory for tests. This can generate tests diferent from real scenarios on production. Due to this, Test Enabler library was created. The library provide a lot of base classes to create any type of component test as Repository test. You just put the library for your Spring version, and you can use BaseClasses to create your tests. This library can be accessed by [test-enabler's git-hub](https://github.com/alexferreiradev/test-enabler). The library is using Spring framework and not the Spring Boot, so you can use for all Spring projects because is spring pure library. In the future we can have for others frameworks like Quarkus and Micronaults, you can contribute to this, just follow the [how to contribute](https://github.com/alexferreiradev/test-enabler) instructions.  

## Conclusion
In this article, we showed how to create integration tests with the Spring framework. We use the TestContainers library to create all platforms required by the application. If you like this content and want to talk more about a problem, call me. You have a special chance to book on [my calendly] this week. It will be a pleasure to help you solve your problem.

[article for Quarkus]: https://dev.to/alexferreiradev/stop-suffering-with-tests-use-testcontainers-4m3k
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's Github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]
