# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# DevOps -  Gitflow
---


# 1. Gitflow

Instalação: https://github.com/nvie/gitflow/wiki/Installation

Introducing GitFlow - Open Source Projects: https://datasift.github.io/gitflow/IntroducingGitFlow.html


## Framework de Trabalho

> **O que é o Gitflow?**
>> É um processo que visa utilizar o git como ferramenta para gerenciar a criação de novas features, correções de bugs e releases.

## Importância da padronização do processo de desenvolvimento --


**(Falta um padrão. Não adequado)**
```
$ git checkout -b formulario_de_registro
$ git checkout -b bug_01
$ git push origin master
```

**(Forma adequada)**

```
$ git checkout -b feature/registro
$ git checkout -b hotfix/registro
$ git push origin develop
$ git checkout master && git merge develop
```

**Fluxo:**

- Padrão
- Legibilidade
- Processo


## Como funciona o gitflow?

São duas branches principais:

- **master** :: código fonte do que está em produção
- **develop** :: tudo o que está no ciclo de desenvolvimento, inclusive novas funcionalidades.


(*) **Regra de ouro!** :: NUNCA *comitar* direto no "master"! É o branch sagrado, ninguém toca nesse cara.


## Branch :: **develop**

A branch "develop" terá sempre 3 branches atreladas, que são branches auxiliares que ao final serão sempre "mergeadas" com a branch develop:

- features
- releases
- hiotfix


## Branch :: **features**

Destinada a novas funcionalidades



Ao criar uma feature, um branch "feature" será criado com base no último push do branch "develop".

    develop  --->  feature

Exemplo:
```
$ git checkout develop
$ git checkout -b feature/register
```



Terminado o desenvolvimento da feature, é feito um merge do conteúdo da branch "feature" para a branch "develop".

    develop  <---  feature

```
$ git checkout develop     
$ git merge feature/register
```


## Branch :: **releases**

Lançamento de uma nova versão da aplicação.

Tudo o que é desenvolvido vai sendo armazenado em "develop" (todas as novas features), e quando é de comum acordo agrupar essas funcionalidades para que sejam lançadas para a produção, é feito um "merge" da branch "develop" com a branch "release" e futuramente é feito o merge com o "master".


    develop  --->  release  -->  master

```
$ git checkout develop
$ git checkout -b release/1.0.0
$ git checkout master
$ git merge release/1.0,0
```

Aqui pode ser aplicada a **"tag"** para indicar a versão que está em produção.


## Branch :: **hotfix**

Destinada a correção de bugs em produção (não se mistura bugs com novas funcionalidades).

    develop  <---  hotfix  -->  master


Ao corrigir um bug, é criado um branch "hotfix" baseado no branch "master". Após corrigir o problema, é feito um merge da branch "hotfix" nas branches "master" e "develop".

Exemplo:

```
$ git checkout master
$ git checkout -b hotfix/recurso
```

Após a correção:
```
$ git checkout master
$ git merge hotfix/recurso
$ git checkout develop
$ git merge hotfix/recurso
```

Feito os merges necessários, podemos apagar esse branch:
```
$ git branch -D hotfix/recurso

```

## Extensão Gitflow

O Gitflow possui uma extensão para facilitar todo o processo, porém a utilização da mesma é TOTALMENTE opcional.

Para iniciar um projeto usando a extensão:

```
$ git flow init

```

Apresentando os mesmo passos mostrados até agora, mas usando a extensão do gitflow:


## Branch :: **features**

    develop  --->  feature

```
$ git flow feature start feature/register
```

Finalizado:

    develop  <---  feature

```
$ git flow feature finish feature/register
```

## Branch :: **releases**

    develop  --->  release  -->  master

```
$ git flow release start '1.0.0'
$ git flow release finish '1.0.0'
```


## Branch :: **hotfix**

   develop  <---  hotfix  -->  master

```
$ git flow hotfix start hotfix/recurso
$ git flow hotfix finish hotfix/recurso
```


# 2. Gitflow na prática

Simulação de como usar o gitflow.

A simulação será feita em um diretório vazio:

## Instalação

