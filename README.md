# k8s-studies
Anotações e códigos de estudo sobre o ecossistema do Kubernetes

# Container Orchestration

- Processo para automatizar o deploy e gerenciamento de containers
- Auta disponibilidade e escalabilidade
- Balanceamento de carga entre os nós 

# Arquitetura do Kubernetes

## Node
- Maquina (física ou virtual) onde o Kubernetes é instalado
- Worker
    - Onde os containers são executados
    - Components usados
        - Container Runtime
        - kubelet
- Master
    - É um Node configurado como Master
    - Ele vigia os outros Nodes e fica responsável pela real orquestração dos containeres nesses Nodes
    - Components usados
        - API Server
        - etcd
        - controller
        - scheduler

## Cluster
- É um conjunto de Nodes
- É possível dividir a carga entre os Nodes

## Components

### API Server
- É um front-end para o Kubernetes
### etcd 
- armazenamento chave-valor
- usado para armazenar todos os dados para gerenciar o Cluster
### Scheduler
- É responsável por distribuir o trabalho entre os containeres entre vários Nodes
- Procura por conteiners recém-criados e os atribui aos Nodes
### Controller
- São o cérebro por trás da orquestração
- São responsáveis por notificar quando os Nodes, contaiers ou end points cairem
- Tomas as decisões para criar novos containers nesses casos
### Container Runtime
- Software usado para executar os containers
- Pode ser o Docker
### kubelet
- É o agente que roda em cada Node no Cluster
- É reponsável por garantir que os containers estão sendo executados nos Nodes como esperado
- Iterage com o Master Node provendo informações de saúde do Worker Node

## kubectl
- utilitário de linha de comando (cli)
- é usado para deployar e gerencirar aplicações no Cluster Kubernetes


# Conceitos

## PODs

- É uma única instãncia de uma aplicação
- É um objeto do kubernetes que encapsula os containeres
- É o menor objeto que é possível criar no Kubernetes
- É possível ter mais de um container no mesmo POD, mas apenas quando é um container utilitário
- Comandos `kubectl`
    - kubectl get pods
    - kubectl describe pod `pod_name`
    - kubectl apply -f `pod.yaml`
    - kubectl delete pod `pod_name`
- Configuralção de Pod como YAML
    ```yaml
        apiVersion: v1 # versão da API do Kubernetes
        kind: Pod # tipo de objeto que será cirado
        metadata: # dados referentes ao objeto
            name: myapp-pod
            labels:
                app: myapp
                type: front-end

        spec: # informações adicionais do objeto, pode ser diferente de acordo com o tipo de objeto
            containers:
                - name: nginx-container
                    image: nginx
    ```
