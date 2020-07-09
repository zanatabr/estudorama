# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# DevOps -  Kubernetes no GCP
---

# 1. Iniciando com Kubernetes no GCP

Opção: Kubernetes Engine -> Clusters

Iniciaremos com um cluster de apenas um node.

- Usar a opção "Criar cluster". Configurações de cluster:
- Nome do cluster: dfdx-cluster-1
- Zona do cluster: us-central1-c
- Pool de Nós
  - Name: dfdx-pool-1
  - Número de nós: 1
  - Tipo de máquina: g1-small 
  - Tamanho do disco de inicialização (por nó): 10 GB
- Kubernetes Engine Monitoring: desativado 


# 2. Fazendo deploy de recursos

## Configuração

O primeiro passo é configurar a conexão do kubectl local ao cluster Kubernetes no GCP.

Na listagem de clusters criados, clicar em "Conectar". Será apresentado um popup informando a linha de comando a ser executada na máquina local:

```
gcloud container clusters get-credentials dfdx-cluster-1 --zone us-central1-c --project dfdx-279419
```

Antes de executar, é necessário instalar o Cloud SDK (vide informações em: https://cloud.google.com/sdk/docs/quickstarts?hl=pt-br).

```
$ curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-293.0.0-linux-x86_64.tar.gz
$ tar zxvf google-cloud-sdk-293.0.0-linux-x86_64.tar.gz google-cloud-sdk
$ ./google-cloud-sdk/install.sh
```

Fazer a autenticação:

```
$ gcloud auth login
```

Executar a instrução fornecida para conectar ao cluster:
```
$ gcloud container clusters get-credentials dfdx-cluster-1 --zone us-central1-c --project dfdx-279419
```

Verificar informações do cluster:
```
$ kubectl cluster-info
Kubernetes master is running at https://34.68.180.99
GLBCDefaultBackend is running at https://34.68.180.99/api/v1/namespaces/kube-system/services/default-http-backend:http/proxy
KubeDNS is running at https://34.68.180.99/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://34.68.180.99/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

## Aplicação dos descritores referentes ao NGinx

Para exemplificar, serão aplicados os descritores gerados nas aulas anteriores:

**arquivo:** configmap.yaml
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

      rewrite ^/google$ https://google.com permanent;
    }
```

**arquivo:** deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-nginx  
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-nginx
  template:
    metadata:
      labels:
        app: hello-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17-alpine
        ports: 
        - containerPort: 80

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
```

**arquivo:** service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector: 
    app: hello-nginx
  type: LoadBalancer
  ports:
  - port: 80
```


Aplicação do **ConfigMap**:

```
$ kubectl apply -f configmap.yaml 
configmap/nginx-conf created
```

Verificar no Dashboard do GCP se a configuração foi criada.


Aplicação do **Deployment**:

```
$ kubectl apply -f deployment.yaml 
deployment.apps/hello-nginx created
```

Verificar no Dashboard do GCP, em **Cargas de Trabalho** se a configuração foi criada.


Aplicação do **Service**:

```
$ kubectl apply -f service.yaml 
service/nginx-service created
```

Verificar no Dashboard do GCP, em **Serviços e Entradas** (Services & Ingress) se a configuração foi criada.

Feita a criação do serviço, verificar o IP fornecido e testar o acesso pelo navegador.


## Aplicação dos descritores referentes ao MySQL

Descritor do **PersistentVolumeClaim**:

**arquivo:** persistent-volume.yaml
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
```

Aplicação do descritor do **PersistentVolumeClaim**:

```
$ kubectl apply -f persistent-volume.yaml 
persistentvolumeclaim/mysql-pv-claim created
```

Verificar no Dashboard do GCP, em **Armazenamento**, se a configuração foi criada.

Criação do **Secret** com a senha do MySQL:

```
$ kubectl create secret generic mysql-pass --from-literal=password='a1s2d3f4'
secret/mysql-pass created
```

Verificar no Dashboard do GCP, em **Configuração**, se a configuração foi criada.


**arquivo:** deployment-mysql.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-server  
spec:  # spec do Deployment
  replicas: 1

  selector:  # selector usado para criar o service
    matchLabels:
      app: mysql-server
      tier: db

  template:  # referente ao POD
    metadata:
      labels:
        app: mysql-server
        tier: db
    spec:    # spec do POD
      containers:
      - name: mysql-server
        image: mysql:5.7
        args:  # pulo-do-gato: ver anotações
          - "--ignore-db-dir=lost+found"
        
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mysql-pass
              key: password

        ports: 
        - containerPort: 3306

        volumeMounts: # monta o volume efetivamente
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql

      volumes: # definição do volume
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```

Aplicação do descritor do **Deployment**:

```
$ kubectl apply -f deployment-mysql.yaml 
deployment.apps/mysql-server created
```

Verificar no Dashboard do GCP, em **Cargas de Trabalho** se a configuração foi criada.


Aplicação do **Service**:

**arquivo:** service-mysql.yaml
```
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

```
$ kubectl apply -f service-mysql.yaml 
service/mysql-service created
```

Verificar no Dashboard do GCP, em **Serviços e Entradas** se a configuração foi criada.

Neste caso não teremos nenhum IP publicado para acesso externo (endpoint).


# 3. Fazendo upgrade do cluster

Como fazer deploy de recursos quando não se tem CPU ou quantidade de memória livre suficientes?

Foi feito um teste, aumentando o número de réplicas, até que o cluster mostrasse a sua insuficiência para atender a solicitação.

Os pods excedentes ficam pendentes até que surjam recursos liberados para atender a solicitação (Ex.: Derrubar outro serviço), mas não é isso o que se quer.

## O que se quer é realmente fazer um upgrade

No cluster, em **Node Pools** podemos *adicionar um novo pool*, com mais nodes.


# 4. Gerenciamento de recursos :: Requests

Como controlar a quantidade de memória e CPU usados pelos PODs?

O Kubernetes tem um conceito chamado de **Requests** (solicitações, requisições). 

Sempre que um Pod for aprovisionado, é "requerido" uma quantidade de Memória e de CPU, e o Kubernetes sabe se tem ou não recursos covCPUmputacionais para atender essa requisição. Se o K8s não tiver esses recursos disponíveis, deixará o Pod pendente até que os recursos solicitados sejam liberados ou surjam novos recursos (novas instâncias).

## Como saber quais recursos estão disponíveis?

Em **clusters**, acessar os detalhes de um cluster criado, e acessar **nodes**.

Cada máquina que compõe o cluster apresenta dados como:

- CPU requested (requerido pelos Pods)
- CPU allocatable (Total disponibilizado inicialmente)
- Memory requested (memória requerida pelos Pods)
- Memory allocatable (disponibilizado inicialmente)

Cada node do cluster usa uma **vCPU** (assim como na AWS ou outro provedor de cloud computing). A unidade usada para medir a quantidade de CPU alocada/requerida em um node é o **mCPU** (milicpu, que é uma fração da **vCPU**).

Outro termo usado é o **"millicore"** que, de forma análoga, seria uma fração do **"core"**.

Exemplo de alocação de **cpu** (vide seção **resources**):

**arquivo:** deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-nginx  
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-nginx
  template:
    metadata:
      labels:
        app: hello-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17-alpine
        ports: 
        - containerPort: 80

        resources:
          requests:
            # memory: "64Mi"
            cpu: "200m"

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
```

No exemplo acima, cada **Pod** requer **200mCPU** para que seja iniciado. Foram feitos alguns testes aumentando o número de **réplicas**, até que não houvessem mais recursos disponíveis.

Neste tópico foi visto o **"quanto"** cada **"Pod"** **solicita** (requer) para que seja carregado. Ou seja, é o mínimo requerido.

O próximo tópico apresentará uma forma de **limitar** a quantidade de recursos para cada Pod, ou seja, o máximo permitido.


# 5. Trabalhando com limites


Em um cenário qualquer, quando não se configura o máximo de recursos permitido para cada serviço/Pod, se o serviço começa a ser excessivamente usado, ele começa a "roubar" recursos (memória / CPU) de outro processo que está sendo executado, bloqueando também a possibilidade de subida de novas réplicas caso sejam necessárias.

Para tratar esse aspecto, o Kubernetes tem um conceito chamado de **Limits**, que indica o limite máximo de recursos para cada réplica.


**trecho do arquivo:** deployment.yaml
```
        resources:
          requests:
            memory: "100Mi"
            cpu: "200m"
          limits:
            cpu: "250m"
            memory: "200Mi"
```            

**Importante** No caso do uso de CPU, o K8s consegue tratar o limite máximo de alguma forma (ex.: desacelerando o processo), mas no caso do uso de memória não. Se o consumo de memória do Pod ultrapassar o limite estabelecido, o K8s derruba o Pod automaticamente. Isso é algo positivo, pois força a equipe de desenvolvimento a tratar e otimizar o consumo desses recursos.


# 6. Trabalhando com Autoscaler

**HPA** :: Horizontal Pod Autoscaler

Mecanismo do K8s que faz o controle do autoscaling de Pods de acordo com a carga.

Para simular e demonstrar o funcionamento desse recurso, será criada uma imagem com "php e apache", que disponibilizará algum item para sobrecarregar o processamento.

**arquivo:** index.php
```
<?php 
sx = 0.0001;
for($i=0; $i<= 10000000; $i++) {
  $x += sqrt($x);
}
echo "Code.education Rocks!";
```

Teste local:

```
$ php index.php
```

**arquivo:** Dockerfile
```
FROM php:7-apache
COPY index.php /var/www/html/index.php
RUN chmod a+rx index.php
```

Build da imagem:

```
$ docker build -t zanatabr/php-apache-hpa .
```

Carga da imagem no Docker Hub:

```
$ docker push zanatabr/php-apache-hpa 
```

Apenas para que seja feito o teste, o deploy será feito diretamente, sem o uso do arquivo declarativo (yaml):

```
$ kubectl run php-apache-hpa --image=zanatabr/php-apache-hpa --requests=cpu=200m --expose --port=80
```

Verificar no Dashboard do GCP, em **Workload** e **Services & Ingress**, se os itens foram criados.


## Configuração do autoscaling

Se fosse feito por linha de comando:

```
$ kubectl autoscale deployment php-apache-hpa --cpu-percent=20 --min=1 --max=10
```

Mas a configuração será feita de forma declarativa:

**arquivo:** hpa.yaml
```
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache-hpa
spec:
  maxReplicas: 5
  minReplicas: 1
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: php-apache-hpa
  targetCPUUtilizationPercentage: 20
```

Isso é feito através de um **Metrics Server**. Por padrão, a cada 15s é feita uma verificação. Após 3 minutos, se o valor se mantiver acima do percentual informado, é feita a carga de novas réplicas, até o limite estabelecido. Passados 5 minutos, se o valor se mantiver inferior ao percentual informado, as réplicas são desalocadas até o mínimo estabelecido. 

Se quiser usar isso fora do GCP ou em algum cloud que não o disponibiliza, basta instalar o **Metrics Server**.

Exemplos:
- https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html
- https://medium.com/@cagri.ersen/kubernetes-metrics-server-installation-d93380de008



Aplicação:

```
$ kubectl apply -f hpa.yaml
```

Verificação:

```
$ kubectl get hpa
```

Agora, para forçar a barra e gerar sobrecarga de processamento, será criada uma máquina

```
$ kubectl run -it loader --image=busybox /bin/sh
```

E a partir dessa máquina, executaremos um loop infinito que faz chamadas ao serviço:

```
# while true; do wget -q -O- https://php-apache-hpa.default.svc.cluster.local; done;
```

Em outro terminal, para monitorar:

```
$ watch kubectl get hpa
```

Observar o percentual de uso e o número de réplicas.

O segundo passo é o de interromper o loop infinito e verificar o número de réplicas diminuindo.



# 7. kubernetes e hpa 

## Desafio

1o. Nessa fase, você deverá implementar exatamente o mesmo algorítimo que foi implementado em PHP (no php-apache-hpa / looping somando a raiz quadrada), porém na linguagem Go Lang. Não esqueça de seguir os passos abaixo:

- Nome do deployment, service e nome da imagem deverá se chamar: go-hpa
- Desenvolva os testes
- Implemente o processo de CI
- Faça o push da imagem no Docker Hub (seu-user/go-hpa)
- Faça o deploy da imagem no K8S (criando os arquivos de deployment e services)
- Cada réplica deverá consumir no mínimo 50m e no máximo 100m.

2o. Implemente o "hpa" para que esse deployment tenha as seguintes características:

- O processo de escala inicia quando a CPU passar de 15%
- Quantidade mínima de pods: 1
- Quantidade máxima de pods: 6

3o. Crie um POD e faça requisições através de um looping infinito e verifique se o autoscaler está funcionando corretamente.

## Formato de Entrega:

Crie um repositório no github contendo tanto o projeto em Go (com pelo menos uma PR verificada através do processo de CI), bem como os arquivos yamls necessários para executar todo o processo.




# Solução do desafio


**arquivo:** src/fakewebserverhpa.go
```
package main

import (
	"fmt"
	"math"
	"net/http"
)

func main() {
	http.HandleFunc("/", fakeWebServer)
	http.ListenAndServe(":8000", nil)
}

func fakeWebServer(w http.ResponseWriter, r *http.Request) {
	dummy()
	fmt.Fprintf(w, greeting("Code.education Rocks!"))
}

func dummy() {
	x := 0.0001
	for i := 0; i <= 10000000; i++ {
		x += math.Sqrt(x)
	}
}

func greeting(msg string) string {
	return ("<b>" + msg + "</b>")
}
```



**arquivo:** src/fakewebserverhpa_test.go
```
package main

import "testing"

func TestHttpSrv_DeveResultarBtesteB(t *testing.T) {
	got := greeting("teste")
	want := "<b>teste</b>"

	if got != want {
		t.Errorf("greeting(\"teste\") \n got: %v \n want: %v \n", got, want)
	}
}
```



**arquivo:** Dockerfile
```
FROM golang:1.7.0-alpine AS builder

ENV GO111MODULE=on \
    CGO_ENABLED=1

WORKDIR /build

# Copia o código necessário para o build da aplicação
COPY ./src .

# Build da aplicação
RUN go build fakewebserverhpa.go

# Cria o diretorio /dist contendo apenas os arquivos necessários para o runtime
# Depois, serão copiados como / (root) da imagem resultante
WORKDIR /dist
RUN cp /build/fakewebserverhpa ./fakewebserverhpa

# Cria a imagem mínima para runtime, baseado na imagem raiz 
FROM alpine:latest

COPY --chown=0:0 --from=builder /dist/fakewebserverhpa /fakewebserverhpa

EXPOSE 8000

ENTRYPOINT ["/fakewebserverhpa"]
```

Criação da imagem:

```
$ docker build -t zanatabr/go-hpa .
```

Teste inicial:

```
$ docker run --name gohpa -p 8000:8000 -d zanatabr/go-hpa
```
\* Acesso feito pelo navegador http://localhost:8000

Push da imagem para o Docker Hub:

```
$ docker push zanatabr/go-hpa
```


**arquivo:** fakewebserverhpa.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: go-hpa  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: go-hpa
  template:
    metadata:
      labels:
        app: go-hpa
    spec:
      containers:

      - name: go-hpa
        image: zanatabr/go-hpa

        ports: 
        - name: http
          containerPort: 8000

        resources:
          requests:
            cpu: "50m"
          limits:
            cpu: "100m"
---

apiVersion: v1
kind: Service
metadata:
  name: go-hpa-service
spec:
  selector: 
    app: go-hpa
  type: LoadBalancer
  ports:
  - name: http
    port: 8000

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: go-hpa
spec:
  maxReplicas: 6
  minReplicas: 1
  scaleTargetRef:
    apiVersion: autoscaling/v1
    kind: Deployment
    name: go-hpa
  targetCPUUtilizationPercentage: 15
```

Criação de um cluster no GCP com 3 nodes.

Aplicação dos descritores dos itens no cluster criado:

```
$ kubectl apply -f fakewebserverhpa.yml
```



## Realização do teste de carga

Para forçar a barra e gerar sobrecarga de processamento, foi criado um Pod:

```
$ kubectl run -it loader --image=busybox /bin/sh
```

E a partir desse Pod foi executado um loop infinito que faz chamadas ao serviço:

```
# while true; do wget -q -O- http://go-hpa-service.default.svc.cluster.local:8000; done;
```

Em outro terminal, para monitorar:

```
$ watch kubectl get hpa
```


## Implementação do processo de CI no GCB


Foi criado um Trigger no GCB, apontando para o repositório Git:

```
https://github.com/zanatabr/fullcycle-devops-k8s-hpa
```

Criado o arquivo:

**arquivo:** cloudbuild.yaml
```
steps:

- id: "Execução dos testes unitários"
  name: 'golang'
  args: ['go', 'test', './...']

- id: "Build da imagem Docker"
  name: 'gcr.io/cloud-builders/docker'
  args: ['build', '-t', 'gcr.io/$PROJECT_ID/go-hpa', '.']

- id: "Push da imagem Docker"
  name: 'gcr.io/cloud-builders/docker'
  args: ['push', 'gcr.io/$PROJECT_ID/go-hpa']
```

Feito o commit e o push das modificações.

Após o push, foi verificado que o Trigger foi acionado corretamente.