# Stop suffering with tests, use TestContainers

The devOps help us a lot, but just a big infrastructure don't resolve the problem with pipelines. The pipeline creation is simple, but the server to run the environments have high cost. According with [cost] the cost to create a server is 2k until 15k. Its is a higher cost depends on the company. More over, the cost might be duplicated when need an envirorment to production and another to staging. The cost to test can increase a lot with a two envirorments. In addition, a developer team can lead with the problem of different versions of platforms. One developer can install a data base with the version x and another developer install the version y. The first developer can create a test that work in the version x, but don't work in version y. 

So, to waste less money and continue increasing the delivery quality, in this article we will show how can use two tools, docker and testContainers to decrease the cost to test systems with Java. The docker can be used to provisioning the infrastructure to yours dependencies and the testContainers is a library that permit you to create differents scenarios to your testing system.
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
You can follow the quick start from [quick-start2][quick-start1]
- real use example
- ref to new article 

## Conclusion
- what were discussed
- limitations 
- Others libs examples


[cost]: https://www.servermania.com/kb/articles/how-much-does-a-typical-home-server-cost/
[TestContainers-quickstart]: https://www.testcontainers.org/quickstart/junit_5_quickstart/
[TestContainer-lib]: https://www.testcontainers.org/quickstart/junit_5_quickstart/#1-add-testcontainers-as-a-test-scoped-dependency
[quick-start1]:[TestContainers-quickstart]

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
                        <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
                    custo de servidor
                        <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
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
        write sections
            done
            intro
            lib
            uso
            conclusao
        lead magnet
            create link to calendly to articles
                a way to capture the email and create network
            put link to calendly in article
        next plans
            plan code
            plan the conclusion
            plan cronograma

