# ДЗ #5 Хранение данных в Kubernetes. Volumes, Storages, Statefull-приложения (лекция #6)

## kind кластер
```bash
kind create cluster
```

## Установка minio
```bash
kubectl apply -f kubernetes-volumes/minio-statefulset.yaml
kubectl apply -f kubernetes-volumes/minio-headless-service.yaml 
```

## Хранение секретов в secrets (⭐).
Создал манифест с секретами, изменил описание StatefulSet для использования secrets. Для тестирования внечале необходимо применить манифест secrets.yaml, затем minio-statefulset-secrets.yaml

```bash
kubectl apply -f kubernetes-volumes/with-secrets/minio-secret.yaml
kubectl apply -f kubernetes-volumes/with-secrets/minio-statefulset-secret.yaml
```

### Проверка
В описание пода должна появится информация об используемых переменных окружения из секретов.
```
Environment Variables from:
      minio-access-keys  Secret  Optional: false
```