```
$ sudo apt-get install git-flow

$ mkdir ~/projeto/fullcycle-devops-gitflow

$ cd ~/projeto/fullcycle-devops-gitflow

$ git flow init
Initialized empty Git repository in /home/zanata/curso-fullcycle/devops/gitflow/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master] 
Branch name for "next release" development: [develop] 

How to name your supporting branch prefixes?
Feature branches? [feature/] 
Bugfix branches? [bugfix/] 
Release branches? [release/] 
Hotfix branches? [hotfix/] 
Support branches? [support/] 
Version tag prefix? [] 
Hooks and filters directory? [/home/zanata/curso-fullcycle/devops/gitflow/.git/hooks]
```

Verificação:

```
$ git branch
* develop
  master
```

Tudo preparado para o trabalho.


## Iniciando o processo

A ideia é criar um sistema de registro de usuários, que apresentará uma página "registre-se" ao ser acessada pelo usuário.

```
(develop) $ git flow feature start register
Switched to a new branch 'feature/register'

Summary of actions:
- A new branch 'feature/register' was created, based on 'develop'
- You are now on branch 'feature/register'

Now, start committing on your feature. When done, use:

     git flow feature finish register
```

**arquivo:** ~/projeto/fullcycle-devops-gitflow/register.html
```
<html>Registre-se!</html>
```

```(feature/register)$ git add .
(feature/register)$ git commit -m "Adiciona register"
```

Terminamos o desenvolvimento da funcionalidade.

```
$ git flow feature finish register
Switched to branch 'develop'
Updating 6dd6642..66d77ed
Fast-forward
 register.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 register.html
Deleted branch feature/register (was 66d77ed).

Summary of actions:
- The feature branch 'feature/register' was merged into 'develop'
- Feature branch 'feature/register' has been locally deleted
- You are now on branch 'develop'
```

Perceber que continuamos apenas com dois branches:

```
(develop)$ git branch
* develop
  master
```

A ideia agora é gerar uma versão nova do sistema (**release**).

```
(develop)$ git flow release start 0.1.0
Switched to a new branch 'release/0.1.0'

Summary of actions:
- A new branch 'release/0.1.0' was created, based on 'develop'
- You are now on branch 'release/0.1.0'

Follow-up actions:
- Bump the version number now!
- Start committing last-minute fixes in preparing your release
- When done, run:

     git flow release finish '0.1.0'
```

Finalizados os últimos ajustes:

```
(release/0.1.0)$ git flow release finish 0.1.0
```

É apresentada a mensagem padrão para o merge (será deixada como está):

```
Merge branch 'release/0.1.0'

# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
```

Em seguida é solicitada a mensagem para a tag da versão:

```
Versão contendo a página inicial para o registro de usuários.
#
# Write a message for tag:
#   0.1.0
# Lines starting with '#' will be ignored.
```

Finalizadas as inserções das mensagens:

```
....
Switched to branch 'master'
Merge made by the 'recursive' strategy.
 register.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 register.html
Already on 'master'
Switched to branch 'develop'
Already up to date!
Merge made by the 'recursive' strategy.
Deleted branch release/0.1.0 (was 66d77ed).

Summary of actions:
- Release branch 'release/0.1.0' has been merged into 'master'
- The release was tagged '0.1.0'
- Release tag '0.1.0' has been back-merged into 'develop'
- Release branch 'release/0.1.0' has been locally deleted
- You are now on branch 'develop'
```

Já temos uma tag:

```
$ git tag
0.1.0
```


## Tratanto um "hotfix"

```
$ git flow hotfix start ajustando_sinal
Switched to a new branch 'hotfix/ajustando_sinal'

Summary of actions:
- A new branch 'hotfix/ajustando_sinal' was created, based on 'master'
- You are now on branch 'hotfix/ajustando_sinal'

Follow-up actions:
- Start committing your hot fixes
- Bump the version number now!
- When done, run:

     git flow hotfix finish 'ajustando_sinal'

(hotfix/ajustando_sinal)$ 
```

Modificação:

**arquivo:** ~/projeto/fullcycle-devops-gitflow/register.html
```
<html>Registre-se!!!!</html>
```

