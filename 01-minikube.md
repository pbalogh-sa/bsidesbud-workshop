# Installing minikube

## Prerequirements
- Hyperkit driver on OSX
- Installed Virtualbox in case of GNU/linux and Windows
- KVM on GNU/Linux

## Mac
```shell
brew cask install kubernetes-cli minikube
```
or instal manually:
```shell
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 && chmod +x minikube

sudo mv minikube /usr/local/bin

```

## Windows

```shell
choco install minikube kubernetes-cli
```
or download binary from:
https://storage.googleapis.com/minikube/releases/v0.35.0/minikube-windows-amd64.exe
and install it

## GNU/Linux
```shell
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube

sudo cp minikube /usr/local/bin && rm minikube
```

# Starting minikube:

## Mac
```shell
minikube start --vm-driver hyperkit --cpus 2 --memory 4096
```

## Windows and GNU/Linux
```shell
minikube start --cpus 2 --memory 4096
```

## set docker0 link
```shell
minikube ssh
sudo ip link set dev docker0 promisc on
```

### [Try the Banzaicloud PKE](https://github.com/banzaicloud/pke)

## [02 Installing helm](./02-helm.md)