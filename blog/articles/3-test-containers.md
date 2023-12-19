# Stop suffering with tests, use TestContainers and Spring

A developer lead with a lot of activities, one of them is deploy an application for production. They need to ensure the quality to deploy in production.  They create a pipeline to run a set of automated tests to hold the quality. This article helps java developers to create automated test. So you will learn how to create integration tests using Spring. If you want to use Quarkus, see this [article for quarkus]. First we will show how to create the base classes for our tests. Next, we will show how to run an application using platforms managed by a library. Finally, we will create a integration test example.

## Using a web platform Spring
A web system need some platforms to work. A database is the first thing, another can be a cache and many others platform types are used. A backend developer need to set up all these platforms to test the system locally. The fasted way use the docker to run a container to each platform. The docker compose is used to run all the containers to run the web system in seconds. However , when the developer need to create integration tests, the docker compose is not enough.   

### Creating infrastructure with Docker
All software needs some infrastructure like a database or a message broker. The basic process for a developer to test the software in the development stage is manually creating the environment in your workstation. Docker is a tool that can help the developer to run each dependency in containers. Find more about Docker at [Docker Official].

### Provisioning with TestContainers library
The library `TestContainers` make it easy to create all the environments for your tests at the start of the test platform. It permits you to create any type of container programmatically. You can set the ports, the networks, version of the containers and can set the correct configuration for each test. Find more information at [TestContainers quick start].

### Creating a integration test
The integration test is a test that runs the application with all the dependencies. The test can be a simple test that runs a method or a test that runs a complete flow of the application. So, the developer need to create a test that use the web platform to simulate the real environment. The TestContainers library can help the developer to create the environment for the test. The first step is to create a base class to create the containers.

First of all, you need to add the library to your dependency management. In Gradle you can do that:
```groovy
testImplementation "org.junit.jupiter:junit-jupiter:5.8.1"
testImplementation "org.testcontainers:testcontainers:1.17.2"
testImplementation "org.testcontainers:junit-jupiter:1.17.2"
```

#### Container class
The container class is a class that extends the `GenericContainer` class from the TestContainers library. The class need to have a constructor that receive the image name and the version of the container. Each platform that the application need, databases, message broker and others need a container class. There are some container class in the TestContainer library, but if you need a specific configuration, you need to create a new class.

#### Base test class
After created the containers classes, you can create a base class to your integration tests. In this case you create a class to create the containers and replace the connection configuration for each platform that you need in your application. For example, below you can see a example java class for Spring: 

```java
@SpringBootTest
@ActiveProfiles("test")
public class IntegrationTest {
    
        @Container
        public static final PostgreSQLContainer<?> POSTGRES_CONTAINER = new PostgreSQLContainer<>("postgres:13.2");
    
        @Container
        public static final RedisContainer<?> REDIS_CONTAINER = new RedisContainer<>("redis:6.2.1");
    
        @DynamicPropertySource
        static void postgresProperties(DynamicPropertyRegistry registry) {
            registry.add("spring.datasource.url", POSTGRES_CONTAINER::getJdbcUrl);
            registry.add("spring.datasource.username", POSTGRES_CONTAINER::getUsername);
            registry.add("spring.datasource.password", POSTGRES_CONTAINER::getPassword);
        }
    
        @DynamicPropertySource
        static void redisProperties(DynamicPropertyRegistry registry) {
            registry.add("spring.redis.host", REDIS_CONTAINER::getHost);
            registry.add("spring.redis.port", REDIS_CONTAINER::getFirstMappedPort);
        }
}

```

The example above show how to create a base class to replace the connection configuration for a database and redis cache platforms for example. So, you need to configure in this class each platfrom that your application use. 

A important thing to notice is the static container field. The TestContainers library create the container and when is a statis field, the container is re-used by all the tests. So, with this configuration, the containers are created only one time for all the tests, a singleton container.

After that, you can create your integration test class. The example below show how to create a integration test class for a Spring application:

```java
public class UserRepositoryIT extends IntegrationTest {
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    public void shouldCreateUser() {
        User user = new User("Alex");
        userRepository.save(user);
        assertThat(userRepository.findById(user.getId())).isPresent();
    }
}
```

You run this test and you will see the containers created by TestContainers library, the spring will replace the connection configuration for the database and the cache and will create the repository bean using the correct database connection.

### Environment
The example used in this tutorial is hosted by [Github](https://github.com/alexferreiradev/tecnologias_java/tree/international-career-day-23/Spring/sample-api/src/test/java/dev/alexferreira/sampleapi/infrastructure/kafka). The versions used are following:
* Junit: 5
* TestContainers: 1.17.6
* Java 11
* Spring boot: 2.5.6 

## Conclusion
In this article, we show how to use TestContainers library to create integration tests for Spring framework.

 In Addition, more libraries integrate with Docker in other languages. The community created a lot of repositories in the [TestContainers's github account] to share binds to other languages.

If you like this content and want to talk more about a problem that you are facing in your day aligned with tests, you have a special chance to book on [my calendly] this week. It will be a pleasure to help you solve your problem.

[article for quarkus]: https://dev.to/alexferreiradev/stop-suffering-with-tests-use-testcontainers-4m3k
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]
