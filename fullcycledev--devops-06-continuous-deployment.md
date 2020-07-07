# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# DevOps -  Continuous Deployment
---

# 1. Conceitos de CD

## **Continuous Delivery** ou **Continuous Deployment**

**Continuous Delivery** - Capacidade de entregar software rapidamente (agilizar os releases). Desenvolvimento -> Processo de CI --> QA --> Deploy (manual).

**Continuous Deployment** - Capacidade de fazer tudo o que foi dito anteriormente, e que o deploy também seja totalmente automatizado.

Há situações em que um é mais adequado que o outro. Em um processo mais burocratizado, que depende de regulamentações, talvez o mais adequado é não automatizar tudo.


# 2. Configurando Ambiente

## O que será feito?

O repositório git "laravel" será usado:
- Ao fazer o push para qualquer branch que não seja o "master", será executado o processo de CI;
- Se o o push for feito no branch "master", serão executados os processos de CI e CD;
- Teremos então duas regras/gatilhos para trabalhar com o Google Cloud Build;
- Teremos também um `cloudbuild.yaml` destinado para o desenvolvimento e outro destinado ao ambiente produtivo;
- Também serão duas versões do Dockerfile, pois em um deles não usará um volume externo e precisaremos copiar o conteúdo para a imagem a ser gerada;

**Passo 1** - Criar um novo cluster no GCP

O cluster criado anteriormente e será criado um novo cluster (apenas um node -> `dfdx-docker-laravel`)

Fazer a conexão ao cluster:

```
$ gcloud container clusters get-credentials dfdx-docker-laravel --zone us-central1-c --project dfdx-279419
Fetching cluster endpoint and auth data.
kubeconfig entry generated for dfdx-docker-laravel.
```

**Passo 2** - Remover os Triggers do Cloud Build

Remover os acionadores/triggers criados anteriormente para o repositório `zanatabr/fullcycle-devops-laravel`

**Passo 3** - Criar novo Trigger do Cloud Build

Criar novo trigger apontando para o branch `master` do repositório `zanatabr/fullcycle-devops-laravel`.

A configuração utilizará o arquivo `cloudbuild.prod.yaml`, que fará o processo de CI e de CD.

**Passo 4** - Iniciar a modificação do código fonte

Iniciar uma nova feature:

```
$ git flow feature start cd
Switched to a new branch 'feature/cd'

Summary of actions:
- A new branch 'feature/cd' was created, based on 'develop'
- You are now on branch 'feature/cd'

Now, start committing on your feature. When done, use:

     git flow feature finish cd
```

Criar os diretórios para armazenar os descritores do Kubernetes:

```
(feature/cd)$ mkdir k8s
(feature/cd)$ mkdir k8s/app
(feature/cd)$ mkdir k8s/mysql
(feature/cd)$ mkdir k8s/redis
(feature/cd)$ mkdir k8s/nginx
```

# 3. Configurando Mysql e Redis

**Configuração do MySQL** - Será aproveitada a configuração feita na seção anterior.

Criação do secret com a senha do MySQL
```
$ kubectl create secret generic mysql-pass --from-literal=password='a1s2d3f4'
secret/mysql-pass created
```

**arquivo:** ./projeto/k8s/mysql/mysql.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-server  
spec:  
  replicas: 1
  selector: 
    matchLabels:
      app: mysql-server
      tier: db

  template: 
    metadata:
      labels:
        app: mysql-server
        tier: db
    spec:   
      containers:
      - name: mysql-server
        image: mysql:5.7
        args:  
          - "--ignore-db-dir=lost+found"
        
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mysql-pass
              key: password

        ports: 
        - containerPort: 3306

        volumeMounts: 
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql

      volumes: 
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim

---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  ports:
  - port: 3306
  selector:
    app: mysql-server
    tier: db
  clusterIP: None
```

Aplicação do descritor do mysql no cluster:

```
(feature/cd)$ kubectl apply -f k8s/mysql/mysql.yaml 
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/mysql-server created
service/mysql-service created
```

Verificar no Dashboard do GCP se tudo foi criado corretamente.


**Configuração do Redis**

**arquivo:** ./projeto/k8s/redis/redis.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis  
spec:  
  selector: 
    matchLabels:
      app: redis
  replicas: 1
  template: 
    metadata:
      labels:
        app: redis
        role: master
    spec:   
      containers:
      - name: master
        image: redis
        ports: 
        - containerPort: 6379

---

apiVersion: v1
kind: Service
metadata:
  name: redis-service
  labels:
    app: redis-service
    role: master
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis
    role: master
```

