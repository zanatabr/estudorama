# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# Microsserviço: Catálogo de Vídeos - Pontapé Inicial
---

# 1. Apresentação

Instrutor: Luiz Carlos - Code Education

# 2. Criando aplicação Laravel 

- Objetivo
  - Criação do microsserviço de vídeo
- Requisitos
  - É bom conhecer um pouco mais sobre o Laravel
  - App feita com Laravel configurada e preparada para ser usada em container (Docker)
  - Usar a mesma app desenvolvida no módulo de DevOps (ver: desafio Docker)
  - Instrutor fornecerá "esqueleto" da App no GitHub
    - https://github.com/codeedu/laravel-microservice-quickstart

## Preparação

### Clonagem do código de startup

```
$ mkdir ~/curso-fullcycle/catalogo
$ git clone https://github.com/codeedu/laravel-microservice-quickstart
$ mv laravel-microservice-quickstart fullcycle-catalog-ms-videos
$ git remote remove origin
$ git remote add origin git@github.com:zanatabr/fullcycle-catalog-ms-videos.git
$ git push -u origin master
```

### Teste inicial

```
$ cd ~/curso-fullcycle/catalogo/fullcycle-catalog-ms-videos
$ docker-compose up -d
Creating network "fullcycle-catalog-ms-videos_app-network" with driver "bridge"
Creating micro-videos-redis ... done
Creating micro-videos-db    ... done
Creating micro-videos-app   ... done
Creating micro-videos-nginx ... done
```

Navegador: http://localhost:8000

```
$ docker logs micro-videos-app
2020/07/15 13:23:12 Waiting for: tcp://db:3306
2020/07/15 13:23:12 Connected to tcp://db:3306
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 86 installs, 0 updates, 0 removals
  - Installing doctrine/inflector (v1.3.0): Downloading (100%)         
  - Installing doctrine/lexer (1.1.0): Downloading (100%)         
  - Installing dragonmantank/cron-expression (v2.3.0): Downloading (100%)         
  - Installing erusev/parsedown (1.7.3): Downloading (100%)         
  - Installing symfony/polyfill-ctype (v1.12.0): Downloading (100%)         
  - Installing phpoption/phpoption (1.5.0): Downloading (100%)         
  - Installing vlucas/phpdotenv (v3.5.0): Downloading (100%)         
  - Installing symfony/css-selector (v4.3.4): Downloading (100%)         
...
```

# 3. Errata - Conhecendo o Laravel 6.0

## Upgrade para a versão 6.0 do Laravel

- Da versão 5.x para 6.x
  - https://laravel.com/docs/6.x/upgrade
- Da versão 6.x para 7.x 
  - https://laravel.com/docs/7.x/upgrade
- Ver
  - https://github.com/laravel/framework


# 4. Errata - Criando aplicação Laravel

(None)


# 5. Errata - Migrando Laravel 5.8 para Laravel 6.0

(None)

# 6. Dicas sobre Docker no Window

(None)


# 7. Configuração do ambiente de desenvolvimento
# 8. Melhorando autocomplete no Laravel
# 9. Dicas sobre Debug no Laravel 