```
(hotfix/ajustando_sinal)$ git add .
(hotfix/ajustando_sinal)$ git commit -m "Adiciona exclamação"


(hotfix/ajustando_sinal)$ git flow hotfix finish ajustando_sinal
```

Solicita a inserção da mensagem do merge para o master (manter):

```
Merge branch 'hotfix/ajustando_sinal'

# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
```

Solicita a inserção da mensagem para a nova tag:

```
Tag de correção.
#
# Write a message for tag:
#   ajustando_sinal
# Lines starting with '#' will be ignored.
```


Solicita a inserção da mensagem do merge para o develop (manter):

```
Merge branch 'hotfix/ajustando_sinal'

# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
```

Finalizada a inserção da mensagem:

```
...
Switched to branch 'master'
Merge made by the 'recursive' strategy.
 register.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
Switched to branch 'develop'
Merge made by the 'recursive' strategy.
 register.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
Deleted branch hotfix/ajustando_sinal (was 5e4cf66).

Summary of actions:
- Hotfix branch 'hotfix/ajustando_sinal' has been merged into 'master'
- The hotfix was tagged 'ajustando_sinal'
- Hotfix tag 'ajustando_sinal' has been back-merged into 'develop'
- Hotfix branch 'hotfix/ajustando_sinal' has been locally deleted
- You are now on branch 'develop'
```

Verificação:

```
$ git branch
* develop
  master
```

```
$ git tag
0.1.0
ajustando_sinal
```

# 3. Gitflow e Pull Requests

Para essas simulações:
- Será utilizado o diretório criado no item anterior;
- Foi criado um repositório fake no GitHub (exemplo-gitflow)


## Meu repositório no GitHub

```
$ git remote add origin https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git

$ git push -u origin master

$ git push origin develop --tags

```

Verificação de:
- Como ficou lá no painel do GitHub
- Opção para "Compare & Pull Request" (verificado, mas nada foi modificado)


## Criar nova funcionalidade "esqueceu a senha"

```
(master)$ git flow feature start esqueceu_senha
Switched to a new branch 'feature/esqueceu_senha'

Summary of actions:
- A new branch 'feature/esqueceu_senha' was created, based on 'develop'
- You are now on branch 'feature/esqueceu_senha'

Now, start committing on your feature. When done, use:

     git flow feature finish esqueceu_senha
```


**arquivo criado:** ~/projeto/fullcycle-devops-gitflow/senha.html
```
<h1>Esqueceu a senha?</h1>
```

```
(feature/esqueceu_senha)$ git add .
(feature/esqueceu_senha)$ git commit -m "Adiciona feature para mudar senha"
```

A feature não será finalizada agora através do git flow. Será feito um push para o repositório no github, com a intenção de que as modificações sejam revisadas por alguém/time.

```
(feature/esqueceu_senha)$ git push origin feature/esqueceu_senha
```

Lá no GitHub, podemos optar por criar um "pull request".
Baseado no branch "esqueceu_senha", quero enviar para o "develop". 

**NUNCA** para o "master", sempre para o "develop" !!!

Será solicitado um comentário: 

```
Funcionalidade referente ao esqueceu a senha. Veja como ficou.
```

Acionar o **Create pull request**.

Feito isso, perceber na interface do GitHub algumas informações sobre o uso de "integração contínua" (a ser visto posteriormente) e também a possibilidade de prosseguir com o "merge".


## Prática Comum para avaliar um **"pull request"**

1. Segue-se um template com um checklist de pontos a serem revisados.
2. Após o checklist, outros membros da equipe (adicionar reviewers) revisam o checklist da pull request para garantir que nenhum ponto da checklist tenha passado batido.
3. Após a revisão de todos os revisores, confirma-se o "merge pull request"
4. Feito o merge, o branch que originou o "pull request" torna-se desnecessário. Então podemos removê-lo do repositório (no github). 

### Mas como fica lá no respositório que está na máquina do desenvolvedor que solicitou o "pull request"?

Este é o ponto que gera um grande dilema para quem usa o Gitflow, porque ocorreram modificações no repositório central (neste caso, o Github), mas não foi finalizado o branch da feature através do gitflow na máquina do desenvolvimento.

Temos três abordagens para resolver isso:

