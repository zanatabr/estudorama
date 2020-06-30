# Full Cycle Development

[Link de acesso ao treinamento](http://portal.code.education)

# DevOps -  Kubernetes

---
# 1. Introdução ao Kubernetes

Orquestrador/gerenciador de contêineres.

## O que é o Kubernetes?

> Fonte: [kubernetes.io](https://kubernetes.io/pt/)  
>> Kubernetes (K8s) é um produto Open Source utilizado para automatizar a implantação, o dimensionamento e o gerenciamento de aplicativos em contêiner.

## De onde veio?

Surgiu no Google. Projetos que deram origem:

1. Borg
2. Omega
3. Kubernetes

## Pontos importantes

1. Kubernetes é disponibilizado através de um conjunto de APIs
2. Normalmente acessamos a API usando a CLI: `kubectl`
3. Tudo é baseado em estado. Você configura o estado de cada objeto
4. Kubernetes Master  (Cluster)
   1. Kube-apiserver
   2. Kube-controller-manager
   3. Kube-scheduler
5. Outros Nodes:
   1. Kubelet
   2. Kubeproxy

## Dinâmica "superficial"

1. **Cluster** Conjunto de máquinas (Nodes)
   1. Cada máquina possui uma quantidde de vCPU e Memória

![Cluster](/imagens/fullcycledev--devops-04-kubernetes-img010.jpg)


2. **Pods** Unidade que contém os contêineres provisionados
   1. O Pod representa os processos rodando no cluster
   2. Pode-se ter mais de um contêiner em cada Pod, mas são raras as vezes em que isso se torna necessário.

![Pods](/imagens/fullcycledev--devops-04-kubernetes-img020.jpg)


3. **Deployment** outro tipo de objeto que tem o objetivo de aprovisionar os pods
   1. Para aprovisionar os pods, ele precisa saber quantas réplicas serão disponibilizadas, e isso é informado através dos ReplicaSets (quantas réplicas de cada set)

![Deployment - 1 Node](/imagens/fullcycledev--devops-04-kubernetes-img030.jpg)

![Deployment - 1 Node](/imagens/fullcycledev--devops-04-kubernetes-img040.jpg)

Caso o número exigido por algum **ReplicaSet** aumente e não haja recursos computacionais disponíveis, o K8s deixará a execução pendente até que surjam as condições adequadas.

![Deployment - 1 Node - Excedeu recursos](/imagens/fullcycledev--devops-04-kubernetes-img050.jpg)

Caso isso ocorra e haja outro **Node** disponível, os pods serão criados no outro Node, desde que haja recursos disponíveis.

![Deployment - 2 Nodes](/imagens/fullcycledev--devops-04-kubernetes-img060.jpg)





# 2. Entendendo Services

Interface de comunicação entre o mundo externo e os pods.

## Services
É uma forma de agregar um conjunto de pods para então implementar políticas de visibilidade.

### Tipos de Services - Principais

* ClusterIP
* NodePort
* LoadBalancer

### Services - Cluster IP

Neste caso, os acessos são feitos apenas dentro do cluster. Não há acesso do mundo externo. O acesso é feito através do **Proxy**, que sabe para qual **Service** encaminhar a requisição, e este por sua vez sabe qual **Pod** está disponível para fazer o atendimento.

![Cluster IP](/imagens/fullcycledev--devops-04-kubernetes-img070.jpg)


### Services - NodePort

O **NodePort** não trabalha com **Proxy** para resolver o **Service**, em vez disso temos **Ports TCP** atribuídos em cada **Node** destinado a um **Service** específico.

![NodePort](/imagens/fullcycledev--devops-04-kubernetes-img080.jpg)


### Services - LoadBalancer

A ideia principal do **LoadBalancer** é a de expor um IP externo (ex.: Internet), e quando o tráfego chegar até o LB, ele verifica a carga, e a distribui para o **Node** / **Service** adequado.

![Load Balancer](/imagens/fullcycledev--devops-04-kubernetes-img085.jpg)


### Services - Selectors

O **Selector** é definido no **Service** para que seja feito um filtro para verificar quais **Pods** serão colocadas dentro do **Service**.

![Selectors](/imagens/fullcycledev--devops-04-kubernetes-img090.jpg)

Quando é feito o deploy, o seletor de cada **Pod** é definido para que seja feita a seleção pelo **Service** adequado.


# 3. Instalando Minikube

O Minikube é uma ferramenta que facilita a execução do K8s localmente, disponibilizando um cluster single-node (apenas um único node) em uma Máquina Virtual. É recomendado apenas para o uso em testes e desenvolvimento (em um notebook por exemplo), pois é limitado e não oferece todos os recursos.

Website: [Installing Kubernetes with Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/)

\* Não foi feita a instalação, pois já uso o KinD.
\* A instalação do Minikube já instala o `kubectl`

# 3.1. Instalação do Kind 

\* Não faz parte do treinamento, mas achei melhor anotar

O KinD (Kubernertes in Docker) é uma ferramenta para executar **clusters** K8s localmente, usando contêineres Docker como sendo os seus **Nodes**.

Foi inicialmente projetado para testar o próprio Kubernetes, mas é outra alternativa para testes, aprendizado, desenvolvimento local e processo de CI.

Website: [Installing Kubernetes with Kind](https://kubernetes.io/docs/setup/learning-environment/kind/)  
Guia Rápido: [Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start/)

## Instalação no Linux

```
$ curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.8.1/kind-$(uname)-amd64
$ chmod +x ./kind
$ sudo mv ./kind /usr/local/bin/kind
```

## Criação de um cluster

```
$ kind create cluster

Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.18.2) 🖼 
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Thanks for using kind! 😊
```

Será criado um cluster com o nome "kind" por padrão. Para atribuir um nome de contexto diferente para o cluster, deve-se usar a flag `--name`.

```
$ kind create cluster --name dfdx-cluster

Creating cluster "dfdx-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.18.2) 🖼 
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-dfdx-cluster"
You can now use your cluster with:

kubectl cluster-info --context kind-dfdx-cluster

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/
```

## Visualizar os clusters

```
$ kind get clusters
```

## Listar os nodes de um cluster

\* O `kubectl` deve ser instalado antes (vide item **3.2. Instalação do kubectl**)

```
$ kubectl get nodes

NAME                         STATUS   ROLES    AGE   VERSION
dfdx-cluster-control-plane   Ready    master   36m   v1.18.2

```

# 3.2. Instalação do kubectl

\* Não faz parte do treinamento, mas achei melhor anotar

O `kubectl` é um cliente que possibilita a comunicação com clusters do K8s.

Guia de instalação do kubectl: [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Instalação:

```
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

$ chmod +x ./kubectl

$ sudo mv ./kubectl /usr/local/bin/kubectl
```

Para testar se deu tudo certo:

```
$ kubectl version --client
```


## kubectl: alias e autocomplete

Guia de instalação do kubectl: [Optional kubectl configurations]
https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations

Execute o seguinte comando para configurar o alias e autocomplete para o kubectl.

### No Bash

```
$ echo 'source <(kubectl completion bash)' >>~/.bashrc
$ sudo -i
# kubectl completion bash >/etc/bash_completion.d/kubectl
```

Se tiver um alias para o `kubectl`, é possível extender o autocomplete para trabalhar com o alias:

```
$ echo 'alias k=kubectl' >>~/.bashrc
$ echo 'complete -F __start_kubectl k' >>~/.bashrc
```

### No ZSH

Adicionar a linha seguinte no arquivo `~/.zshrc`:

```
source <(kubectl completion zsh)
```

Se tiver um alias para o `kubectl`, é possível extender o autocomplete para trabalhar com o alias:


```
$ echo 'alias k=kubectl' >>~/.zshrc
$ echo 'complete -F __start_kubectl k' >>~/.zshrc
```

# 3.3. Retornando para o conteúdo da aula

Foi feita a remoção do cluster criado no teste anterior, mantendo apenas o cluster padrão (kind):

```
$ kind delete clusters dfdx-cluster
```

Listar os serviços (Apenas para validação):
```
$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2m51s
```

Listar os pods (Apenas para validação):
```
$ kubectl get pods
No resources found in default namespace.
```

Listar os deployments (Apenas para validação):
```
$ kubectl get deployments
No resources found in default namespace.
```


# 4. Criando o nosso primeiro Pod

Será feita a criação de um pod de forma bem "artesanal", somente a título de exemplo. A criação de pods é feita normalmente usando *Deployments*, e não de forma "unitária". Isso será visto na sequência.

**arquivo:** pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: pod-exemplo
spec:
  containers:Deployment
  - name: pod-exemplo
    image: nginx:1.17-alpine
```

Aplicação do conteúdo do arquivo yaml para criar o pod:
```
$ kubectl apply -f pod.yaml
```

Verificação da situação do pod criado:
```
$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
pod-exemplo   1/1     Running   0          2m38s
```

Verificação do log:
```
$ kubectl logs pod-exemplo
(saída vazia - ok)
```

Mas como será feito o acesso ao NGinx que está rodando neste pod? 

Tudo o que recebe tráfego externo depende de um **Service**, que pode ser do tipo **ClusterIP**, **NodePort** ou **Loadbalancer**

Este **Pod** criado como exemplo será removido.

No próximo passo será feita a criação de um **Deployment**, que por sua vez criará o **Pod** . Depois será criado um **Service** para que possamos expor o **Deployment** que contém os **Pods**, para que possamos acessar o NGinx através do navegador.


# 5. Trabalhando com Deployments

## 5.1. Criação de Deployment de forma "artesanal" (na linha de comando)

Cria um **deployment**, que por sua vez cria um **pod** de apenas um container da imagem "nginx:1.17-alpine":
```
$ kubectl create deployment hello-nginx --image=nginx:1.17-alpine
```

Verificar a situação do deployment recém criado:
```
$ kubectl get deployments
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-nginx   1/1     1            1           54s
```

Verificar os **pods** criados:
```
$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-nginx-6485484cdf-t8mq6   1/1     Running   0          5m10s
```

Criar um **service** que possibilitará acessar o **pod** contendo o nginx (Não tem LoadBalancer no Minikube):
```
$ kubectl expose deployment hello-nginx --type=LoadBalancer --port=80
```


```
$ kubectl get services
NAME          TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
hello-nginx   LoadBalancer   10.103.99.137   <pending>     80:30564/TCP   92s
kubernetes    ClusterIP      10.96.0.1       <none>        443/TCP        12m
```

Com o Minikube, temos uma forma de "expor" o endereço do **service** para que possamos acessá-lo pelo navegador:

```
$ minikube service hello-nginx
```

Uma janela do navegador será aberta apontando para um endereço IP local (não o IP do Cluster), port 30564 (ou a que for exposta na ocasião).

/* Não consegui replicar isso com o KinD.

Remoção dos itens criados:

```
$ kubectl delete service hello-nginx 
service "hello-nginx" deleted
```

```
$ kubectl delete deployments hello-nginx 
deployment.apps "hello-nginx" deleted
```
ou, simplesmente:

```
$ kubectl delete deployments --all
```


## 5.2. Criação de Deployment declarativo (convencional - arquivo yaml)

Forma tradicional/convencional.


**arquivo:** deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-nginx  
spec:
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
```


```
$ kubectl apply -f deployment.yaml 
deployment.apps/hello-nginx created
```

Verificar a situação do deployment recém criado:
```
$ kubectl get deployments
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-nginx   1/1     1            1           8m33s
```

Verificar os **pods** criados:
```
$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-nginx-67dd48d89b-krqtj   1/1     Running   0          8m42s
```


# 6. Criando nosso Service

**arquivo:** service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector: 
    app: hello-nginx
```


```
$ kubectl apply -f service.yaml 
service/nginx-service created
```


```
$ kubectl get service
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP      10.96.0.1      <none>        443/TCP        14h
nginx-service   LoadBalancer   10.96.66.219   <pending>     80:30225/TCP   65s
```


Com o Minikube, temos uma forma de "expor" o endereço do **service** para que possamos acessá-lo pelo navegador:

```
$ minikube service hello-nginx
```

Uma janela do navegador será aberta apontando para um endereço IP local (não o IP do Cluster), port 30225 (ou a que for exposta na ocasião).

/* Não consegui replicar isso com o KinD.

# 7. Escalando Pods

Réplicas:

**arquivo:** deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-nginx  
spec:
  replicas: 3
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
```


```
$ kubectl apply -f deployment.yaml 
deployment.apps/hello-nginx configured
```

Verificar os **pods** criados:
```
$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-nginx-67dd48d89b-5ksnc   1/1     Running   0          106s
hello-nginx-67dd48d89b-krqtj   1/1     Running   0          13h
hello-nginx-67dd48d89b-pptkq   1/1     Running   0          106s
```


```
$ kubectl get deployments
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-nginx   3/3     3            3           16h
```



# 8. Trabalhando com ConfigMap

Possibilitar a modificação das configurações/estados de objetos dos contêineres.

Neste exemplo, modificaremos o parâmetro de redirecionamento do arquivo ngnix.conf, para reescrever a URL e redirecionar para o google.com.

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

Criação do ConfigMap:
```
$ kubectl apply -f configmap.yaml 
configmap/nginx-conf created
```

Verificar se o ConfigMap está OK:
```
$ kubectl get configmaps
NAME         DATA   AGE
nginx-conf   1      49s
```

Uso do **ConfigMap** criado no **Deployment**:

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


```
$ kubectl apply -f deployment.yaml
deployment.apps/hello-nginx configured
```

Verificar os **pods** criados:
```
$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-nginx-7797bd79df-6vp2t   1/1     Running   0          57s
hello-nginx-7797bd79df-m9v9j   1/1     Running   0          64s
```

Com o Minikube, temos uma forma de "expor" o endereço do **service** para que possamos acessá-lo pelo navegador:

```
$ minikube service hello-nginx
```

Uma janela do navegador será aberta apontando para um endereço IP local (não o IP do Cluster).

/* Não consegui replicar isso com o KinD.




# 9. Configurando Mysql do Zero

```
```


```
```



# 10. Montando volume persistente no Mysql
# 11. Trabalhando com Secret no Mysql
# 12. Criando Mysql Service 