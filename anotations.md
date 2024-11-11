# Desafio DEVOPS & CLOUD

5 pilares: CALMS

- Culture
- Automation
- Lean
- Measurement (medição)
- Sharing (compartilhamento)

## DOCKER

https://www.youtube.com/watch?v=50VgSfrI_ts&t=3919s

```md
docker container run --name <nome> hello-world
docker container run -it ubuntu /bin/bash (já entra no terminal)
apt update && apt install curl -y (para utilizar no terminal criado)
curl http://www.google.com (testar o terminal)
```

Container com aplicação rodando:

```md
docker container run -d nginx
docker container exec -it <id> /bin/bash
docker container run -d -p 8080:80 nginx
docker container rm -f $(docker container ls -qa) exclui todos containers listados
docker build -t conversao-distancia -f Dockerfile . (contrução da imagem)
docker container run -d -p 8181:5000 conversao-distancia (contrução do container)
http://localhost:8181 (conferir a aplicação rodando localhost)
```

Agora vamos armazenar e versionar a imagem para compartilhamento com squad:

```md
https://hub.docker.com/ (cloud images)
mhbordalo/conversao-distancia:v1
namespace     repositório    :Tag

docker tag local-image:tagname new-repo:tagname
docker login
docker push new-repo:tagname
docker build -t mhbordalo/conversao-distancia:v1 -f Dockerfile .
docker login
docker push mhbordalo/conversao-distancia:v1
docker build -t mhbordalo/conversao-distancia:v2 --push .
```

Boas práticas: subir a última versão com a Tag:latest

```md
docker tag mhbordalo/conversao-distancia:v1 mhbordalo/conversao-distancia:latest
docker push mhbordalo/conversao-distancia:latest
```

Executar o container onde quiser, não ficar preso no localhost:

```md
docker container run -d -p 8080:5000 mhbordalo/conversao-distancia:v1
```

Limpar tudo:

```md
docker container rm -f $(docker container ls -aq)
docker image rm -f $(docker image ls -aq)
docker system prune
```

Rodar a imagem do dockerhub  e testar no navegador:

```md
docker container run -d -p 8080:5000 mhbordalo/conversao-distancia:v1
http://localhost:8080/
```

## KUBERNETES

https://www.youtube.com/watch?v=Dte8fLrh16k

Orquestrador de containers => arquitetura de clusters

1- Control plane => orquestra os clusters

- API Server
- ETCD
- Scheduler
- Contrler Menager

2- Worker => executa os containers

- Kubelet
- Kube-proxy

Kubernetes on-premisse => gerenciamento total do dev
Kubernetes as a Service => cloud provider entrega tudo pronto (recomendado)
Kubernetes local => para etudos e testes

Instalar docker, kubectl e k3d 
sudo apt-get update
kubectl cluster-info

k3d cluster create (cria cluster kubernetes com um unico node)
kubectl get nodes (lista os nodes criados)
k3d cluster list (lista os clusters)
k3d cluster delete (deleta o cluster)

k3d cluster create meucluster --servers 3 --agents 3 (server=controlplane e agents=node)
k3d cluster create meucluster --servers 1 --agents 2 (utilizar na aula)
k3d cluster create meucluster --servers 1 --agents 2 -p "8080:30000@loadbalancer"(bind de porta)

kubectl api-resource (lista a apiversion do cluster kubernetes)

## DEPLOY

Pod => executa os containers
Deployment => gerenciamento do replicaSet
ReplicaSet => escalabilidade e resiliencia dos Pods, controlador dos Pods
Service => expor os Pods e a aplicação no cluster kubernetes

kubectl create -f k8s/deployment.yaml  (kubernetes cria esses objetos)
kubectl apply -f k8s/deployment.yaml (aplica esses objetos no arquivo que já foi criado)
kubectl apply -f k8s/deployment.yaml && watch 'kubectl get pod' (aplica e lista pods)

kubectl get pods (lista os pods criados)
kubectl describe pod <nomeDoPod> (detalhamento do Pod)
kubectl get replicaset (info do replicaset, controlador dos Pods)
kubectl get deployment (lista o deploy)
kubectl get all (lista pod, service, deployment e replicaset)

kubectl get replicaset,pod (lista replicaset e pods)
kubectl get pod -o wide (mais detalhes dos pods)

kubectl delete pod <nomePod> && watch 'kubectl get pod' (deleta e já sobe outro pod)

kubectl port-forward pod/<nomePod> 8080:5000 (redirecionamento para porta do pod)
http://localhost:8080/  => pode testar aplicação localhost

kubectl rollout history deployment <nomeAplicação> (mostra revisões da aplicação)
kubectl rollout undo deployment <nomeAplicação> && watch 'kubectl get pod' (rollback de versão)

Utilizar 'service' como ponto único de comunicação com o pod

on-premisse
Service ClusterIp => comunicação interna entre os pods
Service NodePort => expõe o pod para o mundo externo

cloud
Service LoadBalance => para trabalhar com cloud provider

## CLOUD AWS

