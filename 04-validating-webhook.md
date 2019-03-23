# Installing ValidatingWebhook

```shell
helm install --name validator --namespace validator \
    --set externalAnchore.anchoreHost=anchore.anchore.svc.cluster.local \
    --set externalAnchore.anchoreUser=admin \
    --set externalAnchore.anchorePass=foobar \
    banzaicloud-stable/anchore-policy-validator
```

Listing resources
```shell
kubectl get all -n validator
kubectl get validatingwebhookconfigureations
```
