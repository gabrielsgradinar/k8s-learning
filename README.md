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

## Controllers

- São os processos que monitoram os objetos do Kubernetes

    ### Replication Controller

    - Ajuda na execução de várias instâncias de um único POD, criando alta disponibilidade
    - Ajuda na criação de um novo POD quando o atual estiver falhando
    - Garante que o número específicado de PODs estão em execução o tempo todo
    - Ajuda também com o balanceamento de carga entre os PODs e consegue fazer isso em mais de um Node ao mesmo tempo no Cluster
    - Configuração do Replication Controller em YAML
        ```yaml
        apiVersion: v1
        kind: ReplicationController
        metadata:
        name: myapp-rc
        labels:
            app: myapp
            type: front-end
        spec:
            template: # Template do Pod que será controlade
                metadata:
                name: myapp-pod
                labels:
                    app: myapp
                    tier: front-end
                spec:
                containers:
                    - name: nginx
                    image: nginx
            replicas: 3 # Número de Pods que serão executados
        
        ```
    - `Replica Set`
        - Novo jeito recomendado para se usar
        - Substitui o Replication Controller
        - Consegue controlar outros objetos que forem criados fora da definição, mas que tenham labels que foram definidas no `selector`
        -  Configuração do Replica Set em YAML
            ```yaml
            apiVersion: apps/v1
            kind: ReplicaSet
            metadata:
            name: myapp-replicaset
            labels:
                app: myapp
            spec:
            template:
                metadata:
                name: myapp-pod
                labels:
                    app: myapp
                spec:
                containers:
                    - name: nginx
                    image: nginx
            replicas: 3
            selector:
                matchLabels:
                app: myapp
            ```
## Deployment

- Tem a hierarquia mais alta dos objetos do Kubernetes
- Tem a capacidade de atualizar as instâncias subjacentes sem problemas usando atualizações contínuas.
- Consegue desfazer as alterações facilmente a qualquer momento
- Configuração do Deployment em YAML
    ```yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: myapp-replicaset
        labels:
            app: myapp
        spec:
        template:
            metadata:
            name: myapp-pod
            labels:
                app: myapp
            spec:
            containers:
                - name: nginx
                image: nginx
        replicas: 3
        selector:
            matchLabels:
                app: myapp
    ```
- Rollout
    - Quando um `Deployment` é criado, um `Rollout` é trigado
    - Um novo Rollout cria um novo `Deployment revision`
    - Quando a aplicação e os conteineres são atualizado, um novo Deployment revision é criada
    - Com isso é possível voltar uma versão do Deployment se necessário
    - comando kubectl
        - kubectl rollout status deployment/test
        - kubectl rollout history deployment/test
        - kubectl rollout undo deployment/test
        
