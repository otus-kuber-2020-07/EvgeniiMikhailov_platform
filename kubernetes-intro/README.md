# ДЗ #1 Знакомство с Kubernetes (лекция #2)

## Разберитесь почему все pod в namespace kube-system восстановились после удаления.

* **core-dns** запускается в deployment, поэтому при удаление подов replicaset восставливает поды.
* **kube-proxy** управляется и создается Daemonset.
* **kube-apiserver**, **etcd**, **kube-controller-manager**, **kube-scheduler** являются статическими подами, их запускает kubelet ноды, так как их описание находится в папке /etc/kubernetes/manifests/
В параметрах запуска kubelet (в файле /etc/systemd/system/kubelet.service.d/10-kubeadm.conf) указан путь к директории из которой создавать статические поды --pod-manifest-path /etc/kubernetes/manifests
* **kube-addon-manager** и **storage-provisioner** являются аддонами minikube. Они управляются с помощью команды minikube addons

## Создан Dockerfile для веб-серверва
* сервер слушает на 8000 порту
* отдает содержимое папки /app
* работает от UID 1001

Запустить контейнер можно командой
```bash
docker container run -p 8000:8000 -d evgeniim/web:0.1
```

## Манифест пода
* Создан манифест пода.
* Добавлен init контейнер

Запуск и проверка работы
```bash
kubectl apply -f web-pod.yaml
kubectl port-forward --address 0.0.0.0 pod/web 8000:8000
```

## Frontend сервис Online Boutique (Hipster Shop)
Генерация манифеста
```bash
kubectl run frontend --image evgeniim/boutique-frontend:0.0.1 --restart=Never --dry-run=client -o yaml > frontend-pod.yaml
```

## Задание со ⭐
Под не запускался из-за отсутсвия переменных окружений.
