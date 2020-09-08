# ДЗ #4 Сетевое взаимодействие (Pod, сервисы) (лекция #5)

## Добавление проверок Pod

Ответы на вопросы для самопроверки:

1. Почему следующая конфигурация валидна, но не имеет смысл?
```
livenessProbe:
  exec:
    command:
    - "sh"
    - "-c"
    - "ps aux | grep my_web_server_process"
```
**Ответ**: Если процесс с PID=1 завершится, с ненулевым exit code, то котнейнер будет перезагружен.

2. Бывают ли ситуации, когда она все-таки имеет смысл?

**Ответ**: Имеет смысл это делать, если мы отслеживаем статус процессов у которых PID != 1, например дочерних процессов.

## Создание Deployment

### Тестирование разных вариантов maxSurge и maxUnavailable.
#### *maxUnavailable* = 0; *maxSurge* = 100%
Вначале создаются новые поды. По готовности нового пода удаляется старый под.

#### *maxUnavailable* = 0; *maxSurge* = 0
Не валидная спецификация. Оба этих поля не могут равняться нулю однвоременно.

#### *maxUnavailable* = 100%; *maxSurge* = 100%
Одновременно создаются новые поды и удаляются старые. Данная стратегия может привести к простою сервиса.

#### *maxUnavailable* = 100%; *maxSurge* = 0
Вначале старые поды удаляются, после удаления создаются новые поды.

## Создание Service
## Тестирование IPVS
## Установка MetalLB
```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
kubectl apply -f kubernetes-networks/metallb-config.yaml
```
Опубликовал CoreDNS через LoadBalancer (⭐)

## Установка Ingress
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/deploy.yaml
```
Опубликовал web сервис через Ingress

## Ingress для Dashboard (⭐)
Установка
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.4/aio/deploy/recommended.yaml
kubectl apply -f ./kubernetes-networks/dashboard/dashboard-ingress.yaml
```
source: https://github.com/kubernetes/dashboard

## Canary для Ingress (⭐)
Создал 2 deployment с nginx вместе с serice и configmap с конфигурацией nginx чтобы отличать различные версии deployment по возвращаемым nginx данным. Все манифесты применяются в отдельный namespace canary.

В качестве теста реализовал перенапрелвение 50% травфика на новые поды. В процессе реализации столкнулся с проблемой, что трафик не балансировался. Логи ingress controller подсказали, что проблема в отсутсвие имени хоста в настройках ingress (cannot merge alternative backend canary-deployment-2-80 into hostname that does not exist). Добавление опции host решило проблему.

Реализовано перенаправление трафика на canary с помощью заголовка http. Способ проверки

curl -H "canary:always" -H "host: deployment.cluster.local" 172.17.255.2/canary
Second deployment-2-9f4d8446-g6kh8
curl -H "host: deployment.cluster.local" 172.17.255.2/canary 
First deployment-1-5cf8c54df-lxtwh