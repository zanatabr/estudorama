# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# DevOps -  Docker
---

# 1. Introdução

## Pilares

- Conceitos básicos do Docker
- Na prática
- Docker compose


## Objetivos

- O que são Containers
- Como funcionam os Containers
- Como o Docker funciona
- Principais comandos utilizando Docker
- Dockerfile
- Trabalhando com imagens Docker


# 2. Instalando Docker

Onde obter o script: 
https://get.docker.com/

Script normalmente usado:

```
$ curl -sSL https://get.docker.com/ | sh
```

**Importante:** Adicionar o usuário ao grupo Docker. Para ter efeito, é necessário fazer o logout e novo login.

```
$ sudo usermod -aG docker $USER
```

Feito isso, testar com uma simples verificação de versão

```
$ docker info
```

# 3. Hello World Com Docker

```
$ docker run hello-world
```

```
$ docker ps
# ou
$ docker container ls
```

```
$ docker ps -a
# ou
$ docker container ls -a
```

```
$ docker rm 297d723a832e
# ou
$ docker container rm 297d723a832e
```

```
$ docker images
# ou
$ docker image ls
```

```
$ docker rmi bf756fb1ae65
# ou
$ docker image rm bf756fb1ae65
```

# 4. Gerenciamento Básico De Containers

Buscar pela imagem do NGinx no DockerHub.



Executa a imagem "nginx", mas deixa o terminal travado.
```
$ docker run nginx
```

Opção "dettached".
```
$ docker run -d nginx

```

```
$ docker container ls
```

```
$ docker rm 297d723a832e
# ou
$ docker container rm 297d723a832e
```


# 5. Expondo Portas

O "docker container run" inicia um novo container de uma imagem (modo antigo "docker run"):

```
$ docker container run -d --name my_nginx nginx:alpine
```

```
$ docker container ls

$ docker container stop my_nginx

$ docker container start my_nginx
```


Enquanto isso, no navegador:
http://localhost:80  

\* (Não funciona, porque não foi feito o mapeamento da porta para o host)


Nova tentativa:
```
$ docker container stop my_nginx

$ docker container run -d --name nginx_porta --publish 8080:80 nginx:alpine
```

Enquanto isso, no navegador:
http://localhost:8080

\* OK. Agora funciona.


```
$ docker container stop nginx_porta

$ docker container ls -a

$ docker container prune
```

# 6. Executando Comandos No Container

```
$ docker container run -d --name my_nginx --publish 8080:80 nginx
```

Executa o comando "uname -a" no container "my_nginx" (mas retorna imediatamente):
```
$ docker container exec my_nginx uname -a
```

Executa o comando "bash" no container "my_nginx" (mas retorna imediatamente)
```
$ docker container exec my_nginx bash
```

Executa o comando "sh" no container "my_nginx" (mas se mantém no container, de forma interativa)
```
$ docker container exec -it my_nginx bash

/# apt-get update
/# apt-get install vim

/# vim /usr/share/nginx/html/index.html
```

Modificar alguma coisa no arquivo, salvar e verificar o resultado no navegador.

É claro que a modificação só existirá enquanto o container em questão estiver em execução.


# 7. Iniciando Com Volumes

```
$ docker container run -d --name my_nginx -p 8080:80 -v $(pwd):/usr/share/nginx/html nginx
```