https://www.youtube.com/watch?v=FU0U4SsgA_g&t=2033s

- On-Premisse => datacenter local => Infra, SO e Aplicação da empresa
- IaaS - Infraestrutura como Serviço => Infra Cloud, SO e Aplicações da empresa
- PaaS - Plataforma como Serviço => Infra e SO Cloud, Aplicações da empresa
- SaaS - Software como Serviço => Infra, SO e Aplicações Cloud Provider

No Console AWS: https://aws.amazon.com/pt/console/

Criar a role AWS 'Service:eks' => *permanece na conta*
- IAM > Roles >  Create role > AWS Service > Use case > EKS > EKS Cluster > Next > Policy 'AmazonEKSClusterPolicy' > Next > Role name 'eks-cluster' > Create role

Criar a role AWS 'Service:ec2' => *permanece na conta*
- IAM > Roles >  Create role > AWS Service > Use case > EC2 > EC2 > Next > Policy 'AmazonEKS_CNI_Policy','AmazonEKSWorkerNodePolicy', AmazonEC2ContainerRegistryReadOnly' > Next > Role name 'eks-worker' > Create role

Criar a infraestrutura de rede => *excluir da conta no final do teste*
- CloudFormation > Create stack > Prepare template 'choose an existing template' > Specify template 'Amazon S3 URL' > Amazon S3 URL <colar o link do arquivo yaml> > https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml > next > Stack name 'eks-desafio' > next > next > submit > CREATE_IN_PROGRESS > CREATE_COMPLETE

Criar o Cluster Kubernetes => *excluir da conta no final do teste*
- EKS (Elastic Kubernetes Services) > Create cluster in region 'N.Virginia' > Name 'eka-desafio' > Cluster IAM role 'eks-cluster' > Next > Networking VPC 'vpc...|eks-desafio-VPC' > Subnets 'selecionar as criadas' > Security groups 'Selecionar o criado' > Next > Next > Next > Create > Creating > Active

Configurar Kubectl localhost => *permanece na máquina localhost*
- Executar AWS-CLI no terminal para configurar interação AWS via linha de comando > https://aws.amazon.com/pt/cli/ > autenticar AWS-CLI na AWS Plataform > IAM > user > Security credentials > Access key 'key and value' > no terminal: $ aws configure 'enter' inserir configurações 'enter' > aws eks update-kubeconfig --name eks-desafio 'para acessar o cluster kubernetes na AWS' > kubectl get nodes > No resources found 'conectado' > kubectl cluster-info > 'Kubernetes control plane is running at https://...' >

Configurar os nodes na AWS => *excluir da conta no final do teste*
- EKS > Clusters > eks-desafio > Compute > Node groups > Add node group > Name 'default' > Node IAM role 'eks-worker' > Next > Maximum size '4' > Next > 'deixar subnets privadas' > Next > Next > Create > Active no terminal: $ kubectl get nodes > 'mostrar dois nodes criados'

## CI/CD

https://www.youtube.com/watch?v=-fetrWSH6uw

CI - Continuous Integrations
- codificação
- commit
- build
- teste
- geração do pacote de entrega

CD - Continuous Delivery
- release
- teste
- aceite
- deploy em produção

GitHub Actions
- workflow (arquivo yml)
- events (trigger)
- jobs (tasks ordenadas)
- step (etapas ordenadas)
- actions (ações que serão executadas)
- runners (agente de execução)

*** na home do projeto no GitHub digite . e enter, abre o VSCode dentro do GitHub ***

Documentação actions para utilizar no CI-CD:
https://github.com/marketplace?type=actions

## PROMETHEUS E GRAFANA (visão 360)

https://www.youtube.com/watch?v=QCPceOGBh7s

Prometheus > métricas > precisa ter o cluster kubernetes AWS criado:

```md
No terminal: `kubectl get nodes` > conferir o cluster kubernetes > `kubectl get pod` > conferir o deploy da aplicação > `kubectl get svc` > conferir a aplicação publicada > url aplicação > `acd271c7d62c649cca1951e1688707fb-1507887014.us-east-1.elb.amazonaws.com`
```

Inserir manifesto no código fonte e instalar prometheus e grafana:

```md
kubectl apply -f prometheus/deployment.yaml
$ kubectl get all > 'verificar a instalação publicada' > url prometheus > a5cf84bbb42874c5499c5f346c4e0ecb-912793385.us-east-1.elb.amazonaws.com > url métricas > http://a5cf84bbb42874c5499c5f346c4e0ecb-912793385.us-east-1.elb.amazonaws.com/metrics
```

Montagem do Dasboard:

```md
url grafana > a813078c5454f44efa64defa7e93dc6c-539485828.us-east-1.elb.amazonaws.com > pegar a senha dentro do kubernetes utilizando a linha de comando a seguir > kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo > autenticar user admin > dentro do grafana adicionar data sources prometheus com endpoint http://prometheus-server > configurar dashboards ou utilizar link de dashboards prontos > https://grafana.com/grafana/dashboards/
```
