# Waste less money, use testContainers

The devOps help us a lot, but just a big infrastructure don't resolve the problem with pipelines. The pipeline creation is simple, but the server to run the environments are of higher cost. 
- data with the costs
- : AWS: 327$ + 3$ = 330$ monthly

So, to waste less money there are two tools, docker and testContainers. The docker can be used to provisioning the infrastructure to yours dependencies and the testContainers is a library that permit you to create differents scenarios to your testing system.
  In this article we will show how to use these tools to create your test environment and have a lowest cost.

## Creating infrastructure with Docker
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
                custo de servidor
                    <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
                    list sites of servers
                        the lower cost
                    stimate a cost with AWS
                noticia sobre custo
                blog que referencia custo
                    <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/> <https://www.servermania.com/kb/articles/how-much-does-a-server-cost-for-a-small-business/>>
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
