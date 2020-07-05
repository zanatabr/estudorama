# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# DevOps - Continuous Integration

# 1. Entendendo Integração Contínua

## O que é Continuous Integration (CI)? 

> "É uma prática de desenvolvimento que visa integrar o código de diversos membros de um time de um repositório compartilhado. Cada integração é verificada através de builds e testes automatizados".

Garantir, após cada modificação, que o software continua funcionando e que o código não seja "quebrado" no repositório central.

1. Automatiza os processos e "garante" que o código de todos está funcionando em conjunto
2. Detecta erros antes de seguir para a produção
3. Garante que o build está sendo gerado corretamente

## Dinâmica do processo

1. Vários DEVs criam Pull Requests em um repositório Git centralizado
2. Cada vez que um Pull Request é feito, o processo de CI é executado
   1. Faz um clone da branch
   2. Baixa as dependências
   3. Executa os testes
   4. Finaliza o processo de build


## Boas práticas

Fonte: https://codeship.com/continuous-integration-essentials

1. Pipeline rápido  (produtividade e custo)
2. Ambiente isolado (para não onerar os demais ambientes)
3. Rodar os testes mais rápidos primeiro, depois os mais demorados
4. Todos do time podem ter acesso aos resultados


## Ferramentas populares

1. Jenkins
2. CircleCI
3. Travis
4. Google Cloud Build
5. Amazon Cloud Build
6. Azure Pipelines
7. GitLab CI
8. Go CD
9. Team City


# 2. Iniciando com GCP

Usaremos o Google Cloud Build que é uma das ferramentas existentes no Google Cloud Platform (GCP).

Para o curso, tentar usar um cupom de desconto (normalmente cupons de US$300, o que daria pra fazer o treinamento todo).

## GCP
https://cloud.google.com

### 1. Feito a crição de um projeto (dfdx)

    Nome do projeto
     dfdx
    ID do projeto
     dfdx-279419
    Número do projeto
     736432436464 

### 2. Instalar também as ferramentas de linha de comando

   https://cloud.google.com/sdk
   https://cloud.google.com/sdk/docs/quickstarts
   https://cloud.google.com/sdk/docs/quickstart-debian-ubuntu

   (Tentarei depois em uma imagem Docker)

### 3. Habilitar o Google Cloud Build



# 3. Criando primeiro Trigger

Aqui será criado um trigger no GCB para que seja disparado um build sempre que ocorrer um push no nosso repositório Git.

Apenas para testar todo o fluxo do pipeline, será usada a imagem do Lavarel que usamos anteriormente, mas deixaremos o "dockerize" de lado. Para isso, será usado um *docker-compose.yml* diferenciado para o processo de build.

Arquivo criado a partir do docker-compose.yaml.

**arquivo:** ~/projeto/laravel/docker-compose.cloudbuild.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        # entrypoint: dockerize -template ./.docker/app/.env.tmpl:.env -wait tcp://db:3306 -timeout 40s -wait-retry-interval 3s ./.docker/app/entrypoint.sh
        # environment:
        #   - DB_HOST=db
        #   - DB_DATABASE=laravel
        #   - DB_USERNAME=root
        #   - DB_PASSWORD=root
        volumes:
          - .:/var/www
        networks:
          - app-network
        depends_on:
          - db
          - redis

    nginx:
        build: .docker/nginx
        container_name: nginx
        # entrypoint: dockerize -template /etc/nginx/conf.d/nginx.conf:/etc/nginx/conf.d/nginx.conf -wait tcp://app:9000 -timeout 40s -wait-retry-interval 5s nginx
        # command: -g "daemon off;" -c /etc/nginx/nginx.conf
        environment:
          - APP_HOST=app
          - APP_PORT=9000
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www
        networks:
          - app-network
        depends_on:
          - app

    db:
        image: mysql:5.7
        # Este comando é importante para que o mysql execute adequadamente
        command: --innodb-use-native-aio=0
        container_name: db
        restart: always
        tty: true
        ports:
          - "3306:3306"
        volumes:
          - ./.docker/dbdata:/var/lib/mysql
        environment:
          - MYSQL_DATABASE=laravel
          - MYSQL_ROOT_PASSWORD=root
          - MYSQL_USER=root
        networks:
          - app-network

    redis:
        image: redis:alpine
        container_name: redis
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```

Arquivo yaml em que serão armazenados todos os passos que o Cloud Build deverá executar.

Obs.: O Cloud Build oferece duas opções: 1) O CB lê e executa o Dockerfile (faz a verificação apenas do que está no Dockerfile); 2) O CB lê o cloudbuild.yaml (RECOMENDADO, pois possibilita fazer testes mais aprofundados de uma pipeline); 

**arquivo:** ~/projeto/laravel/cloudbuild.yaml
```
steps:
- id: "Rodando docker-compose"
  name: 'gcr.io/$PROJECT_ID/docker-compose'
  args: ['-f','docker-compose.cloudbuild.yaml','up','-d']
