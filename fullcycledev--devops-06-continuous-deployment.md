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
      root /var/www/public;

      location ~ \.php$ {
          try_files $uri =404;
          fastcgi_split_path_info ^(.+\.php)(/.+)$;
          fastcgi_pass app-service:9000;
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
# 6. Configurando Cloudbuild
# 7. Configurando Cloudbuild e Nginx
# 8. Resolvendo Permissão do Kubernetes
# 9. Retoques Finais
# 10. Desafio final 