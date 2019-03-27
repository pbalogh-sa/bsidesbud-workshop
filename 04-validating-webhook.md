# Installing ValidatingWebhook

```shell
helm repo add banzaicloud-stable http://kubernetes-charts.banzaicloud.com/branch/master
helm repo update
```

```shell
kubectl create ns validator
helm install --name validator --namespace validator \
    --set externalAnchore.anchoreHost="http://anchore-anchore-engine-api.anchore.svc.cluster.local:8228" \
    --set externalAnchore.anchoreUser=admin \
    --set externalAnchore.anchorePass=foobar \
    banzaicloud-stable/anchore-policy-validator
```

Listing resources
```shell
kubectl get all -n validator
```
```shell
NAME                                                          READY   STATUS      RESTARTS   AGE
pod/validator-anchore-policy-validator-6bc977564c-5mxrp       1/1     Running     0          2m17s
pod/validator-anchore-policy-validator-default-policy-x8thl   0/1     Completed   0          2m17s

NAME                                         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/validator-anchore-policy-validator   ClusterIP   10.98.93.225   <none>        443/TCP   2m17s

NAME                                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/validator-anchore-policy-validator   1/1     1            1           2m17s

NAME                                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/validator-anchore-policy-validator-6bc977564c   1         1         1       2m17s

NAME                                                          COMPLETIONS   DURATION   AGE
job.batch/validator-anchore-policy-validator-default-policy   1/1           7s         2m17s
```

```shell
kubectl get validatingwebhookconfigurations
```
```shell
NAME                                                      CREATED AT
validator-anchore-policy-validator.admission.anchore.io   2019-03-24T14:59:43Z
```

```shell
kubectl get validatingwebhookconfigurations validator-anchore-policy-validator.admission.anchore.io -o yaml
```

```yaml
apiVersion: v1
items:
- apiVersion: admissionregistration.k8s.io/v1beta1
  kind: ValidatingWebhookConfiguration
  metadata:
    creationTimestamp: "2019-03-24T14:59:43Z"
    generation: 1
    name: validator-anchore-policy-validator.admission.anchore.io
    ownerReferences:
    - apiVersion: v1
      kind: Pod
      name: validator-anchore-policy-validator-6bc977564c-5mxrp
      uid: 7417cff7-4e45-11e9-9d9b-9e5cae354fd9
    resourceVersion: "4579"
    selfLink: /apis/admissionregistration.k8s.io/v1beta1/validatingwebhookconfigurations/validator-anchore-policy-validator.admission.anchore.io
    uid: 752010a7-4e45-11e9-9d9b-9e5cae354fd9
  webhooks:
  - clientConfig:
      caBundle: CACERTBASE64
      service:
        name: kubernetes
        namespace: default
        path: /apis/admission.anchore.io/v1beta1/imagechecks
    failurePolicy: Fail
    name: validator-anchore-policy-validator.admission.anchore.io
    namespaceSelector:
      matchExpressions:
      - key: scan
        operator: NotIn
        values:
        - noscan
    rules:
    - apiGroups:
      - ""
      apiVersions:
      - '*'
      operations:
      - CREATE
      resources:
      - pods
    sideEffects: Unknown
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

```shell
kubectl get apiservices.apiregistration.k8s.io
```
**output:**
```shell
...
v1.storage.k8s.io                      Local                                          True        26m
v1alpha1.security.banzaicloud.com      Local                                          True        3m
v1beta1.admission.anchore.io           validator/validator-anchore-policy-validator   True        3m
v1beta1.admissionregistration.k8s.io   Local                                          True        26m
...
```

[related lines in anchore-image-validator](https://github.com/banzaicloud/anchore-image-validator/blob/bf0238de10f012df39fbc887da1ebe5214c409d1/cmd/main.go#L75)

[related lines in anchore-policy-validator chart](https://github.com/banzaicloud/banzai-charts/blob/4ee1b838b965d16740da79efdb9d1c0028452734/anchore-policy-validator/values.yaml#L3)

## [05 Testing allow policy](./05-allow-policy.md)
