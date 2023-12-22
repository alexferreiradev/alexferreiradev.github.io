# Stop suffering with tests, use TestContainers and Spring

A developer deals with a lot of activities, one of which is deploying a production application. They need to ensure the quality to deploy in production.  They create a pipeline to run a set of automated tests to hold the quality. This article helps Java developers to create automated tests. So you will learn how to create integration tests using Spring. If you want to use Quarkus, see this [article for Quarkus]. First, we will show how to create the base classes for our tests. Next, we will show how to run an application using platforms managed by a library. Finally, we will create an integration test example.

## Using a web platform Spring

A web system needs some platforms to work. A database is the first and the second can be a cache. A back-end developer needs to set up all these platforms to test the local system. The fastest way use the docker to run a container to each platform. Using docker compose you can run all the containers by one command. So, using docker and docker compose you can run the web system in seconds. But, when the developer needs to create integration tests, the docker-compose is not enough. In this article, we will show how to create containers for tests using a library.

### Creating infrastructure with Docker

Docker is a tool that can help the developer to run each dependency in containers. Find more about Docker at [Docker Official].

### Provisioning with TestContainers library

The library `TestContainers` creates all the environments for your tests. The library starts all containers for each platform before the test starts to run. It permits you to create any type of container using codes. You can set a lot of container configurations. You can set ports, networks, and the container's version. Find more information at [TestContainers quick start]. The first step is to create a base class to create the containers. First of all, you need to add the library to your dependency management. In Gradle, you can do that:
```groovy
testImplementation "org.junit.jupiter:junit-jupiter:5.8.1"
testImplementation "org.testcontainers:testcontainers:1.17.2"
testImplementation "org.testcontainers:junit-jupiter:1.17.2"
```

#### Container class

The container class is a class that extends the `GenericContainer` class. This class is inside the TestContainers library and has a lot of methods to set up a container. The class needs to have a constructor that receives the image name and the version of the container. Each platform required by the application needs a container class. For example, databases, message brokers, and caches, each need a container class. There are already some container classes inside the library as known databases. But, if you need a specific configuration, you need to create a new class.

### Creating a integration test

The integration test is a test that runs the application with all the dependencies. The test can be a simple test that runs a method or a test that runs a complete flow of the application. So, the developer needs to create a test that uses the web platform to simulate the real environment. After creating the container classes, you can create a base integration class. You will use this class to set up all your test changes in one place.

#### Base test class
After creating the container classes, you can create a base class for your integration tests. In this case, you create a class to create the containers and replace the connection configuration for each platform that you need in your application. For example, below you can see an example java class for Spring:

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

The example above shows how to create a base class to replace the connection configuration for a database and Redis cache platforms for example. So, you need to configure in this class each platform that your application uses.

An important thing to notice is the static container field. The TestContainers library creates the container and when there is a static field, the container is re-used by all the tests. So, with this configuration, the containers are created only one time for all the tests, a singleton container.

After that, you can create your integration test class. The example below shows how to create an integration test class for a Spring application:

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

You run this test and you will see the containers created by the TestContainers library, the spring will replace the connection configuration for the database and the cache and will create the repository bean using the correct database connection.

### Environment
The example used in this tutorial is hosted by [Github](https://github.com/alexferreiradev/tecnologias_java/tree/international-career-day-23/Spring/sample-api/src/test/java/dev/alexferreira/sampleapi/infrastructure/kafka). The versions used are following:
* Junit: 5
* TestContainers: 1.17.6
* Java 11
* Spring boot: 2.5.6 

## Conclusion
In this article, we showed how to create integration tests with the Spring framework. We use the TestContainers library to create all platforms required by the application. The community created a lot of repositories to share binds to other languages. See more in the [TestContainers's Github account]. If you like this content and want to talk more about a problem, call me. You have a special chance to book on [my calendly] this week. It will be a pleasure to help you solve your problem.


[article for Quarkus]: https://dev.to/alexferreiradev/stop-suffering-with-tests-use-testcontainers-4m3k
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's Github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]
