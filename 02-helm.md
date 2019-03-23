# Installing helm

## Mac
```shell
brew install kubernetes-helm
```

```shell
curl -Lo helm.tar.gz https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-darwin-amd64.tar.gz
tar -xzf helm.tar.gz
mv darwin-amd64/helm /usr/local/bin/helm

```

## Windows
```shell
choco install kubernetes-helm
```
or download zip and install it:
https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-windows-amd64.zip


## GNU/Linux
```shell
curl -Lo helm.tar.gz https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-linux-amd64.tar.gz
tar -xzf helm.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

# Create tiller service account:
```shell
kubectl create serviceaccount tiller -n kube-system
kubectl create clusterrolebinding tiller-binding --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
```