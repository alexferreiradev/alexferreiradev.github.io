# Stop suffering with tests, use TestContainers and Spring

One of the activities that a developer lead today is deploy the application for production. They need to create a pipeline to run a lot of jobs to garantee the quality and deploy for staging and next for production. The set of jobs for quality is for run the automated tests 

This article is to help backend java developers to create automated test using Spring and the Testcontainers library. If you want to use Quarkus, see this [article for quarkus]. 

First we will show how to create the base classes for our tests, next we will create the containers 

## Using a web platform Spring

A web system need some platforms to work. A database is the first thing, another can be a cache and many others platform types are used. A backend developer need to set up all these platforms to test the system locally. The fasted way use the docker to run a container to each platform. The docker compose is used to run all the containers to run the web system in seconds. However, when the developer need to create integration tests, the docker compose is not enough.   

## Conclusion
In this article, we show how to .

 In Addition, more libraries integrate with Docker in other languages. The community created a lot of repositories in the [TestContainers's github account] to share binds to other languages.

If you like this content and want to talk more about a problem that you are facing in your day aligned with tests, you have a special chance to book on [my calendly] this week. It will be a pleasure to help you solve your problem.

[TestContainers quick start]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[Spring]: https://spring.io/quickstart
[my calendly]: https://calendly.com/alexferreiradev/action-to-presentation
[TestContainers's github account]: https://github.com/testcontainers

## MM
Em arquivo separado (mm)[./plan.txt]
