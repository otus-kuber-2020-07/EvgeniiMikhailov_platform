# ДЗ #2 Kubernetes controllers, ReplicaSet, Deployment, DaemonSet (лекция #3)

ДЗ выполняется на кластере Kubernetes созданом с помощью kind
```bash
kind create cluster --config ./kubernetes-controllers/kind-config.yaml
```

## ReplicaSet

В манифесте ReplicaSet не хватает секции "selector".

Replicaset не обновляет поды если изменился шаблон (template). Шаблон применяется для новых контейнеров.

## Deployment

Создал манифест ReplicaSet и Deployment для Paymentservice

### Задание со ⭐
#### Аналог Blue-Green
Достигается за счет настройки **maxsurge=100%** и **maxunavailable=0**
```bash
$ kubectl apply -f paymentservice-deployment.yaml | kubectl get rs -w
NAME                        DESIRED   CURRENT   READY   AGE
paymentservice-6cf5d57746   3         3         3       68s
paymentservice-5cd65c5f57   3         0         0       0s
paymentservice-5cd65c5f57   3         0         0       0s
paymentservice-5cd65c5f57   3         3         0       0s
paymentservice-5cd65c5f57   3         3         1       2s
paymentservice-6cf5d57746   2         3         3       70s
paymentservice-6cf5d57746   2         3         3       70s
paymentservice-6cf5d57746   2         2         2       70s
paymentservice-5cd65c5f57   3         3         2       2s
paymentservice-6cf5d57746   1         2         2       70s
paymentservice-6cf5d57746   1         2         2       70s
paymentservice-6cf5d57746   1         1         1       70s
paymentservice-5cd65c5f57   3         3         3       3s
paymentservice-6cf5d57746   0         1         1       71s
paymentservice-6cf5d57746   0         1         1       71s
paymentservice-6cf5d57746   0         0         0       71s
```

Вначале создались новые поды, затем начали удаляться старые поды.

#### Reverse Rolling Update
Достигается за счет настройки **maxsurge=0** и **maxunavailable=1**
```bash
$ kubectl apply -f paymentservice-deployment.yaml | kubectl get rs -w
NAME                        DESIRED   CURRENT   READY   AGE
paymentservice-5cd65c5f57   3         3         3       38s
paymentservice-6cf5d57746   0         0         0       0s
paymentservice-6cf5d57746   0         0         0       0s
paymentservice-5cd65c5f57   2         3         3       38s
paymentservice-6cf5d57746   1         0         0       0s
paymentservice-5cd65c5f57   2         3         3       38s
paymentservice-5cd65c5f57   2         2         2       38s
paymentservice-6cf5d57746   1         0         0       0s
paymentservice-6cf5d57746   1         1         0       0s
paymentservice-6cf5d57746   1         1         1       1s
paymentservice-5cd65c5f57   1         2         2       39s
paymentservice-6cf5d57746   2         1         1       1s
paymentservice-5cd65c5f57   1         2         2       39s
paymentservice-5cd65c5f57   1         1         1       39s
paymentservice-6cf5d57746   2         1         1       2s
paymentservice-6cf5d57746   2         2         1       2s
paymentservice-6cf5d57746   2         2         2       3s
paymentservice-5cd65c5f57   0         1         1       41s
paymentservice-6cf5d57746   3         2         2       3s
paymentservice-6cf5d57746   3         2         2       3s
paymentservice-6cf5d57746   3         3         2       3s
paymentservice-5cd65c5f57   0         1         1       41s
paymentservice-5cd65c5f57   0         0         0       41s
paymentservice-6cf5d57746   3         3         3       4s
```

## Probes
Настроил probes для frontend.

Протестировал влияние readiness probe на обновление приложения. Статус обновления приложения можно проверить с помощью команды
```bash
kubectl rollout status deployment/frontend
```

## DaemonSet ⭐ и ⭐⭐
Создал манифест DaemonSet для [Node Exporter](https://github.com/prometheus/node_exporter) который запускается на всех нодах кластера.
Проверка работы
```bash
$kubectl get daemonsets.apps # should be 6 READY pods
$kubectl port-forward node-exporter-xxxxxx 9100:9100
$curl localhost:9100/metrics # should return metrics
```
