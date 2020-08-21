# ДЗ #3 Безопасность и управление доступом (лекция #4)

## task01
Создал 2 serviceaccount bob и dave. Bob дана ClusterRole admin.

Проверить корректность настройки можно с помощью команд
```bash
kubectl get pods -n kube-system --as system:serviceaccount:default:bob
kubectl get pods -n kube-system --as system:serviceaccount:default:dave
```
При этом команда, запущенная от bob, выполнится усспешно, а от имени dave с ошибкой Forbidden.

## task02

## tas03