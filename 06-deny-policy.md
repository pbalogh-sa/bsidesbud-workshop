# Testing deny policy

## Change policy to DenyAll
```shell
curl -X GET -u "admin":"foobar" http://localhost:8228/v1/policies/a81d4e45-6021-4b42-a217-a6554015d431 -o denyall.json
```
### Edit file
```json
{
     "active": true,
     "created_at": "2019-03-24T14:59:47Z",
     "last_updated": "2019-03-24T14:59:47Z",
...
}
```

```shell
curl -X PUT -u "admin":"foobar" -H "Content-Type: application/json" -d @denyall.json --url http://localhost:8228/v1/policies/a81d4e45-6021-4b42-a217-a6554015d431
```

```shell
curl -X GET -u "admin":"foobar" http://localhost:8228/v1/policies
```
**response:**
```json
[
  {
    "active": false,
    "created_at": "2019-03-24T14:59:47Z",
    "last_updated": "2019-03-24T14:59:47Z",
    "name": "RejectHigh",
    "policyId": "0cd4785e-71fa-4273-8ea5-3b15f515cca4",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  },
  {
    "active": false,
    "created_at": "2019-03-24T14:59:47Z",
    "last_updated": "2019-03-24T14:59:47Z",
    "name": "RejectCritical",
    "policyId": "bdb91dcc-62ca-49a2-a497-ee8a3bb7ec9f",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  },
  {
    "active": false,
    "created_at": "2019-03-24T14:59:47Z",
    "last_updated": "2019-03-24T14:59:47Z",
    "name": "BlockRoot",
    "policyId": "377c130d-0af7-45d4-adf9-cd72878993e2",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  },
  {
    "active": true,
    "created_at": "2019-03-24T14:59:47Z",
    "last_updated": "2019-03-24T15:45:31Z",
    "name": "DenyAll",
    "policyId": "a81d4e45-6021-4b42-a217-a6554015d431",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  },
  {
    "active": false,
    "created_at": "2019-03-24T14:59:46Z",
    "last_updated": "2019-03-24T15:45:31Z",
    "name": "AllowAll",
    "policyId": "97b33e2c-3b57-4a3f-a12b-a8c0daa472a0",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  }
]
```

## Start alpine pod:
```shell
kubectl apply -f deploy/alpine-deny-deployment.yaml
```
```shell
kubectl get all
```
**output:**
```shell
NAME                                READY   STATUS    RESTARTS   AGE
pod/alpine-allow-5c56d9bc7c-t9pnp   1/1     Running   0          3m10s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/alpine-allow   1/1     1            1           3m10s
deployment.apps/alpine-deny    0/1     0            0           15s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/alpine-allow-5c56d9bc7c   1         1         1       3m10s
replicaset.apps/alpine-deny-7c685675f7    1         0         0       15s
```

```shell
kubectl describe rs alpine-deny-<changeit>
```
**output:**
```shell
Events:
  Type     Reason        Age                 From                   Message
  ----     ------        ----                ----                   -------
  Warning  FailedCreate  79m (x15 over 81m)  replicaset-controller  Error creating: admission webhook "validator-anchore-policy-validator.admission.anchore.io" denied the request: Image failed policy check: alpine:3.9
```
### Validator logs:
```shell
kubectl logs validator-anchore-policy-validator-<changeit> -n validator
```
**output:**
```shel
...
time="2019-03-24T16:30:55Z" level=info msg="Sending request" bodyParams="map[]" url="http://anchore-anchore-engine-api.anchore.svc.cluster.local:8228/v1/images/sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82/check?history=false&detail=false&tag=3.9"
time="2019-03-24T16:30:55Z" level=info msg="Anchore Response Body" response="[\n  {\n    \"sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82\": {\n      \"docker.io/3.9:latest\": [\n        {\n          \"detail\": {},\n          \"last_evaluation\": \"2019-03-24T16:30:55Z\",\n          \"policyId\": \"a81d4e45-6021-4b42-a217-a6554015d431\",\n          \"status\": \"fail\"\n        }\n      ]\n    }\n  }\n]\n"
time="2019-03-24T16:30:55Z" level=warning msg="Image failed policy check" image="alpine:3.9"
...
```

## [07 Whitelisting](./07-whitelist.md)