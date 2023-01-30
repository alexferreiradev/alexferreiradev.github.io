# Stop suffering with tests, use TestContainers

Developers need to lead with two or more system during the work day. The quality from these systems depends on the integration and unit tests. Its require a pipeline and a big infrastructure. The pipeline run tests in a server similar to production server, called staging. According to [[1],[2]], the cost to mantain a server is 2k to 15k in production. It is a high cost depending on the company. The cost duplicates when needs a environment for production and another for staging. But a pipiline that run tests in staging environment can result in a lot of false positive errors when a dependent system is down or a team is deploying a new version. 

Also, a developer team can lead with anothers problems, the high platforms amout to install, the difference between platforms's versions per developer, the difficult to update the platform version and the difference between platforms's versions in production and developer environment.

A rest api system need at least one database and a message broker system. But a real market system need an cache service, storage system an the list of platforms can increse a lot according the market requirements. So, to run locally the system, a developer need to install all this platforms. The number can increase exponentialy when has more systems. The developer need to install different versions to each platform to each system.

One developer can install a platform with _version x_ and another developer install _version z_. The first developer can create a test that works in the _version x_, but doesn't work in _version z_. So, the second developer can not run the tests and require help to discovery the error because of the difference in the versions.

The problem increase when a bug happens in production and don't happens locally because of difference in the versions. In addition, when a new developer try to set up the developer environment, the code clonned doesn't have the version of the platforms to the tests or run the system locally and install the lasted version available.    

Problems to dev like me:
- manage diferrent platforms: database, cache
- diferent platforms versions in the team
- updating infra version
- different version between production and stg

This article is to help developers to waste less money and to continue increasing the system delivery quality. We will show how can use two tools, Docker and TestContainers to decrease the cost to test systems with Java. Use Docker to provide the infrastructure to your dependencies and the TestContainers is a library that permits you to create different scenarios for your tests. 

Therefore, the reader will learn how to use these tools to create your test environment and have the lowest cost.

## Creating infrastructure with Docker

All software needs some infrastructure like a database or a message broker. The basic process for a developer to test the software in the development stage is manually creating the environment in your workstation. Docker is a tool that can help the developer to run each dependency in containers. Find more about Docker at [Docker Official].

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

However, in this article, we did not cover how to use the TestContainers with other web platforms like Spring. In Addition, more libraries integrate with Docker in other languages. In the [TestContainers's github account] there are a lot of repositories that the community created to share binds to other languages.

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
