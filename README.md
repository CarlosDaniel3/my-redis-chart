# my-redis-chart

## Objetivo do desafio
Criar um deployment de Redis em um cluster Kubernetes utilizando Helm para gerenciar a instalação e configuração. O objetivo é configurar o armazenamento persistente para os dados e expor o serviço para acesso interno no cluster.

## Comandos Úteis
Instalação do Chart
```
helm install my-redis-release .
```
Listagem dos pods que possuem a role master
```
kubectl get po -l role=master
```
Listagem dos pods que possuem a role slave
```
kubectl get po -l role=slave
```