Se testarmos no navegador (http://localhost:8080) veremos que o conteúdo apresentado não é o padrão do NGinx.

Foi mapeado um volume como um "bind".

## Teste a ser feito 

Acessar o shell do container e verificar se o diretório mapeado apresenta o conteúdo externo (do host):
```
$ docker container exec -it my_nginx bash
# cd /usr/share/nginx/html
# ls -la
```

O conteúdo exibido deverá ser o do diretório do host mapeado como volume.


# 8. Continuando Com Volumes

```
$ docker volume create --driver local --opt device={$pwd} --opt o=bind volume_local

$ docker volume ls

$ docker volume inspect volume_local
```


Testar o volume:
```
$ docker run -d --name nginx2 -p 8081:80 -v volume_local:/usr/share/nginx/html nginx
```

# 9. Trabalhando Com Networks

Tipos de rede:

- **Bridge** (ponte - comunicação entre containeres)
- **None** (nenhum acesso a rede externa)
- **Host** (container acessa rede do host)

```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
687fb567509b        bridge              bridge              local
ca40ffd943b0        host                host                local
6d29df51ba33        none                null                local
```

Exemplo:

```
$ docker container run -d --name nginx1 nginx
$ docker container run -d --name nginx2 nginx
```

```
$ docker container exec -it nginx1 bash
/# apt-get install iputils-ping
/# ping nginx2
```
\* Não funciona


```
$ docker network inspect bridge
```

\* Ao fazer a verificação: Dá para ver que os dois contêineres estão nessa rede bridge, e cada um deles tem um IP.

De volta ao container nginx1
```
/# ping 172.17.0.3   (ou o ip que for definido)
```

\* Agora OK.


A rede "padrão" bridge não faz resolução de nomes.

E se quisermos usar o nome?

```
$  docker network create -d bridge my_network
```

```
$ docker container run -d --name nginx3 --net=my_network nginx
$ docker container run -d --name nginx4 --net=my_network nginx
```

```
$ docker container exec -it nginx3 bash
/# apt-get update
/# apt-get install iputils-ping
/# ping nginx4
```

A resolução de nomes funciona nas redes "bridge" criadas.


# 10. Docker Commit

## Como criar uma imagem?
- Através do Dockerfile
- Através de um commit de um container modificado.


Retorna uma lista de IDs dos containeres que estão parados:
```
$ docker container ls -a -q
```

Remove todos os containeres da lista:
```
$ docker container rm $(docker container ls -a -q) -f
```

Fazer uma pequena modificação no container:
```
$ docker container run -d --name nginx -p 8080:80 nginx
```

```
$ docker container exec -it nginx bash
/# apt-get update
/# apt-get install vim
/# vim /usr/share/nginx/html/index.html

```

Fazer alguma modificação na página html e salvar.

Sair do moto interativodo container e retornar para o host.

No host, será gerado um novo snapshot do container que está em execução (uma nova imagem). Pegar o ID do container e usar na instrução a seguir (ex: 123123123):
```
$ docker container commit 123123123 zanatabr/nginx-commit
```

Para testar, criar um novo container usando essa imagem:
```
$ docker container run -d --name nginx2 -p 8082:80 zanatabr/nginx-commit
```

Testar no navegador. Nova imagem criada OK!


# 11. Docker Push


Antes de começar, faça o login (neste caso, no DockerHub):

```
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: zanatabr
Password: 
WARNING! Your password will be stored unencrypted in /home/zanata/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

```
$ docker image push zanatabe/nginx-commit:v2
```

O teste feito foi remover as imagens do cache local e feito um "docker run" da imagem carregada para o GitHub.


# 12. Trabalhando Com Dockerfile

**arquivo:** ~/projeto/Dockerfile
```
FROM php:7.3-cli
```

```
$ docker image build -t test_swoole .
```

```
$ docker image ls
```

\* Perceber que a imagem criada possui o mesmo hash da imagem base, pois não foi feita nenhuma modificação (nenhum layer foi adicionado).


**arquivo:** ~/projeto/Dockerfile
```
FROM php:7.3-cli
RUN pecl install swoole \
    && docker-php-ext-enable swoole
```

```
$ docker image ls
```

\* Perceber que a imagem criada possui o hash diferente do hash da imagem base.

A ideia agora é disponibilizar um novo arquivo index.php para ser servido pelo container, e isso pode ser feito de duas formas:

1. Copiar o arquivo index.php para a imagem no momento do build
2. Subir o container apontando para um volume criado em que o arquivo index.php esteja armazenado

Neste momento será adotada a primeira opção.

O arquivo index será criado baseando-se no conteúdo oferecido na página do Swoole  (https://www.swoole.co.uk/#get-started)

**arquivo:** index.php
```
<?php
$server = new Swoole\HTTP\Server("0.0.0.0", 9501);

$server->on("start", function (Swoole\Http\Server $server) {
    echo "Swoole http server is started at http://127.0.0.1:9501\n";
});

$server->on("request", function (Swoole\Http\Request $request, Swoole\Http\Response $response) {
    $response->header("Content-Type", "text/plain");
    $response->end("Hello World\n");
});

$server->start();
```

**arquivo:** ~/projeto/Dockerfile
```
FROM php:7.3-cli
RUN pecl install swoole \
    && docker-php-ext-enable swoole
COPY index.php /var/www
```

```
$ docker image build -t test_swoole .
```

```
$ docker image ls
```

Agora é preciso "expor" a porta TCP servida pelo container.

**arquivo:** ~/projeto/Dockerfile
```
FROM php:7.3-cli
RUN pecl install swoole \
    && docker-php-ext-enable swoole
COPY index.php /var/www
EXPOSE 9501
```

```
$ docker image build -t test_swoole .
```

```
$ docker container run -d --name swoole -p 9501:9501 test_swoole
```

```
$ docker container ls
```

Perceber que o container não ficou em execução.

```
$ docker container ls -a
```

O container executou e finalizou imediatamente. Isso aconteceu porque o container não possui um **ENTRYPOINT** (comando que o container executa durante a sua existência).

**arquivo:** ~/projeto/Dockerfile
```
FROM php:7.3-cli
RUN pecl install swoole \
    && docker-php-ext-enable swoole
COPY index.php /var/www
EXPOSE 9501
ENTRYPOINT ["php", "/var/www/index.php","start"]
```

```
$ docker container rm swoole
```

```
$ docker image build -t test_swoole .
```

```
$ docker container run -d --name swoole -p 9501:9501 test_swoole
```

Teste feito no navegador:
http://localhost:9501/

\* OK


Pra finalizar: gerar uma imagem com tag e carregar para o DockerHub

```
$ docker image build -t zanatabr/php-swoole:latest .
```

```
$ docker image ls
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
test_swoole           latest              94e582baa059        7 minutes ago       425MB
zanatabr/php-swoole   latest              94e582baa059        7 minutes ago       425MB
nginx                 latest              9beeba249f3e        2 days ago          127MB
php                   7.3-cli             e79a6d8a4a9e        3 days ago          398MB
```

```
$ docker image push zanatabr/php-swoole:latest
```

Observações:
- **swoole** funciona como um **NodeJS**, mas para PHP
- **pecl** é como se fosse o **npm**, mas para PHP


# 13. Instalando Laravel Com Dockerfile

Inicialmente o Laravel será baixado no host.

Ver: https://laravel.com/


## Instalação do "composer"
https://www.digitalocean.com/community/tutorials/como-instalar-e-usar-o-composer-no-ubuntu-18-04-pt

\* Após a instalação, precisa reiniciar o terminal.

## Instalação do laravel

```
$ composer create-project laravel/laravel
```

*Supported tags and respective Dockerfile links*
- https://hub.docker.com/_/php
- https://github.com/docker-library/docs/blob/master/php/README.md#supported-tags-and-respective-dockerfile-links

Precisa dessas dependências:

```
$ sudo apt install curl php-cli php-xml php-mbstring git unzip
```

### 1a Etapa

```
$ cd ~/projeto/laravel
```


**arquivo:** ~/projeto/laravel/Dockerfile
```
FROM php:7.4.6-fpm-alpine3.11
EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```

```
$ docker image build -t zanatabr/laravel .
```

Forçar a interrupção e a remoção dos contêineres:

```
$ docker container rm $(docker container ls -aq) -f
```

```
$ docker container run -d --name laravel -v $(pwd):/var/www -p 9000:9000 zanatabr/laravel
```

No navegador: http://localhost:9501/

\* Nada, não funcionou, porque não está acessando o php-fpm, e o php-fom não está chamando a configuração específica do laravel.


Acessar o container para ver se o volume está funcionando:

```
$ docker container ls

$ docker container exec -it 858835a4a702 apk add bash

$ docker container exec -it 858835a4a702 bash

/# cd /var/www
/# ls
```

\* OK. O compartilhamento do volume está funcionando e o laravel pode ser acessado pelo container.

**Problemas**: Ainda não temos um servidor web, e como estamos em um ambiente de desenvolvimento, podemos executar o "artisan serve" para garantir que temos um servidor web rodando.

```
/# php artisan serve --host=0.0.0.0
```

No navegador: http://localhost:8000/

\* (OK)


Essa é a forma "complicada" de preparar o laravel para o ambiente de desenvolvimento, em que podemos modificar os arquivos no host (máquina local), enquanto o container "serve" esses arquivos.

O ideal, nesse momento, é trabalhar com o docker-compose (a ser visto).


# 14. Fazendo O Build De Uma Imagem Com Laravel


## 1a Etapa

```
$ cd ~/projeto/laravel
```

**arquivo:** ~/projeto/laravel/Dockerfile
```
FROM php:7.4.6-fpm-alpine3.11
WORKDIR /var/www
RUN rm -rf /var/www/html
COPY . /var/www
RUN ln -s public html
EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```


Forçar a interrupção e remoção dos contêineres
```
$ docker container rm $(docker container ls -aq) -f
```

```
$ docker image build -t zanatabr/laravel .
```

```
$ docker container run -d --name laravel -v $(pwd):/var/www -p 9000:9000 zanatabr/laravel
```

.. voltar um diretório pra não fazer confusão

```
$ cd ~/projeto 
# ou 
$ cd ..
```

```
$ docker container run -d --name laravel -p 8000:8000 zanatabr/laravel
```

```
$ docker container exec -it laravel apk add bash
```

```
$ docker container exec -it laravel bash

/# ls

/# php artisan serve --host=0.0.0.0
```

```
$ docker image push zanatabr/laravel
```

# 15. Iniciando Com Docker-Compose


## Docker Compose - Instalação

https://docs.docker.com/compose/install/

Trecho copiado da própria página - verificar antes a última release no GitHub

```
Run this command to download the current stable release of Docker Compose:

$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

$ sudo chmod +x /usr/local/bin/docker-compose
```

## Início

Foi criado um diretório oculto ".docker" na pasta "laravel".

```
mkdir ~/projeto/laravel/.docker
```

Criado o arquivo Dockerfile a seguir:

**arquivo:** ~/projeto/laravel/.docker/nginx/Dockerfile
```
FROM nginx:1.15.0-alpine
RUN rm /etc/nginx/conf.d/default.conf
COPY ./nginx.conf /etc/nginx/conf.d
```

**arquivo:** ~/projeto/laravel/.docker/nginx/conf.d
```
server {
	listen 80;
	index index.php index.html;
	root /var/www/public;

	location ~ \.php$ {
		try_files $uri =404;
		fastcgi_split_path_info ^(.+\.php)(/.+)$;
		fastcgi_pass app:9000;
		fastcgi_index index.php;
		include fastcgi_params;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		fastcgi_param PATH_INFO $fastcgi_path_info;
	}

	location / {
		try_files $uri $uri/ /index.php?$query_string;
		gzip_static on;
	}
}
```

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www
```

```
$ docker-compose up
```

## 1a. Modificação

**arquivo:** ~/projeto/laravel/Dockerfile
```
FROM php:7.4.6-fpm-alpine3.11
RUN apk add bash mysql-client
RUN docker-php-ext-install pdo pdo_mysql

WORKDIR /var/www
RUN rm -rf /var/www/html

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# RUN composer install && \
#         cp .env.example .env && \
#         php artisan key:generate && \
#         php artisan config:cache

# COPY . /var/www
RUN ln -s public html

EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www

    redis:
        image: redis:alpine
        expose:
          - 6379
```

```
$ docker-compose up -d
```

> CTRL+C


## 2a. Modificação:

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        volumes:
          - .:/var/www

    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www

    redis:
        image: redis:alpine
        expose:
          - 6379
```

```
$ docker-compose up -d
```

Ou, se precisar que o build de alguma imagem seja refeita:
```
$ docker-compose up -d --build
```

No navegador: http://localhost:8000/
\* (OK no vídeo) - 

No meu caso apresentou o erro:
The stream or file "/var/www/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denied

Explicação para correção encontrada em:

- https://mazecube.net/laravel-docker/
- https://stackoverflow.com/questions/51422569/laravel-application-is-unable-to-write-to-files-locally-throwing-a-permission-d

Para não perder muito tempo, foi aplicado no host:
```
$ sudo chmod -R 777 ~/projeto/laravel/storage/
```


## 3a. Modificação - Adição de rede

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        volumes:
          - .:/var/www
        networks:
          - app-network

    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www
        networks:
          - app-network

    redis:
        image: redis:alpine
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```


Modificação (apontamento do redis):

**arquivo:** ~/projeto/laravel/.env
```
...
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379
...
```

# 16. Configurando Mysql Com Docker-Compose

Modificação (adição do mysql):

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        volumes:
          - .:/var/www
        networks:
          - app-network

    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www
        networks:
          - app-network

    db:
        image: mysql:5.7
        command: --innodb-use-native-aio=0
        container_name: db
        restart: always
        tty: true
        ports:
          - "3306:3306"
        environment:
          - MYSQL_DATABASE=laravel
          - MYSQL_ROOT_PASSWORD=root
          - MYSQL_USER=root
        networks:
          - app-network

    redis:
        image: redis:alpine
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```


Modificação (apontamento do db):

**arquivo:** ~/projeto/laravel/.env
```
...
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=root
...
```

```
$ docker-compose up -d
```

Verificação:

```
$ docker container exec -it app bash

/# ls
/# php artisan migrate
/# exit
```


> Até aqui, verificar que as migrações foram executadas
> mas... tem uma pegadinha


Nova verificação:
```
$ docker-compose down

$ docker-compose up -d

$ docker container exec -it app bash

/# php artisan migrate
/# exit
```

Perceber que as migrações foram executadas novamente!
O container de "db" foi baixado anteriormente, então o BD também foi destruído.


## Criação de volumes para persistir os dados do MySQL


Modificação (volumes do mysql):

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        volumes:
          - .:/var/www
        networks:
          - app-network

    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www
        networks:
          - app-network

    db:
        image: mysql:5.7
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
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```

Nova verificação (agora com o volume criado):

```
$ docker-compose down

$ docker-compose up -d

$ docker container exec -it app bash

/# php artisan migrate
/# exit
```

Nas primeiras tentativas do "migrate" ocorreu um erro de falha de conexão. Após algum tempo o migrate foi feito. Isso ocorre porque o container de "db" ainda não havia sido carregado (explicação a ser vista posteriormente).

Foram feitos outros testes (baixar e subir a pilha de serviços) e a migração funcionou corretamente.


# 17. Resolução De Problemas Com Docker-Compose

A ideia é fazer com que o serviço "app" fique disponível apenas após a carga do serviço "db" (mysql). 

Uma das formas usadas é a adição da chave **"depends_on"** no serviço para fazer com que ele aguarde a subida de outro serviço antes de ser carregado.

Exemplo:
```
...
    app:
        build: .
        container_name: app
        volumes:
          - .:/var/www
        networks:
          - app-network
        depends_on:
          - db
...
```

Usar isso dá a impressão de que está tudo OK. Na versão "3" do "docker-compose" não funciona assim. Isso faz apenas com que seja modificada a ordem em que as coisas são carregadas, mas não garante que o serviço do qual ele depende esteja "pronto" (e funcionando adequadamente).

Para facilitar e corrigir o problema, a versão do "compose file" será modificada para "2.3", e alguns itens serão adicionados, sendo:

- **condition: service_healthy** (indica que depende de outro serviço, desde que esse esteja "saudável")
- **healthcheck: test** (teste a ser executado para saber se o serviço está pronto para uso)
- **healthcheck: interval** (Se não estiver Ok, aguardar quanto tempo para fazer outro teste)
- **healthcheck: timeout** (aguardar até quanto tempo para obter uma resposta)
- **healthcheck: retries** (testar até quantas vezes)


**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '2.3'

services:
    
    app:
        build: .
        container_name: app
        volumes:
          - .:/var/www
        networks:
          - app-network
        depends_on:
          db:
            condition: service_healthy

    nginx:
        build: .docker/nginx
        container_name: nginx
        restart: always
        tty: true
        ports:
          - "8000:80"
        volumes:
          - .:/var/www
        networks:
          - app-network

    db:
        image: mysql:5.7
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
        healthcheck:
          test: ["CMD-SHELL", "mysql --user=root --database=laravel --password=root --execute='SELECT count(table_name) > 0 FROM information_schema.tables;' --skip-column-names -B"]
          interval: 10s
          timeout: 10s
          retries: 4

    redis:
        image: redis:alpine
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```

Teste realizado:

```
$ docker-compose down
```

Remoção da pasta `~/projeto/laravel/.docker/dbdata`.

```
$ sudo rm -rf ~/projeto/laravel/.docker/dbdata

$ docker-compose up -d

$ docker container exec -it app bash

/# php artisan migrate
/# exit
```

Migração executada sem problemas, pois os serviços foram carregados de forma coerente.


# 18. Publicando imagem Laravel

## [ATUALIZAÇÃO]

Agora que você já aprendeu muito sobre docker, gostaríamos que dividisse esse exercício em duas etapas:

1o. Configurar um ambiente Laravel utilizando o docker-compose com:

1. Nginx
2. PHP-FPM
3. Redis
4. MySQL

Lembrando que o volume do código fonte deve ser compartilhado com a App.

Após realizarmos a clonagem do repositório e executarmos: docker-compose up -d, poderemos ver a aplicação Laravel rodando com o erro de autoload na porta: 8000, uma vez que o docker-compose não executou o composer install do PHP, logo, não se preocupe com tal detalhe nesse momento. 

2o. Após ter tido sucesso na etapa anterior, faça a configuração do framework Laravel seguindo as etapas (dentro do container):

    execute composer install
    crie o arquivo .env baseado no .env.example 
    execute: php artisan key:generate 
    execute: php artisan migrate

\* Nesse momento, quando você acessar a aplicação no browser, finalmente, você deverá ver a página inicial do Laravel funcionando.

Baseado nessas últimas ações, gere o build da imagem desse container e faça a publicação em sua conta no Hub do Docker.

Lembre-se: Ao gerar o build da imagem, TODO o conteúdo incluindo arquivos como vendor, .env, etc deverão ser incluídos.

Adicione o endereço da imagem no seu dockerhub no README.md e faça o commit para um repositório público do Github.

Arquivos e códigos úteis para auxiliar no exercício incluindo nginx.conf e linha de comando para baixar o composer. Clique aqui (https://gist.github.com/wesleywillians/62c3846f789c02729b856606ae0b7feb).


Download-composer  (fornecido)
```
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
```

nginx.conf  (fornecido)
```
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```


**Minha imagem gerada**

https://hub.docker.com/repository/docker/zanatabr/laravel

**Meu repositório no GitHub**
```
git remote add origin https://github.com/zanatabr/fullcycle-devops-laravel.git
git push -u origin master
```


# 19. Dependências entre containers

Explicação rápida sobre:

- o uso da versão 2.3 do docker-compose para gerenciar a dependência entre containers.
- superioridade da versão 3, devido a integração com outros serviços
- problema é que o recurso que possibilitava a "dependência condicional" saiu na versão 3

O que foi feito:

- Mudança para a versão 3
- Dependência de "db" continua existindo, mas foi removida a chave "condition"
- Remoção do "healthcheck" do serviço "db"
- Praticamente, agora as dependências são mencionadas apenas para definir a ordem de criação dos contêineres

**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
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
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```

## dockerize

https://github.com/jwilder/dockerize

**Para quê serve o dockerize?**

Uma das utilidades é cuidar da dependência entre contêineres, verificar se outro serviço está respondendo.


**arquivo:** ~/projeto/laravel/Dockerfile
```
FROM php:7.4.6-fpm-alpine3.11

WORKDIR /var/www

RUN apk add --no-cache openssl bash mysql-client && \
    docker-php-ext-install pdo pdo_mysql && \
    rm -rf /var/www/html && \
    # install laravel
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    # install laravel
    composer create-project laravel/laravel .  && \
    # update dependencies 
    composer install && \
    cp .env.example .env && \
    php artisan key:generate && \
    php artisan config:cache && \
    ln -s public html


ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```

Verificação:

```
$ docker container exec -it app bash
```

Verificar a documentação do dockerize:

```
/# dockerize -wait tcp://db:3306
/# exit
```

Outra verificação:
- comentar o serviço "db" no docker-compose.yaml
- carregar a pilha de serviços novamente
- fazer o teste do dockerize no container do "app"
- problema: da forma como foi feito, são feitos testes por apenas 10s (uma vez por segundo).


Aumentando o timeout para 40s (um teste por segundo)
```
/# dockerize -wait tcp://db:3306 -timeout 40s
/# exit
```


Faremos a substituição do "entrypoint" do serviço "app":

- entrypoint é o comando a ser executado quando o container subir
- tal comando deve ficar em execução, porque é o seu processo quem segura o container no ar. Se o processo cair, o container também cai.
- Vide nova entrada no "entrypoint:" no docker-compose
- neste caso foi usado o "dockerize" como entrypoint, mas este foi usado apenas para garantir que o "db" estará funcionando antes que o "app" seja carregado, para que este consiga fazer as migrações de banco de dados.
- na sequência do "dockerize" é informado o comando a ser executado, que é o real objetivo do container (entrypoint original, que neste caso seria o "php-fpm").
- Uma prática sugerida é a de criar um "script" para servir como "entrypoint"


**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        entrypoint: dockerize -wait tcp://db:3306 -timeout 40s ./.docker/entrypoint.sh
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
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```


**arquivo:** ~/projeto/laravel/.docker/entrypoint.sh
```
#!/bin/bash
php artisan migrate
php-fpm
```

```
$ chmod +x ~/projeto/laravel/.docker/entrypoint.sh
```

```
$ docker-compose down
$ docker-compose up -d
```


# 20. Trabalhando com templates

## Como tratar variáveis de ambiente como se fossem templates?

No arquivo .env da aplicação, encontram-se várias chaves e valores que podem variar de acordo com o ambiente (desenvolvimento, homologação, produção, etc). O "dockerize" pode ajudar, porque pode gerar exatamente esses tipos de templates.



Cópia do arquivo ~/projeto/laravel/.env para modificação

**arquivo:** ~/projeto/laravel/.docker/app/.env
```
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:dwREvnZWZZKAefWk+AmvPjI4yvajRLq1+w+O4bDJWn8=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack

DB_CONNECTION=mysql
DB_HOST={{ .Env.DB_HOST }}
DB_PORT=3306
DB_DATABASE={{ .Env.DB_DATABASE }}
DB_USERNAME={{ .Env.DB_USERNAME }}
DB_PASSWORD={{ .Env.DB_PASSWORD }}

BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=null
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=mt1

MIX_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
MIX_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```


**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        entrypoint: dockerize -template ./.docker/app/.env:.env -wait tcp://db:3306 -timeout 40s ./.docker/entrypoint.sh
        environment:
          - DB_HOST=db
          - DB_DATABASE=laravel
          - DB_USERNAME=root
          - DB_PASSWORD=root
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
        expose:
          - 6379
        networks:
          - app-network

networks:
    app-network:
        driver: bridge
```

Verificação:

```
$ rm -f ~/projeto/laravel/.env
$ docker-compose down
$ docker-compose up -d
```

Verificar a "recriação" do arquivo `~/projeto/laravel/.env` com os valores corretos para o ambiente.


---
Modificação completa do Dockerfile da imagem, para fazer com que a instalação do Laravel seja feita durante a "subida" do container.

O "composer install", que atualiza as dependências do projeto Laravel, não funcionaria corretamente, exceto se a imagem contivesse todos os arquivos do projeto (que foi a experiência que fiz anteriormente), mas não é isso o que se quer. O que se pretende é deixar os arquivos do Laravel "externos ao container".

Isso pode ser feito com a ajuda do "dockerize", que possibilita a execução de um "entrypoint" externo (como foi visto há pouco).



Modificação do entrypoint.sh

**arquivo:** ~/projeto/laravel/.docker/entrypoint.sh
```
#!/bin/bash
composer install
php artisan key:generate
php artisan migrate
php artisan config:cache
php-fpm
```

**arquivo:** ~/projeto/laravel/Dockerfile
```
FROM php:7.4.6-fpm-alpine3.11

RUN apk add --no-cache openssl bash mysql-client
RUN docker-php-ext-install pdo pdo_mysql

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer 

# install dockerize
ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

WORKDIR /var/www
RUN rm -rf /var/www/html
RUN ln -s public html

EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```


Verificação:

```
$ docker-compose down
$ docker-compose up -d
$ docker container logs app
```


# 21. Otimizando Imagens

A ideia aqui é fazer com que a imagem gerada não seja sobrecarregada com coisas que não serão usadas em produção (no nosso caso, o bash, o composer, etc).


Modificações no Dockerfile:

- Apenas como exemplo, será feita a instalação do "nodejs" e do "npm", supondo que seriam necessários para realizar algum procedimento durante a geração da imagem;
- bash, composer, npm, etc... nada disso será necessário na imagem gerada para produção;
- como "fechar" a imagem para produção? Dá pra fazer isso com o "multi-stage build". Na primeira etapa é feito o build da imagem com tudo o que é necessário para gerar os elementos que a imagem precisará em produção, e na segunda etapa uma outra imagem base sobrepõe a original e o que nos interessa é copiado para essa nova imagem, sem levar as "tranqueiras desnecessárias".

**arquivo:** ~/projeto/laravel/Dockerfile.prod
```
FROM php:7.4.6-fpm-alpine3.11 as builder

RUN apk add --no-cache openssl bash mysql-client nodejs npm
RUN docker-php-ext-install pdo pdo_mysql

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer 

# install dockerize
ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

WORKDIR /var/www
RUN rm -rf /var/www/html
RUN ln -s public html

# faz a cópia do que está na máquina host para a imagem em construção
COPY . /var/www

RUN composer install \
    && php artisan key:generate \
    && php artisan cache:clear \
    && chmod -R 775 storage

RUN npm install

# Sobrepõe a imagem anterior
FROM php:7.4.6-fpm-alpine3.11
RUN apk add --no-cache mysql-client
RUN docker-php-ext-install pdo pdo_mysql
WORKDIR /var/www
RUN rm -rf /var/www/html

# Copia o conteúdo gerado na imagem do primeiro estágio
# para a imagem gerada no segundo estágio
COPY --from=builder /var/www .


EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```

Verificação:

```
$ cd ~/projeto/laravel/

$ docker image build -t zanatabr/laravel-optmized -f Dockerfile.prod .

$ docker container run --name laravel -d zanatabr/laravel-optmized

$ docker container exec -it laravel sh
/# ls -la
/# exit
```



# 22. Desafio Docker

Parabéns! Se você chegou até aqui é porque você está de fato compreendendo os principais conceitos do Docker!

Nesse desafio você terá duas tarefas extremamente importantes:

1o. Baseado em nosso projeto exemplo Laravel, utilize o sistema de templates do Dockerize para que ele ajude no processo de deixar o arquivo nginx.conf mais flexível, ou seja, tanto o host e porta da chamada do php-fpm possam ser definidos como variáveis de ambiente no docker-compose.yaml. 

O resultado final é que quando rodemos docker-compose up -d, tanto o host e a porta do nginx possam ser definidas através de variáveis de ambiente no docker-compose.yaml. 

Dica: Esse processo é bem similar ao que vimos no curso com o arquivo .env do Laravel. Colocamos as varáveis de template no arquivo .env para o dockerize e ele fez o processo de substituição. Nesse caso, faça o mesmo processo para o arquivo nginx.conf colocando as variáveis para o host e porta do php-fpm.

2o. Esse desafio é muito empolgante principalmente se você nunca trabalhou com a linguagem Go!
Você terá que publicar uma imagem no docker hub. Quando executarmos:

```
docker run <seu-user>/codeeducation 
```

Temos que ter o seguinte resultado: Code.education Rocks!

Se você perceber, essa imagem apenas realiza um print da mensagem como resultado final, logo, vale a pena dar uma conferida no próprio site da Go Lang para aprender como fazer um "olá mundo".

Lembrando que a Go Lang possui imagens oficiais prontas, vale a pena consultar o Docker Hub.

3o. A imagem de nosso projeto Go precisa ter menos de 2MB =)

