# ДЗ #3 Безопасность и управление доступом (лекция #4)

## task01
Создал 2 serviceaccount bob и dave. Bob назначил ClusterRole admin.

Проверить корректность настройки можно с помощью команд
```bash
kubectl get pods -n kube-system --as system:serviceaccount:default:bob
kubectl get pods -n kube-system --as system:serviceaccount:default:dave
```
При этом команда, запущенная от bob, выполнится усспешно, а от имени dave с ошибкой Forbidden.

## task02
Создал ns prometheus, создал sa carol.
Назначил права всем sa из prometheus ns возможность делать get,list,watch для pods во всем кластере.

Проверить можно с помощью команды
```bash
kubectl get pods -n kube-system --as system:serviceaccount:prometheus:carol
```

## tas03
Создал отдельный ns dev с двумя serviceaccounts (jane и ken). Jane назначил ClusterRole admin с помощью RoleBinding. Ken назначил ClusterRole view с помощью RoleBinding.

Проверить можно с помощью команды
```bash
kubectl get pods -n dev --as system:serviceaccount:dev:jane
kubectl get pods -n dev --as system:serviceaccount:dev:ken
```