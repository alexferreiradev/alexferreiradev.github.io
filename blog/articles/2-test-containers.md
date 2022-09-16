# Stop suffering with tests, use TestContainers

The devOps help us a lot, but just a big infrastructure don't resolve the problem with pipelines. The pipeline creation is simple, but the server to run the environments have high cost. According with [cost] the cost to create a server is 2k until 15k. Its is a higher cost depends on the company. More over, the cost might be duplicated when need an envirorment to production and another to staging. The cost to test can increase a lot with a two envirorments. In addition, a developer team can lead with the problem of different versions of platforms. One developer can install a data base with the version x and another developer install the version y. The first developer can create a test that work in the version x, but don't work in version y. 

So, to waste less money and continue increasing the delivery quality, in this article we will show how can use two tools, docker and testContainers to decrease the cost to test systems with Java. The docker can be used to provisioning the infrastructure to yours dependencies and the testContainers is a library that permit you to create differents scenarios to your tests.
In this article we will show how to use these tools to create your test environment and have a lowest cost. 
```
- : AWS: 327$ + 3$ = 330$ monthly
- R$6.99 – R$799.99+/mês para manter um servidor
- Um servidor de pequeno porte mantido numa sala com ar condicionado consome pelo menos R$ 250,00 por mês.
  Isso custa cerca de R$ 3.000,00 por ano.[https://blog.unimake.com.br/gemini/7-despesas-que-sua-empresa-vai-ter-com-um-servidor-de-backup-interno/]
```

## Creating infrastructure with Docker