Dica: No vídeo de introdução sobre o Docker quando falamos sobre o sistema de arquivos em camadas, apresento uma imagem "raiz", talvez seja uma boa utilizá-la.

Divirta-se


## Solução - Item 1

**arquivo:** ~/projeto/laravel/.docker/nginx/nginx.conf.tmpl
```
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass {{ .Env.APP_HOST }}:{{ .Env.APP_PORT }};
        fastcgi_index index.php;
        include /etc/nginx/fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```

**arquivo:** ~/projeto/laravel/.docker/nginx/Dockerfile
```
FROM nginx:1.15.0-alpine

# install dockerize
ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

RUN rm /etc/nginx/conf.d/default.conf
COPY ./nginx.conf.tmpl /etc/nginx/conf.d/nginx.conf
```


**arquivo:** ~/projeto/laravel/docker-compose.yaml
```
version: '3'

services:
    
    app:
        build: .
        container_name: app
        entrypoint: dockerize -template ./.docker/app/.env.tmpl:.env -wait tcp://db:3306 -timeout 40s -wait-retry-interval 3s ./.docker/app/entrypoint.sh
        environment:
          - DB_HOST=db
          - DB_DATABASE=laravel
          - DB_USERNAME=root
          - DB_PASSWORD=root
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
        entrypoint: dockerize -template /etc/nginx/conf.d/nginx.conf:/etc/nginx/conf.d/nginx.conf -wait tcp://app:9000 -timeout 40s -wait-retry-interval 5s nginx
        command: -g "daemon off;" -c /etc/nginx/nginx.conf
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



## Solução - Itens 2 e 3


**arquivo:** ~/projeto/smallestgo/Dockerfile
```
FROM golang:1.7.0-alpine AS builder