### **1a. abordagem** - Ao trabalhar com "pull request", não finalizar a branch local com "git flow feature finish"

```
(feature/esqueceu_senha)$ git checkout develop

(develop)$ git pull origin develop

(develop)$ git branch
* develop
  feature/esqueceu_senha
  master

(develop)$ git branch -D feature/esqueceu_senha
Deleted branch feature/esqueceu_senha (was 12631ec).
```

### **2a. abordagem** - Finalizar a branch local com "git flow feature finish" após aceitar o pull request no repositório remoto (**Abordagem recomendada pelo instrutor**)

```
$ git flow feature start login
```

**arquivo criado:** ~/projeto/fullcycle-devops-gitflow/login.html

```
<h1>Login</h1>
```

```
$ git add .
$ git commit -m "Adiciona funcionalidade de login"
$ git push origin feature/login
```

Lá no Github, criar a pull request, apontando para a branch "develop", e disparar o "merge pull request".

```
$ git flow feature finish login
Switched to branch 'develop'
Updating 34900de..52398fb
Fast-forward
 login.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 login.html
Username for 'https://github.com': zanatabr
Password for 'https://zanatabr@github.com': 
To https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git
 - [deleted]         feature/login
Deleted branch feature/login (was 52398fb).

Summary of actions:
- The feature branch 'feature/login' was merged into 'develop'
- Feature branch 'feature/login' has been locally deleted; it has been remotely deleted from 'origin'
- You are now on branch 'develop'
```

```
$ git pull origin develop
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From https://github.com/zanatabr/fullcycle-devops-gitflow-sample
 * branch            develop    -> FETCH_HEAD
   34900de..fdf5223  develop    -> origin/develop
Updating 52398fb..fdf5223
Fast-forward
```

Não teve modificação de código, mas teve uma nova entrada no log.

```
$ git push origin develop
Everything up-to-date
```


### **3a. abordagem** - Fazer a aprovação do pull request no repositório remoto, sem fazer o merge por lá, e finalizar a branch local com "git flow feature finish" e push das modificações para o repositório remoto

```
$ git flow feature start esqueci_email
```

**arquivo criado:** ~/projeto/fullcycle-devops-gitflow/esqueci_email.html
```
<h1>Esqueci meu e-mail</h1>
```
```
$ git add .

$ git commit -m "Adiciona funcionalidade: esqueci meu e-mail"

$ git push origin feature/esqueci_email
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 393 bytes | 393.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote: 
remote: Create a pull request for 'feature/esqueci_email' on GitHub by visiting:
remote:      https://github.com/zanatabr/fullcycle-devops-gitflow-sample/pull/new/feature/esqueci_email
remote: 
To https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git
 * [new branch]      feature/esqueci_email -> feature/esqueci_email
```

Lá no Github, criar a pull request, apontando para a branch "develop", mas NÃO disparar o "merge pull request".

Supondo que toda a revisão tenha sido feita, que os revisores tenham aprovado, não será feito o disparo do merge desse pull request.

Em vez disso, na máquina do desenvolvedor, finalizar a branch da feature:

```
$ git flow feature finish esqueci_email
Switched to branch 'develop'
Updating fdf5223..dc36016
Fast-forward
 esqueci_email.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 esqueci_email.html
Username for 'https://github.com': zanatabr
Password for 'https://zanatabr@github.com': 
To https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git
 - [deleted]         feature/esqueci_email
Deleted branch feature/esqueci_email (was dc36016).

Summary of actions:
- The feature branch 'feature/esqueci_email' was merged into 'develop'
- Feature branch 'feature/esqueci_email' has been locally deleted; it has been remotely deleted from 'origin'
- You are now on branch 'develop'
```

```
$ git push origin develop
Username for 'https://github.com': zanatabr
Password for 'https://zanatabr@github.com': 
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 393 bytes | 393.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git
   fdf5223..dc36016  develop -> develop
```

Perceber lá no repositório central (GitHub) que o merge foi feito (*Pull request successfully merged and closed*).

Não faz sentido manter a branch da feature no repositório remoto, então podemos removê-la.


# 4. Trabalhando com releases

É o momento em que geramos a versão da aplicação, criamos uma tag para identificar em qual commit foi gerada aquele release, e a partir disso atualizamos a branch master do repositório.


