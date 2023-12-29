# Gerenciamento de versões
Siga o que foi descrito neste arquivo para geração de versões e versionamento de códigos.

## Versionamento
### Novas funcionalidades
Crie uma branch `develop` para servir como branch de acumulo de novas funcionalidades. A cada nova feature crie uma branch a partir da develop, ex. `sprint_3-estoque`. Portanto, a branch develop conterá todas branches de features mergiadas.
### Correções
Crie uma branch a partir da versão a ser corrigida, ex. `hotfix_1.0.1` e depois faça merge para o `master` e `develop`. Lembrando que este tipo de branch não conterá alterações, somente correções.
### Merge request
Todos merge devem ser feitos via `merge request`. Após realização de merge, delete a branch no remoto.
## Versões
As versões deste projeto utiliza o semantic version como padrão, divido entre major, minor e path: `major.minor.path`, 1.0.2, sendo 2 como path da versão 1.0.1. 
### Uso de major
A major deve ser utilizada somente para novas funções que não estão relacionadas com as existentes, gerando novos requisitos, novas telas ou novas integrações.

Pode ser utilizada também na remoção de funções, desativação, depreciação dentre outras alterações de compatibilidade. 
### Uso de minor
Utilize para Adição de funções relacionadas com funções já existentes, ou seja, adições, incrementos que não necessitam de novas telas, ou novos dados. Pode utilizar para ajuste de funções, como por exemplo envio de email ao fazer um cadastro.

Não utilize para remoção de funções. 
### Uso de path 
Utilize para correção de versões minor. Não deve ser utilizado para fazer adição de funções ou incremento, somente correçoes rápidas e simples.

## Geração de versão
O site é a versão de código que está no `master`. Portanto, antes de fazer o merge para o master, na `develop` você deve alterar a versão no arquivo: `index.html`. Faça o PR para o master e pronto.