```

O Google Cloud Build oferece imagens Docker para tarefas específicas (rodar comandos Docker, específica do Ubuntu, etc). No arquivo yaml anterior, o nome da imagem foi definido no atributo "name", que por sua vez receberá o valor através de uma variável de ambiente.

```
$ git add .
$ git commit -m "carga do cloudbuild"
$ git push origin master
```

## No Google Cloud Build

Criar um novo Trigger (Acionador)

    Fonte: (GitHub) zanatabr/fullcycle-devops-laravel
    Nome: default-push-trigger-1
    Descrição: Enviar por push para qualquer branch
    Evento: Enviar para uma ramificação
    Fonte -> Ramificação: master
    Configuração da compilação -> Tipo de arquivo: Arquivo de configuração do Cloud Build (yaml ou json)
    Local do arquivo de configuração do Cloud Build:  /cloudbuild.yaml

Ao fazer o disparo manual do gatilho, percebe-se que ocorre um erro porque o "docker compose" não é encontrado.

O objetivo foi alcançado, que seria a criação do trigger e a execução do build (mesmo com erro). Os ajustes serão feitos nos próximos passos.

# 4. Entendendo instalação do Docker

O nosso arquivo cloudbuild.yaml faz referência a uma imagem docker que estaria no Google Cloud Registry, mas a imagem ainda não foi carregada.

No arquivo "cloudbuild.yaml" indicamos que o primeiro passo do nosso pipeline seria a execução do "docker compose", e isso seria feito através de um container Docker que está no endereço 'gcr.io/$PROJECT_ID/docker-compose'.

O problema é que não temos nada no nosso Docker Container Registry.

Teremos que fazer o build de uma imagem do Docker Compose e carregar para o Container Registry do GCP (não usaremos o DockerHub como registry).


## O que temos lá no Container Registry do Google 

[gcr.io/cloud-builders/]
- Docker
- Git
- go
- ...
- imagens públicas

Mas não temos uma imagem específica para "docker compose"

Faremos então a inserção do docker compose no nosso registry, em "gcr.io/$PROJECT_ID", onde $PROJECT_ID é o identificador do nosso projeto no GCP (dfdx).

## Instalação do docker-compose
1. Dockerfile (instala o docker-compose)
2. Criar um "cloudbuild.yaml"
   1. Usar o Docker do cloud-builders
   2. Gera o build a partir do Dockerfile
   3. Com a imagem criada, fazer o push da imagem no "gcr.io/$PROJECT_ID"


# 5. Instalando docker-compose no registry


**Repositório de containers "cloud-builders"**
https://console.cloud.google.com/gcr/images/cloud-builders

**O meu Container Registry (ainda vazio)**
https://console.cloud.google.com/gcr/images/dfdx-279419


**Repositório de código-fonte das imagens Docker preparadas  pela comunidade, para a construção de passos no Google Cloud Build**
https://github.com/GoogleCloudPlatform/cloud-builders-community


**Usaremos este Dockerfile (Docker-compose)**
https://github.com/GoogleCloudPlatform/cloud-builders-community/tree/master/docker-compose

```
$ mkdir ~/projeto/laravel/docker-compose
```

**arquivo:** ~/projeto/docker-compose/Dockerfile
```
FROM ubuntu:bionic

ARG DOCKER_COMPOSE_VERSION

