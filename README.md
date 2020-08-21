# Evgenii Mikhailov Platform repository

## [ДЗ **#1** Знакомство с Kubernetes (лекция #2)](kubernetes-intro/README.md)
* Разобрался почему все pod в namespace kube-system восстановились после удаления.
* Создал Dockerfile (web сервер на 8000 порту, отдает содержимое папки app, UID 1001)
* Создал манифест для Пода
* Собрал образ frontend сервиса Online Boutique (Hipster Shop)
* Разобрался почеу не запускался под с frontend service (⭐)

## [ДЗ **#2** Kubernetes controllers, ReplicaSet, Deployment, DaemonSet (лекция #3)](kubernetes-controllers/README.md)
* Создал ReplicaSet для frontend и paymentservice
* Создал Deployment для paymentservice
* Протестировал аналог blue-green и reverse rolling update для Deployment (⭐)
* Настроил Readiness Probe для frontend сервиса
* Настроил DaemonSet для [Node Exporter](https://github.com/prometheus/node_exporter) который запускается на всех нодах кластера (⭐ и ⭐⭐)

## [ДЗ **#3** Безопасность и управление доступом (лекция #4)](kubernetes-security/README.md)
* Создал 2 serviceaccount bob и dave. Bob назначил ClusterRole admin.
* Создал ns prometheus, создал sa carol. Назначил права всем sa из prometheus ns возможность делать get,list,watch для pods во всем кластере.
* Создал отдельный ns dev с двумя serviceaccounts (jane и ken). Jane назначил ClusterRole admin с помощью RoleBinding. Ken назначил ClusterRole view с помощью RoleBinding.
