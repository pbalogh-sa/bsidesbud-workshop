# Whitelisting deployments

## Used Custom Resource Definitions
```shell
kubectl get crd
```
**output:**
```shell
NAME                                      CREATED AT
audits.security.banzaicloud.com           2019-03-24T14:59:41Z
whitelistitems.security.banzaicloud.com   2019-03-24T14:59:41Z
```

## Audits
```shell
kubectl get audits -o wide
```
**output:**
```shell
NAME                                 RELEASENAME                     IMAGE    RESULT                                    ACTION
replicaset-alpine-allow-5c56d9bc7c   alpine-allow-5c56d9bc7c-t9pnp   alpine   [Image passed policy check: alpine:3.9]   allowed
replicaset-alpine-deny-7c685675f7    alpine-deny-7c685675f7-flhqc    alpine   [Image failed policy check: alpine:3.9]   reject
```

## Whitelistitems

### Deploy whitelistitem
```shell
kubectl apply -f deploy/whitelist-alpine-deny.yaml
```

```shell
kubectl get whitelistitems -o wide
```
**output:**
```shell
NAME          REASON      CREATOR
alpine-deny   bsidesbud   me
```

## Result
```shell
kubectl get rs
```
**output:**
```shell
NAME                      DESIRED   CURRENT   READY   AGE
alpine-allow-5c56d9bc7c   1         1         1       13m
alpine-deny-7c685675f7    1         0         0       10m
```

**few minutes later alpine-deny-7c685675f7 starts the pod**
```shell
kubectl get rs
```
**output:**
```shell
NAME                      DESIRED   CURRENT   READY   AGE
alpine-allow-5c56d9bc7c   1         1         1       18m
alpine-deny-7c685675f7    1         1         1       15m
```

## Check audits
```shell
kubectl get audits -o wide
```
**output:**
```shell
NAME                                 RELEASENAME                     IMAGE    RESULT                                    ACTION
replicaset-alpine-allow-5c56d9bc7c   alpine-allow-5c56d9bc7c-t9pnp   alpine   [Image passed policy check: alpine:3.9]   allowed
replicaset-alpine-deny-7c685675f7    alpine-deny-7c685675f7-hgbbw    alpine   [Image failed policy check: alpine:3.9]   allowed
```