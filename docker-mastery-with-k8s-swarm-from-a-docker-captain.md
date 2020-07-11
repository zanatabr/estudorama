
# Docker Mastery: with Kubernetes +Swarm from a Docker Captain

[Link de acesso ao treinamento](https://www.udemy.com/course/docker-mastery)

Course to build, compose, deploy, and manage containers from local development to high-availability in the cloud
> https://www.bretfisher.com/dockermastery.

**Repositório GitHub do curso**
> https://github.com/bretfisher/udemy-docker-mastery

**Chat do Curso - Cadastro Slack**
> http://chat.dockermastery.com/

> https://dockermastery.slack.com/

**Canal no Youtube - Bret Fisher Docker and DevOps**
> https://www.youtube.com/channel/UC0NErq0RhP51iXx64ZmyVfg

*My Terminal and Shell Setup for macOS, where I live all day. Most can easily be replicated in Linux and Windows.*
> https://www.bretfisher.com/shell/

Ver também:

> https://blog.container-solutions.com/the-cloud-native-engineer-role

> https://github.com/cncf/landscape

**CMDER - Para Windows - Portable console emulator**
> https://cmder.net/

**Docker Compose - Instalação**
> https://docs.docker.com/compose/install/

**Docker Machine - Instalação**
> https://docs.docker.com/machine/install-machine/

**Mike G Coleman - Docker Employee**
> https://github.com/mikegcoleman/docker101/blob/master/Docker_eBook_Jan_2017.pdf

**Docker Internals - Cgroups, namespaces, and beyond: what are containers made from?**
> https://www.youtube.com/watch?v=sK5i-N34im8

**Teste Online - Outra opção para fazer**
> http://play-with-docker.com/

**SpaceVim - A community-driven vim distribution**
> https://spacevim.org/

**Announcing Docker Enterprise Edition**
> https://www.docker.com/blog/docker-enterprise-edition/

**BOM - Package Management Basics: apt, yum, dnf, pkg**
> https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg


# 1. Course Introduction and Docker Intro

# 2. Instalação no Linux

Há 3 formas de se fazer a instalação:

- script
- store
- docker-machine

Não usar os pacotes padrão fornecidos por `apt/yum`. Normalmente são pacotes defasados e não oficiais.

## Instalação via Script

É preferível usar o script do próprio Docker para adicionar seus repositórios e instalar todas as dependências: 

**Onde obter o script**
> https://get.docker.com/

**Script normalmente usado:**
```
$ curl -sSL https://get.docker.com/ | sh
```

**Importante:** Adicionar o usuário ao grupo Docker. Para ter efeito, é necessário fazer o logout e novo login.
```
$ sudo usermod -aG docker $USER
```

Feito isso, testar com uma simples verificação de versão:
```
$ docker info
```

## Docker Machine

Executa uma VM Linux pequena no VirtualBox.

**Docker Machine - Instalação**
> https://docs.docker.com/machine/install-machine/

Trecho copiado da própria página

> If you are running Linux:
```
$ base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machine
```

**Releases do Docker machine (Pegar sempre a última)**
> https://github.com/docker/machine/releases


## Docker Compose

**Docker Compose - Instalação**
> https://docs.docker.com/compose/install/

Trecho copiado da própria página - verificar antes a última release no GitHub (abaixo):

> Run this command to download the current stable release of Docker Compose:
```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

$ sudo chmod +x /usr/local/bin/docker-compose
```


**Releases do Docker Compose (Pegar sempre a última)**
> https://github.com/docker/compose/releases

```
$ docker-machine ls
```

Mostra as variáveis de ambiente da máquina virtual padrão (usar para ter os dados de acesso):
```
docker-machine env default
```

Ver também :: Installing Bash Completion (Só para Mac?)

## Recomendações

- Após instalar o docker, instalar também o docker-machine e o docker-compose

- Run more nodes (AWS, VirtualBox, etc - ver: https://docs.docker.com/machine/drivers)
```  
docker-machine create --driver
```

- Instalar o Visual Studio Code e Plugins
https://code.visualstudio.com


# 3. Criação e Uso de Containers

## 3.18. Verificação da Instalação e Configurações

Verifica a versão do Docker e se está funcionando (se o CLI consegue "conversar" com o engine):
```
$ docker version
```

Exibe diversos valores de configuração do engine:
```
$ docker info
```

**Docker command format (novo formato de "comandos gerenciais")**
```
$ docker <command> <sub-command> (options)
```

O formato antigo continua funcionando:
```
$ docker <comand> option
```

## 3.19. Iniciando um servidor NGnix

- Image vs. Container
- run/stop/remove containers
- check container logs and processes
- Registry de imagens default do Docker (Docker Hub - hub.docker.com)
- "`docker container run`" inicia um novo container de uma imagem
- modo antigo "`docker run`"
```
$ docker container run --publish 80:80 ngnix
```

O que essa instrução executa:
1. Faz o download da imagem "ngnix" do Docker Hub
2. Inicia um novo container da imagem
3. Expõe o port 80 no IP do host
4. Roteia o tráfego para o IP do container, no port 80


> **[CTRL+C]** - Envia um sinal de "stop" ao processo quando executado em foreground

Executa em background (detached):
```
$ docker container run --publish 80:80 --detach ngnix
```

Lista os contêineres em execução (apenas os que estão em execução, pois há outros):
```
$ docker container ls
```

\* Modo antigo "`docker ps`"


Interrompe a execução de um container (do processo), mas não remove o container:
```
$ docker container stop <id ou name>
```

\* Modo antigo "`docker stop`"


Lista todos os contêineres (os que estão "em execução" e os que estão "parados"):
```
$ docker container ls -a
```

### run VS. start

- "`docker container run`" sempre inicia um NOVO container
- "`docker container start`" inicia um container existente que está "parado"


### Nomear Container

Quando não informado, o nome do container é gerado de forma aleatória usando uma mistura de nomes e sobrenomes
de hackers e cientistas notáveis.

É possível dar um nome específico a um container.
```
$ docker container run --publish 80:80 --name meu_webserver ngnix
```

### Logs (logs, top)

- Exibe os logs de um container específico
- Usar `--help` para ver todas as opções
```
$ docker container logs <id ou name>
```

\* Modo antigo: "`docker logs`"

Exemplo:
```
$ docker container logs meu_webserver
```

Exibe os processos que estão sendo executados em um container:
```
$ docker container top <id ou name>
```

### Remover/Deletar um ou mais contêineres

Apaga os contêineres que estão "parados":
```
$ docker container rm <id ou name> [<id ou name> <id ou name> ...]
```

\* Modo antigo: "`docker rm`"


Exemplo:
```
$ docker container rm 63f 698 0de
```
\* Perceber que não foi necessário informar o hash completo dos IDs


Força para que todos os contaiineres sejam apagados (inclusive os que estão em execução):
```
$ docker container rm -f <id ou name> [<id ou name> <id ou name> ...]
```

## 3.20. O que acontece ao executar "docker container run"


Tomando como base o exemplo do NGnix

1. Verifica se a imagem está no cache local
2. Verifica no repositório remoto de imagens (default Docker Hub)
3. Faz o download da imagem e armazena no cache local de imagens
4. Cria um novo container baseado na imagem e o prepara para iniciar
5. Fornece ao container um IP virtual na rede privada "dentro" do docker engine.
6. Abre a porta 80 no host e encaminha (forwards) para a porta 80 do container
7. Inicia o container usando o comando CMD definido no Dockerfile da imagem


Exemplo de modificação do comando padrão:
```
$ docker container run --publish 8080:80 --name webhost -d nginx:1.11 ngnix -T
```

- 8080 :: porta TCP exposta pelo host
- 80 :: porta TCP exposta pelo container
- 1.11 :: versão da imagem
- nginx -T :: modifica o CMD executado ao iniciar o container


## 3.21. Containers vs. VMs - É só um processo

- Containers não são Mini-VMs
- São apenas processos
- Limitados aos recursos que podem acessar
- São interrompidos quando o processo termina

Exemplo:
```
$ docker run --name mongo -d mongo
```
ou
```
$ docker run --name mongo -d mongo:latest
```

Exibir quais imagens estão em execução:
```
$ docker ps
```
ou
```
$ docker container ls
```


Exibir quais processos estão sendo executados no container:
```
$ docker top mongo
```
ou
```
$ docker container top mongo
```

Mostrar todos os processos em execução:
```
$ ps aux
```

Perceber que o processo que está sendo executado no container
é exibido na lista de processos do host (ver: mongod).

Quando o container é interrompido, o processo também deixa 
de existir no host.

## 3.23. Gerenciando Múltiplos contêineres

Usar sempre:
>https://docs.docker.com e --help 

### Executar/Exercício

* Execução de 3 contêineres: nginx, mysql e httpd (apache server)
* Executar todos no modo `--detach` (ou `-d`), com nomes específicos (`--name`)
* Portas TCP a serem usadas:
  * nginx :: 80:80
  * httpd :: 8080:80
  * mysql :: 3306:3306
* Quando executar o "`mysqld`", usar a opção `--env` (ou `-e`) para passar a variavel/valor `MYSQL_RANDOM_ROOT_PASSWORD=yes`
* Usar o "`docker container logs`" no mysql para encontrar a senha aleatória criada no início do serviço
* limpar tudo usando "`docker container stop`" e "`docker container rm`"
* usar o "`docker container ls`" para garantir que tudo está em ordem (antes e depois da limpeza).


### Execução do exercício

Opção `-p` (`--publish`) - Sempre no formato (**HOST:CONTAINER**):
```
$ docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
```
```
$ docker container logs db
```
\* Procurar o texto `GENERATED ROOT PASSWORD` no log exibido.

```
$ docker container run -d --name webserver -p 8080:80 httpd
```

```
$ docker ps
# ou
$ docker container ls
```

```
$ docker container run -d --name proxy -p 80:80 nginx
```

```
$ docker ps
# ou
$ docker container ls
```

```
$ curl localhost

$ curl localhost:8080

$ docker container stop 1e123 b995a d2845

$ docker ps -a

$ docker container ls -a

$ docker container rm 1e123 b995a d2845

$ docker container ls -a

$ docker images ls
```

## 3.25. CLI Monitoring and Inspection


Exibe a lista de processos em um container:
```
$ docker container top <id>
```

Detalhes de configuração de um container:
```
$ docker container inspect <id>
```

Mostra a "estatística de uso" (stats) dos contêineres (dados reais sobre a performance do container):
```
$ docker container stats [<id>]
```

\* Modo antigo: "`docker stats`"


Apresenta metadados sobre o container (startup config, volumes, networking, etc):
```
$ docker container inspect <id>
```

\* Modo antigo: "`docker inspect`"


## 3.26. Usando um Shell dentro de um Container - Não necessita de SSH


Inicia um novo container de forma interativa:
```
$ docker container run -it
```

Executa comandos adicionais em um container existente:
```
$ docker container exec -it
```

- Opção `-t` (pseuso-tty): simula um terminal real, como é feito pelo SSH
- Opção `-i` (interactive): Mantém a sessão aberta para receber entrada do terminal


"Cria" um container e executa o "bash". Terminada a execução do "bash", o container "é interrompido":
```
$ docker container run -it --name proxy nginx bash
/# ls -la
/# exit
```

```
$ docker container run -it --name ubuntu ubuntu
/# apt-get update   # no próprio container
/# apt-get install -y curl
/# curl google.com   # apenas como teste
/# exit   # saiu do container
```

O container que acabamos de usar aparece agora como "parado":
```
$ docker container ls -a
```

Podemos "iniciar novamente" o container que acabamos de usar:
```
$ docker container start -ai ubuntu
```

Podemos executar um "comando adicional" em um container que já está em execução:
```
$ docker container exec -it mysql bash
```

Obs.: O comando "`ps`" não vem na imagem "mysql". Pode-se instalar o "`ps`" acessando o shell do container e executar:
"`apt-get update && apt-get install -y procps`".


## Alpine Linux

Distribuição Linux pequena (aprox. 5MB de tamanho), focada em segurança.

```
$ docker pull alpine

$ docker image ls
```

Perceber a diferença de tamanho da imagem Alpine em relação às demais.


Gera um erro, porque a imagem Alpine não disponibiliza o "bash"
```
$ docker container run -it alpine bash
```

Agora OK.
```
$ docker container run -it alpine sh
```


# 3.27 Docker Networks 

## Private and Public communications for Containers


### Revisão

A opção `-p` (`--publish`) em "`docker container run`" expõe uma porta TCP do container para a máquina host. Sempre no formato (`HOST:CONTAINER`):

```
$ docker container run -p 80:80 --name webhost -d nginx 
```

Para testes e desenvolvimento locais, a parte de rede normalmente funciona.

Verifica qual porta TCP o container expõe:
```
$ docker container port <container>
```
```
$ docker container port webhost
```

Verificar qual é o IP do container:

A opção `--format` formata a saída dos comandos usando "Templates Go":

```
$ docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost
```

- Cada container conecta a uma rede "bridge" virtual privada 
- Cada VN roteia através de um firewall NAT no IP do host
- Todos os container na VN podem conversar com qualquer outro que não tenha a opção "`-p`"
- Boas práticas :: criar uma VN para cada app
  - VN "my_web_app" para o mysql e php/apache containers
  - VN "my_api" para o mongo e nodejs containers


## "Baterias inclusas, mas são removíveis"

O padrão funciona bem em muitos casos, mas facilita a troca de peças para personalizar.

- Criar novas VNs
- Plugar containers a mais de uma VN (ou nenhuma)
- Desconsiderar a VN e usar o IP do host (--net=host)
- Uso de diferentes driver de rede Docker para ganhar novas habilidades


### Fluxo de tráfego e Firewalls (27)
Como as redes Docker movem pacotes para dentro e para fora.

```
[container -p 80:80]   <--> [VN bridge/docker0]  <-->   [if net 80]
  [httpd -p 8080:80]   <-->   [VN net_my_app]    <-->   [if net 8080]
```

# 3.28. FIXME: Change In Official Nginx Image Removes Ping

Hey just a quick note before doing the next few lectures. A recent June 2017 change in the official nginx image https://hub.docker.com/_/nginx (nginx  or nginx:latest ) removes ping, but I use it in the next few videos to test connectivity, so you might get an error about "ping not found". I'm working on updates to those videos but until I can get them processed and uploaded, just do this:

Anywhere I do a docker container run <stuff> nginx , where nginx  is the image you should use, replace that with nginx:alpine , which still has ping command in it.

There are other ways to solve this, including adding the ping util with apt-get, making your own image, etc. More info in this Q&A answer. 


# 3.29. Docker Networks :: CLI Management of Virtual Networks

VER: Network Documentation

- https://docs.docker.com/network/
- https://docs.docker.com/network/network-tutorial-standalone/
- https://docs.docker.com/network/network-tutorial-host/
- https://docs.docker.com/network/network-tutorial-overlay/
- https://docs.docker.com/network/network-tutorial-macvlan/


## Mostrar as redes
```
$ docker network ls
```

## Inspecionar uma rede
```
$ docker network inspect <rede>
```

Exemplo:
```
$ docker network inspect docker0
```

- `--network bridge`
  - VN default do Docker, que é NAT"ada" por trás do IP do host
- `--network host`
  - Ganha desempenho ao fazer um "bypass" na VN, mas sacrifica o modelo de segurança do container
- `--network none`
  - Remove a eth0 e permite apenas o uso da interface localhost no container
- As subnets criadas iniciam normalmente em 172.17.x.x e pra cima


## Criar uma rede

```
$ docker network create --driver
```

Exemplo: Cria uma rede do tipo bridge

```
$ docker network create minha_rede_app
```

Exemplo: Especificar uma rede para um container
```
$ docker container run -d --name new_nginx --network minha_rede_app nginx
```

## "Plugar" uma rede a um container

Cria um NIC dinamicamente em um container em uma VN existente
```
$ docker network connect <network> <container>
```


## "Desplugar" uma rede de um container
```
$ docker network disconnect <network> <container>
```


## Docker Networks :: Default Security

Se as aplicações forem executadas em um único servidor:

- Crie suas apps fazendo com que o frontend/backend fiquem na mesma rede Docker
- A intercomunicação entre eles jamais sai do host
- Todas as portas expostas externamente são fechadas por padrão
- Você deve expor manualmente via opção "-p", que é a melhor segurança padrão.
- Será visto posteriormente com  Swarm e Overlay networks


# 3.30. Docker Networks :: DNS :: Como os contêineres encontra uns aos outros

- Entender como o DNS é a chave para facilitar a comunicação entre contêineres
- Como funciona por default com redes customizadas
- Como usar o "`--link`" para habilitar o DNS na rede "bridge" padrão

##  Esqueça os endereços IP!!!!

Usar endereços IP estáticos e endereços IP para se comunicar com contêineres é um "anti-pattern". 

Faça o seu melhor para evitar isso.


\* O daemon do Docker tem um servidor DNS embutido que os contêineres usam por padrão. Os nomes dos contêineres são usados como "hostnames".

- DNS: Why It’s Important and How It Works
  - https://dyn.com/blog/dns-why-its-important-how-it-works/
- A fun and colorful explanation of how DNS works.
  - https://howdns.works/


Cria um novo container e o "pluga" na rede "my_app_net":

```
$ docker container run -d --name my_nginx --network my_app_net nginx
```

Teste bacana para verificar rede:

```
$ docker container exec -it my_nginx ping new_nginx
$ docker container exec -it new_nginx ping my_nginx
```

\* A rede "`bridge`" padrão tem uma desvantagem, pois não tem um DNS server. Isso pode ser resolvido usando a opção "`--link`" na criação do container, que possibilita fazer a ligação do container com a "`bridge network`", mas é ainda mais vantajoso "criar uma rede" para as aplicações, para não ter que fazer isso sempre. 

Isso é resolvido/facilitado com o uso do *Docker Compose*, porque ele cria uma nova VN sempre que uma pilha de serviços for carregada.




# 3.31. Exercício - CLI App Testing

Pré-requisitos

- Saber como usar a opção "`-it`" para obter o shell de um container
- Entender o básico de distribuições Linux como Ubuntu e CentOS
- Saber como "rodar" (run) um container

Como será realizado:

- Uso de contêineres de diferentes distros Linux para verificar a versão do "`curl`"
- Uso de dois terminais diferentes para iniciar o "`bash`" no "`centos:7`" e "`ubuntu:14.04`", usando a opção "`-it`"
- Aprender a opção "`docker container --rm`" para executar um cleanup seguro.
- Garantir que o "`curl`" foi instalado na última versão para a distro Linux.
  - Ubuntu: `apt-get update && apt-get install curl`
  - Centos: `yum update curl`
- Verificar a versão (em cada container): `curl --version`


## TERMINAL 01

```
$ docker container run --rm -it centos:7 bash
/# yum update curl
/# curl --version
```


## TERMINAL 02

```
$ docker container run --rm -it ububtu:14.04 bash
/# apt-get update && apt-get install -y curl
/# curl --version
```


# 3.33. FIXME: Bug in alpine affects nslookup

In the next assignment, you'll be using the tool nslookup inside the alpine:latest image, but in early 2020 there was a bug introduced to the latest Alpine image 3.11.3 that affects how nslookup works on hostnames, so for the next Assignment on DNS Round Robin, either change your command to work around it with nslookup search. (with a dot added) or use an older Alpine image like alpine:3.10. 


Hopefully, they'll fix it soon. I'm tracking this bug: https://github.com/gliderlabs/docker-alpine/issues/539


# 3.34. Exercício - DNS Round Robin Test

- Round-robin DNS
  - https://en.wikipedia.org/wiki/Round-robin_DNS

Explicação básica: Dois hosts com DNS names diferentes, mas que respondem ao mesmo DNS name (Exemplo: google.com - há milhares de servidores por trás desse nome)

Pré-requisitos:

- Saber como usar a opção "`-it`" para obter o shell de um container
- Entender o básico de distribuições Linux como Ubuntu e CentOS
- Saber como "rodar" (run) um container

Como será realizado:

- Desde o Docker Engine 1.11, podemos ter vários contêineres em uma rede criada, respondendo ao mesmo endereço DNS
- Criar uma nova VN (`default bridge driver`)
- Criar dois contêineres da imagem "`elasticsearch:2`"
- Pesquisar e usar a opção "`-network-alias search`" quando criar os contêineres, para fornecer a eles um DNS Name adicional que utilizarão para responder.
- Executar "`alpine nslookup search`" com a opção "`--net`" para verificar que na listagem surgirão dois contêineres com o mesmo DNS Name.
- Executar várias vezes a instrução "`centos curl -s search:9200`" com a opção "`--net`", até que ambos os nomes sejam apresentados

\* As opções "`--net-alias`" e "`--network-alias`" funcionam


## Terminal 01

Cria uma nova VN:

```
$ docker network create dude
```


1a instância:

```
$ docker container run -d --net dude --net-alias search elasticsearch:2
```

2a instância:

```
$ docker container run -d --net dude --net-alias search elasticsearch:2
```

Verificação:

```
$ docker container ls
```

\* Perceber que serão apresentados dois hosts com o mesmo DNS Name

```
$ docker container run --rm --net dude alpine:3.10 nslookup search
```


 Executar algumas vezes e verificar o resultado.  Em algum momento o nome dos contêineres começarão a se repetir.

```
$ docker container run --rm --net dude centos curl -s search:9200
```

```
$ docker container ls
```

```
$ docker container rm -f <container1> <container2>
```


# Seção 4: Container Images, Where To Find Them and How To Build Them

# 4.35. Docker Image (O que é e o que não é)

O que é uma imagem?

- Binários de apps e dependências
- Metadados sobre os dados da imagem e como executar a imagem
- Não é um Sistema Operacional completo. Não tem kernel, módulos do kernel (Exemplo: drivers). Somente os binários que a aplicação precisa, porque o HOST fornece o Kernel.


- Oficial: Docker Image Specification v1.0.0
  - https://github.com/moby/moby/blob/master/image/spec/v1.md
- Lista das Imagens Docker oficiais
  - https://github.com/docker-library/official-images/tree/master/library


# 4.36 Docker Hub Registry Images

- Básico sobre o Docker Hub (hub.docker.com)
- Buscar imagens públicas oficiais e outras boas imagens
- Como baixar imagens e o básico sobre as "image tags"

(*) Dar uma olhada no repositório do Brett Fisher
- https://hub.docker.com

(*) O interessante das imagens oficiais é a oferta de documentação mais completa sobre a imagem.

```
$ docker image ls
```

Faz o download da imagem nginx
```
$ docker pull nginx
```

```
$ docker pull nginx:1.11.9 
```

```
$ docker pull nginx:1.11.9-alpine
```


# 4.37. Images & their layers - Image Cache

- Imagens são produzidas a partir de modificações no file system e metadados
- Cada camada é identificada unicamente e é armazenada somente uma vez no host
- Isso economiza espaço de armazenagem no host e tempo de transferência no push/pull.
- Um container é somente a leitura/escrita de camada do topo da imagem


Vide: *Images & their layers*
  - https://docs.docker.com/storage/storagedriver/

```
$ docker image ls
```


Mostra as camadas (layers) de mudanças feitas em uma imagem (modo antigo: `docker history`):

```
$ docker image history <image>
```


Exemplos:

```
$ docker image history nginx:latest
$ docker image history mysql
```

Exibe o JSON com os metadados da imagem (modo antigo: `docker inspect`):

```
$ docker image inspect <image>
```


# 4.48. Image Tagging & Push - Enviar para o Docker Hub

## Image ID vs. Tag

Tecnicamente, uma imagem não possui nome, apenas um ID.

O que ajuda na referência a uma imagem é um marcador (tag) composto por três itens: 

```
<user>/<repo>:<tag>
```

Se não for especificada, a tag padrão é "`latest`".


## Repositórios Oficiais

Estão no "**root namespace**" do registry, então não precisam de um "account name" na frente do "repo name"



Atribui uma ou mais tags a uma imagem (modo antigo: `docker tag`)

```
$ docker image tag <source_image[:tag]> <target_image[:tag]> 
```

```
$ docker pull mysql/mysql-server
```


Uma nova tag foi criada para uma imagem existente no cache. Ao listar as imagens que estão no cache local (`docker image ls`), perceber que uma "nova" imagem surge na listagem, mas trata-se da mesma imagem (mesmo ID).

```
$ docker image tag nginx bretfisher/nginx
```


Carrega as camadas modificadas de uma imagem para o Image Registry (por padrão Docker Hub):

```
$ docker image push <image>
```

\* É necessário fazer o login no registry


Faz o login no Docker Hub por padrão, mas você pode informar a URL de outro servidor:

```
$ docker login [<server>]
```

Ao fazer o login, as informações de autenticação são armazenadas no arquivo: "`~/.docker/config.json`"  (No Mac isso é armazenado no Keychain).

\* Fazer sempre o logout em máquinas compartilhadas quando terminar o trabalho, para proteger a sua conta

```
$ docker logout
```

# 4.39. Dockerfile - Básico de Construção de Imagens

- Dockerfile Reference
  - https://docs.docker.com/engine/reference/builder/

**Dockerfile** é a "receita" usada para criar uma imagem.

(*) **Package Manager** - Gerenciadores de pacotes como o "`apt`" e "`yum`" são uma das razões para criar contêineres baseados (from) em Debian, Ubuntu, Fedora ou CentOS

(*) **Variáveis de ambiente** - Uma razão pela qual preferem injetar chave/valor (key/value) é que isso funciona em qualquer lugar, em qualquer S.O. e configuração.

(*) ver exemplo do diretório "`dockerfile-sample-1`"

```
$ cat Dockerfile 
# NOTE: this example is taken from the default Dockerfile for the official nginx Docker Hub Repo
# https://hub.docker.com/_/nginx/
# NOTE: This file is slightly different than the video, because nginx versions have been updated 
#       to match the latest standards from docker hub... but it's doing the same thing as the video
#       describes
FROM debian:stretch-slim
# all images must have a FROM
# usually from a minimal Linux distribution like debian or (even better) alpine
# if you truly want to start with an empty container, use FROM scratch

ENV NGINX_VERSION 1.13.6-1~stretch
ENV NJS_VERSION   1.13.6.0.1.14-1~stretch
# optional environment variable that's used in later lines and set as envvar when container is running

RUN apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y gnupg1 \
	&& \
	NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; \
	found=''; \
	for server in \
		ha.pool.sks-keyservers.net \
		hkp://keyserver.ubuntu.com:80 \
		hkp://p80.pool.sks-keyservers.net:80 \
		pgp.mit.edu \
	; do \
		echo "Fetching GPG key $NGINX_GPGKEY from $server"; \
		apt-key adv --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY" && found=yes && break; \
	done; \
	test -z "$found" && echo >&2 "error: failed to fetch GPG key $NGINX_GPGKEY" && exit 1; \
	apt-get remove --purge -y gnupg1 && apt-get -y --purge autoremove && rm -rf /var/lib/apt/lists/* \
	&& echo "deb http://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list \
	&& apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y \
						nginx=${NGINX_VERSION} \
						nginx-module-xslt=${NGINX_VERSION} \
						nginx-module-geoip=${NGINX_VERSION} \
						nginx-module-image-filter=${NGINX_VERSION} \
						nginx-module-njs=${NJS_VERSION} \
						gettext-base \
	&& rm -rf /var/lib/apt/lists/*
# optional commands to run at shell inside container at build time
# this one adds package repo for nginx from nginx.org and installs it

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log
# forward request and error logs to docker log collector

EXPOSE 80 443
# expose these ports on the docker virtual network
# you still need to use -p or -P to open/forward these ports on host

CMD ["nginx", "-g", "daemon off;"]
# required: run this command when container is launched
# only one CMD allowed, so if there are multiple, last one wins
```


# 4.40. Docker Build - Criação de imagens

Busca o arquivo Dockerfile no diretório e cria a imagem:

```
$ docker image build -t customnginx .
```

(*) Caso seja necessário modificar um Dockerfile, evitar ao máximo modificar as linhas que estão na parte superior do arquivo. Cada modificação (linha modificada) gera um novo layer na imagem.

(*) No build da imagen, usar a opção "`-f`" ou "`--file`" para especificar um arquivo cujo nome seja diferente de `Dockerfile`.


# 4.41. Docker Build - Extensão de imagens oficiais

Busca o arquivo Dockerfile no diretório e cria a imagem:

```
$ docker image build -t customnginx .
```

(*) ver exemplo do diretório "dockerfile-sample-2"

```
$ cat Dockerfile 
# this shows how we can extend/change an existing official image from Docker Hub

FROM nginx:latest
# highly recommend you always pin versions for anything beyond dev/learn

WORKDIR /usr/share/nginx/html
# change working directory to root of nginx webhost
# using WORKDIR is preferred to using 'RUN cd /some/path'

COPY index.html index.html

# I don't have to specify EXPOSE or CMD because they're in my FROM
```

```
$ cat index.html 
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">

  <title>Your 2nd Dockerfile worked!</title>

</head>

<body>
  <h1>You just successfully ran a container with a custom file copied into the image at build time!</h1>
</body>
</html>
```

```
$ docker image build -t nginx-with-html .
```

```
$ docker container run -p 80:80 --rm nginx-with-html
```

```
$ docker image tag nginx-with-html:latest bretfisher/nginx-with-html:latest
```



# 4.42. Exercício - Build your own image


Obsservações e como deve ser feito:

- `Dockerfile`: parte process workflow", parte "arte"
- "Dockerizar" uma aplicação Node.js existente
- Montar o Dockerfile; Fazer o buid; Testar; Carregar (registry); Remover; Executar.
- Espera-se que seja feito iterativamente. Raramente se faz corretamente na primeira vez.
- Instruções detalhadas em "`dockerfile-assignment-1/Dockerfile`"
- Usar a versão Alpine da imagem oficial do '`node`' 6.x
- Resultado esperado: website disponível em `http://localhost`
- Atribuir uma TAG e carregar a imagem no Docker Hub (conta free)
- Remover a imagem do cache local e executá-la novamente a partir do Docker Hub

Execução:

```
$ cd dockerfile-assignment-1/

$ vi Dockerfile
```

```
FROM node:6-alpine
EXPOSE 3000
RUN apk add --update tini
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY package.json package.json
RUN npm install && npm cache clean
COPY . .
CMD ["tini", "--", "node", "./bin/www" ]
```


1o teste:

```
$ docker build -t testnode .
```

```
$ docker container run --rm -p 80:3000 testnode 
```

\* Abrir no navegador.


```
$ docker tag testnode bretfisher/testing-node
```

```
$ docker push bretfisher/testing-node
```

```
$ docker image rm bretfisher/testing-node
```

```
$ docker container run --rm -p 80:3000 bretfisher/testing-node
```

# 4.44. Using Prune to Keep Your Docker System Clean

> PRUNE (podar, suprimir, aparar, desbastar)

- Uso do Prune para manter o sistema "limpo"
  - https://youtu.be/_4QzP7uwtvI

Remove todos os contêineres não usados:

```
$ docker container prune
```

Remove todas as imagens não utilizadas:

```
$ docker image prune -a
```

Exibe o espaço usado:

```
$ docker system df
```

(*) Se estiver usando o Docker Toolbox

A VM Linux não "compacta" automaticamente. Deve-se deletar e recriar a VM (tenha a certeza de ter feito o backup do que estiver nos contêineres e volumes).


Para recriar a VM padrão do toolbox:

```
$ docker-machine rm default
$ docker-machine create
```


\* **ANTES** era feito algo como isso:


Criação de "aliases"

```
$ cat alias.sh
drma='docker rm $(docker ps -a -q)'
drmai='docker rmi $(docker images -q)'
```


(*) ver

```
$ docker system
```


Apresenta informações sobre o uso de disco:

```
$ docker system df
```


Remove "tudo" o que não estiver em execução (**CUIDADO!!!**):

```
$ docker system prune
```


# Seção 5: Container Lifetime & Persistent Data: Volumes, Volumes, Volumes

# 5.45. Container Lifetime & Persistent Data

Links:

- An introduction to immutable infrastructure
  - https://www.oreilly.com/radar/an-introduction-to-immutable-infrastructure/
- The twelve-factor app :: A methodology for building software-as-a-service apps
  - https://12factor.net/
- 12 Fractured Apps
  - https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c#.cjvkgw4b3
- Docker Storage Introduction
  - https://docs.docker.com/storage/
  - https://docs.docker.com/storage/

Resumo:

- Pesistência de dados - Volumes de Dados
- Contêineres "normalmente" são "imutáveis" e "efêmeros" (transitórios, temporário, passageiro)
- Bind Mounts e seus problemas
- O cenário ideal seria o de uma "Infraestrutura imutável", em que faríamos apenas o re-deploy de contêineres, sem nenhuma modificação, mas e os BDs ou dados únicos produzidos pelas aplicações?
- Docker oferece características que asseguram essa "separação de responsabilidades"
- É possível apagar, desligar, modificar a versão, reiniciar um container e manter os dados (dados persistentes). O container pode ser volátil, mas os dados não.
- Duas soluções para esse problema: **Volumes** e **Bind Mounts**
  - **Volumes**: Opção de configuração para um container que cria um local especial fora do container (UFS) para armazenar dados, preservando os dados e permitindo que se "plugue" o container que quiser a esse local (volume de dados)
  - **Bind Mounts**: liga um caminho do container a um caminho do host (como se fosse um compartilhamento de rede, mas de algum recurso do host)


# 5.46. Persistent Data - Data Volumes

- Comando `VOLUME` no `Dockerfile`
- Dar uma olhada do `Dockerfile` da imagem oficial do "`mysql`" (Docker Hub)
  - https://github.com/docker-library/mysql/blob/d284e15821ac64b6eda1b146775bf4b6f4844077/8.0/Dockerfile


Exemplo:
 
``` 
VOLUME /var/lib/mysql
```

Indica que tudo o que for manipulado pelo container no diretório "`/var/lib/mysql`" do próprio container, na verdade será armazenado em um volume externo (diretório criado na máquina host).


Exemplo:

Baixar a imagem oficial do mysql

```
$ docker pull mysql
```

Solicitar a inspeção da imagem baixada para ver os metadados, pois nem sempre se tem o Dockerfile da imagem disponível, mas as informações dele passam a fazer parte dos metadados da imagem.

```
$ docker image inspect mysql
```


Na inspeção podemos encontrar as informações dos volumes.

Carregar um container mysql:

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql
```


Verifica se o container está em execução:

```
$ docker container ls
```

```
$ docker container inspect mysql
```

Com os metadados da inspeção sendo exibidos, podemos buscar as configurações de pontos de montagem ("`Mounts`"), que indicam em qual diretório do host estão sendo gravados/lidos os dados mapeados pelo "volume" do container.

Exibe os dados dos volumes criados:

```
$ docker volume ls
```


Com o Id do Volume em mãos, verificar os dados:

```
$ docker volume inspect <volumeId>
```

O "`Mountpoint`" indica o local no filesystem do host em que os dados do volume são gravados.


## Demonstração de um problema

Essa forma de "gerenciar" os voolumes não é muito amigável. Da perspectiva do container, não é simples saber quais volumes são usados, e da perspectiva dos volumes não é simples saber a quais contêineres estão conectados. 

Segue um exemplo de problema:

Criação de 2 contêineres mysql

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql

$ docker container run -d --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql
```


Listar os volumes (perceber que ao menos dois volumes serão exibidos, e não é simples perceber qual deles pertence a qual container)

```
$ docker volume ls
```


Se interrompermos os contêineres, os volumes continuam lá.

```
$ docker container stop mysql

$ docker container stop mysql2
```

Não exibe nenhum container em execução:

```
$ docker container ls
```


Exibe todos os contêineres (inclusive os parados):

```
$ docker container ls -a
```

Os volumes criados anteriormente continuam lá:

```
$ docker volume ls
```

Se removermos os dois contêineres...

```
$ docker container rm mysql mysql2
```

... os volumes criados anteriormente **CONTINUAM** lá

```
$ docker volume ls
```


Os dados estão lá, os dados estão seguros. Os dados sobrevivem após o encerramento do executável, mas não é nada amigável gerenciar isso.

(*) **Named Volumes** - Forma amigável de atribuir volumes a contêineres

Este criaria um volume "não nomeado". Não é o que queremos:

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v /var/lib/mysql mysql
```

Aqui criaremos um "volume nomeado". Aí Sim!!!

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
```


Perceber a criação de um "volume nomeado"

```
$ docker volume ls
```


Forçando a remoção do container criado

```
$ docker container rm -f mysql
```


Se criarmos outro container, apontando para o mesmo volume... Tudo bem!!!

```
$ docker container run -d --name mysql3 -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
```

O volume "mysql-db" continua lá

```
$ docker volume ls
```

As configurações para "Volumes" e "Mounts" agora são apresentadas de uma forma mais amigável

```
$ docker container inspect mysql3
```

## Para que serve o "**docker volume create**"?

É requerido antes de usar o "`docker run`" para usar drivers específicos e labels.


# 5.48. Persistent Data - Bind Mounts

O **Bind Mount** é o tipo de persistência de dados usado quando se deseja mapear arquivos/diretórios que estão no host para um diretório do container.

- Mapeia um arquivo ou diretório do **HOST** para um arquivo ou diretório do **CONTAINER**
- Basicamente: dois ponteiros que apontam para o mesmo arquivo/diretório
- Não pode ser usado no `Dockerfile`. Somente no "`container run`"
  - ... run -v /Users/bret/stuff:/path/container (mac/linux)
  - ... run -v //c/Users/bret/stuff:/path/container (windows)

(*) Interessante para ambiente de desenvolvimento local ou testes locais

(*) ver exemplo do diretório "dockerfile-sample-2" para comparação


Ao executar a instrução a seguir, se testarmos no navegador (http://localhost) veremos que o conteúdo apresentado não é o padrão do NGinx:

```
$ docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx
```


Se testarmos no navegador (http://localhost:8080) veremos que o conteúdo apresentado **É** o padrão do NGinx:

```
$ docker container run -d --name nginx2 -p 8080:80 nginx
```


**Teste a ser feito**: Acessar o shell do container e verificar se o diretório mapeado apresenta o conteúdo externo (do host)

```
$ docker container exec -it nginx bash
/# cd /usr/share/nginx/html
/# ls -la
```

O conteúdo exibido deverá ser o do diretório do host mapeado como volume.



# 5.49. Exercício - Named Values

Cenário real: Upgrade de DB server em containers.

Em uma máquina comum, seriam aplicados os patches e demais itens, mas em um container que deveria ser imutável?

- Crie um container "`postgres`", na versão "`9.6.1`", usando um volume nomeado "`psql-data`"
- Use a documentação oficial da imagem no Docker Hub para aprender sobre o caminho mapeado em `VOLUME` e as versões necessárias para executá-la
- Verifique os logs, interrompa a execução do container
- Crie um novo container "`postgres`", na versão "`9.6.2`", usando o mesmo volume nomeado
- Verifique os logs para validar
- (*) Isso somente funciona com versões "`patch`", a maioria dos DB's SQL requerem a execução de comandos manuais para atualizar o DB para versões major/minor. Nota: Isso é uma limitação do DB, não do container).


Ver documentação oficial no Docker Hub primeiro

> VOLUME /var/lib/postgresql/data

```
$ docker container run -d --name psql -v psql-data:/var/lib/postgresql/data postgres:9.6.1 
```

Verificar se está tudo ok no log

```
$ docker container logs -f psql
```

Parar a execução do container

```
$ docker container stop psql
```


Executar um novo container, na versão 9.6.2

```
$ docker container run -d --name psqls -v psql-data:/var/lib/postgresql/data postgres:9.6.2 
```


Verificar se está tudo ok no log

```
$ docker container logs -f psql2
```
\* TUDO OK!!!


# 5.51. Exercício - Bind Mounts

- Será usado um **Jekyll** (Static Site Generator) para iniciar um webserver local
- Não é uma opção a ser usada no desenvolvimento web : é um exemplo de "ponte" entre arquivos do host com apps sendo executadas em contêineres
- (*) ver exemplo do diretório "bindmoubt-sample-1"
- O container detecta as modificações nos arquivos do host e atualiza o webserver
- iniciar o container com: 
  - `docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve`
- Atualizar o conteúdo do navegador para ver as mudanças
- Modifique o arquivo do diretório "`_posts\`" e atualize o conteúdo do navegador para ver as mudanças

Execução

```
$ cd bindmoubt-sample-1

$ docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve 
```

O servidor ficará rodando e servindo o conteúdo do diretório "`./bindmoubt-sample-1`"

Modificar o arquivo "`_posts/2017-03-05-welcome-to-jekyll.markdown`" no HOST (alguma coisa no título, por exemplo).

Perceber no log do container que foi detectada a modificação do arquivo

Atualizar o conteúdo do navegadoe e ver a modificação feita


# 5.53. Database Passwords in Containers

Desde o advento do Docker, alguns DBs têm permitido o startup de servidores através de contêineres sem o uso de senha. É possível indicar uma senha, mas isso não é obrigatório.

Em fev/2020 algo mudou no postgres. Agora é necessário indicar uma senha, ou fazer com que permita qualquer conexão (o que era o padrão antes dessa modificação).

Para o "`docker run`", e para as seções que usam o Docker Compose, será necessário indicar uma senha através de uma variável de ambiente:

```
POSTGRES_PASSWORD=minhasenha
```

OU pedir para ignorar as senhas com a seguinte variável de ambiente:

```
POSTGRES_HOST_AUTH_METHOD=trust
```

Importante perceber que essa modificação foi feita na imagem oficial do Docker Hub, e não afeta o postgres.


# Seção 6: Making It Easier with Docker Compose: The Multi-Container Tool

# 6.54. Docker Compose and The docker-compose.yml File

Combinação: ferramenta de linha de comando e arquivo de configuração

- Possibilita criar relacionamento entre contêineres, o que permite juntar algumas soluções e fornecer um serviço
- Guarda as configurações de execução das instruções "`docker container run`" em um arquivo fácil de ler
- Facilita a inicialização dos contêineres, redes, exposição de portas e volumes com uma única linha de comando
- Compreende 2 coisas separadas, mas relacionadas:
  - (1) Arquivo no formato YAML que descreve as opções para a nossa solução: containers, networks, volumes
  - (2) Uma ferramenta CLI chamada "docker-compose" usada para desenvolvimento e automação de testes locais com esses arquivos YAML


## O arquivo `docker-compose.yml`

- O formato YAML do compose tem suas próprias versões: 
  - 1; 2; 2.1; 3; 3.1
  - Deve ser a primeira linha do arquivo
  -  Mínima recomendada: 2 (Version 2 and above provide significantly more features then the old default version 1, and what we will be using as a default base for this course. Bonus Note: v2.x is actually better for local docker-compose use, and v3.x is better for use in server clusters (Swarm and Kubernetes))
  - O arquivo YAML pode ser usado com o comando "`docker-compose`" para automação do docker local (Exemplo: maquina do desenvolvedor).
  - OU diretamente pelo "docker" em produção com o Swarm (como v1.13)
- Boa documentação de ajuda: `docker-compose --help`
- O nome "`docker-compose.yml`" é o padrão, mas pode ser usado outro nome com a opção "`-f`" do "`docker-compose`"

Links:
- YAML Sample
  - https://yaml.org/start.html
- YAML Quick Reference
  - https://yaml.org/refcard.html
- Compose file version 3 reference
  - https://docs.docker.com/compose/compose-file/
- Compose File Version Differences
  - https://docs.docker.com/compose/compose-file/compose-versioning/
- Docker Compose Releases
  - https://github.com/docker/compose/releases



**Exemplo 1 - arquivo:** compose-sample-1/template.yml
```
version: '3.1'  # if no version is specificed then v1 is assumed. Recommend v2 minimum

services:  # containers. same as docker run
  servicename: # a friendly name. this is also DNS name inside network
    image: # Optional if you use build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run
  servicename2:

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create
```



**Exemplo 2 - arquivo:** compose-sample-1/docker-compose.yml
```
version: '2'

# same as 
# docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'
```


**Exemplo 3 - arquivo:** compose-sample-1/compose-2.yml
```
version: '2'

services:

  wordpress:
    image: wordpress
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: example
      WORDPRESS_DB_PASSWORD: examplePW
    volumes:
      - ./wordpress-data:/var/www/html

  mysql:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: examplerootPW
      MYSQL_DATABASE: wordpress
      MYSQL_USER: example
      MYSQL_PASSWORD: examplePW
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  mysql-data:
```


**Exemplo 4 - arquivo:** compose-sample-1/compose-3.yml
```
version: '3'

services:
  ghost:
    image: ghost
    ports:
      - "80:2368"
    environment:
      - URL=http://localhost
      - NODE_ENV=production
      - MYSQL_HOST=mysql-primary
      - MYSQL_PASSWORD=mypass
      - MYSQL_DATABASE=ghost
    volumes:
      - ./config.js:/var/lib/ghost/config.js
    depends_on:
      - mysql-primary
      - mysql-secondary
  proxysql:
    image: percona/proxysql
    environment: 
      - CLUSTER_NAME=mycluster
      - CLUSTER_JOIN=mysql-primary,mysql-secondary
      - MYSQL_ROOT_PASSWORD=mypass
   
      - MYSQL_PROXY_USER=proxyuser
      - MYSQL_PROXY_PASSWORD=s3cret
  mysql-primary:
    image: percona/percona-xtradb-cluster:5.7
    environment: 
      - CLUSTER_NAME=mycluster
      - MYSQL_ROOT_PASSWORD=mypass
      - MYSQL_DATABASE=ghost
      - MYSQL_PROXY_USER=proxyuser
      - MYSQL_PROXY_PASSWORD=s3cret
  mysql-secondary:
    image: percona/percona-xtradb-cluster:5.7
    environment: 
      - CLUSTER_NAME=mycluster
      - MYSQL_ROOT_PASSWORD=mypass
   
      - CLUSTER_JOIN=mysql-primary
      - MYSQL_PROXY_USER=proxyuser
      - MYSQL_PROXY_PASSWORD=s3cret
    depends_on:
      - mysql-primary
```


# 6.55. Docker Compose - Além do Básico

Links:

- (Debate) Only one host for production environment. What to use: docker-compose or single node swarm?
  - https://github.com/BretFisher/ama/issues/8
- Docker Compose download for Linux
  - https://github.com/docker/compose/releases

Resumo:

- A instalação do CLI acompanha as versões para Windows/Mac, mas para Linux deve ser feito o download separado
- Não é uma ferramenta adequada para produção, mas é ideal para desenvolvimento local e testes
- Os comandos mais comuns são:
  - (1) `$ docker-compose up`
    - configura os volumes/networks e inicia todos os contêineres
  - (2) `$ docker-compose down`
    - interrompe todos os contêineres e remove cont/vol/netw
- Se todos os seus projetos tiverem um "`Dockerfile`" e um "`docker-compose.yml`", então os novos devs que chegarem precisarão fazen apenas algo como:
  - (1) `$ git clone github.com/some/software`
  - (2) `$ docker-compose up`


**Exemplo - arquivo:** compose-sample-2/docker-compose.yml
```
version: '3'

services:
  proxy:
    image: nginx:1.13 # this will use the latest version of 1.13.x
    ports:
      - '80:80' # expose 80 on host and sent to 80 in container
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
  web:
    image: httpd  # this will use httpd:latest
```

**arquivo:** compose-sample-2/nginx.conf
```
server {

	listen 80;

	location / {

		proxy_pass         http://web;
		proxy_redirect     off;
		proxy_set_header   Host $host;
		proxy_set_header   X-Real-IP $remote_addr;
		proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header   X-Forwarded-Host $server_name;

	}
}
```

```
$ cd compose-sample-2
```

```
$ docker-compose up
# ou (Detached)
$ docker-compose up -d
```

```
$ docker-compose ps
```

```
$ docker-compose top
```

```
$ docker compose down
```



# 6.57 Exercício - Escrever um Compose File para um serviço Multi-Container

- Compose File para um CMS Drupal (verificar o Docker Hub)
- Usar a imagem "`drupal`" em conjunto com a do "`postgres`"
- Usar a chave "`ports`" para expor a porta TCP 8080 (http://localhost:8080)
- Não esquecer de configurar o "`POSTGRES_PASSWORD`" para o postgres
- Dar uma navegada no setup do Drupal via navegador
- Dica: O Drupal assume que o DB seja o "`localhost`", mas este é o service name (lembrar dos casos de DNS)
- Extra: Usar "`volumes`" para armazenar os dados persistentes do Drupal

## 1a versão

**arquivo:** compose-assignment-2/docker-compose.yml
```
version: '2'

services:
  drupal:
    image: drupal
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles       
      - drupal-sites:/var/www/html/sites      
      - drupal-themes:/var/www/html/themes
   postgres:
    image: postgres
    environment:
      - POSTGRES_PASSWORD=mypasswd

volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
```

```
$ docker compose up
```


Derruba os servicos e remove os volumes:

```
$ docker compose down -v
```


## 2a versão - Mais completa

**arquivo:** compose-assignment-2/docker-compose.yml
```
version: '2'

services:
  drupal:
    image: custom-drupal
    build: .
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles       
      - drupal-sites:/var/www/html/sites      
      - drupal-themes:/var/www/html/themes
 
  postgres:
    image: postgres:12.1
    environment:
      - POSTGRES_PASSWORD=mypasswd
    volumes:
      - drupal-data:/var/lib/postgresql/data

volumes:
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
```

**arquivo:** compose-assignment-2/Dockerfile
```
FROM drupal:8.8.2


RUN apt-get update && apt-get install -y git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/html/themes

RUN git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupal.org/project/bootstrap.git \
    && chown -R www-data:www-data bootstrap
```


# 6.58. Adicionar o Build de imagens no Compose File

- O compose também pode "buildar" imagens customizadas 
- O build é disparado no "`docker-compose up`" se a imagem não for encontrada no cache
- Pode-se forçar o build com o "`docker-compose build`"
- Boa alternativa para builds complexos que contêm muitas variáveis ou argumentos de build

Link:

- Docs Docker
  - https://docs.docker.com/compose/compose-file/#build

## Exemplo: NGinx fazendo o proxy-reverso para um Apache Server

**arquivo:** compose-assignment-3/docker-compose.yml
```
version: '2'

# based off compose-sample-2, only we build nginx.conf into image
# uses sample site from https://startbootstrap.com/template-overviews/agency/

services:
  proxy:
    build:
      context: .
      dockerfile: nginx.Dockerfile
    ports:
      - '80:80'
  web:
    image: httpd
    volumes:
      - ./html:/usr/local/apache2/htdocs/
```
  
**arquivo:** compose-assignment-3/nginx.Dockerfile
```
FROM nginx:1.13

COPY nginx.conf /etc/nginx/conf.d/default.conf
```

**arquivo:** compose-assignment-3/nginx.conf 
```
server {

	listen 80;

	location / {

		proxy_pass         http://web;
		proxy_redirect     off;
		proxy_set_header   Host $host;
		proxy_set_header   X-Real-IP $remote_addr;
		proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header   X-Forwarded-Host $server_name;

	}
}
```

## Execução

```
$ cd compose-assignment-3
$ docker-compose up
```

Perceber que a imagem é construída durante a subida da pilha de serviço

```
$ docker-compose down 
```

Outra forma de "derrubar" a pilha de serviços é fazer com que sejam removidas as imagens (ver `$ docker compose down --help`).

```
$ docker compose down --rmi local
```


# 6.59. Exercício: Build and Run Compose

- Criar uma imagem customizada do "drupal" para testes locais
- Compose não foi criado apenas para desenvolvedores. Ele facilita também o teste de apps.
- Ver arquivo `README.md`

**arquivo:** compose-assignment-2/README.md 
```
# Assignment: Compose For On-The-Fly Image Building and Multi-Container Testing

Goal: This time imagine you're just wanting to learn Drupal's admin and GUI, or maybe you're a software tester and you need to test a new theme for Drupal. When configured properly, this will let you build a custom image and start everything with `docker-compose up` including storing important db and config data in volumes so the site will remember your changes across Compose restarts.

- Use the compose file you created in the last assignment (drupal and postgres) as a starting point.
- Let's pin image version from Docker Hub this time. It's always a good idea to do that so a new major version doesn't surprise you.

## Dockerfile
- First you need to build a custom Dockerfile in this directory, `FROM drupal:8.8.2` NOTE: if it fails to build, try the lastest 8 branch version with `FROM drupal:8`
- Then RUN apt package manager command to install git: `apt-get update && apt-get install -y git`
- Remember to cleanup after your apt install with `rm -rf /var/lib/apt/lists/*` and use `\` and `&&` properly. You can find examples of them in drupal official image. More on this below under Compose file.
- Then change `WORKDIR /var/www/html/themes`
- Then use git to clone the theme with: `RUN git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupal.org/project/bootstrap.git`
- Combine that line with this line, as we need to change permissions on files and don't want to use another image layer to do that (it creates size bloat). This drupal container runs as www-data user but the build actually runs as root, so often we have to do things like `chown` to change file owners to the proper user: `chown -R www-data:www-data bootstrap`. Remember the first line needs a `\` at end to signify the next line is included in the command, and at start of next line you should have `&&` to signify "if first command succeeds then also run this command"
- Then, just to be safe, change the working directory back to its default (from drupal image) at `/var/www/html`

## Compose File
- We're going to build a custom image in this compose file for drupal service. Use Compose file from previous assignment for Drupal to start with, and we'll add to it, as well as change image name.
- Rename image to `custom-drupal` as we want to make a new image based on the official `drupal:8.8.2`.
- We want to build the default Dockerfile in this directory by adding `build: .` to the `drupal` service. When we add a build + image value to a compose service, it knows to use the image name to write to in our image cache, rather then pull from Docker Hub.
- For the `postgres:12.1` service, you need the same password as in previous assignment, but also add a volume for `drupal-data:/var/lib/postgresql/data` so the database will persist across Compose restarts.

## Start Containers, Configure Drupal
- Start containers like before, configure Drupal web install like before.
- After website comes up, click on `Appearance` in top bar, and notice a new theme called `Bootstrap` is there. That's the one we added with our custom Dockerfile.
- Click `Install and set as default`. Then click `Back to site` (in top left) and the website interface should look different. You've successfully installed and activated a new theme in your own custom image without installing anything on your host other then Docker!
- If you exit (ctrl-c) and then `docker-compose down` it will delete containers, but not the volumes, so on next `docker-compose up` everything will be as it was.
- To totally clean up volumes, add `-v` to `down` command.
```


**arquivo:** compose-assignment-2/Dockerfile
```
FROM drupal:8.8.2


RUN apt-get update && apt-get install -y git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/html/themes

RUN git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupal.org/project/bootstrap.git \
    && chown -R www-data:www-data bootstrap

WORKDIR /var/www/html
```


**arquivo:** compose-assignment-2/docker-compose.yml 
```
version: '2'
services:

  drupal:
    image: custom-drupal
    build: .
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles       
      - drupal-sites:/var/www/html/sites      
      - drupal-themes:/var/www/html/themes
 
  postgres:
    image: postgres:12.1
    environment:
      - POSTGRES_PASSWORD=mypasswd
    volumes:
      - drupal-data:/var/lib/postgresql/data

volumes:
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
```

## Execução

```
$ docker compose up
```

Verificar navegador: http://localhost:8080


# Seção 7: Swarm Intro and Creating a 3-Node Swarm Cluster

# 7.60. Docker Swarm - Intro

Criação de um cluster Swarm com 3 nodes

- Como automatizar o cliclo de vida do container?
- Como facilitar o "escalonamento" (out, in, up, down)?
- Como garantir que os contêineres serão recriados se falharem?
- Como substituir contêineres "sem tempo de parada" (blue/green deploy)?
- Como controlar/rastrear onde os contêineres iniciaram?
- Como criar VNs entre diferentes nodes?
- Como garantir que somente servidores confiáveis execute os nossos contêineres?
- Como armazenar secrets, chaves, senhas e fornecẽ-los ao contêiner correto (e somente para aquele contêiner)?


# 7.61. Swarm Mode

O Docker por si é só um mecanismo para "executar contêineres".

O **Swarm Mode** é uma solução de clusterização construída dentro do Docker.

Links:

- Vídeo: Docker 1.12 Swarm Mode Deep Dive Part 1: Topology
  - https://www.youtube.com/watch?v=dooPhkXT9yI
- Vídeo: Docker 1.12 Swarm Mode Deep Dive Part 2: Orchestration
  - https://www.youtube.com/watch?v=_F6PSP-qhdA
- Heart of the SwarmKit: Topology Management 
  - https://speakerdeck.com/aluzzardi/heart-of-the-swarmkit-topology-management
- Vídeo: Heart of the SwarmKit: Store, Topology & Object Model
  - https://www.youtube.com/watch?v=EmePhjGnCXY
- Raft - Understandable Distributed Consensus
  - http://thesecretlivesofdata.com/raft/
- Deploy services to a swarm
  - https://docs.docker.com/engine/swarm/services/
- How-to Add SSH Keys to New or Existing Droplets 
  - https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/
- (*) Docker Swarm Firewall Ports
  - https://www.bretfisher.com/docker-swarm-firewall-ports/
- $100 de crédito na Digital Ocean
  - https://www.digitalocean.com/?refcode=ee97875d52fa&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=CopyPaste
- Microsoft Hyper-V driver for Docker
  - https://docs.docker.com/machine/drivers/hyper-v/
- How To Configure Custom Connection Options for your SSH Client
  - https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client


# 7.62. Swarm Services

## Criação do primeiro serviço e escalonamento local

O que acontece durante o "`docker swarm init`":

- Muitas PKI (Public Key Infrastructure ou ICP no Brasil) e automação de segurança
  - Root Signing Certificate criado para o nosso Swarm
  - O Certificado é direcionado para o primeiro node Manager
  - Tokens de conexão são criados
- Um DB é criado para armazenar root CA, configurações e secrets
  - Criptografado por padrão no disco (v 1.13+)
  - Não necessita de outro sistema chave/valor para manter a orquestração/secrets
  - Replica os logs entre os Managers através de TLS em um "plano de controle"

Verificar o status do "Swarm" (se ativo ou inativo)

```
$ docker info
```

Inicializar o Swarm

```
$ docker swarm init
```

Exibe listagem com os nodes participantes do cluster
```
$ docker node ls
```

(*) **SERVICES** no Swarm é o que substitui o **DOCKER RUN**

```
$ docker service --help
```


Exemplo:

```
$ docker service create alpine ping 8.8.8.8
```


Perceber o número de réplicas

```
$ docker service ls
```

Perceber nome do "**NODE**" e o número (incremento) adicionado ao nome do serviço

```
$ docker service ps <name ou id>
```

O "docker container ls" continua funcionando, mas apresentará informações adicionadas pelo serviço

 Atualizar o serviço, aumentando o número de réplicas:

``` 
$ docker service update  <name ou id> --replicas <qtde>
```


Exemplo:

```
$ docker service update k5gu6t6yq8r1 --replicas 3
$ docker service ls
$ docker service ps frosty_newton
```

Dar uma olhada no help do "`docker update`". Há muitas opções para limitar o uso de CPU e memória (entre outros)

Ver também "`docker service update --help`"


## Teste

- Remover um container participante de um serviço. 
- Verificar se o número de réplicas reduziu
- Verificar se a réplica foi recuperada (se subiu novamente)

```
$ docker container ls
$ docker container rm -f <name>.1.<id>
$ docker service ls
```

Perceber o "histórico" do serviço (o que falhou e foi recarregado)

```
$ docker service ps frosty_newton
```


Para realmente "derrubar" todos os contêineres, precisamos derrubar o serviço

```
$ docker service rm frosty_newton
$ docker service ls
$ docker container ls
```

# 7.65. Swarm - Criação de um Cluster Swarm com 3 nodes ; Host Option

## Opções para realizar essa tarefa (teste)

### 1) Usar o "play-with-docker.com"

Necessário apenas um navegador, mas o ambiente é "resetado" após 4 horas

### 2) Usar a opção "docker-machine" + VirtualBox

Executa localmente, mas requer uma máquina com 8GB+ RAM

```
$ docker-machine create node1
$ docker-machine create node2
...
$ docker-machine ssh node1
```

Ou:

```
$ docker-machine env node1
eval $(docker-machine env node1)
```

### 3) Digital Ocean + Docker Install

Mais parecido com um ambiente de produção, mas cada node custa de US$5 a US$10 por mês


### 4) Por sua conta e risco

O "`docker-machine`" pode fazer o aprovisionamento de máquinas na Amazon, Azure, DO, Google, etc.

Instale o docker em qualquer lugar com o "get.docker.com"


## Exemplo de utilização na Digital Ocean:

- Criação dos nodes no painel da D.O.
- Acesso aos nodes via ssh ::  `ssh root@104.236.114.90`
- Instalar o docker em cada node (script get.docker.com)

Via: `docker swarm init --advertise-addr <IP-addr>`

```
node1# docker swarm init --advertise-addr 104.236.114.90
```


Executar o "`docker swarm join`" gerado durante o init do "node1"

```
node2# docker swarm join ....
node3# docker swarm join ....
```

```
node1# docker node ls
```

```
node1# docker node update --role manager node2
```


Este token faz com que o node que o execute passe a fazer parte do cluster como um manager "reachable"

```
node1# docker swarm join-token manager
```

```
node3# docker swarm join... (gerado há pouco)
```


Agora temos 3 "Managers": um "**Leader**" e dois "**Reachable**"

```
node1# docker node ls
```

```
node1# docker service create --replicas 3 alpine ping 8.8.8.8
```

Perceber que temos 3 réplicas criadas

```
node1# docker service ls
```


Mostra o serviço que está sendo executado neste node

```
node1# docker node ps
```


Mostra o serviço que está sendo executado no node2

```
node1# docker node ps node
```


Mostra informações do serviço em todos os nodes

```
node1# docker service ps <nome-servico>
```


# Seção 8: Swarm Basic Features and How to Use Them In Your Workflow

# 8.66. Escalando para Multi-Host com Overlay Networking

- Na criação da rede: "--driver overlay"
- Tráfego de conteiner-a-conteiner dentro de um único Swarm
- Possível usar criptografia IPSec (AES) na criação da rede
- Cada serviço pode ser conectado a múltiplas redes
--- Exemplo: front-end, back-end

node1# docker network create --driver overlay mydrupal

node1# docker network ls

node1# docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=mypass postgres

node1# docker service ls

node1# docker service ps psql

node1# docker container logs psql

node1# docker service create --name drupal --network mydrupal -p 80:80 drupal

node1# docker service ls

node1# watch docker service ls

node1# docker service ps drupal

Cada serviço subiu em um node diferente, e a comunicação entre eles ocorre através dos "nomes dos serviços".

node1# docker service inpect drupal

Se abrirmos três abas de navegadores e em cada um deles informarmos o endereço IP de cada node, apontando para o port TCP 80, os três endereços mostrarão o conteúdo do Drupal, mas o metadado apresentado pelo inspect anterior mostra que há somente um IP Virtual para o serviço.


************************************************

# 8.67. Escalando com Routing Mesh (malha)
-- Global Traffic Router -- 

-- Use swarm mode routing mesh
https://docs.docker.com/engine/swarm/ingress/

- "Roteia" pacotes ingress (entrada) destinados a um Serviço para a Tarefa (Task) apropriada
- Expande todos os nodes no Swarm
- Usa primitivas do Kernel do Linux (IPVS)
- Faz um balanceamento de carga dos Serviços do Swarm entre as suas Tasks
- Isso é feito de duas formas:
-- 1. Container-para-container em uma rede Overlay (usa Virtual IP)
-- 2. Tráfego de entrada externo para as portas TCP publicadas (todos os nodes ficam ouvindo)


node1# docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2

node1# docker service ps search

node1# curl localhost:9200
node1# curl localhost:9200
node1# curl localhost:9200
node1# curl localhost:9200
... observar nomes dos hosts

-- Routing Mesh --
-- É um balanceamento de carga stateless
-- O LB está na camada 3 (TCP) do modelo OSI, não na camada 4 (DNS)
-- As limitações podem ser superadas com:
---- Nginx ou HAProxy LB proxy (OU)
---- Docker Enterprise Edition, que vem com um web proxy L4 (DNS).


# 8.68. Exercício: Criação de uma aplicação multi-serviço / multi-node

- Aplicação de Votação distribuída
- ver diretório "swarm-app-1"
- criar: 1 volume, 2 nw, 5 serviços


%%% arquivo: swarm-app-1/README.md %%%%%%%%%%%%%%%%%%%%%%
# Assignment: Create A Multi-Service Multi-Node Web App

## Goal: create networks, volumes, and services for a web-based "cats vs. dogs" voting app.
Here is a basic diagram of how the 5 services will work:

![diagram](./architecture.png)
- All images are on Docker Hub, so you should use editor to craft your commands locally, then paste them into swarm shell (at least that's how I'd do it)
- a `backend` and `frontend` overlay network are needed. Nothing different about them other then that backend will help protect database from the voting web app. (similar to how a VLAN setup might be in traditional architecture)
- The database server should use a named volume for preserving data. Use the new `--mount` format to do this: `--mount type=volume,source=db-data,target=/var/lib/postgresql/data`

### Services (names below should be service names)
- vote
    - bretfisher/examplevotingapp_vote
    - web front end for users to vote dog/cat
    - ideally published on TCP 80. Container listens on 80
    - on frontend network
    - 2+ replicas of this container

- redis
    - redis:3.2
    - key/value storage for incoming votes
    - no public ports
    - on frontend network
    - 1 replica NOTE VIDEO SAYS TWO BUT ONLY ONE NEEDED

- worker
    - bretfisher/examplevotingapp_worker:java
    - backend processor of redis and storing results in postgres
    - no public ports
    - on frontend and backend networks
    - 1 replica

- db
    - postgres:9.4
    - one named volume needed, pointing to /var/lib/postgresql/data
    - on backend network
    - 1 replica
    - remember set env for password-less connections -e POSTGRES_HOST_AUTH_METHOD=trust

- result
    - bretfisher/examplevotingapp_result
    - web app that shows results
    - runs on high port since just for admins (lets imagine)
    - so run on a high port of your choosing (I choose 5001), container listens on 80
    - on backend network
    - 1 replica
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%



%%% arquivo: swarm-app-1/answer.sh  %%%%%%%%%%%%%%%%
docker network create -d overlay backend
docker network create -d overlay frontend

docker service create --name vote -p 80:80 --network frontend --replicas 2 bretfisher/examplevotingapp_vote

docker service create --name redis --network frontend redis:3.2

docker service create --name db --network backend -e POSTGRES_HOST_AUTH_METHOD=trust --mount type=volume,source=db-data,target=/var/lib/postgresql/data postgres:9.4

docker service create --name worker --network frontend --network backend bretfisher/examplevotingapp_worker:java

docker service create --name result --network backend -p 5001:80 bretfisher/examplevotingapp_result
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


# 8.70. Swarm Stacks and Production Grade Compose


-- Features not supported in Docker Stack Deploy
https://docs.docker.com/compose/compose-file/#not-supported-for-docker-stack-deploy

-- Production Servers: Compose vs Swarm
https://github.com/BretFisher/ama/issues/8

-- Stacks - basicamente, são compose files para ambientes produtivos
-- Stacks aceitam arquivos Compose como sendo uma definição declarativa para serviços, networks e volumes
-- Passamos a usar "docker stack deploy" em vez de "docker service create"
- As Stacks gerenciam todos esses objetos pra gente, incluindo "network overlay" por stack. Adiciona o nome da stack no início do seu nome.
- Nova chave "deploy:" no arquivo Compose (direcionado para produção, não faz sentido ter um build aqui). Não pode fazer "build"
- O Compose agora ignora a chave "deploy:", o Swarm ignora a chave "build:"
- Não é necessário ter o "docker-compose" cli em um servidor Swarm
- Usar a versão 3+ para os arquivos Compose (yml).



%%% arquivo: swarm-stack-1/example-voting-app-stack.yml %%%%%%%%%
version: "3"
services:

  redis:
    image: redis:alpine
    ports:
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    environment:
      - POSTGRES_HOST_AUTH_METHOD=trust
    deploy:
      placement:
        constraints: [node.role == manager]
  vote:
    image: bretfisher/examplevotingapp_vote
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure
  result:
    image: bretfisher/examplevotingapp_result
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  worker:
    image: bretfisher/examplevotingapp_worker:java
    networks:
      - frontend
      - backend
    depends_on:
      - db
      - redis
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints: [node.role == manager]

  visualizer:
    image: dockersamples/visualizer
    ports:
      - "8080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]

networks:
  frontend:
  backend:

volumes:
  db-data:
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

Execução:
-- Deploy
$ docker stack deploy -c example-voting-app-stack.yml voteapp

$ docker stack ls

-- Apresenta as Tasks (não os contêineres)
$ docker stack ps voteapp

-- Comparar com
$ docker container ls

-- Apresenta os serviços
$ docker stack service voteapp

$ docker network ls


(***) Dar uma boa olhada no "dockersamples/visualizer"



# 8.71. Secrets Storage for Swarm
-- Protegendo variáveis de ambiente / config. vars --

-- Solução simples de segurança para armazenar "secrets" no Swarm
-- O que é um secret?
---- Usernames and passwords
---- Certificados TLS e chaves
---- Chaves SSH
---- Qualquer dado que se queira "ocultar"
-- Há outras alternativas como o "Vault"
-- Comporta binários ou strings genéricas de até 500Kb
-- Não requer apps para que sejam reescritas
-- Assim como o Docker 1.12.0 Swarm Raft DB, é criptografado no disco
-- Somente é armazenado no disco nos nodes Manager
-- TLS+Mutual Auth - Manager e Workers
-- Os "secrets" são primeiramente armazenados no Swarm, depois atribuídos aos Serviços
-- Somente os contêineres dos Serviços atribuídos é que podem vê-lo
-- Parecem com arquivos no contêiner, mas na verdade estão em um fs in-memory
---- /run/secrets/<secret_name> ou /run/secrets/<secret_alias>
-- O docker-compose local pode usar secrets baseados em arquivos, mas não é seguro (faking secure)



# 8.72. Using Secrets with Swarm Services

-- Manage sensitive data with Docker secrets
https://docs.docker.com/engine/swarm/secrets/


%%% arquivo: secrets-sample-1/psql_user.txt %%%%%%%%%
mypsqluser
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

-- criação de secret baseada em arquivo
$ docker secret create psql_user psql_user.txt

-- criação direta
$ echo "myDBpassWORD" | docker secret create psql_pass -

-- nenhuma informação crítica é exibida
$ docker secret inspect psql_user

-- uso do secret 
$ docker service create --name psql --secret psql_user --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql_user postgres





# 8.73. Using Secrets with Swarm Stacks

-- Secrets in Compose Files
https://docs.docker.com/compose/compose-file/#secrets-configuration-reference

%%% arquivo: secrets-sample-2/psql_user.txt %%%%%%%%%
dbuser
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

%%% arquivo: secrets-sample-2/psql_password.txt %%%%%%%%%
QpqQcgD7dxVG
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

%%% arquivo: secrets-sample-2/docker-compose.yml %%%%%%%%%
version: "3.1"

services:
  psql:
    image: postgres
    secrets:
      - psql_user
      - psql_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/psql_password
      POSTGRES_USER_FILE: /run/secrets/psql_user

secrets:
  psql_user:
    file: ./psql_user.txt
  psql_password:
    file: ./psql_password.txt
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


$ docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2


$ docker stack deploy -c docker-compose.yml mydb

$ docker secret ls

-- ao remover a stack, os secrets tambémsão removidos
$ docker stack rm mydb



# 8.74. Exercício: Criar uma Stack com secrets e deploy

- Usar o compose file do diretório "compose-assignment-2"
- Renomear a imagem para o nome oficial "drupal:8.2"
- Remover a sessão "build:"
- Adicionar um secret via "external:"
- usar a variável de ambiente "POSTGRES_PASSWORD_FILE"
- Adicionar o secret via: 
---- $ echo "<pw>" | docker secret create psql-pw -
- Copiar o compose file para um novo arquivo yml no node1 do Swarm, fazer o deploy e ver se funciona

%%% arquivo: secrets-assignment-1/docker-compose.yml %%%%%
version: '3.1'

services:

  drupal:
    image: drupal:8.8.2
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - drupal-themes:/var/www/html/themes
 
  postgres:
    image: postgres:12.1
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw
    secrets:
      - psql-pw
    volumes:
      - drupal-data:/var/lib/postgresql/data

volumes:
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:

secrets:
  psql-pw:
    external: true
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


node1# echo "dhdgdhghg" | docker secret create psql-pw - 
node1# docker stack deploy -c docker-compose.yml drupal
node1# docker stack ps drupal


# Seção 9: Swarm App Lifecycle


# 9.76. Usando Secrets com Docker Compose - local

(*) Não é seguro, mas funciona... é só uma forma de permitir ao developer as características que ele usaria em produção para testes. Os "secrets" são baseados em arquivos e não oferecem nenhuma segurança. Não é recomendado usar em ambientes produtivos. Talvez passe a funcionar de forma semelhante ao Swarm na versão 11 do docker-compose

(**) Vale a pena rever - Dicas interessantes sobre CI

- Testes feitos em uma máquina sem Swarm 
$ docker node ls

-- Ver conteúdo dos arquivos já mencionados (secrets-sample-2)
$ cd secrets-sample-2
$ docker-compose up -d
$ docker-compose exec psql cat /run/secrets/psql_user




# 9.77. Ciclo de vida completo - Dev, Build & Deploy - Compose

Apresenta uma forma de trabalho que organiza os arquivos Compose para o ambiente de desenvolvimento local, CI e produção.


-- Multiple Compose files
https://docs.docker.com/compose/extends/#multiple-compose-files

-- Use Compose in production
https://docs.docker.com/compose/production/


- Nota: "docker-compose -f a.yml -f b.yml config" "geralmente" funciona.
- Nota: A chave "extends:" do Compose "ainda não funciona" em Stacks. Uma outra forma de fazer o "override" de arquivos compose.

Para este cenário:
- Local: "docker-compose up" - ambiente de desenvolvimento
- Remoto: "docker-compose up" - ambiente de Integração Contínua (CI)
- Remoto: "docker stack deploy" - ambiente de produção


(*) ver arquivos em "swarm-stack-3"

%%%% swarm-stack-3/docker-compose.override.yml  %%%%%%%%%%%%%%
version: '3.1'

services:

  drupal:
    build: .
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - ./themes:/var/www/html/themes
 
  postgres:
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw
    secrets:
      - psql-pw
    volumes:
      - drupal-data:/var/lib/postgresql/data

volumes:
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:

secrets:
  psql-pw:
    file: psql-fake-password.txt


%%%% swarm-stack-3/docker-compose.prod.yml %%%%%%%%%
version: '3.1'

services:

  drupal:
    ports:
      - "80:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - drupal-themes:/var/www/html/themes
 
  postgres:
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw
    secrets:
      - psql-pw
    volumes:
      - drupal-data:/var/lib/postgresql/data

volumes:
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:

secrets:
  psql-pw:
    external: true


%%%% swarm-stack-3/docker-compose.test.yml %%%%%%%%%
version: '3.1'

services:

  drupal:
    image: custom-drupal
    build: .
    ports:
      - "80:80"

  postgres:
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw
    secrets:
      - psql-pw
    volumes:
      # NOTE: this might be sample data you host in your CI server
      # so you can do integration testing with sample data
      # this may not work on Docker for Windows/Mac due to bind-mounting
      # database data across OS's, which doesn't always work
      # in those cases you should use named volumes
      - ./sample-data:/var/lib/postgresql/data
secrets:
  psql-pw:
    file: psql-fake-password.txt


%%%% swarm-stack-3/docker-compose.yml %%%%%%%%%%%
version: '3.1'

services:

  drupal:
    image: custom-drupal:latest

  postgres:
    image: postgres:12.1


%%%% swarm-stack-3/psql-fake-password.txt %%%%%%%
mypasswd

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


-- Faz o uso do "docker-compose.yml" (que não define quase nada) e por padrão carrega também o "docker-compose.override.yml".
-- Dá pra verificar o comportamento através do "inspect"
$ docker-compose up -d
$ docker instect <image...>
$ docker-compose down


-- passagem de dois arquivos Compose (Exemplo, para um ambiente de CI)
$ docker-compose -f docker-compose.yml -f docker-compose.text.yml up -d
-- verificar a diferença
$ docker instect <image...>


-- Combina os dois Compose files e gera a composição a ser usada em produção
$ docker-compose -f docker-compose.yml -f docker-compose.prod.yml config

-- A saída gerada (output.yml) é o arquivo a ser usado em produção
$ docker-compose -f docker-compose.yml -f docker-compose.prod.yml config > output.yml



# 9.78. Service Update - Modificando as coisas em tempo de voo

-- docker service update
https://docs.docker.com/engine/reference/commandline/service_update/

- Provê uma forma de substituir tasks/contêineres em um serviço
- Limita o "downtime" (não previne, apenas reduz)
- Na maioria das vezes é só substituição de contêineres
- Inclui opções para "rollback" e "healthcheck" (***)
- Subcomandos para "escalar" e fazer o "rollback" (acesso rápido)
-- "docker service scale web=4" e "docker service rollback web"
- O deploy de uma Stack pré-existente, funciona como um "service update"


Exemplos (Swarm Update): 

-- Atualizar a imagem usada para uma nova versão
$ docker service update --image myapp:1.2.1 <servicename>

-- Adicionar variável de ambiente e remover uma porta TCP
$ docker service update --env-add NODE_ENV=production --publish-rm 8080

-- Alterar o número de réplicas de dois serviços
$ docker service scale web=8 api=6


Exemplos (Swarm Update in Stack Files): 

-- Mesmo comando. Apenas edite o arquivo YAML e...:
$ docker stack deploy -c file.yml <stackname>


Na prática:

$ docker service create -p 8088:80 --name web nginx:1.13.7

$ docker service ls

$ docker service scale web=5

-- alterando a versão da imagem
$ docker service update --image nginx:1.13.6 web

-- modificar o port publicado
$ docker service update --publish-rm 8088 --publish-add 9090:80 web


-- (Dica) Força a atualização do serviço, mesmo que não haja modificação alguma (dá um refresh no balanceamento, por exemplo)
$ docker service update --force web


-- Limpeza:: remover o serviço criado
$ docker service rm web


# 9.79. HEALTHCHECK in Docker Files


-- PHP/Laravel app for Docker examples (Good Defaults)
https://github.com/BretFisher/php-docker-good-defaults

-- HEALTHCHECK in Docker Files
https://docs.docker.com/engine/reference/builder/#healthcheck


-- Healthcheck in ComposeFiles
https://docs.docker.com/compose/compose-file/#healthcheck


- HEALTHCHECK foi adicionado na versão 1.12
- Suportado no Dockerfile, Compose YAML, docker run e Swarm Services
- O Docker Engine "executará" o comando no container (Exemplo: curl localhost)
- Verifica a resposta: "exit 0" (OK) ou "exit 1" (Error)
- Os contêiineres podem assumir 3 status: starting, healthy, unhealthy
- Bem melhor do que ficar verificando se "o binário continua executando?"
- Não é nada "muito avançado". É uma monitoração básica.
- O status de Healthcheck é exibido no "docker container ls"
- O histórico dos últimos 5 Healthchecks é exibido com "docker container inspect"
- O "docker run" não faz nada com os Healthchecks
- Já os "serviços" farão a substituição das Tasks se ocorrer uma falha no Healthcheck.
- As atualizações de serviço aguardam a verificação do Healthcheck antes de continuarem


-- Exemplo de Heathcheck com Docker Run --

$ docker run \
    --health-cmd="curl -f localhost:9200/_cluster/health || false" \
    -- health-interval=5s \
    -- health-reties=3 \
    -- health-timeout=2s \
    -- health-start-period=15s \
    elasticsearch:2


-- Exemplo de Heathcheck com Dockerfile --

No arquivo:
   --interval=DURATION (default: 30s)
   --timeout=DURATION (default: 30s)
   --start-period=DURATION (default: 0s) (17.09+)
   --retries=N (default: 3)

Comando básico, usando opções default
   HEALTHCHECK curl -f http://localhost/ || false

Comando com opções customizadas
   HEALTHCHECK --timeout=2s --interval=3s --retries=3 \
      CMD curl -f http://localhost/ || exit 1


-- Exemplo: Healthcheck no Dockerfile NGinx --

   # Website estático executando em um NGinx
   # Apenas testa a URL padrão
   FROM nginx:1.13

   HEALTHCHECK --timeout=3s --interval=30s \
      CMD curl -f http://localhost/ || exit 1


-- Exemplo: Healthcheck in PHP Nginx Dockerfile --

   PHP-FPM running behind Nginx, test the Nginx 
   and FPM status URLs

   FROM your-nginx-php-fpm-combo-image
   # don't do this if php-fpm is another container
   # must enable php-fpm ping/status in pool.ini
   # must forward /ping and /status urls from nginx to php-fpm
   HEALTHCHECK --interval=5s --timeout=3s \
      CMD curl -f http://localhost/ping || exit 1


-- Exemplo: Healthcheck in postgres Dockerfile --

   Use a PostgreSQL utility to test for ready state

   FROM postgres
   # specify real user with -U to prevent errors in log
   HEALTHCHECK --interval=5s --timeout=3s \
      CMD pg_isready -U postgres || exit 1
   

-- Exemplo: Healthcheck in Compose/Stack Files --

   version: "2.1" (minimum for healthchecks)
      services:
         web:
            image: nginx
               healthcheck:
                  test: ["CMD", "curl", "-f", "http://localhost"]
                  interval: 1m30s
                  timeout: 10s
                  retries: 3
                  start_period: 1m #version 3.4 minimum


Na prática:

$ docker container run --name p1 -d postgres

-- nenhuma informação sobre a saúde do container é exibida
$ docker container ls

$ docker container run --name p2 -d --health-cmd="pg_isready -U postgres || exit 1" postgres

-- perceber a informação da verificação do status de saúde (health)
$ docker container ls

-- Procurar pela chave "Health" (histórico)
$ docker container inspect <id>


-- verificação dos 3 status
$ docker service create --name p1 postgres
$ docker service create --name p2 --health-cmd="pg_isready -U postgres || exit 1" postgres

-- Cleanup
$ docker container rm -f p1 p2
$ docker service rm p1 p2




# Seção 10: Container Registries: Image Storage and Distribution


# 10.81. Docker Hub - Aprofundando

-- Docker Hub - Docker Registry + Image Build
https://hub.docker.com/

-- Um Registry deve fazer parte do panejamento de um container (Não é opcional)
-- Diferença entre Docker Store (store.docker.com) e Docker Hub
-- Diferença entre Docker Cloud (cloud.docker.com) e Docker Hub
-- Uso do Docker Registry como uma loja privada de imagens
-- Outras opções de Registries


-- Docker Hub é o registry publico de imagens mais popular, mas não é o único
-- O Docker Registry é realmente mais leve na criação de imagens
-- Como fazer o link entre GitHub/BitBucket e Docker Hub e criar as imagens automaticamente ao se fazer um commit
---- Não usar a opção "Create Registry", e sim a opção "Create Automated Build", que permite o uso de "webhooks" com o GitHub

-- Encadear a criação de imagens



# 10.82. Docker Registry

-- Configuring a registry
https://docs.docker.com/registry/configuration/

-- Garbage collection
https://docs.docker.com/registry/garbage-collection/

-- Registry as a pull through cache
-- Uso do Registry como um "espelho" do Docker Hub
https://docs.docker.com/registry/recipes/mirror/


- Registry privado de imagens
- Parte do repositório GitHub docker/distribution
---- https://github.com/docker/distribution
- Não oferece todas as funcionalidades do Docker Hub ou outros, não tem interface gráfica web, aceita somente autenticação básica (sem controle de autorização: ou pode tudo, ou não pode)
- No seu Core: uma API web e sistema de armazenagem escrito em Go
- Suporta armazenagem local, S3/Azure/Alibaba/Google Cloud e OpenStack Swift
- Recomenda-se que a opção "--registry-mirror" seja habilitada para que seja feito o chache do Hub


# 10.83. Run a Private Docker Registry

- Executar na porta TCP padrão: 5000
- "Re-taggear" uma imagem existente e carregá-la para o novo registry
- Remover a imagem do cache local e fazer o pull a partir do novo Registry
- Re-criar o registry usando um "bind mount" e perceber como ele armazena os dados

-- Registry and Proper TLS --
- "Seguro por Padrão": Docker não fala com um registry sem HTTPS
- Exceto para localhost (127.0.0.0/8)
- Ou atribuir "insecure-registry" no engine, para remotos que usam TLS auto-assinados




%%%% arquivo: registry-sample-1/README.md %%%%%%%%%%%%%%%% 
# Commands for setting up a local registry

- enable insecure registries in Daemon

- Enable SSL
mkdir -p certs 
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt

docker run --rm -e COMMON_NAME=127.0.0.1 -e KEY_NAME=registry -v $(pwd)/certs:/certs centurylink/openssl
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


$ docker container run -d -p 5000:5000 --name registry registry
$ docker container ls


$ docker image ls

$ docker pull hello-world
$ docker run hello-world

$ docker tag hello-world 127.0.0.1:5000/hello-world

$ docker push 127.0.0.1:5000/hello-world

$ docker container rm <container em execução>

$ docker image remove hello-world
$ docker image remove 127.0.0.1:5000/hello-world

$ docker image pull 127.0.0.1:5000/hello-world


--

$ docker container kill registry
$ docker container rm registry
$ docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:/var/lib/registry registry
$ ll registry-data
$ docker image ls
$ docker push 127.0.0.1:5000/hello-world
$ ll registry-data
$ tree registry-data/


Recapitulação: Private Docker Registry Recap

- Run the registry image
$ docker container run -d -p 5000:5000 --name registry registry

- Re-tag an existing image and push it to your new registry
$ docker tag hello-world 127.0.0.1:5000/hello-world
$ docker push 127.0.0.1:5000/hello-world

- Remove that image from local cache and pull it from new registry
$ docker image remove hello-world
$ docker image remove 127.0.0.1:5000/hello-world
$ docker pull 127.0.0.1:5000/hello-world

- Re-create registry using a bind mount and see how it stores data
$ docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:/var/lib/registry registry



-- (***) Interessante
https://training.play-with-docker.com/



== Docker Registry com Swarm ==

- Funciona do mesmo modo que em "localhost"
- Devido a "malha de roteamento" (Routing Mesh), todos os nodes podem ver 127.0.0.1:5000
- Decidir como armazenar as imagens (volume driver)
- Importante: TODOS os nodes devem ser capazes de acessar as imagens
- Dica pró: Se possível, use um registry SaaS (Exceto se houver alguma restrição legal ou semelhante)

Os testes foram feitos no "labs.play-with-docker.com"

- inicializar com 5 managers
$ docker node ls

$ docker service create --name registry --publish 5000:5000 registry

$ docker srvice ps registry

-- para ver o catálogo (formato JSON)
http://pwd10-0-2-3-5000.host.labs.play-with-docker.com/v2/_catalog

$ docker pull hello-world

$ docker tag hello-world 127.0.0.1:5000/hello-world

$ docker push 127.0.0.1:5000/hello-world

-- verificar novamente o catálogo (formato JSON)
http://pwd10-0-2-3-5000.host.labs.play-with-docker.com/v2/_catalog

$ docker pull nginx

$ docker tag nginx 127.0.0.1:5000/nginx

$ docker push 127.0.0.1:5000/nginx

-- verificar novamente o catálogo (formato JSON)
http://pwd10-0-2-3-5000.host.labs.play-with-docker.com/v2/_catalog


$ docker service create --name nginx -p 80:80 --replicas 5 --detach=false 127.0.0.1:5000/nginx

$ docker service ps nginx



==========================================

-- Swarm Raft Quorum and Recovery (Laura Frank from DockerCon 2017
-- Everything You Thought You Already Knew About Orchestration
https://www.youtube.com/watch?v=Qsv-q8WbIZY




=======================================================
KUBERNETES
=======================================================


== Kubernetes Intro ==

-- History of Kubernetes
https://en.wikipedia.org/wiki/Kubernetes

-- Kubernetes Page
https://kubernetes.io/

O que é?
- Kubernetes = orquestrador de contêineres
- Orquestrador de Contêineres = Faz com que muitos servidores ajam como apenas um
- Lançado em 2015 pelo Google - Mantido pela comunidade open-source
- Nada mais é que uma camada executada sobre o Docker
-- Provê API/CLI para gerenciar contêineres distribuídos entre os servidores


-- Kubernetes Partners
https://kubernetes.io/partners/#conformance



Por quê?
- Rever "Swarm Mode" orquestração nativa"
- Orquestração: Próximo passo lógico na jornada para acelerar o DevOps
- Primeiro: entenda porque você "pode" precisar de orquestração
- Nem toda solução precisa de orquestração
- Como posso estimar o benefício da orquestração?  ==> Quantidade de Servidores * Taxa de Modificações
- Decidir qual orquestrador usar. Há outros, mas os mais populares são Swarm e K8s
- Se for K8s, decidir qual distribuição: Cloud ou auto-gerenciado (Docker Enterprise, Rancher, OpenShift, Canovical, VMWare PKS).


== Kubernetes vs Swarm ==

- Os dois são orquestradores de contêineres
- Swarm: Mais simples de implantar/gerenciar
- K8s: Mais funcionalidades e flexibilidade 
- Estude os dois e entenda quais são as suas necessidades

-- Vantagens do Swarm --
- Já vem com o Docker (único fornecedor)
- Mais simples de implantar e gerenciar
- Segue a regra 80/20: 20% de funcionalidades e 80% de casos de uso (Swarm resolve a maioria dos problemas)
- Seguro por padrão
- Simples de resolver problemas (troubleshoot)


-- Vantagens do K8s --
- A maioria dos Clouds suportam K8s e oferecem a solução
- Os vendors estão criando suas próprias distribuições
- Amplamente adotado pelacomunidade
- Flexível: Cobre a maior parte dos casos de uso



---------------------------------------------------
== Kubernetes - Instalação e Primeiro Pod ==
---------------------------------------------------


== Kubernetes - Terminologia da Arquitetura == 

- Kubernetes: Sistema de orquestração completo
- K8s
- K8s é uma série de contêineres, CLI's e configurações
- Kubectl (Kube Control): CLI para configurar o K8s e gerenciar apps
- Node (ou Worker): Um Server em um cluster K8s
- Kubelet: Agente K8s que executa em cada node, que possibilita ao node se comunicar de volta com o Master
- Control Plane (ou Master): Conjunto de contêineres que gerenciam o cluster
---- Inclui os contêineres: API Server, scheduler, controller manager, etcd, CoreDNS, etc.


== Kubernetes - Instalação Local ==

-- Download do minikube
https://github.com/kubernetes/minikube/releases/

-- MikroK8s para hosts Linux
https://github.com/ubuntu/microk8s

-- Instalação do kubectl no Windows
https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows

-- Katacoda - K8s no navegador - Playground
https://www.katacoda.com/courses/kubernetes/playground

-- Play with K8s no navegador
https://play-with-k8s.com

- Há muitas formas de fazer a instalação. Foco: no que for mais simples para aprender
 
- Docker Desktop: Forma mais simples (Preferencial)
--- Executa/configura contêineres K8s Master
--- Gerencia a instalação do kubectl e certificados
--- Facilita a instalação, a desabilitação e remoção através da Docker GUI

- Docker Toolbox: Usar o MiniKube
--- Download do instalador para Windows (GitHub)
--- Após a instalação, usar o "minikube start"
--- Bem parecido com a experiência do "docker-machine"
--- Cria uma VM Virtualbox com a configuração de um master K8s
--- Não instala o kubectl



- No seu Host ou VM Linux: MicroK8s 
--- A instalação é feita diretamente no S.O.
--- Instala o K8s (sem o Docker Engine) no localhost (Linux)
--- Usa o snap (em vez do apt ou yum) para instalar
--- Controla os serviços MicroK8s através de comandos "microk8s.algumacoisa"
--- O kubectl é acessível via "microk8s.kubectl"
--- Adicione um alias ao seu shell (~/.bash_profile)
----- alias kubectl=microk8s.kubectl


- Ou: Katacoda ou Play-With-K8s no navegador


(*) Versões usadas neste curso
- Após o lançamento da versão 1.18 do K8s, a sintaxe e a funcionalidade do comando "kubectl run" mudaram. O curso foi desenhado para versões entre 1.14 e 1.17, que são as principais versões usadas pelos "cloud vendors", Docker Desktop e "enterprise solutions"

A versão 1.18 vem como padrão nos instaladores do minikube e MicroK8s, mas é possível forçar a instalação da versão 1.17 (o que é recomendado para agora), ou ignorar algumas das instruções ininiais sobre o "kubectl run", porque ele não cria mais "deplotments/jobs/cronjobs/services...". Ele cria apenas "pods".

- versões do K8s
https://github.com/kubernetes/kubernetes/releases

(minikube)

- minikube - instalar versão específica do K8s
$ minikube start --kubernetes-version='1.17.4'

- diferentemente do Docker Descktop, que permite usar "localhost" para os serviços K8s, ominikube é executado em um VirtualBox (por padrão) e tem o seu IP próprio. Para encontrar o IP use "minikube ip"

- Lembrar de interromper o minikube quando não estiver susando, para não ocupar recursos da máquina:  "minikube stop"

- Verificar o status do que está rodando no minikube: "minikube status"

(MikroK8s)

- MikroK8s - instalar versão específica do K8s
$ sudo snap install mikrok8s --classic --channel=1.17/stable

- Antes de usá-lo, é necessário habilitar o pode CoreDNS para que ele resolva os nomes DNS dos serviços: "mikrok8s.enable dns"

- Verificar o status do que está rodando no MikroK8s: "mikrok8s.status"

== K8s Container Abstractions ==

-- Pod Overview
https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/

-- Service
https://kubernetes.io/docs/concepts/services-networking/service/

-- Namespace
https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/

Definições:

- Pod :: Um ou mais contêineres executando juntos em um Node
--- É a unidade básica de deploy. Os contêineres estão sempre em "pods". 

- Controller :: Cria/atualiza pods e outros objetos
--- Vários tipos de Controllers: Deployment, ReplicaSet, StatefulSet, DaemonSet, Job, CronJob, etc.

- Service: network endpoint para se conectar a um pod

- Namespace: Grupo filtrado de objetos em um cluster. Não é uma característica de segurança, é só uma forma de visualização usando comandos do kubectl



== Kubectl command styles - run, create & apply ==

- Temos 3 formas para criar pods com o CLI kubectl
- kubectl run (mudando para fazem somente a criação de pod)
- kubectl create (cria alguns recursos via CLI ou YAML)
- kubectl pply (cria/atualiza qualquer coisa via YAML)


== First Pod - Usando kubectl run ==


-- kubectl Cheat Sheet
https://kubernetes.io/docs/reference/kubectl/cheatsheet/

-- kubectl for Docker Users (***) Boa analogia!
https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/

-- Tá funcionando? 
$ kubectl version

-- As duas formas de fazer o deploy de Pods (containers): Via comandos ou via YAML

-- Exemplo: Executando um pod de um nginx web server (ignorar os alertas por enquanto)
$ kubectl run my-nginx --image nginx

-- Listar os pods
$ kubectl get pods

-- Listar todos os objetos 
$ kubectl get all

-- Pods --> ReplicaSet --> Deployment

-- Cleanup
$ kubectl delete deployment my-nginx
$ kubectl get all


(*) Mudanças no "kubectl run" após a v1.18

A partir da versão 1.18, o "kubectl run" faz apenas uma coisa: cria pods.
Há muitas razões pra isso, mas uma das principais é reduzir a complexidade de como o comando "run" trabalha e mover a tarefa de criação de recursos para o comando "kubectl create". A ideia é tornar o "kubectl run" similar ao "docker run". Ele cria apenas um pod, assim como o "docker run" cria apenas um container.

(Tabela de comparação)

- Antes da versão 1.18 :: cria um Deployment chamado "nginx" (que cria uma ReplicaSet, que cria um Pod)
$ kubectl run nginx --image nginx

- Após a versão 1.18 :: cria um Pod chamado "nginx"
$ kubectl run nginx --image nginx

- pós a versão 1.18 :: cria um Deployment
$ kubectl create deployment nginx --image nginx


== Scaling ReplicaSets ==

Criação de mais Pods com "kubectl scale"

- Início: Um deploy para uma réplica/pod
$ kubectl run my-apache --image httpd

$ kubectl get all

-- Scalando com outro pod
$ kubectl scale deploy/my-apache --replicas 2
ou
$ kubectl scale deployment my-apache --replicas 2

$ kubectl get all

== Inspecting K8s Objects ==
== Inspect Deployment Objets ==

-- Stern : Multi pod and container log tailing for Kubernetes 
https://github.com/wercker/stern

$ kubectl get pods

-- Get container logs
$ kubectl logs deployment/my-apache --follow --tail 1

$ kubectl logs -l run=my-apache

Obtendo vários detalhes sobre um objeto, incluindo os eventos (Similar ao inspect do Docker)

$ kubectl get pods
$ kubectl describe pod/my-apache-xxxx-yyyy

Watch a command (para ver o pod ser recriado)
$ kubectl get pods -w

-- em um tab separado
$ kubectl delete pod/my-apache-xxxx-yyyy



== (14) Exposing K8s Ports ==

(*) Services

-- Service
https://kubernetes.io/docs/concepts/services-networking/service/

-- Service Types (Tutorial)
https://kubernetes.io/docs/tutorials/services/

== Service Types :: Expondo Pods com Services ==

-- Definição dos quatro tipos de Service --

-- Exposição de Contêineres

- "kubectl expose" cria um "service" para um pod
- Um "service" é um endereço estável para pod(s). É um endpoint consistente, que deve ser acessível de dentro e de fora do cluster. 
- Para se conectar a um pod(s), precisamos de um "service" (sobre o pod). Quando um pod é criado no K8s, um nome DNS não é automaticamente fornecido para conexão externa via endereço IP. 
- CoreDNS possibilita resolver/encontrar "services" pelo nome. Há várias formas além do comando "expose" para criar um "service", mas o foco agora é fazer o "mais comum", que é usar o CoreDNS.
- São 4 os tipos de "services" :: ClusterIP, NodePort, LoadBalancer e ExternalName

(*) ClusterIP  (default) - Basic Service Types --
- Disponível apenas no cluster (um conjunto de pods se comunicando com outro conjunto de pods)
- Um único IP virtual interno é alocado
- Os Pods podem acessar/alcançar o serviço (service) pelo número do Port da app.
- Serviço sempre disponível no K8s (quando se pensa em fornecedores K8s)

(*) NodePort - Basic Service Types --
- Um pouco diferente do ClusterIP. Foi desenhado para permitir que "algo fora do cluster" se comunique com o "service" através de endereços IP dos nodes.
- "Portas Altas" (acima de 1024 - Range: 30000-32767 -- Isso pode ser configurado) são alocadas em cada node. Não dá pra alocar o port 80, por exemplo.
- O Port é aberto em cada IP do node.
- Qualquer um pode se conectar (se puder alcançar o node)
- Outros pods precisam ser atualizados para essa porta (port)
- Serviço sempre disponível no K8s (quando se pensa em fornecedores K8s)

(*) LoadBalancer - Basic Service Types --
- Comumente usado na nuvem
- Controla um endpoint LoadBalancer externo ao cluster, através da linha de comando K8s.
- O que é feito nos bastidores: Quando criamos um LoadBalancer, são automaticamente criados ClusterIP e NodePort e os tornam disponíveis, e ficam disponíveis para o mundo externo.
- Só está disponível quando o provedor de infra fornece um LB (Exemplo: AWS ELB, etc)
- Cria um par de serviços NodePort+ClusterIP, e diz ao LB para enviar ao NodePort
- Basicamente é só uma automação que nos ajuda a economizar um passo ao executar algo na linha de comando AWS, ou interface web, para configurar o LB automaticamente.

(*) ExternalName - Basic Service Types --
- Pouco usado
- É usado quando algo interno do cluster precisa se comunicar com o mundo externo e para isso depende da resolução de nomes
- Adiciona um registro CNAME DNS ao CoreDNS
- Não é usado para Pods, mas para dar aos pods um nome DNS para acessar algo "fora" do Kubernetes

(*) Kubernetes Ingress 
- mais adiante




== Criação de um serviço ClusterIP ==
-- Exposição de Deployments --

- Using a Service to Expose Your App (Tutorial)
https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/

1o - criar um Deployment para podermos apontar os serviços
2o - criar diferentes tipos de serviços que apontam para este Deployment

- Abrir dois terminais/shells para acompanhar
- No primeiro shell
1$ kubectl get pods -w

- No segundo shell, inicie um servidor http simples usando o código de exemplo
2$ kubectl create deployment httpenv --image=bretfisher/httpenv

- Escalar para 5 replicas
$ kubectl scale deployment/httpenv --replicas=5

- Criar um serviço ClusterIP (padrão)
$ kubectl expose deploymnet/httpenv --port 8888

[[Execução:]]

SHELL01$ kubectl get pods -w

SHELL02$ kubectl create deployment httpenv --image=bretfisher/httpenv

SHELL02$ kubectl scale deployment/httpenv --replicas=5

SHELL02$ kubectl expose deploymnet/httpenv --port 8888


-- Inspecionar o serviço ClusterIP --

- Verificar qual IP foi alocado
$ kubectl get service

- Lembrar que este IP é usado apenas internamente pelo cluster. Como acessá-lo através do curl? Neste caso será usado um "macete" com o "kubectl run", que funciona de forma semelhante ao "docker run". Neste caso será levantado um pod e acessaremos o shell
$ kubectl run --generator run-pod/v1 tmp-shell --rm -it --image bretfisher/netshoot -- bash
$ curl httpenv:8888

- Se estiver em um host Linux
$ curl [ip-do-serviço]:8888

[[Execução:]]
SHELL02$ kubectl get service

SHELL02$ kubectl run --generator run-pod/v1 tmp-shell --rm -it --image bretfisher/netshoot -- bash

SHELL-POD# curl httpenv:8888





== Criação de um serviço NodePort e LoadBalancer ==

- NodePort
https://kubernetes.io/docs/concepts/services-networking/service/#nodeport

(*) Para executar esses passos, é necessário ter seguido os passos da sessão anterior, pois precisaremos do Deployment criado

-- verificar se está tudo lá
SHELL$ kubectl get all


- Expor um NodePort para que possamos acessá-lo através do IP do host (incluindo localhost em ambiente Windows/Linux/macOS)
$ kubectl expose deployment/httpenv --port 8888 --name httpenv-np --type NodePort

- Um serviço NodePort cria também um ClusterIP.

- Os 3 tipos de serviço são aditivos, ou seja, cada um deles cria o que está abaixo dele:
-- ClusterIP <-- NodePort <-- LoadBalancer

[[Execução:]]

SHELL01$ kubectl get all

SHELL01$ kubectl expose deployment/httpenv --port 8888 --name httpenv-np --type NodePort

SHELL01$ kubectl get services

-- Ou o port que for informado na listagem
SHELL01$ curl localhost:32334



-- Adicionar um serviço LoadBalancer

- Se estiver executando em um Docker Desktop (Windows ou Mac), é fornecido um LoadBalancer nativo que publica o "--port" no localhost
$ kubectl expose deployment/httpenv --port 8888 --name httpenv-lb --type LoadBalancer
$ curl localhost:8888

- Se estiver executando em um kubeadm, minikube ou MikroK8s :: Não fornece um Loadbalancer nativo. Você ainda pode executar o comando, ele permanecerá em "pendente" (mas o NodePord funciona)


-- Cleanup (antes de prosseguir)

$ kubectl delete service/httpenv service/httpenv-np
$ kubectl delete service/httpenv-lb deployment/httpenv



== Kubernetes Service DNS :: Básico sobre CoreDNS ==

- Kubernetes DNS-Based Service Discovery (especificação)
https://github.com/kubernetes/dns/blob/master/docs/specification.md

- CoreDNS for Kubernetes
https://www.coredns.io/plugins/kubernetes/

- Desde a versão 1.11, o DNS interno é tratado pelo CoreDNS (antes havia o KubeDNS, que foi depreciado)
- Assim como no Swarm, é um "DNS-Based Service Discovery" :: Quando um serviço é criado, você obtém o hostname que corresponde ao serviço, mas o hostname faz parte de um nome "comprido", o "fully qualified domain name" (FQDN).
- Até agora temos usado "hostnames" para acessar Serviços (Exemplo: $ curl <hostname>)
- Mas isso só funciona para Services em um mesmo Namespace ($ kubectl get namespaces)
- Os serviços também têm um FQDN
--> curl <hostname>.<namespace>.svc.cluster.local

-- Namespaces do K8s são similares aos Stacks do Swarm (mas não tanto... Namespaces são parâmetros organizacionais)


== (15) Kubernetes Management Techniques ==

== Run, Expose, and Create Generators ==

-- Kubectl Usage Conventions
https://kubernetes.io/docs/reference/kubectl/conventions/

- Estes comandos usam templates auxiliares chamados "Generators"
- Cada recurso no K8s tem uma especificação ou "spec"
$ kubectl create deployment sample --image nginx --dry-run -o yaml
- Pode-se obter a saída desses templates com as opções "--dry-run -o yaml"

Exemplos de Generators

- Usar o "dry-run" com a saída yaml possibilita visualizar os generators

$ kubectl create deployment test --image nginx --dry-run -o yaml

$ kubectl create job test --image nginx --dry-run -o yaml

- É necessário que o "deployment" exista antes do próximo comando funcionar
$ kubectl create deployment test --image nginx
$ kubectl expose deployment/test --port 80 --dry-run -o yaml


- Cleanup
$ kubectl delete deployment test



== O futuro do "kubectl run" ==

-- kubectl Usage - Best Practices
https://kubernetes.io/docs/reference/kubectl/conventions/#best-practices

- Ainda funciona (v1.12-v1.15)
- A meta é reduzir suas funcionalidades para apenas criar Pods e reduzir a sua complexidade
- -- Atualmente o padrão é criar Deployments (apresentando warnings)
- -- Tem vários "gererators", mas todos eles foram depreciados
- -- A ideia é torná-lo tão fácil como o "docker run", com apenas uma tarefa
- Não é recomendado usar em produção
- Usar apenas para casos simples de dev/testes ou pods para resolução de problemas
- Tem sido usado até o momento devido a questões históricas, mas deve-se usar o "kubectl create" de agora em diante.

-- Confusão com o velho Run --
- Os "generators" ativam diferentes Controllers baseado nas opções
- Usar o "dry-run" possibilita a visualização de quais "generators" são usados
- Cada uma das execuções a seguir mostra que o "run" executa um "generator" diferente (Deployment, Deployment+Service, Job, CronJob), com a respectiva mensagem de alerta. Isso é o que gera a maior confusão e torna o comando "run" complexo
-> $ kubectl run test --image nginx --dry-run
-> $ kubectl run test --image nginx --port 80 --expose --dry-run
-> $ kubectl run test --image nginx --restart OnFailure --dry-run
-> $ kubectl run test --image nginx --restart Never --dry-run
-> $ kubectl run test --image nginx --schedule "*/1 * * * *" --dry-run



== Imperative vs. Declarative ==

- Imperativo: Foco em "como" o proggrama opera (cada passo, em ordem, um após o outro)
- Declarativo: Foco em "o quê" um programa deve realizar (Qual é o objetivo final?)

Exemplo: "Eu gostaria de uma xícara de café"
- Imperativo: Eu fervo a água, despejo 42 gramas de café solúvel na xícara, despejo 200ml de água, etc.
- Declarativo: "Barista, eu gostaria de uma xícara de café". (O barista é o "motor" que realiza os passos, incluindo possíveis novas tentativas de se fazer uma xícra de café, e somente finaliza quando eu receber uma xícara de café)

[[Kubernetes Imperative]]

- Exemplos: kubectl run, kubectl create deployment, kubectl update
-> Começamos com um estado que conhecemos (não existe deployment)
-> Solicitamos ao "kubectl run" para criar um "deployment"
- Diferentes comandos são necessários para modificar o "deployment"
- Diferentes comandos são necessários "por objeto"
- O modo Imperativo é mais fácil quando conhecemos o "estado"
- O modo Impertivo é mais fácil quando estamos começando 
- O modo Impertivo é mais fácil para humanos no CLI (linha de comando)
- O modo Impertivo NÃO é fácil para automação


[[Kubernetes Declarative]]

- Exemplo: kubectl apply -f my-resources.yaml
-> Não conhecemos o estado atual
-> Conhecemos apenas o que desejamos como resultado final (conteúdo do yaml)
- A cada vez o mesmo comando (poucas exceções para "delete")
- Os recursos podem estar todos em um arquivo, ou vários arquivos (aplicando um diretório inteiro)
- É necessário entender as chaves e valores do YAML
- É bem mais trabalhoso que um simples "kubectl run" para iniciar um simples "pod"
- É a forma mais fácil para automatizar a orquestração
- É um possível caminho feliz para o GitOps


== Três abordagens de gerenciamento do K8s ==

-> Kubernetes Object Management (Techniques)
https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/

-> Managing Kubernetes Objects Using Imperative Commands (examples)
https://kubernetes.io/docs/tasks/manage-kubernetes-objects/imperative-command/

-> Imperative Config Files
https://kubernetes.io/docs/tasks/manage-kubernetes-objects/imperative-config/

-> Declarative Config Files
https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/

- *Comandos Imperativos*: run, expose, scale, edit, create deployment
-> Ideal para projetos de desenvolvimento, aprendizado ou pessoais
-> Fácil de aprender, difícil de gerenciar ao longo do tempo

- *Objetos Imperativos*: "create -f file.yml", "replace-f file.yml", "delete..."
-> Bom para ambientes de produção pequenos, um único arquivo por comando
-> Armazena as modificações em arquivos yaml (baseados no Git), o que promove uma documentação e registro histórico
-> Difícil de automatizar


- *Objetos Declarativos*: "apply -f file.yml" ou dir\, diff
-> Ideal para ambiente produtivo, mais fácil de automatizar
-> Mais difícil de entender e prever modificações
-> Similar ao Docker Swarm: "docker stack deploy"

- *Regra Importante*
-> Não misturar as três abordagens

- *Recomendações*
-> Aprenda o CLI Imperativo para controlar facilmente o seu ambiente local e configuração de testes
-> Para ambientes produtivos, mude para "apply -f file.yml" e "apply -f diretorio\"
-> Armazene os arquivos yaml no Git, e faça o commit de cada modificação antes de aplicá-la
-> Isso o treina para que depois faça o GitOps (em que os commits no Git são automaticamente aplicados nos clusters)


== (16) Moving to Declarative Kubernetes YAML ==

== Kubectl apply (116) ==

-> Declarative Management of Kubernetes Objects Using Configuration Files
https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/

- Por que não focar em: kubectl create, kubectl replace e kubectl edit?
-> Porque não é essencialmente um bom caminho para gerenciar a infraestrutura de produção. Não apresentam uma estrutura sólida de DevOps, e o que interessa agora é exatamente o que está relacionado ao estilo de containers DevOps. Você pode usá-los, mas a recomendação aqui é que não torne isso uma prática.
- A forma preferencial para DevOps
-> $ kubectl apply -f filename.yml

[[Exemolos de uso do kubectl apply]]

- criar/atualizar recursos a partir de um arquivo
-> $ kubectl apply -f myfile.yaml

- criar/atualizar recursos a partir de um diretório contendo arquivos yaml
-> $ kubectl apply -f mydir/

- criar/atualizar recursos a partir de uma URL 
-> $ kubectl apply -f https://bret.run/pod.yml

- Seja cuidadodo, dê uma olhada antes (navegador ou curl)
-> $ curl -L https://bret.run/pod




== K8s Configuration YAML (117) ==
-- Uso de Manifests para controlar recursos --

-> Understanding Kubernetes Objects
https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/

- O YAML do K8s é um pouco mais complexo que os "Compose Files", mas possibilita fazer muito mais coisas
- Os arquivos de configuração podem ser escritos em YAML ou JSON, mas é preferível que sejam escritos em YAML, porque YAML é mais simples para ser lido e manipulado por humanos, enquanto o JSON é melhor para computadores.
- Nos bastidores o YAML é convertido para o formato JSON
- Cada arquivo contém um ou mais "manifestos". Um manifesto é a descrição completa de um recurso.
- Cada manifesto descreve um objeto da API (deployment, job, secret). 
- Cada manifesto precisa de 4 partes (root key:values no arquivo), sendo:
-> apiVersion:
-> kind:
-> metadata:
-> spec:

Alguns exemplos:

-- Único Pod
-- Não é algo que faríamos, mas é o arquivo de configuração mais simples
%%% arquivo: k8s-yaml/pod.yml %%%%%%%%%%%%%
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.17.3
    ports:
    - containerPort: 80
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

- Deployment de duas réplicas do mesmo container
%%% arquivo: k8s-yaml/deployment.yml %%%%%%
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.3
        ports:
        - containerPort: 80
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


-- Combinação de serviço e deployment
-- Perceber que a separação entre manifestos é feita com três hífens "---"
%%% arquivo: k8s-yaml/app.yml %%%%%%%%%%%%%
apiVersion: v1
kind: Service
metadata:
  name: app-nginx-service
spec:
  type: NodePort
  ports:
  - port: 80
  selector:
    app: app-nginx
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: app-nginx
  template:
    metadata:
      labels:
        app: app-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.3
        ports:
        - containerPort: 80
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


== Building your YAML files (118) ==
-- Comandos para ajudar "do zero" --

[[Root Keys]]

- "kind:" - Podemos obter uma lista de recursos que o cluster suporta (essa lista pode variar e ser acrescida com itens específicos de terceiros, que podem engordar a API. A maioria nem mesmo usaremos, então não perder tempo memorizando. A coluna KIND dessa listagem é o que nos interessa nesse momento.) - A coluna APIGROUP é reclacionda à versão da API
-> $ kubectl api-resources

- Perceber que alguns recursos têm multiplas APIs (old vs. new)

- "apiVersion:" - Podemos obter uma lista das versões da API que o cluster suporta.
-> $ kubectl api-versions

- "kind:" + "apiVersion:" - são usadas em conjunto para decidir qual recurso será usado e qual versão de API será escolhida para usar esse recurso. Antes de criar o YAML, é preciso ter isso em mãos.

- "metadata:" - apenas o nome é requerido

- "spec:" - É onde todas as ações estão. Muda completamente dependendo do recurso que está sendo criado. 




== Building your YAML Spec (119) ==
-- Comandos para descobrir Specs --

- Como obter "todas" as "keys" que cada "kind" suporta
$ kubectl explain <name-of-kind> --recursive
$ kubectl explain services --recursive

- Obter mais detalhes, apenas sobre a "spec" do recurso
$ kubectl explain <name-of-kind>.spec
$ kubectl explain services.spec

- Aprofundando um pouco mais na spec
$ kubectl explain <name-of-kind>.spec.type
$ kubectl explain services.spec.type

- "spec:" pode ter sub "spec:" de outros recursos
$ kubectl explain deployment.spec.template.spec.volumes.nfs.server

-> Recomenda-se consultar a documentação (contém alguns exemplos)
-> K8s API Reference
https://kubernetes.io/docs/reference/#api-reference




== Dry Runs and Diff's (120) ==
-- Testar o YAML antes do Deploy --

-> APIServer dry-run and kubectl diff
https://kubernetes.io/blog/2019/01/14/apiserver-dry-run-and-kubectl-diff/

- As novas versões do K8s iniciando na v1.13 traz essa nova opção que nos permite ver o que será modificado antes de realmente ser modificado.
- Antes tínhamos o "dry-run", que só funcionava do lado cliente
- Agora temos uma opção de "dry-run" no servidor que envia o YAML para o servidor e compara, descobre o que realmente precisa acontecer, e retorna para a linha de comando o que está sendo modificado.

- Ver como funciona, usando o arquivo: k8s-yaml/app.yml (vide acima)

- dry-run a create (client side only)
$ kubectl apply -f app.yml --dry-run

- dry-run a create/update on server
$ kubectl apply -f app.yml --server-dry-run

- para visualizar a diferença que ocorrerá
-$ kubectl diff -f app.yml


== Labels and Label Selectors (121) ==

-> Label selectors
https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors

-> Difference between Labels and Annotations in Kubernetes
https://vsupalov.com/kubernetes-labels-annotations-difference/

-> Recommended Labels
https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/

-> Assigning Pods to Nodes
https://kubernetes.io/docs/concepts/configuration/assign-pod-node/

-> Taints and Tolerations
https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/

- Os "Labels" ficam abaixo do "metadata:" no YAML
- Lista simples de "key: value" que identificam os recursos para posterior seleção, agrupamento ou filtro.
- Raramente usado para descrever o objeto. Os recursos que estamos criando têm diferentes propriedades.
- Exemplos comuns incluem:  "tier: frontend", "app: api", "env: prod", "customer: acme.co"
- "Labels" são muito simples, tem limitação de tamanho e caracteres que podem ser usados. São feitos para descrever o recurso. Não serve para coisas complexas, grandes ou sem identificação.
- "Annotations" são para dados mais complexos e dados "multi-valorados", ou coisas parecidas (dados de configuração, como proxies e "ingress stuff")
 
- Filtrar por "labels", usando "get"
$ kubectl get pods -l app=nginx

- Há muitas outras opções para se fazer esses filtros. Verificar a documentação do K8s.

- "Aplicar" as modificações somente nos itens que combinam com o "label" informado
$ kubectl apply -f myfile.yml -l app=nginx



[[Label Selector]]

- A "cola" que diz aos Services e Deployments quais "pods" são deles
- Vários recursos usam "Label Selectors" para fazer um "link" com os recursos dependentes.
- Pode-se ver essa combinação no YAML de Service e Deployment
- Ver como funciona, usando o arquivo: k8s-yaml/app.yml (vide acima)
-> No "spec:" do "Service", tem um "selector:", que nos informa a "key & value" da "label" para os pods para os quais ele precisa direcionar o tráfego.
-> No "spec:" do "Deployment", tem um "selector:" próprio "matchLabels, app, app-nginx"
- Ambos os objetos estão selecionando "pods" baseados na combinação desses labels (match). Isso significa que no template abaixo da nossa "spec:", estamos modelando os "pods" que possuem o "label" indicado

- Use Labels e Selectors para controlar quais pods vão para quais nodes
- Taints e Tolerants também controlam o que pode ser colocado no node (diz aos nodes que eles não devem ter certas coisas). Evitar o uso até que seja realmente necessário.

- Cleanup
$ kubectl get all
$ kubectl delete <resource-type>/<resource-name>


== (17) Próximos passos e o futuro do K8s ==

== Storage in Kubernetes (123) ==

- Volumes
https://kubernetes.io/docs/concepts/storage/volumes/

- StatefulSets
https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/

- PersistentVolume
https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/

- O storage e as "stateful workloads" são muito difíceis em todos os sistemas
- Os contêineres tornam tornam as coisas mais difíceis e mais fáceis do que antes
- "StatefulSets" é um novo tipo de recurso, tornando Pods mais "aderentes"
- Recomendação: Evitar "stateful workloads" nos deploys iniciais
-> Usar "db-as-a-service" sempre que puder
- Criar e conectar Volumes: 2 tipos
-> "Volumes" (inserir um template dentro do Spec no YAML) 
---> É amarrado ao ciclo de vida do Pod
---> Todos os contêineres de um único Pod podem compartilhá-lo
-> "PersistentVolumes"  (claim) 
---> Usado ou definido "fora" do Pod. É criado no nível do "cluster".
---> Separa a configuração do storage da configuração do Pod que está usando o storage
---> Pode ser compartilhado com vários Pods
---> O plugin CSI (Container Storage Interface) é a nova forma de se conectar ao storage




== Ingress (124) ==
(Layer 7 Control of HTTP)

- Ingress
https://kubernetes.io/docs/concepts/services-networking/ingress/

- Ingress Controllers
https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/

- Set up Ingress on Minikube with the NGINX Ingress Controller
https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

- Traefik & Kubernetes
https://docs.traefik.io/v2.0/providers/kubernetes-crd/#traefik-ingressroute-definition

- Nenhum dos nossos tipos de Service trabalham na camada 7 da OSI (HTTP)
- Como "rotear" conexões para fora, baseando-se no "hostname" ou URL?
- "Ingress Controllers" (opcional) faz isso usando proxies de terceiros
-  O NGinx é popular, mas temos também o Traefik, HAProxy, F5, Enviy, Istio, etc.
-  A implementação é específica para o Controller escolhido
-  




== CRD's and the Operator Pattern (125) ==

- Custom Resources
https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/

- Operator pattern
https://kubernetes.io/docs/concepts/extend-kubernetes/operator/

- OperatorHub.io
https://operatorhub.io/

- Awesome Operators List
https://github.com/operator-framework/awesome-operators

- Pode-se adicionar Resources e Controllers de terceiros (não apenas usar itens do K8s)
- Isso extende a API e o CLI do K8s
- Um padrão está emergindo para usar essas coisas juntas
- Operator: automatiza o deploy e o gerenciamento de apps complexas
- Exemplo: Databases, ferramentas de monitoração (como o Prometheus), backups, "ingresses" customizados






== Higher Deployment Abstractions (126) ==
(Deployment Options - Higher Abstractions for Apps)

- [Planilha] Kubernetes application management tools
https://docs.google.com/spreadsheets/d/1FCgqz1Ci7_VCz_wdh8vBitZ3giBtac_H8SBw4uxnrsE/edit#gid=0

- Deploying compose-on-kubernetes 
https://github.com/docker/compose-on-kubernetes/tree/master/docs

- Introducing kustomize; Template-free Configuration Customization for Kubernetes
https://kubernetes.io/blog/2018/05/29/introducing-kustomize-template-free-configuration-customization-for-kubernetes/

- kustomize
https://github.com/kubernetes-sigs/kustomize

- Docker App - Make your Docker Compose applications reusable, and share them on Docker Hub 
https://github.com/docker/app

- CNAB Spec - Cloud Native Application Bundle
https://cnab.io/

- Todos os comandos do "kubectl" falam apenas com a API K8s
- K8s tem um conjunto limitado de templates, versionamento, rastreamento e gerenciamento de suas apps
- Há no momento mais de 60 ferramentas de terceiros para fazer isso, mas a maioria delas estão extintas
- "Helm" é o mais popular. A maioria das distribuições dão suporte a ele.
- "Compose on Kubernetes" vem com o Docker Desktop

[Templating YAML]
- Muitas das ferramentas de deploy oferecem opções de "template"
- Uma solução se torna necessária conforme o número de ambientes/apps cresce
- O "Helm" contínua sendo o líder nesse caso, mas pode ser complexo
- O "Kustomize" funciona bem (desde a v.1.14)
- "docker app" e "compose-on-kubernetes" são opções





== Kubernetes Dashboard (127) ==
(Web GUI para gerenciamento de cluster)

- K8s Dashboard
https://github.com/kubernetes/dashboard

- GUI padrão
- Algumas distribuições oferecem a própria GUI (Rancher, Docker Ent, OpenShift)
- As Clouds não oferecem isso por padrão. Você tem que fazer o deploy
- Cuidado com os ataques. Havendo uma porta de entrada, os hacker (principalmente os mineradores de bitcoins) tentarão se beneficiar das vulnerabilidades de frameworks web, cross-site scripting, etc. Proteger de alguma forma (random high ports, VPN, etc).



== Namespaces and Context (128) ==

- Configure Access to Multiple Clusters
https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/

- Namespaces
https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/

- Namespaces limitam o escopo (conhecidos como "virtual clusters")
- Não está relacionado aos namespaces do Docker/Linux 
- Não precisaremos deles em pequenos clusters
- Há algumas coisas nativas para esconder coisas do sistema dos usuários "kubectl"

$ kubectl get namespaces
$ kucectl get all --all-namespaces

- Context modifica o namespace e o cluster "kubectl"
- Ver o arquivo "~/.kube/config" para perceber as 3 partes de um contexto: cluster, a autenticação ou usuário, e o namespace.

$ kubectl config get-contexts
- praticamente apresenta o conteúdo desse arquivo
- kubectl config set*




== Future Kubernetes (129) ==

- K8s v1.18 Release Notes
https://kubernetes.io/docs/setup/release/notes/

- Maior foco na estabilidade e segurança
-> v1.14 e v1.15 são releases bem maiores (o que é bom)
-> Auditoria de segurança recebte criou um backlog
- Limpeza de itens depreciados como os generators do "kubectl run"
- Melhoria de características como o "server-side dry-run"
- Mais Operators e melhorias
- Helm 3.0 (facilidade no deploy, chart repos, libs)
- Knative - Serverless workloads on Kubernetes
- k3s - mini, simple K8s
- k3OS - Minimal Linux OS for k3s
- Service Mesh - Nova camada no tráfego de apps distribuídas, para melhorar o controle, a segurança e a monitoração








== (18) Docker Security Good Defaults and Tools ==


== Section Intro: Top 10 Security Steps for Docker (130) ==

- What security concerns should I have with Docker? How should I go about locking it down?
https://github.com/BretFisher/ama/issues/17


== Docker Cgroups and Namespaces (131) ==

- Docker security
https://docs.docker.com/engine/security/security/

- 29 Docker security tools compared.
https://sysdig.com/blog/20-docker-security-tools/

== Docker Engine's Out-Of-The-Box Security Features (132) ==

- AppArmor security profiles for Docker
https://docs.docker.com/engine/security/apparmor/

- Seccomp security profiles for Docker
https://docs.docker.com/engine/security/seccomp/


== Docker Bench, The Host Configuration Scanner (133) ==

- docker-bench-security
https://github.com/docker/docker-bench-security

- CIS Docker Benchmarks
https://www.cisecurity.org/benchmark/docker/


== Using USER in Dockerfiles to Avoid Running as Root (134) ==

- dockercon19 - Sample Dockerfile of using USER
https://github.com/BretFisher/dockercon19/blob/master/1.Dockerfile

- Docs: USER Stanza in Dockerfiles
https://docs.docker.com/engine/reference/builder/#user

== Docker User Namespaces for Extra Host Security (135) ==

- More Info on User Namespaces
https://integratedcode.us/2015/10/13/user-namespaces-have-arrived-in-docker/

- Docs: How to Use User Namespace
https://docs.docker.com/engine/security/userns-remap/


== Code Repo and Image Scanning for CVE's (136) ==

- CVE Database
https://cve.mitre.org/

- Snyk, one of the ways to scan your Git repos and Images
https://snyk.io/

- What is Shift Left Security?
https://www.twistlock.com/resources/shift-left-security/

- Aqua MicroScanner, to scan your your images during docker build
https://github.com/aquasecurity/microscanner

- Aqua Trivy, to scan your images after their built
https://github.com/aquasecurity/trivy

== Sysdig Falco, Content Trust, and Custom Seccomp and AppArmor Profiles (137) ==

- Sysdig Falco
https://sysdig.com/opensource/falco/

- Docker Content Trust
https://docs.docker.com/engine/security/trust/content_trust/

- Docs: Seccomp security profiles
https://docs.docker.com/engine/security/seccomp/

- Docs: AppArmor profiles
https://docs.docker.com/engine/security/apparmor/

== Docker Rootless Mode (138) ==

- Rootless mode
https://github.com/docker/engine/blob/v19.03.0-rc3/docs/rootless.md

- Experimenting with Rootless Docker
https://www.docker.com/blog/experimenting-with-rootless-docker/

- Hardening Docker daemon with Rootless mode
https://www.youtube.com/watch?v=Qq78zfXUq18

- Rootless mode Install Script
https://get.docker.com/rootless

== The Security Top 10 Differences for Windows Containers (139) ==

== What are Distroless Images? (140) ==

-  GoogleContainerTools / distroless
https://github.com/GoogleContainerTools/distroless


== Are Swarm and Kubernetes Secrets Really Secure? (141) ==





== (19) Docker 19.03 Release New Features ==
== 142. Section Intro ==
== 143. BuildKit and the new Docker buildx CLI ==
== 144. Docker Context and SSH Connections ==
== 145. Docker App and Image Packaging of Compose YAML ==
== 146. Rootless Mode in Docker Engine ==
== 147. Docker Desktop Enterprise ==
== 148. Docker Desktop Enterprise Clusters ==


== (20) DevOps and Docker Clips ==
== 149. DevOps and Docker Talk Intro ==
- YouTube Live Channel - Bret Fisher Docker and DevOps
https://www.youtube.com/channel/UC0NErq0RhP51iXx64ZmyVfg 
- Podcast - DevOps and Docker Talk
https://www.bretfisher.com/podcast/
== 150. Alpine Base Images. Are They Really More Secure? ==
- Alpine Linux
https://alpinelinux.org/
- CVE Database
https://cve.mitre.org/
- Blog on CVE Scanners and their effectiveness on Alpine images
https://kubedex.com/follow-up-container-scanning-comparison/
== 151. Dealing With Non-root Users In Containers and File Permissions ==
== 152. Apache Web Server Design. Many Sites In One Container, or Many Cont ==
== 153. Docker Network IP Subnet Conflicts with Outside Networks ==
- Three places to control different docker IP subnet settings
https://serverfault.com/questions/916941/configuring-docker-to-not-use-the-172-17-0-0-range/942176#942176
== 154. Raspberry Pi Development in Docker ==
- Alex Ellis' Raspberry PI blog post
https://blog.alexellis.io/tag/raspberry-pi/
- QEMU Emulator in Docker Desktop
https://docs.docker.com/docker-for-mac/multi-arch/
== 155. Windows 10 Containers Get Process Isolation ==
- Setting up Windows 10 Docker Process Isolation
https://stefanscherer.github.io/how-to-run-lightweight-windows-containers-on-windows-10/
- Moby/Moby PR for Process Isolation on Win10
https://github.com/moby/moby/pull/38000
== 156. Should You Move Postgres to Containers ==
- HPE and Docker Whitepaper on MySQL performance
https://d.pr/f/Zjv65z/1BuMbZ8p
- Sysbench and Docker Setups to Benchmark Physical, Virtual, and Container-based MySQL
https://github.com/BretFisher/sysbench-docker-hpe
== 157. Using Supervisor To Run Multiple Apps In A Container ==
- Docker Docs on Supervisor and Multiple Services In Containers
https://docs.docker.com/config/containers/multi-service_container/
- Bret's Example of PHP-FPM plus Nginx Supervisor Config
https://github.com/BretFisher/php-docker-good-defaults/blob/master/supervisord.conf
== 158. Should You Use Docker Compose or Swarm For A Single Server? ==
- Only one host for production, should I use docker-compose or Swarm
https://github.com/BretFisher/ama/issues/8
== 159. Docker Environment Configs, Variables, and Entrypoints ==
- Twelve Factor Apps: The Config
https://12factor.net/config
- Sample PHP Dockerfile with lots of ENV
https://github.com/BretFisher/php-docker-good-defaults/blob/master/Dockerfile
- Those Same ENV's overwritten with docker-compose.yml
https://github.com/BretFisher/php-docker-good-defaults/blob/master/docker-compose.yml
- Simple example of using Docker ENV's to create custom app config
https://github.com/BretFisher/php-docker-good-defaults/blob/master/docker-php-entrypoint
- Docker MySQL Official Image Entrypoint Script that creates ENV's from files (for secrets)
https://github.com/docker-library/mysql/blob/a7a737f1eb44db467c85c8229df9d886dd63460e/8.0/docker-entrypoint.sh#L21-L41
- 3 Docker Compose Features for Improving Team Development Workflow
https://www.oreilly.com/content/3-docker-compose-features-for-improving-team-development-workflow/
== 160. Java and JBoss in Containers. One .war File Per Container? ==
== 161. TLS in Dev and Prod with Docker ==
- Using TLS for Localhost
https://letsencrypt.org/docs/certificates-for-localhost/
- My Examples of using Traefik with Swarm
https://github.com/BretFisher/dogvscat
- Traefik Proxy
https://containo.us/traefik/
== 162. Multiple Docker Images From One Git Repo ==
- Docker Build Documentation
https://docs.docker.com/engine/reference/commandline/build/
== 163. Docker + ARM, Using Raspberry Pi or AWS A1 Instances with Docker ==
- Docker and ARM announcement
https://www.theregister.co.uk/2019/04/24/docker_arm_collaberation/
- Docker Mastery for Node.js
https://www.bretfisher.com/docker-mastery-for-nodejs/
== 164. Docker and Swarm RBAC Options ==
== 165. ENTRYPOINT vs. CMD, what's the difference in Dockerfiles ==
- ENTRYPOINT in Dockerfiles
https://docs.docker.com/engine/reference/builder/#entrypoint
- ENTRYPOINT Best Practices
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#entrypoint
- ENTRYPOINT vs CMD
http://www.johnzaccone.io/entrypoint-vs-cmd-back-to-basics/
== 166. How to Use External Storage in Docker ==
== 167. Can I Turn a VM into a Container? == 
- image2docker for windows container
https://github.com/docker-archive/communitytools-image2docker-win
- image2docker linux container
https://github.com/docker-archive/communitytools-image2docker-linux
- image2docker demo
https://www.youtube.com/watch?v=YVfiK72Il5A


== 168. Startup Order With Multi-Container Apps ==
- The 12 Factor App
https://12factor.net/


== (21) Dockerfile and Compose File Reviews ==
== 169. Section Intro - My Reviews Of Your Dockerfiles ==
== 170. Real World PHP Dockerfile Review ==
== 171. Real World PHP, Apache, and Alpine Dockerfile Review ==
== 172. Real World PHP and FPM Dockerfile Review ==
== 173. Real World Elasticsearch Compose Stack File ==


== (22) Extra's, Common Questions, and Resources ==
== 174. Node.js Good Defaults For Docker ==
== 175. PHP and Docker Good Defaults Project Template ==
== 176. About the DCA (Docker Certificated Associate) ==
== 177. Bonus ==