Aplicação do descritor do redis no cluster:

```
(feature/cd)$ kubectl apply -f k8s/redis/redis.yaml 
deployment.apps/redis-master created
service/redis-service created
```

Verificar no Dashboard do GCP se tudo foi criado corretamente.


# 4. Configurando Nginx

**arquivo:** ./projeto/k8s/nginx/nginx.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-conf

data:
  nginx.conf: |
    server {
      listen 80;
      index index.php index.html;
      root /usr/share/nginx/html;

      location ~ \.php$ {
          try_files $uri =404;
          fastcgi_split_path_info ^(.+\.php)(/.+)$;
          fastcgi_pass app-service:9000;
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

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:

      - name: nginx
        image: nginx:1.17-alpine

        ports: 
        - name: http
          containerPort: 80

        volumeMounts:

        - mountPath: /etc/nginx/conf.d
          name: nginx-conf
          readOnly: true

      volumes:

      - name: nginx-conf

        configMap:
          name: nginx-conf
          items:
            - key: nginx.conf
              path: nginx.conf

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector: 
    app: nginx
  type: LoadBalancer
  ports:
  - name: http
    port: 80
```


Aplicação do descritor do nginx no cluster:

```
(feature/cd)$ kubectl apply -f k8s/nginx/nginx.yml 
configmap/nginx-conf created
deployment.apps/nginx created
service/nginx-service created
```

Verificar no Dashboard do GCP se tudo foi criado corretamente.

Deverá ocorrer um erro no Pod do NGnix, pois ainda não criamos o "app-service", de quem o NGnix depende (conforme a configuração feita no nginx.conf):

```
(feature/cd)$ kubectl logs nginx-5955bbb48f-rhllt
2020/07/05 20:13:25 [emerg] 1#1: host not found in upstream "app-service" in /etc/nginx/conf.d/nginx.conf:9
nginx: [emerg] host not found in upstream "app-service" in /etc/nginx/conf.d/nginx.conf:9
```


# 5. Configurando Deploy da App

**arquivo:** ./projeto/k8s/app/app.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-conf

data:
  env: |
    APP_NAME=docker-laravel
    APP_ENV=local
    APP_KEY=base64:MpG5uaeGmcnLXQpfLwCa4w6aYNc3AEM0Zq0Yx9h9rT8=
    APP_DEBUG=true
    APP_URL=http://localhost
    LOG_CHANNEL=stack
    DB_CONNECTION=mysql
    DB_HOST=mysql-service
    DB_PORT=3306
    DB_DATABASE=laravel
    DB_USERNAME=root

    BROADCAST_DRIVER=log
    CACHE_DRIVER=file
    QUEUE_CONNECTION=sync
    SESSION_DRIVER=redis
    SESSION_LIFETIME=120

    REDIS_HOST=redis-service
    REDIS_PASSWORD=null
    REDIS_PORT=6379

    MAIL_MAILER=smtp
    MAIL_HOST=smtp.mailtrap.io
    MAIL_PORT=2525
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null

    AWS_ACCESS_KEY_ID=
    AWS_SECRET_ACCESS_KEY=
    AWS_DEFAULT_REGION=us-east-1
    AWS_BUCKET=

    PUSHER_APP_ID=
    PUSHER_APP_KEY=
    PUSHER_APP_SECRET=
    PUSHER_APP_CLUSTER=

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: app  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:

      - name: app
        # a imagem vai variar de acordo com o deploy
        image: img-app-deployment

        ports: 
        - name: http
          containerPort: 80

        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        
        envFrom:
        - configMapRef:
            name: app-conf

        volumeMounts:
        - name: app-conf
          mountPath: /var/www/.env
          subPath: .env # substitui ou cria o .env

      volumes:
      - name: app-conf
        configMap:
          name: app-conf
          items:
            - key: env
              path: .env

---

apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  ports:
  - name: http
    protocol: TCP
    port: 9000
  selector: 
    app: app
  clusterIP: None
```

Aplicação do descritor do app no cluster:

```
(feature/cd)$ kubectl apply -f k8s/app/app.yaml 
configmap/app-conf created
deployment.apps/app created
service/app-service created
```

Verificar no Dashboard do GCP se tudo foi criado corretamente.


# 6. Configurando Cloudbuild e Nginx

O conteúdo do arquivo `cloudbuild.yaml` foi copiado para o arquivo `cloudbuild.prod.yaml` que servirá como base para criarmos o descritor do pipeline de produção:

**arquivo:** cloudbuild.prod.yaml
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

- id: "Build da imagem a partir do Dockerfile.prod"
  name: 'gcr.io/cloud-builders/docker'
  args: ['build','-t','gcr.io/$PROJECT_ID/app-laravel:$SHORT_SHA', '-f', 'Dockerfile.prod', '.']

- id: "Push da imagem"
  name: 'gcr.io/cloud-builders/docker'
  args: ['push','gcr.io/$PROJECT_ID/app-laravel:$SHORT_SHA']
```

O conteúdo do arquivo `Dockerfile` foi copiado para o arquivo `Dockerfile.prod` que servirá como base para criarmos o arquivo de build da imagem de produção:

**arquivo:** Dockerfile.prod
```
FROM php:7.4.6-fpm-alpine3.11

RUN apk update \
    && apk add --no-cache openssl bash mysql-client nodejs npm php7-pear php7-dev gcc musl-dev make \
    && docker-php-ext-install pdo pdo_mysql \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && docker-php-source delete \
    && rm -r /tmp/* /var/cache/* 

WORKDIR /var/www
RUN rm -rf /var/www/html

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer 

# faz a cópia do que está na máquina host para a imagem em construção
COPY . /var/www

RUN composer install \
    && php artisan config:cache \
    && chown -R www-data:www-data /var/www \   
    && chmod -R 775 /var/www/storage

RUN ln -s public html

EXPOSE 9000
ENTRYPOINT ["php-fpm"]
```

Vide: Installing PHP extensions from source in your Dockerfile
https://olvlvl.com/2019-06-install-php-ext-source


## Fechamento da feature "cd"

```
(feature/cd)$ git add .
(feature/cd)$ git commit -m "Adiciona itens para preparar o CD no GCP"
```

A feature não será finalizada agora através do git flow. Será feito um push para o repositório no github, com a intenção de que as modificações sejam revisadas por alguém/time.

```
(feature/cd)$ git push origin feature/cd
```

Lá no GitHub, podemos optar por criar um "pull request". Baseado no branch "cd", quero enviar para o "develop".

```
(feature/cd)$ git flow feature finish cd
```

```
(develop)$ git pull origin develop
```

Não teve modificação de código, mas teve uma nova entrada no log.

```
$ git push origin develop
Everything up-to-date
```

Fazer o merge da branch "master" com origem na branch "develop", e depois observar o disparo da trigger no GCB.

Verificar se a imagem foi gerada e carregada no registry.


## Ajuste do Deployment do app, para substituir a tag da imagem dinamicamente

Foi acrescentado um passo no arquivo `cloudbuild.prod.yaml`:

**arquivo:** cloudbuild.prod.yaml
```
...
- id: "Ajuste do arquivo de Deployment do K8s"
  name: 'gcr.io/cloud-builders/gcloud'
  entrypoint: '/bin/sh'
  args: 
    - '-c'
    - |
      sed 's#img-app-deployment#gcr.io/$PROJECT_ID/app-laravel:$SHORT_SHA#g' k8s/app/app.yaml > k8s/app/app-new.yaml;
      cat .k8s/app/app-new.yaml;
```

```
(develop)$ git add .
(develop)$ git commit -m "Ajuste do arquivo de Deployment do K8s"
(develop)$ git push origin develop
```

No Github, fazer o merge com a branch "master".

## Deploy automático no Kubernetes

Foi acrescentado um passo no arquivo `cloudbuild.prod.yaml`:

**arquivo:** cloudbuild.prod.yaml
```
...
- id: "Deploy no Kubernetes"
  name: 'gcr.io/cloud-builders/kubectl'
  args: 
    - 'apply'
    - '-f'
    - 'k8s/app/app-new.yaml'
  env:
    - 'CLOUDSDK_COMPUTE_ZONE=us-central1-c'
    - 'CLOUDSDK_CONTAINER_CLUSTER=dfdx-docker-laravel'
```

```
(develop)$ git add .
(develop)$ git commit -m "Adiciona passo para deploy automatico no K8s"
(develop)$ git push origin develop
```

No Github, fazer o merge com a branch "master".


## Ajuste no Deployment do nginx

Ao fazer o teste acessando o endpoint do serviço "nginx" pelo navegador, recebemos a página padrão do NGinx.

Isso ocorre porque ao receber o request, o NGnix encontra o arquivo `index.html` no seu diretório "root". 

Para resolver o problema, basta criar um arquivo `index.php` vazio no mesmo diretório. 

**arquivo (trecho modificado):** ./projeto/k8s/nginx/nginx.yaml
```
...
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:

      - name: nginx
        image: nginx:1.17-alpine
        command: ["/bin/sh","-c","touch /usr/share/nginx/html/index.php; nginx -g 'daemon off;'"]

        ports: 
        - name: http
          containerPort: 80
...
```


Aplicação do descritor do nginx no cluster:

```
(develop)$ kubectl apply -f k8s/nginx/nginx.yml
configmap/nginx-conf unchanged
deployment.apps/nginx configured
service/nginx-service unchanged
```

Para validar o que fizemos:

```
(develop)$ kubectl get pods
NAME                            READY   STATUS             RESTARTS   AGE
app-5f95df6796-92kbv            0/1     ImagePullBackOff   0          100m
mysql-server-664bccb9bd-5wqwt   1/1     Running            0          3h42m
nginx-cf5458b7f-sj5ks           1/1     Running            0          104s
redis-master-796b9bd9b8-44z92   1/1     Running            0          3h25m
```

Verificar se o arquivo foi realmente criado no container:

```
(develop)$ kubectl exec -it nginx-cf5458b7f-sj5ks sh
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl kubectl exec [POD] -- [COMMAND] instead.
/ # cd /usr/share/nginx/html/
/usr/share/nginx/html # ls -la
total 20
drwxr-xr-x    1 root     root          4096 Jul  5 22:59 .
drwxr-xr-x    1 root     root          4096 Apr 24 12:58 ..
-rw-r--r--    1 root     root           494 Apr 14 14:46 50x.html
-rw-r--r--    1 root     root           612 Apr 14 14:46 index.html
-rw-r--r--    1 root     root             0 Jul  5 22:59 index.php
```

# 7. Resolvendo Permissão do Kubernetes

Ainda temos um problema. No passo em que é feito o deploy no Kubernetes, ocorre um erro de permissão:

```
Step #10 - "Deploy no Kubernetes": ERROR: (gcloud.container.clusters.get-credentials) ResponseError: code=403, message=Required "container.clusters.get" permission(s) for "projects/dfdx-279419/zones/us-central1-c/clusters/dfdx-docker-laravel".
Finished Step #10 - "Deploy no Kubernetes"
ERROR
ERROR: build step 10 "gcr.io/cloud-builders/kubectl" failed: step exited with non-zero status: 1
```

Isso ocorre porque falta configurar a permissão para que o GCP possa executar o `kubectl` no nosso cluster.

Para isso, devemos acessar **IAM e Administração** e editar o item **Conta de serviço do Cloud Build** (Cloud Build Service Account).

Um **Service Account** é uma conta criada especificamente para um serviço, para que este serviço consiga se comunicar com outros serviços.

Ao editar essa conta, deveremos adicionar um novo **Role/Papel**: *Kubernetes Engine Admin*.

Feito isso, faremos uma nova tentativa de execução da trigger que falhou.


## Ajuste para que a app encontre o conteúdo correto a ser exibido

Ao acessar o endpoint da *app*, de acordo com a configuração que fizemos no *nginx*, o conteúdo "buscado" deveria estar no diretório `/var/www/public`, mas na criação da imagem (no Dockerfile) copiamos o conteúdo para o diretório `/var/www`.

Podemos resolver isso modificando o `Dockerfile`, criando um link simbólico para o diretório `/var/www` ou fazendo a cópia para do conteúdo diretamente pra lá. Esse seria o correto, ou modificar a configuração do `nginx` para apontar para o diretório `/var/www`. 

Outra forma de se fazer isso seria executar o comando de criação do link simbólico durante a criação do Pod, conforme feito no trecho de código a seguir. É uma **gambiarra**, mas será feito assim apenas para demonstrar a funcionalidade:

**arquivo (trecho):** k8s/app/app.yaml
```
...
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:

      - name: app
        # a imagem vai variar de acordo com o deploy
        image: img-app-deployment
        command: ["/bin/sh","-c","ln -s /var/www /usr/share/nginx; php-fpm;"]
...
```

```
(develop)$ kubectl apply -f k8s/app/app.yaml 
configmap/app-conf unchanged
deployment.apps/app configured
service/app-service unchanged
```

# 8. Retoques Finais

## Mas... e as migrações?!

Até agora foi executado o install do composer, mas não foram executadas as migrações de bd. Nem mesmo o banco de dados "laravel" foi criado no MySQL. 

Teste rápido pra ver se está tudo Ok:

```
$ kubectl get pods
NAME                            READY   STATUS    RESTARTS   AGE
app-889bf7f7f-bn7kl             1/1     Running   0          35m
mysql-server-664bccb9bd-crtff   1/1     Running   0          41m
nginx-cf5458b7f-9kqf5           1/1     Running   2          41m
redis-master-796b9bd9b8-xms8g   1/1     Running   0          41m
```

```
$ kubectl exec -it app-889bf7f7f-bn7kl -- sh
/var/www #
```

```
/var/www # cat .env 
APP_NAME=docker-laravel
APP_ENV=local
APP_KEY=base64:MpG5uaeGmcnLXQpfLwCa4w6aYNc3AEM0Zq0Yx9h9rT8=
APP_DEBUG=true
APP_URL=http://localhost
LOG_CHANNEL=stack
DB_CONNECTION=mysql
DB_HOST=mysql-service
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root

BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=sync
SESSION_DRIVER=redis
SESSION_LIFETIME=120

REDIS_HOST=redis-service
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=
```

Limpeza do cache de configuração:

```
/var/www # php artisan config:cache
Configuration cache cleared!
Configuration cached successfully!
```

Tentativa de executar a migração
```
/var/www # php artisan migrate

   Illuminate\Database\QueryException 

  SQLSTATE[HY000] [1049] Unknown database 'laravel' (SQL: select * from information_schema.tables where table_schema = laravel and table_name = migrations and table_type = 'BASE TABLE')
...
```
\* Deu erro, pois o BD 'laravel' não existe.


```
/var/www # ping mysql-service
PING mysql-service (10.28.2.9): 56 data bytes
64 bytes from 10.28.2.9: seq=0 ttl=62 time=1.280 ms
64 bytes from 10.28.2.9: seq=1 ttl=62 time=0.316 ms
64 bytes from 10.28.2.9: seq=2 ttl=62 time=0.309 ms
^C
```

Criação do BD 'laravel':
```
/var/www # mysql -uroot -hmysql-service -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.7.30 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.002 sec)

MySQL [(none)]> create database laravel;
Query OK, 1 row affected (0.019 sec)

MySQL [(none)]> exit
Bye
```

Execução das migrações:
```
/var/www # php artisan migrate
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table (0.04 seconds)
Migrating: 2019_08_19_000000_create_failed_jobs_table
Migrated:  2019_08_19_000000_create_failed_jobs_table (0.02 seconds)
```

\* Agora deu tudo certo.


## Como fazer para que a cada release as migrações sejam executadas?


**arquivo:** ~/projeto/k8s/entrypoint.sh
```
#!/bin/bash
php artisan config:cache
php artisan migrate
```

Aplicar a permissão de execução no aarquivo de script:
```
$ chmod +x ~/projeto/k8s/entrypoint.sh
```

Modificação do entrypoint da app:

**arquivo (trecho):** k8s/app/app.yaml
```
...
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:

      - name: app
        # a imagem vai variar de acordo com o deploy
        image: img-app-deployment
        command: ["/bin/sh","-c","ln -s /var/www /usr/share/nginx; /var/www/k8s/entrypoint.sh; php-fpm;"]
...
```

```
(develop)$ git add .
(develop)$ git push origin develop
```

```
(develop)$ kubectl apply -f k8s/app/app.yaml 
configmap/app-conf unchanged
deployment.apps/app configured
service/app-service unchanged
```



# 9. Desafio final 

Parabéns! Você chegou a nossa fase final onde você consolidará seus conhecimentos participando de todo ciclo de deploy de uma aplicação.

Nessa fase, o objetivo principal será fazer o deploy automático da aplicação já desenvolvida em Go Lang nas fases passadas.

As regras do processo são:
- Quando qualquer push ou uma PR for relizada no Github em um branch diferente do Master, o processo de CI tem que ser executado.
- Quando um merge ou um push entrarem no branch Master, o processo de CI/CD deve ser chamado, fazendo assim o deploy de forma automática no Kubernetes.

Faça o upload de todo código em um repositório, incluindo os do Cloudbuild, para que possamos fazer a avaliação.

Boa sorte e conte sempre conosco!