# Stop suffering with tests, use TestContainers

The devOps help us a lot, but just a big infrastructure don't resolve the problem with pipelines. The pipeline creation is simple, but the server to run the environments are of higher cost. 
- data with the costs
According with [cost] the cost to create a server is 2k until 15k. Its is a higher cost depends on the company. 
- : AWS: 327$ + 3$ = 330$ monthly
- R$6.99 – R$799.99+/mês para manter um servidor
- Um servidor de pequeno porte mantido numa sala com ar condicionado consome pelo menos R$ 250,00 por mês.
  Isso custa cerca de R$ 3.000,00 por ano.[https://blog.unimake.com.br/gemini/7-despesas-que-sua-empresa-vai-ter-com-um-servidor-de-backup-interno/]

More over, the cost might be duplicated when need a envirorment to production and another to staging. According to theses, the cost to test can increase a lot with a two envirorment. 

So, to waste less money and continue increasing the delivery quality, in this article we will show how can use two tools, docker and testContainers to decrease the cost to test systems with Java. The docker can be used to provisioning the infrastructure to yours dependencies and the testContainers is a library that permit you to create differents scenarios to your testing system.
  In this article we will show how to use these tools to create your test environment and have a lowest cost.

## Creating infrastructure with Docker

All softwares need some infra structure like database or a message broker. The basic proccess to a developer test the software in development stage is manually creating the envirorment in your work station. The library TestContainers make easy   
- What is the problem to solve: pipeline compartilhado
- What is docker
- how it works
- how can solve your problem

## Provisioning with TestContainers library
- what is
- what env can I use
- main features

## Using Test Containers
- configuration
- real use example
- ref to new article 

## Conclusion
- what were discussed
- limitations 
- Others libs examples


[cost]: https://www.servermania.com/kb/articles/how-much-does-a-typical-home-server-cost/

## MM
temp tcdb
    Problema
        dores
            construção de ambiente grande para testes integracao
            muita configuração para rodar testes de integracao
            ambiente diferente para cada dev
            teste dependente de plataforma
            custo alto para ambiente de QA
            equipes com stack de tecnologias engessadas
                devido alto custo de montar diferentes arquiteturas de testes
        custo para testar
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
            Intro de solucao
                Existencia de libraries
                docker
                    oq resolve
                test containers
                    oq resolve
        lib test containers
            background
            funcionalidades
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
    apresentação CPGoias 2
        publico
            devs goianos junior e plenos
        topicos
            intro
            test containers
                resolve qual problema
                funcoes
            wiremock
                resolve qual problema
                funcoes
        tempo
            25 a 55 minutos
                com tempo de perguntas
        pratica
            apresentar sistema
                infra
                funcao do servico
                diagrama de atividades
            testes cotidianos
                entradas e saidas
                testes de erros
            Conclusao
                formas de usar libs
        tasks
            done
                leitura de regulamento CPGO2
            create slides contents
            create slides
            create open certificate branch
            add diagrams to
                interfaces
            add integration tests
                interfaces
            add api use with feign
                use interface
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
            custo de ambiente
                done
                    blog que referencia custo
                        <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
                    custo de servidor
                        <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
                        list sites of servers
                            the lower cost
                        stimate a cost with AWS
                    noticia sobre custo
                        pesq e ver tem tem
                            senao deixa soh custo estimado de server mania
            custo de montar ambiente stg
                replicar prd
            custo operacional
                ter equipe dedicada
            listar problemas de nao usar lib
            total de trabalhos remotos
            libs similares a testContainers
        extract info from oficial site
        list main features
        list real problems
        next plans
            plan code
            plan the conclusion
            plan cronograma