## **1a abordagem** - utilizando o "finish" do gitflow

```
$ git flow release start 0.2.0
```

Neste momento são feitas algumas modificações necessárias (atualização do README.md, ajustes necessários, etc).

Antes de finalizar, faz-se um push para o repositório remoto.

```
$ git push origin release/0.2.0
```

Feito isso, lá no repo central (GitHub) faremos a criação do pull request, mas dessa vez apontando para o branch "master", pois essa release entrará em produção.

Supondo que toda a revisão tenha sido feita, que os revisores tenham aprovado, fazer o disparo do merge desse pull request, (não remover a branch da release ainda)

Neste ponto temos o branch "master" atualizado lá no repo central.

Mas ainda temos uma questão pendente, pois o branch "release" ainda está aberto na máquina local do desenvolvedor. O que deve ser feito nesse caso?

**Importante**: Todas as vezes em que for feita uma "release", os valores devem ser iguais nas branches "master" e "develop".

```
$ git flow release finish '0.2.0'
```

É solicitada a mensagem para o merge (manter).

É solicitada a mensagem para a tag:
```
Essa release trouxe as seguintes funcionalidades
1) Login
2) Esqueci a minha senha
3) Esqueci o e-mail
```

```
$ git checkout master

$ git pull origin master
```

É solicitada a mensagem para o merge:
```
(OK)

```

```
$ git checkout develop

$ git merge master

$ git push origin develop --tags
```

Para subir a tag '0.2.0'.


### **2a abordagem** - Sem utilizar o "finish" do gitflow (**Recomendação do instrutor**)

**arquivo modificado:** ~/projeto/fullcycle-devops-gitflow/register.html

```
<h1>Registre-se!!!</h1>
```

```
$ git add .
$ git commit -m "Ajusta: registre-se"
$ git push origin develop

$ git flow release start 0.2.1

$ git push origin release/0.2.1
```

Feito isso, lá no repo central (GitHub) faremos a criação do pull request, mas dessa vez apontando para o branch "master", pois essa release entrará em produção.

Supondo que toda a revisão tenha sido feita, que os revisores tenham aprovado, fazer o disparo do merge desse pull request, (não remover a branch da release ainda)

Neste ponto, temos o branch "master" atualizado lá no repo central.

Na máquina do desenvolvedor, não faremos o "finish" da release através do gitflow.

```
$ git checkout master

$ git pull origin master

$ git branch
  develop
* master
  release/0.2.1
```

```
$ git branch -D release/0.2.1
Deleted branch release/0.2.1 (was d286749).
```

```
$ git tag 0.2.1

$ git tag
0.1.0
0.2.0
0.2.1
ajustando_sinal
```

```
$ git push origin master --tags
Total 0 (delta 0), reused 0 (delta 0)
To https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git
 * [new tag]         0.2.1 -> 0.2.1
```

```
$ git checkout develop
Switched to branch 'develop'
```

```
$ git pull origin develop
From https://github.com/zanatabr/fullcycle-devops-gitflow-sample
 * branch            develop    -> FETCH_HEAD
Already up to date.
```

```
$ git merge master
Updating d286749..49f765c
Fast-forward
```

Tudo OK. Agora os branches "master" e "develop" são iguais.


# 5. Trabalhando com hotfix

Correção de bugs que ocorrem em PRODUÇÃO (master). Quando o problema é resolvido, deve-se atualizar as branches "master" e "develop" com a modificação feita.

Ao se trabalhar com gitflow, surge também uma complicação que é o fato de usarmos "pull requests". Em algumas situações, o uso da "extensão gitflow" pode atrapalhar em vez de ajudar.

**arquivo criado (para testar):** ~/projeto/fullcycle-devops-gitflow/index.html

```
<h1>Página inicial</h1>
```

```
(develop)$ git add .
(develop)$ git commit -m "Adiciona index"
(develop)$ git push origin develop
```

Para simular que outra equipe esteja fazendo modificações na "develop" durante o surgimento de um "bug" em produção.

```
(develop)$ git tag
0.1.0
0.2.0
0.2.1
ajustando_sinal
```

