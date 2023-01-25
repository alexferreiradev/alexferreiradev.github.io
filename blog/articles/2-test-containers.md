# Stop suffering with tests, use TestContainers

DevOps help us a lot, but only a big infrastructure doesn't resolve the problem with pipelines. The pipeline creation is simple, but the server to run the environments has a high cost. According to [[1],[2]], the to create a server is 2k to 15k. It is a higher cost depending on the company. Moreover, the cost duplicates when need an environment for production and another for staging. The cost to test can increase a lot with two environments. Also, a developer team can lead with the problem of different versions of platforms. One developer can install a database with _version x_ and another developer install version _y_. The first developer can create a test that works in the _version x_, but doesn't work in _version y_.

So, to waste less money and continue increasing the delivery quality. In this article, we will show how can use two tools, docker and TestContainers to decrease the cost to test systems with Java. Use docker to provide the infrastructure to your dependencies and the TestContainers is a library that permits you to create different scenarios for your tests.
In this article, we will show how to use these tools to create your test environment and have the lowest cost.

## Creating infrastructure with Docker

All software needs some infrastructure like a database or a message broker. The basic process for a developer to test the software in the development stage is manually creating the environment in your workstation. Docker is a tool that can help the developer to run each dependency in containers. Find more about docker at [Docker Official].

## Provisioning with TestContainers library
The library `TestContainers` make it easy to create all the environments for your tests at the start of the test platform. It permits you to create any type of container programmatically. You can set the ports, the networks, version of the containers and can set the correct configuration for each test. Find more information at [TestContainers quick start].

## Using Test Containers
First of all, you need to add the library to your dependency management. In Gradle you can do that:
```groovy
testImplementation "org.junit.jupiter:junit-jupiter:5.8.1"
testImplementation "org.testcontainers:testcontainers:1.17.2"
testImplementation "org.testcontainers:junit-jupiter:1.17.2"
```
There are two modes to set up containers in your tests, shared container and local container. The first type is to create and stop the container only when JVM stops and the second is to create a new container for each method test. But in both modes, you create a container for your test starting from the creation of a class to your container or set up a library that creates a container for you. You can see below an example to create a container class for Postgres:
```java
public class CustomPostgresContainer extends PostgreSQLContainer<CustomPostgresContainer> {

   public CustomPostgresContainer() {
      super(DockerImageName.parse("postgres:13-alpine").asCompatibleSubstituteFor("postgres"));
      withUsername("postgres");
      withPassword("<password>");
      withDatabaseName("<db-name>");
      withExposedPorts(5432);

      start();
   }
}
```
After that, you can create a container for your test using this class. An observation here is the need for a new dependency that has the class `PostgreSQLContainer`:
```groovy
testImplementation 'org.testcontainers:postgresql:1.17.6'
```

Now look at an example test using that class:

```java

public class ExampleRestIT extends IntegrationTest {

   private CustomPostgresContainer postgresContainer = new CustomPostgresContainer();

   @Test public void shouldStartingAContainer() {
      postgresContainer.start();

      Assertions.assertTrue(postgresContainer.isRunning());
   }
}
```
This scenario is to have control of the container. There is an annotation that will inform to TestContainers what container needs to be started and stopped for each method test. You can see an example below:
```java
@Testcontainers
public class ExampleRestIT extends IntegrationTest {

   @Container
   private CustomPostgresContainer postgresContainer = new CustomPostgresContainer();

   @Test public void shouldStartingAContainer() {
      // postgresContainer.start(); ------ Dont need any more

      Assertions.assertTrue(postgresContainer.isRunning());
   }
}
```

## Using a web platform
But this is a test without any web platform like [Quarkus] or [Spring]. A setup of TestContainers to Quarkus for example needs some new classes. But, before we need to understand our requirements. Depending on your scenario, you will need to start and stop a container for each class of tests. In another you can reuse one container for all tests. For the first case, create a class that implements a `QuarkusTestResourceLifecycleManager`. For example the class below:

```java
public class SharedContainerResource implements QuarkusTestResourceLifecycleManager {
    protected CustomPostgresContainer POSTGRES_CONTAINER = new CustomPostgresContainer();

    @Override
    public Map<String, String> start() {
        return POSTGRES_CONTAINER.configureProperties();
    }

    @Override
    public void stop() {
        POSTGRES_CONTAINER.stop();
    }
}
```

With this class, you can control the creation and destruction of a container. When Quarkus context starts, it will create the container. After, it will call the method start to get all the properties to replace these values. For example, the port to connect to the Postgres database. When the Quarkus context stops, call the method stop and we can stop the container. If we need a new container for each test class, you can remove the stopped container and create a static field like these:

```java
public class SharedContainerResource implements QuarkusTestResourceLifecycleManager {
    protected static final CustomPostgresContainer POSTGRES_CONTAINER = new CustomPostgresContainer();

    @Override
    public Map<String, String> start() {
        return POSTGRES_CONTAINER.configureProperties();
    }

    @Override
    public void stop() {
        // removed the stop of the container. The testContainer library will destroy automatically when the JVM stops
    }
}
```

With that configuration, we can create a container to the context and at the end of execution, the TestContainer library will stop automatically the container. We recommend this feature for scenarios where you have independent tests and don't need new infrastructure, like a clean database. Using this mode you can waste less time running the tests than using a local container.

More details you can find in [TestContainers quick start] from the official documentation.

## Conclusion
In this article, we show how to create integration tests with containers to simulate any kind of environment. We also show the two modes of setup the containers in your tests and the differences between them. Also, we gave examples to use the library TestContainers with the Quarkus platform.

However, in this article, we did not cover how to use the TestContainers with other web platforms like Spring. In Addition, more libraries integrate with docker in other languages. In the [TestContainers's github account] there are a lot of repositories that the community created to share binds to other languages.

If you like this content and want to talk more about a problem that you are facing in your day aligned with tests, you should book some time in [my calendly], it will be a pleasure to help you solve your problem.


[1]: https://www.servermania.com/kb/articles/how-much-does-a-typical-home-server-cost/
[2]: [https://blog.unimake.com.br/gemini/7-despesas-que-sua-empresa-vai-ter-com-um-servidor-de-backup-interno/]
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[Quarkus]: https://quarkus.io/get-started/
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]
