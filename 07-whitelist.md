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
[audit CRD](https://github.com/banzaicloud/banzai-charts/blob/master/anchore-policy-validator/templates/audit-crd.yaml)
```shell
kubectl get audits -o wide
```
**output:**
```shell
NAME                                 RELEASENAME                     IMAGE    RESULT                                    ACTION
replicaset-alpine-allow-5c56d9bc7c   alpine-allow-5c56d9bc7c-t9pnp   alpine   [Image passed policy check: alpine:3.9]   allowed
replicaset-alpine-deny-7c685675f7    alpine-deny-7c685675f7-flhqc    alpine   [Image failed policy check: alpine:3.8]   reject
```

## Whitelistitems
[Whitelistitems CRD](https://github.com/banzaicloud/banzai-charts/blob/master/anchore-policy-validator/templates/whitelist-crd.yaml)

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
```shell
kubectl get whitelistitems alpine-deny -o yaml
```
**output:**
```shell
apiVersion: security.banzaicloud.com/v1alpha1
kind: WhiteListItem
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"security.banzaicloud.com/v1alpha1","kind":"WhiteListItem","metadata":{"annotations":{},"name":"alpine-deny"},"spec":{"creator":"me","reason":"bsidesbud"}}
  creationTimestamp: "2019-03-26T19:56:58Z"
  generation: 1
  name: alpine-deny
  resourceVersion: "4600"
  selfLink: /apis/security.banzaicloud.com/v1alpha1/whitelistitems/alpine-deny
  uid: 504ac0de-5001-11e9-b887-76c558b39908
spec:
  creator: me
  reason: bsidesbud
```

[whitelist checking in anchore-image-validator](https://github.com/banzaicloud/anchore-image-validator/blob/bf0238de10f012df39fbc887da1ebe5214c409d1/cmd/handler.go#L53)

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
replicaset-alpine-deny-7c685675f7    alpine-deny-7c685675f7-hgbbw    alpine   [Image failed policy check: alpine:3.8]   allowed
```