All softwares need some infra structure like database or a message broker. The basic proccess to a developer test the software in development stage is manually creating the envirorment in your work station. The Docker is a tool that can help the dev to run each dependency in containers. More about docker can be found here: [Docker Official](https://docs.docker.com/)

## Provisioning with TestContainers library
The library `TestContainers` make it easy creating all the envirorment to your tests in the start of test platform. It permit you to create any type of container in programatic way. You can set the ports, the networks, the version of the containers and can set the correct configuration to each test. More information can be found here: [TestContainers](https://www.testcontainers.org/quickstart/junit_5_quickstart/)

## Using Test Containers
First of all, you need to add the library in your dependency management. In gradle you can do that:
```groovy
testImplementation "org.junit.jupiter:junit-jupiter:5.8.1"
testImplementation "org.testcontainers:testcontainers:1.17.2"
testImplementation "org.testcontainers:junit-jupiter:1.17.2"
```
There are two mode to setup containers in your tests, shared container and local container. The fist type is to create and stop the container only when JVM stop and the second is to create a new container to each method test. But in both modes you create a container to your test starting from the creation of a class to your container or setup a library that create a container for you. You can see below a example to create a container class to postgres: 
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
After that, you can create a container to your test using this class. Look at a example test using that class:

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

With this class you can have the control of the creation and destruction of a container. When Quarkus context start, will create the container and call the method start to get all the properties that need to be replaced, for example, the port to connect to the postgres database. When the quarkus context stopping, call the method stop and we can stop the container if we need a new container to each test class. In the case of that you can reuse the container to all test classes, you just remove the stop container and create a static field like these: 

```java
public class SharedContainerResource implements QuarkusTestResourceLifecycleManager {
    protected static final CustomPostgresContainer POSTGRES_CONTAINER = new CustomPostgresContainer();

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

With that configuration, we can create a container to the context and in the end of execution, the testContainer library will stop automatically the container. 

More details you can find in [quick-start2][quick-start1] from oficial documentation.

## Conclusion
In this article we show how to create integration tests with containers to simulate any kind of environment. We also show the two modes to setup the containers in your tests and the diferences between them. In addition, we gave examples to use the library testContainers with Quarkus platform.
- limitations 
- Others libs examples


[cost]: https://www.servermania.com/kb/articles/how-much-does-a-typical-home-server-cost/
[TestContainers-quickstart]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[TestContainer-lib]: https://www.testcontainers.org/quickstart/junit_5_quickstart/#1-add-testcontainers-as-a-test-scoped-dependency
[quick-start1]:[TestContainers-quickstart]
[quarkus]:[link]
[spring]:[link]

## MM
temp tcdb
    Problema
        dores
            construção de ambiente grande para testes integracao
            muita configuração para rodar testes de integracao
            ambiente diferente para cada dev
            teste dependente de plataforma
            custo alto para ambiente de QA
            subir ambiente manualmente
            equipes com stack de tecnologias engessadas
                devido alto custo de montar diferentes arquiteturas de testes
        versao de plataformas
            diferentes versoes na equipe de devs
        configuracoes extras
            repassar script para cada dev
        custo para testar
            criar ambiente para pipeline de testes
                aumenta o custo homologacao
    publico
        dev junior
        dev pleno
    AIDA
        action
        des
        i
        A
    sessões do artigo
        intro
            relevancia para problema
                dados
            problema
                alto custo para ambiente de testes ou stg
                inflexibilidade de ambiente stg
                diferentes ambientes entre a equipe
            Intro de solucao
                Existencia de libraries
                docker
                    oq resolve
                test containers
                    oq resolve
        lib test containers
            funcionalidades
                data acess layer intergration tests
                    implantar DB para seus testes
                    manter mesma versao
                application integration tests
                    toda aplicação com dependencias
                    tempo de vida curto
                        somente o teste
                ui acceptances tests
                    teste de interfaces com containers de browsers como selenium
                much more
                    qualquer container que você criar
            background
                suportado
                    jUnit4
                    junit5
                    spock
        uso
            configuração
            examples de funcao
                local
                compartilhado
            ref ao artigo de como usar
        conclusao
            exemplos de outras libs similares
            limitações
    titulo
    tasks
        done
            criar branch
            criar post .md
            iniciar 5 minutes
            plan
                planejar problema
                decidir publico
                plan sections
            criar sections
            think about intro
            pesq conteudos
                done
                    blog que referencia custo
                        <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
                    custo de servidor
                        <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
                        list sites of servers
                            the lower cost
                        stimate a cost with AWS
                    noticia sobre custo
                        pesq e ver tem tem
                            senao deixa soh custo estimado de server mania
                    custo de ambiente
                    listar problemas de nao usar lib
                    custo de montar ambiente stg
                        replicar prd
                    custo operacional
                        ter equipe dedicada
                    total de trabalhos remotos
                    libs similares a testContainers
            extract info from oficial site
                done
                    funcionalidades
                    requisitos
            list main features
            plan sections
                list real problems
            review the sections
            criar projeto de exemplo
                done
                    criar branch em openCertificate
                        test-containers-article
                    criar config maven
                    criar docker compose
                    criar lista de tarefas
                    configurar flyway no projeto
                    criar classes de teste
                        container banco de dados
                            rodar migrations
                        initializer containers compartilhado
                        initializer container local
                        classe abstract test integracao
                    configurar para usuario de invillia ter permisao no git
                        nao consigo fazer push no projeto
                    commit all branch
                        push
                    criar teste de integracao
                        banco
                            repository
                                insercao de dados
                                busca de dados
                            rest
                                create certificate
                                    202
                                    400
                TDD
                    done
                        ajustar migrations com tdd
                            erro
                                done
                                    nao roda flyway
                                        ajustar flyway fora do teste de integracao
                                    Hibernate
                                        nao coloca snake case em colunas
                                            ERROR: column "dateended" of relation "event" does not exist
                                                Posição: 20
                                            solucao
                                                usar @Column para declarar colunas com snake case
                        ajustar api
                            rg null
                                alterar migration
                                    remover not null
                                        deixar somente no dominio a regra
                        criar card para open certificate
                            ajustar test in maven
                                tentar 3 dias
                                    1 dia
                                    2 dia
                                    3 dia
                            assert
                                return zip file
                                    validate
                                        size
                                        unzip
                                            size pdfs
                                            fisrt pdf size
        write sections
            done
                intro
                lib
            uso
                done
                    configuração de libs
                container compartilhado
                    descrever como usar
                    descrever para o que usar
                        funcionamento
                    vantagens
                container local
                    vantagens
            conclusao
        lead magnet
            create link to calendly to articles
                a way to capture the email and create network
            put link to calendly in article
        next plans
            plan code
            plan the conclusion
            plan cronograma
