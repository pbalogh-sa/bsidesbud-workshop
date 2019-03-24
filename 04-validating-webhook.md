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
      caBundle: TFMwdExTMUNSVWRKVGlCRFJWSlVTVVpKUTBGVVJTMHRMUzB0Q2sxSlNVTTFla05EUVdNclowRjNTVUpCWjBsQ1FWUkJUa0puYTNGb2EybEhPWGN3UWtGUmMwWkJSRUZXVFZKTmQwVlJXVVJXVVZGRVJYZHdlbVJ0VFhRS1dUSkdNRXhYVG1oTlFqUllSRlJGTlUxRVRYbE9SRVV3VGxSck1FMVdiMWhFVkVrMVRVUk5lVTFVUlRCT1ZHc3dUVlp2ZDBaVVJWUk5Ra1ZIUVRGVlJRcEJlRTFMWXpOYWFreFhUbWhrUXpGcVdWUkRRMEZUU1hkRVVWbEtTMjlhU1doMlkwNUJVVVZDUWxGQlJHZG5SVkJCUkVORFFWRnZRMmRuUlVKQlRrNXNDa2hSWmpCVGMyczRhMGxvZFRnM1RqZENjRFIwUkU0d1RVRlVUelpWU1RFdlRYRjNUQzh3ZEVab1FYaElOWGRhVTNSWVJYRTNaemxVZG1sNlpqTXJOR0lLY0hneWNqSldRV0ZuWkVOR1MyTjVUVzlrYnpjM1YwNHpiVUphT1VjdmFrcEpPRE0wZDJ0aU1XaG1XSFJ6ZFdJeGJUZFRWRnBEYjJJMFZrOU5RVEpUS3dwRVZrNU5hbEFyTnpSVmVYTkxkV3htVlhnNVdUZ3pla0pqY0ZWRWEwcGtjV1ZuVlV4VFpURlpPVTFEY2sxM1RFVjRjazlHWlc5eVFXWnhkRE5aWWxWMUNsVjZTMDlxS3pKNk1tTjNPV3BaVFRSQk9UbDZPRTlNVVZZek9WTlVSM2Q1Y1V3d1NXaHJTWGRIVldNMmVYWXlOVXBJTlZWb2VIQnVhVWRIVFZsTWFtSUthV2RSWm5ocE9DdFNTMDgxUkZKRFJHRk5NM1JXSzFKRk5rZEhXbUl3TDI1eVNXWlNaV3B6V1V0dmIwaElSbTFuUVZGa1R5dFJaRk0wT0dsd2FVNXFUUW95VHpsTWFuVXJhRE01WlhKaGNEQlpiRWRWUTBGM1JVRkJZVTVEVFVWQmQwUm5XVVJXVWpCUVFWRklMMEpCVVVSQlowdHJUVUl3UjBFeFZXUktVVkZYQ2sxQ1VVZERRM05IUVZGVlJrSjNUVUpDWjJkeVFtZEZSa0pSWTBSQmFrRlFRbWRPVmtoU1RVSkJaamhGUWxSQlJFRlJTQzlOUVRCSFExTnhSMU5KWWpNS1JGRkZRa04zVlVGQk5FbENRVkZEVVVKclNrOUZiRm8yYTFSclZuQTBRV0pIWVZwaU1ITlViMFFyVXlzeFowSkNVRzVvYjFWWU0xZEdPRVJGU201TlZBcEZhbnBXYlU5TFFrMDJNMnhqTWxOdFJUUk5XWGh6V1VsMWMyWXhSMWc1UmpOcU0wRkhRVzFRUm1nM2N6TlNhakE0YUVaaFpqUk1kU3RRYTFKeWExcDJDa3hCWXpWT2RsSnJiVTE1T1hOdVp6SmxZakZaYlZKUGNESTFWRTFEVGxoaEt6bFRUSGhLU1dZMlptRnJVazR6Wm1kbk5HNUVSSE4yVjBrd2QxSlZja01LWTJoSVdtdE5VUzl5V0U0clZpOW9jemRZYjFWM09XeGhUMVZLZUdkelFtZ3JlVTR3T0VNdmF6QTNOV1F3U0c1R1YzZEpUU3RGVFdkaGIwRmFWVU5wUndwdFdFWjVORkZQYkdjeVRuQlBNU3RqY1dVckswdzNNalYwYkd0RlVteHdUalJQYlhWS1EyWkVOM2xxTmtKVFUzTjNSbGwxWkVKVVJuUTNSRkZ6TW1wVkNsVTJRMGhqVWxOWGJGSTVSMGt5YVZCTFFsTlhZbFpCV1hKUFRHZExiMDFZWmtkWWFBb3RMUzB0TFVWT1JDQkRSVkpVU1VaSlEwRlVSUzB0TFMwdENnPT0=
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
