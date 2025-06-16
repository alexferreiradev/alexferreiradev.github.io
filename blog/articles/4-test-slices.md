# Create fast pipelines for tests

Intro

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

The MockMcvTest is a test that will start the Spring just for web application layer, dont need database or others beans to tests this layer. One example is: 
```java
```

### JPA Tests

The MockMcvTest is a test that will start the Spring just for web application layer, dont need database or others beans to tests this layer. One example is:
```java
```

### AMQP Tests

The MockMcvTest is a test that will start the Spring just for web application layer, dont need database or others beans to tests this layer. One example is:
```java
```

## Test Enabler

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
