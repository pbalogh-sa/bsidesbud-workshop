# Installing Anchore-engine

```shell
kubectl create ns anchore
helm repo add banzaicloud-stable http://kubernetes-charts.banzaicloud.com/branch/master
helm repo update
helm install --name anchore --namespace anchore banzaicloud-stable/anchore-engine
```

```shell
kubectl get all -n anchore
```

## Settings

anchore-engine admin user: admin
anchore-engine admin password: foobar