A simulação agora é *surgiu uma falha em produção que precisa ser corrigida*:

```
(develop)$ git flow hotfix start 0.1.1
Switched to a new branch 'hotfix/0.1.1'

Summary of actions:
- A new branch 'hotfix/0.1.1' was created, based on 'master'
- You are now on branch 'hotfix/0.1.1'

Follow-up actions:
- Start committing your hot fixes
- Bump the version number now!
- When done, run:

     git flow hotfix finish '0.1.1'
```

Perceber que o branch da "hotfix" foi criado baseando-se na branch "master".

```
(hotfix/0.1.1)$ ls -la
total 28
drwxr-xr-x 3 zanata zanata 4096 mai 26 06:10 .
drwxr-xr-x 7 zanata zanata 4096 mai 24 20:31 ..
-rw-r--r-- 1 zanata zanata   27 mai 25 20:16 esqueci_email.html
drwxr-xr-x 8 zanata zanata 4096 mai 26 06:10 .git
-rw-r--r-- 1 zanata zanata   14 mai 25 20:16 login.html
-rw-r--r-- 1 zanata zanata   23 mai 25 20:59 register.html
-rw-r--r-- 1 zanata zanata   26 mai 25 20:16 senha.html
```

Perceber que o "index.html" não está nesse branch ainda.

**arquivo criado:** ~/projeto/fullcycle-devops-gitflow/hotfix.html
```
<h1>hotfix</h1>
```

```
(hotfix/0.1.1)$ git add .
(hotfix/0.1.1)$ git commit -m "hotfix 1"
```

Problema resolvido.

As modificações serão carregadas para o repo central (github), para que seja criado o "pull request":

```
(hotfix/0.1.1)$ git push origin hotfix/0.1.1
```

Lá no repo central (GitHub) recebemos a sugestão de criar o "pull request" para os branches "develop" e "hotfix/0.1.1". 

Ainda não faz sentido fazer isso para o branch "develop", mas devemos seguir com o pull request para "hotfix/0.1.1", apontando para o branch "master", pois essa release entrará em produção.

Supondo que toda a revisão tenha sido feita, que os revisores tenham aprovado, fazer o disparo do merge desse pull request, (não remover a branch da release ainda)

Neste ponto, temos o branch "master" atualizado lá no repo central.


**Importante:** Agora vem o problema de se usar a extensão "gitflow" nesse caso (**opinião do instrutor**).

```
(hotfix/0.1.1)$ git flow hotfix finish '0.1.1'
O que acontece?
-> merge: master ()
-> tag: criação (corrigindo bug 1)
-> merge: develop ()
-> remove a branch local "hotfix/0.1.1"

Summary of actions:
- Hotfix branch 'hotfix/0.1.1' has been merged into 'master'
- The hotfix was tagged '0.1.1'
- Hotfix tag '0.1.1' has been back-merged into 'develop'
- Hotfix branch 'hotfix/0.1.1' has been locally deleted; it has been remotely deleted from 'origin'
- You are now on branch 'develop'
```

O branch "develop" está na mesma situação do branch "master". (Agora temos o arquivo "index.html" no branch "develop")

Mas estamos trabalhando com um repo central (github), e isso traz algumas preocupações adicionais, como a que se segue:

```
(develop)$ git checkout master

(master)$ git pull origin master
```

Neste ponto, o git pede pra fazer um **"merge" de um outro "merge"** que já foi feito lá no GitHub, e esse é o problema neste caso.

```
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From https://github.com/zanatabr/fullcycle-devops-gitflow-sample
 * branch            master     -> FETCH_HEAD
   49f765c..a751418  master     -> origin/master
Already up to date!
Merge made by the 'recursive' strategy.
```

O branch "master" já estava atualizado, mas é gerada uma nova linha no log que pode confundir em uma possível análise, e isso deve ser levado em consideração.

Ao mesmo tempo, a situação do branch "develop" está a um passo anterior do branch "master", porque o branch "master" acabou de ganhar um novo "merge".

```
(master)$ git checkout develop

(develop)$ git merge master
```

Aqui é criado "mais um merge", que vai mudar o que está lá no Github novamente. Isso gera uma certa bagunça, porque acabamos criando uma situação na máquina local que é difernte do repositório central, o que nos força a sincronizar as duas coisas.

