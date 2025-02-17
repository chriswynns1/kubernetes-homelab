# How to Install Longhorn on microk8s:
## Add a namespace for Longhorn:
```
microk8s kubectl create namespace longhorn-system
```

## Add the Longhorn Repo to Helm:
```
microk8s helm repo add longhorn https://charts.longhorn.io
```

## Update the Helm Repo:
```
microk8s helm repo update
```

## Install Longhorn:
```
microk8s helm install longhorn longhorn/longhorn --namespace longhorn-system
```

## Uninstall Longhorn:
```
kubectl -n longhorn-system patch -p '{"value": "true"}' --type=merge lhs deleting-confirmation-flag
helm uninstall longhorn -n longhorn-system
kubectl delete namespace longhorn-system
```
