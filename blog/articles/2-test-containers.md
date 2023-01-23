# Stop suffering with tests, use TestContainers

The devOps help us a lot, but just a big infrastructure don't resolve the problem with pipelines. The pipeline creation is simple, but the server to run the environments have high cost. According with [[1],[2]], the to create a server is 2k until 15k. Its is a higher cost depends on the company. More over, the cost might be duplicated when need an envirorment to production and another for staging. The cost to test can increase a lot with a two envirorments. In addition, a developer team can lead with the problem of different versions of platforms. One developer can install a data base with the _version x_ and another developer install the version _y_. The first developer can create a test that work in the _version x_, but don't work in _version y_.

So, to waste less money and continue increasing the delivery quality, in this article we will show how can use two tools, docker and testContainers to decrease the cost to test systems with Java. The docker can be used to provisioning the infrastructure to yours dependencies and the testContainers is a library that permit you to create differents scenarios to your tests.
In this article we will show how to use these tools to create your test environment and have a lowest cost.

## Creating infrastructure with Docker

All softwares need some infra structure like database or a message broker. The basic proccess to a developer test the software in development stage is manually creating the envirorment in your work station. The Docker is a tool that can help the dev to run each dependency in containers. More about docker can be found at [Docker Official].

## Provisioning with TestContainers library
The library `TestContainers` make it easy creating all the envirorment to your tests in the start of test platform. It permit you to create any type of container in programatic way. You can set the ports, the networks, the version of the containers and can set the correct configuration to each test. More information can be found at [TestContainers quick start].

## Using Test Containers
First of all, you need to add the library in your dependency management. In gradle you can do that:
```groovy
testImplementation "org.junit.jupiter:junit-jupiter:5.8.1"
testImplementation "org.testcontainers:testcontainers:1.17.2"
testImplementation "org.testcontainers:junit-jupiter:1.17.2"
```
There are two mode to setup containers in your tests, shared container and local container. The first type is to create and stop the container only when JVM stop and the second is to create a new container to each method test. But in both modes you create a container to your test starting from the creation of a class to your container or setup a library that create a container for you. You can see below a example to create a container class for postgres: 
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
After that, you can create a container to your test using this class. An observation here is the need of a new dependency that has the class `PostgreSQLContainer`:
```groovy
testImplementation 'org.testcontainers:postgresql:1.17.6'
```

Now look at a example test using that class:

```java

public class ExampleRestIT extends IntegrationTest {

   private CustomPostgresContainer postgresContainer = new CustomPostgresContainer();

   @Test public void shouldStartingAContainer() {
      postgresContainer.start();

      Assertions.assertTrue(postgresContainer.isRunning());
   }
}
```
These scenario is to have the control of the container. There is an anotation that will inform to TestContainers what is the container that need to be started and stoped to each method test. You can see an example below: 
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

## Using with web platform
But this is a test without any web platform like [Quarkus] or [Spring]. A setup of TestContainers to Quarkus for example need some new classes. But, before we need to understand ours requirements. Depend on your scenario, you will need to start and stop a container to each class of test or reuse one container to all tests. This configuration can be done with a class that implements a `QuarkusTestResourceLifecycleManager`. For example the class below:

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

With this class you can have the control of the creation and destruction of a container. When Quarkus context start, it will create the container and call the method start to get all the properties that need to be replaced, for example, the port to connect to the postgres database. When the quarkus context stopping, call the method stop and we can stop the container if we need a new container to each test class. In the case of that you can reuse the container to all test classes, you just remove the stop container and create a static field like these: 

```java
public class SharedContainerResource implements QuarkusTestResourceLifecycleManager {
    protected static final CustomPostgresContainer POSTGRES_CONTAINER = new CustomPostgresContainer();

    @Override
    public Map<String, String> start() {
        return POSTGRES_CONTAINER.configureProperties();
    }

    @Override
    public void stop() {
        // removed the stop of container. The testContainer library will destroy automatically when JVM stop
    }
}
```

With that configuration, we can create a container to the context and in the end of execution, the testContainer library will stop automatically the container. This feature is recommended to scenarios that you have tests that is independents and dont need a new infra structure, like a clean database. Using this mode you can waste less time to run the tests than using a local container.

More details you can find in [TestContainers quick start] from oficial documentation.

## Conclusion
In this article we show how to create integration tests with containers to simulate any kind of environment. We also show the two modes to setup the containers in your tests and the diferences between them. In addition, we gave examples to use the library testContainers with Quarkus platform. 

However, in this article we did not cover how to use the testContainers with other web platform like Spring. In Addition, there are more libraries that integrate with docker in another languages. In the [testContainers's github account](https://github.com/testcontainers) there are a lot of repositories that the community create to share binds to anothers languages.


[1]: https://www.servermania.com/kb/articles/how-much-does-a-typical-home-server-cost/
[2]: [https://blog.unimake.com.br/gemini/7-despesas-que-sua-empresa-vai-ter-com-um-servidor-de-backup-interno/]
[Docker Official]: https://docs.docker.com/
[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]:[https://spring.io/quickstart]
[Quarkus]:[https://quarkus.io/get-started/]

## MM
Em arquivo separado (mm)[./plan.txt]