ENV GO111MODULE=on \
    CGO_ENABLED=1

WORKDIR /build

# Copia o código necessário para o build da aplicação
COPY . .

# Build da aplicação
RUN go build hello-code-edu.go

# Cria o diretorio /dist contendo apenas os arquivos necessários para o runtime
# Depois, serão copiados como / (root) da imagem resultante
WORKDIR /dist
RUN cp /build/hello-code-edu ./hello-code-edu

# Cria a imagem mínima para runtime, baseado na imagem raiz
FROM scratch

COPY --chown=0:0 --from=builder /dist /

ENTRYPOINT ["/hello-code-edu"]
```


**arquivo:** ~/projeto/smallestgo/hello-code-edu.go
```
package main

import "fmt"

func main() {
    fmt.Println("Code.education Rocks!")
}
```


# Consultas

vide: https://www.digitalocean.com/community/tutorials/how-to-set-up-laravel-nginx-and-mysql-with-docker-compose-pt

vide: https://x-team.com/blog/docker-compose-php-environment-from-scratch/

vide: https://www.datanovia.com/en/lessons/docker-compose-wait-for-container-using-dockerize-tool/

vide: https://www.datanovia.com/en/courses/docker-compose-wait-for-dependencies/



## GO

vide: https://dev.to/ivan/go-build-a-minimal-docker-image-in-just-three-steps-514i

vide: https://gobyexample.com/hello-world

vide: https://medium.com/rafaeltardivo/desbravando-o-go-setup-e-hello-world-parte-1-89b3c4e4eb8c

vide: https://medium.com/@cashalot/how-to-build-lightweight-docker-container-for-go-app-15e65de6300e

vide: https://medium.com/@chemidy/create-the-smallest-and-secured-golang-docker-image-based-on-scratch-4752223b7324

