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

# 3.1. Instalação do Kind 

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


# 4. Criando o nosso primeiro Pod
# 5. Trabalhando com Deployments
# 6. Criando nosso Service
# 7. Escalando Pods
# 8. Trabalhando com ConfigMap
# 9. Configurando Mysql do Zero
# 10. Montando volume persistente no Mysql
# 11. Trabalhando com Secret no Mysql
# 12. Criando Mysql Service 