# https://docs.docker.com/compose/install/
RUN \
   apt-get -y update && \
   apt-get -y install ca-certificates curl docker.io && \
   rm -rf /var/lib/apt/lists/* && \
   curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
   chmod +x /usr/local/bin/docker-compose

ENTRYPOINT ["/usr/local/bin/docker-compose"]
```


**arquivo:** ~/projeto/docker-compose/cloudbuild.yaml
```
# In this directory, run the following command to build this builder.
# $ gcloud builds submit . --config=cloudbuild.yaml
substitutions:
  _DOCKER_COMPOSE_VERSION: 1.25.5
steps:
- name: 'gcr.io/cloud-builders/docker'
  args:
  - 'build'
  - '--build-arg'
  - 'DOCKER_COMPOSE_VERSION=${_DOCKER_COMPOSE_VERSION}'
  - '-t'
  - 'gcr.io/$PROJECT_ID/docker-compose:latest'
  - '-t'
  - 'gcr.io/$PROJECT_ID/docker-compose:${_DOCKER_COMPOSE_VERSION}'
  - '.'
- name: 'gcr.io/$PROJECT_ID/docker-compose'
  args: ['version']

images:
- 'gcr.io/$PROJECT_ID/docker-compose:latest'
- 'gcr.io/$PROJECT_ID/docker-compose:${_DOCKER_COMPOSE_VERSION}'
tags: ['cloud-builders-community']
```




## Criado repositório no github
https://github.com/zanatabr/fullcycle-devops-cloud-build-docker-composer

```
$ git init
$ echo "# fullcycle-devops-cloud-build-docker-composer" >> README.md
$ git status
$ git add .
$ git commit -m "first commit"
$ git remote add origin https://github.com/zanatabr/fullcycle-devops-cloud-build-docker-composer.git
$ git push -u origin master
```


### No GCB
Criar um novo Trigger apontando para o repositório GitHub que acabamos de criar, e solicitar a sua execução.
Build executado com sucesso.
No nosso Container Registry podemos verificar que a imagem foi carregada pra lá.
Agora sim faz sentido a chamada 'gcr.io/$PROJECT_ID/docker-compose'


# 6. Executando pipeline completo

1. Para garantir que todos os contêineres estão no ar, será executado um passo que executa o "docker container ls".
2. Modificar a versão da imagem do "docker-compose" no "cloudbuild.yaml" para "1.25.5"


**arquivo:** ~/projeto/laravel/cloudbuild.yaml
```
steps:

- id: "Rodando docker-compose"
  name: 'gcr.io/$PROJECT_ID/docker-compose:1.25.5'
  args: ['-f','docker-compose.cloudbuild.yaml','up','-d']

- id: "Rodando docker container ls"
  name: 'gcr.io/cloud-builders/docker'
  args: ['container','ls']
```

```
$ git add .
$ git commit -m "Adiciona step com chamada ao 'docker container ls'"
$ git push origin master
```

Acompanhar o log da trigger disparada e verificar se os passos foram executados corretamente.

**Execução do composer no container "app"**

**arquivo:** ~/projeto/laravel/cloudbuild.yaml
```
steps:

- id: "Execução do docker-compose"
  name: 'gcr.io/$PROJECT_ID/docker-compose:1.25.5'
  args: ['-f','docker-compose.cloudbuild.yaml','up','-d']

- id: "Execução do docker container ls"
  name: 'gcr.io/cloud-builders/docker'
  args: ['container','ls']

- id: "Execução do composer"
  name: 'gcr.io/cloud-builders/docker'
  args: ['exec','-t','app','composer', 'install']
```

```
$ git add .
$ git commit -m "Adiciona step com chamada ao 'composer' no container 'app'"
$ git push origin master
```

Acompanhar o log da trigger disparada e verificar se os passos foram executados corretamente.

**Adição de novos steps no pipeline.**

**arquivo:** ~/projeto/laravel/cloudbuild.yaml
```
steps:

- id: "Execução do docker-compose"
  name: 'gcr.io/$PROJECT_ID/docker-compose:1.25.5'
  args: ['-f','docker-compose.cloudbuild.yaml','up','-d']

- id: "Execução do docker container ls"
  name: 'gcr.io/cloud-builders/docker'
  args: ['container','ls']

- id: "Execução do composer"
  name: 'gcr.io/cloud-builders/docker'
  args: ['exec','-t','app','composer', 'install']

- id: "Copia do .env"
  name: 'gcr.io/cloud-builders/docker'
  args: ['exec','-t','app','cp', '.env.example','.env']

- id: "Execução do key:generate"
  name: 'gcr.io/cloud-builders/docker'
  args: ['exec','-t','app','php', '/var/www/artisan','key:generate']

- id: "Execução dos migrations"
  name: 'gcr.io/cloud-builders/docker'
  args: ['exec','-t','app','php', '/var/www/artisan','migrate']

- id: "Execução dos testes unitários - phpunit"
  name: 'gcr.io/cloud-builders/docker'
  args: ['exec','-t','app','php', '/var/www/vendor/bin/phpunit','-c','/var/www/phpunit.xml']
```

```
$ git add .
$ git commit -m "Adiciona steps adicionais [.env, key:generate, migrations, phpunit]'"
$ git push origin master
```

Acompanhar o log da trigger disparada e verificar se os passos foram executados corretamente.


Tudo isso foi feito apenas para testar. No cotidiano não se faz o commit diretamente no branch "master" ou "developer". Trabalha-se com "pull request", sempre.



# 7. Instalando Cloud Build App no Github

Instalar o Cloud Build no GitHub

    Google Cloud Build - Pay-as-you-go
    Cloud Build charges per build minute above a free tier.
    * 120 free build minutes a day. $0.003/minute after that.
    * Up to 10 concurrent builds included.
    * Automatically push built images to Google Container Registry (    storage costs may apply).    
    * See the Cloud Build pricing page for full details.     https://cloud.google.com/cloud-build/pricing


Ideia de uso: Todas as vezes que criarmos um pull request, fazer com que o GitHub acione essa pull request lá no Cloud Build e execute para verificar se está tudo Ok, para que somente depois façamos o merge.


# 8. Processo de CI

Nessa fase, você deverá repetir o processo ensinado no módulo.

1. Você deverá pegar a sua aplicação Laravel das fases anteriores e adicioná-la em um pipeline de integração contínua utilizando o Google Cloud Build, para isso terá que:

    Gerar a imagem do docker-compose e fazer o push no seu Container Registry do GCP. 
        
    Criar uma trigger para ser disparada todas as vezes que um commit entrar no repositório do Github.
        
    Os steps do Google Cloud Build deverão ser:

    - Executar o docker-compose
    - Executar o composer
    - Copiar o arquivo .env.example para .env
    - Rodar um artisan key:generate
    - Executar as migrações
    - Executar os testes utilizando o PHPUnit

2. Você deverá instalar a App do Google Cloud Build disponível no Market Place do Github. Crie um branch develop em seu repositório. Todas as vezes que uma pull request for criada, imediatamente o Google Cloud Build deverá iniciar o processo de CI.


# 9. Desafio de CI

Se você chegou nessa fase é porque entendeu na prática como funciona um processo de integração contínua! Parabéns =)

Nesse desafio, você será tirado de sua zona de conforto caso você ainda não trabalhe com a linguagem de programação Go.

1. Você deverá criar uma simples aplicação que possua uma função chamada soma que receberá dois parâmetros e retornará a adição desses dois valores.

    Essa função deverá ser chamada na função main do programa. Quando executada, deverá exibir da na tela o resultado da soma de 5 + 5.

2. Crie um teste unitário para essa função.

3. Ative um processo de CI que execute os seguintes passos:

    Executar o teste unitário
    Push da imagem gerada no processo de CI no Container Registry do GCP
    Ative a App do Cloud Build no Github para que cada pull request execute automaticamente o processo de CI

**Dicas:**

    Para fazer o push da imagem, reveja o vídeo onde fazemos a instalação do docker-compose.
    Para executar os comandos Go no Cloud Build, lembre-se que o GCP fornece diversas imagens prontas para isso em seu registro público "cloud-builders"
    No cloudbuild.yaml provavelmente você deverá setar uma variável de ambiente informando o GOPATH em cada etapa. Recomendo que verifique a documentação do Cloud Build na sessão da linguagem Go.
    Crie uma pasta src/[nome-de-seu-projeto-ou-pacote] e coloquei seus arquivos .go nessa pasta.

Bons estudos!

"Amadores sempre estao satisfeitos, mas os profissionais estão sempre preocupados, porque eles sabem que a vida deles dependem do que fazem." Marcel Marceau


Resultado: https://github.com/zanatabr/fullcycle-devops-somago


## Consultas

Golang (GO) e TDD para Iniciantes
https://medium.com/@sheimyrahman/golang-go-e-tdd-para-iniciantes-2418b6eadd92
