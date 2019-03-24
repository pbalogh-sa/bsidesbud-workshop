# Installing ValidatingWebhook

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

``shell
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
