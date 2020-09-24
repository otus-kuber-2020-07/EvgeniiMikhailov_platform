# ДЗ #6 Шаблонизация манифестов. Helm и его аналоги (лекция #7)

## Создал Kubernetes кластер в GCP
```bash
gcloud beta container --project "development-290213" clusters create "k8s-lab" --zone "europe-north1-c" --no-enable-basic-auth --cluster-version "1.16.13-gke.401" --release-channel "regular" --machine-type "e2-medium" --image-type "COS" --disk-type "pd-standard" --disk-size "100" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --num-nodes "3" --enable-stackdriver-kubernetes --enable-ip-alias --network "projects/development-290213/global/networks/default" --subnetwork "projects/development-290213/regions/europe-north1/subnetworks/default" --default-max-pods-per-node "110" --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0
gcloud beta container clusters get-credentials k8s-lab --zone europe-north1-c
```

## Helm
Установил helm 3.

### Полезные команды

#### Создание release
```bash
helm install <chart_name> --name=<release_name> --namespace=<namespace>
```

#### Обновление release
```bash
helm upgrade <chart_name> --name=<release_name> --namespace=<namespace>
```

#### Создание или обновление release
```bash
helm upgrade --install <chart_name> --name=<release_name> --namespace=<namespace>
```

#### Получение установленных release
```bash
kubectl get secrets -n <namespace> | grep <release_name>
```

### Репозиторий stable
```bash
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo list
```

## nginx-ingress
Установил nginx-ingress в отдельный namespace
```bash
kubectl create ns nginx-ingress
helm upgrade --install nginx-ingress stable/nginx-ingress --wait --namespace=nginx-ingress --version=1.41.3
```

TODO: nginx-ingress чарт из stable репозитория устарел, поставить nginx из официального [репозитория](https://github.com/kubernetes/ingress-nginx/tree/master/charts/ingress-nginx)

## cert-manager
```bash
helm repo add jetstack https://charts.jetstack.io
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.16.1/cert-manager.crds.yaml
kubectl create ns cert-manager
helm upgrade --install cert-manager jetstack/cert-manager --wait --namespace=cert-manager --version=0.16.1
```
Документация по установке [cert-manager](https://github.com/jetstack/cert-manager/blob/master/deploy/charts/cert-manager/README.template.md)

TODO: вышла новая версия cert-manager >1.0 [документация по установке](https://cert-manager.io/docs/installation/kubernetes/)

### Завершение установки cert-manager
https://cert-manager.io/docs/configuration/