```
(develop)$ git push origin develop
```




## **Recomendação**

O MÉTODO/PROCESSO do GitFlow ajuda bastante, mas nem sempre o uso da EXTENSÃO GitFlow é adequada.

Vale a pena seguir uma regra: Criar/Iniciar o processo usando a extensão "gitflow", mas *nunca finalizar* com a extensão. Vale mais a pena fazer manualmente, conforme o cenário a seguir:

```
$ git flow hotfix start 0.1.2
```

**arquivo modificado:** ~/projeto/fullcycle-devops-gitflow/hotfix.html
```
<h1>Hotfix - 2</h1>
```

```
(hotfix/0.1.2)$ git add .
(hotfix/0.1.2)$ git commit -m "hotfix 2"
```

Problema resolvido.

As modificações serão carregadas para o repo central (github), para que seja criado o "pull request":

```
(hotfix/0.1.2)$ git push origin hotfix/0.1.2
```

Lá no repo central (GitHub) recebemos a sugestão de criar o "pull request" para os branches "develop" e "hotfix/0.1.2". Ainda não faz sentido fazer isso para o branch "develop", mas devemos seguir com o pull request para "hotfix/0.1.2", apontando para o branch "master", pois essa release entrará em produção.

Supondo que toda a revisão tenha sido feita, que os revisores tenham aprovado, fazer o disparo do merge desse pull request.

Remover a branch "hotfix/0.1.2" do repo central.

Neste ponto, temos o branch "master" atualizado lá no repo central.

Faremos todo o processo de finalização sem o uso da extensão "gitflow".

``` 
(hotfix/0.1.2)$ git checkout master

(master)$ git pull origin master

(master)$ git branch
develop
hotfix/0.1.2
* master
```

Remover a branch do hotfix:
```
(master)$ git branch -D hotfix/0.1.2
```

Criar a tag:
```
(master)$ git tag 0.1.2
```

```
(master)$ git checkout develop

(develop)$ git merge master
```

```
(develop)$ git push origin develop --tags
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 444 bytes | 444.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/zanatabr/fullcycle-devops-gitflow-sample.git
   d204462..a821f72  develop -> develop
 * [new tag]         0.1.1 -> 0.1.1
 * [new tag]         0.1.2 -> 0.1.2
```

O nosso branch "develop" tinha alterações. Conseguimos fazer o merge com a "master" sem gerar mais confusão (merges adicionais).


# 6. Conhecendo o Sourcetree

Ferramenta criada pela equipe do BitBucket

Sourcetree (Só tem para Windows e Mac)
https://www.sourcetreeapp.com/

Há outras opções:

Vide: Top 5 Melhores Clientes Git para Linux, macOS e Windows
https://terminalroot.com.br/2019/10/top-5-melhores-clientes-git-para-linux-macos-e-windows.html



# 7. Semantic Versioning

Convenção SEMVER (Semantic Versioning).

Semantic Versioning 2.0.0 :: https://semver.org/


Exemplo: **1.0.1**  ==>  Major.Minor.Patch

- Major :: Quando essa versão é modificada, significa que ocorrerá uma quebra de compatibilidade com todas as anteriores.
- Minor :: Adição de funcionalidades, mas mantém a compatibilidade.
- Patch :: Correção de bugs.


## SEMVER

- Não pode conter números negativos
- Uma vez que uma versão é gerada, não é possível fazer mais modificações
- A "Major" que comece com 0.y.z é publicamente instável e a compatibilidade pode ser quebrada


## Metadata

- **alpha** :: em desenvolvimento sem se preocupar com testes unitários antigos
- **beta** :: em desenvolvimento, mas os testes unitários agora são válidos
- **rc1** :: Release Candidate 1 :: Candidato para gerar a release :: Testes mais pesados e validados para as novas funcionalidades
- **rc2** :: mais testes devem ser criados
- **rc3** :: mais testes ... para garantir que a versão esteja o "mais pronto" possível, para finalmente ir para a produção.


> 1.0.0-alpha, 1.0.0-alpha.1, 1.0.0-rc.1
