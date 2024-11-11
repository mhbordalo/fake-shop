# FAKE SHOP

Aplicação desenvolvida como base do Desafio DevOps & Cloud:

- Configurar Dockerfile
- Configurar Kubernetes
- Configurar Cloud Provider AWS
- Configurar Prometheus
- Configurar Grafana

## Variável de Ambiente

DB_HOST => Host do banco de dados PostgreSQL.

DB_USER => Nome do usuário do banco de dados PostgreSQL.

DB_PASSWORD => Senha do usuário do banco de dados PostgreSQL.

DB_NAME => Nome do banco de dados PostgreSQL.

DB_PORT => Porta de conexão com o banco de dados PostgreSQL.

## Docker

Link para o repositório do projeto conversão de distância:

[https://github.com/KubeDev/conversao-distancia](https://github.com/KubeDev/conversao-distancia)

## AWS e EKS

Endereço do template de rede para o CloudFormation:

https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml

Link para o repositório do projeto fake shop:

https://github.com/KubeDev/fake-shop

## Prometheus e Grafana

Arquivo de manifesto para o Prometheus e o Grafana:

https://gist.githubusercontent.com/fabricioveronez/3ffbc3ddf826a75c508d5bca9b5a6adb/raw/8677f2ac7a3d9df2d43f63213b936e2a6248592c/deploy-prometheus.yaml

Arquivo com o Flask Dashboard:

https://gist.githubusercontent.com/fabricioveronez/3ffbc3ddf826a75c508d5bca9b5a6adb/raw/8677f2ac7a3d9df2d43f63213b936e2a6248592c/flask-dashboard.json

Comando para obter a senha do Grafana:

```
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
