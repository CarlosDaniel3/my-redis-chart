# my-redis-chart

## Objetivo do desafio
Criar um deployment de Redis em um cluster Kubernetes utilizando Helm para gerenciar a instalação e configuração. O objetivo é configurar o armazenamento persistente para os dados e expor o serviço para acesso interno no cluster.

## Valores personalizados definidos em values.yaml
```yaml
global:
  image:
    repository: redis
    tag: "latest"
    
master:
  replicaCount: 3
  storage: 2Gi
  resources:
    requests:
      cpu: 250m
      memory: 256Mi
    limits:
      cpu: 500m
      memory: 512Mi

slave:
  replicaCount: 3
  resources:
    requests:
      cpu: 200m
      memory: 256Mi
    limits:
      cpu: 400m
      memory: 512Mi
```

## Comandos Úteis
Instalação do Chart
```
helm install my-redis-release .
```
Listagem dos pods que possuem a role "master"
```
kubectl get po -l role=master
```
Listagem dos pods que possuem a role "slave"
```
kubectl get po -l role=slave
```

## Verificando o funcionamento do redis e a persistência de dados

# 1 - Teste de Inserção de Dados no Redis Master
Acesso ao pod do Redis master
```
kubectl exec -it <redis-master-pod> -- redis-cli
```
Inserção de alguns valores
```
SET mykey "Hello World!"
```
Validando se o valor foi inserido corretamente
```
GET mykey
```
# 2 - Verificação da Persistência de Dados nos Volumes
Esse teste poderá ser feito no redis-master ou no slave. Iremos utilizar nesse exemplo o master seguindo os passos abaixo

Acesso a um pod do redis-master
```
kubectl exec -it <redis-master-pod> -- sh
```
Acesso ao diretório de dados persistentes
```
cd /data
```
Checagem da persistência dos dados
```
ls
```
Resultado Esperado
<div style="text-align: center"><br>
    <img align="center" alt="result" height="150px" width="500px" src="https://github.com/CarlosDaniel3/my-redis-chart/blob/main/assets/data-result.png">
</div>

# 3 - Verificação dos Volumes Montados
Confirmação da montagem do PVC no diretório /data
```
kubectl describe pod <redis-master-pod> | grep data
```
# 4 - Verificação após o reiniciamento dos pods
Deleção de um pod redis-master
```
kubectl delete pod <redis-master-pod>
```
Após ele ser recriado, é necessário acessar o pod novamente utilizando o comando abaixo
```
kubectl exec -it <redis-master-pod> -- redis-cli
```
Verificação da persistência dos dados
```
GET mykey
```

# 5 - Verificação do PersistentVolumeClaim (PVC)
Listagem dos PVCs
```
kubectl get pvc
```
Descrição do PVC utilizado pelo Redis
```
kubectl describe pvc <redis-master-pvc>
```
Caso o PVC esteja vinculado a um PersistentVolume(PV) e o status estiver como Bound, a persistência de dados foi feita com sucesso