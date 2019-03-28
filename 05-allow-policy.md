# Testing allow policy

## List Anchore-engine policies
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
    "name": "DenyAll",
    "policyId": "a81d4e45-6021-4b42-a217-a6554015d431",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  },
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
    "created_at": "2019-03-24T14:59:46Z",
    "last_updated": "2019-03-24T14:59:48Z",
    "name": "AllowAll",
    "policyId": "97b33e2c-3b57-4a3f-a12b-a8c0daa472a0",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  }
]
```

## Evaluate check alpine:3.9:
```shell
curl -X GET -u "admin":"foobar" "http://localhost:8228/v1/images/sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82/check?history=false&detail=false&tag=docker.io/alpine:3.9"
```
**response:**
```json
[
  {
    "sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82": {
      "docker.io/alpine:3.9": [
        {
          "detail": {},
          "last_evaluation": "2019-03-24T15:18:00Z",
          "policyId": "97b33e2c-3b57-4a3f-a12b-a8c0daa472a0",
          "status": "pass"
        }
      ]
    }
  }
]
```

## Starting a new pod in our K8s cluster
```shell
git clone https://github.com/pbalogh-sa/bsidesbud-workshop.git
cd bsidesbud-workshop
```

```shell
kubectl apply -f deploy/alpine-allow-deployment.yaml
```
```shell
kubectl get all
```
**output:**
```shell
NAME                                READY   STATUS    RESTARTS   AGE
pod/alpine-allow-5c56d9bc7c-t9pnp   1/1     Running   0          15s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/alpine-allow   1/1     1            1           15s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/alpine-allow-5c56d9bc7c   1         1         1       15s
```

### Validator logs
```shell
kubectl logs validator-anchore-policy-validator-<changeit> -n validator
```
**output:**
```shell
...
time="2019-03-24T15:33:24Z" level=info msg="Sending request" bodyParams="map[]" url="http://anchore-anchore-engine-api.anchore.svc.cluster.local:8228/v1/images/sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82/check?history=false&detail=false&tag=3.9"
time="2019-03-24T15:33:24Z" level=info msg="Anchore Response Body" response="[\n  {\n    \"sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82\": {\n      \"docker.io/3.9:latest\": [\n        {\n          \"detail\": {},\n          \"last_evaluation\": \"2019-03-24T15:33:24Z\",\n          \"policyId\": \"97b33e2c-3b57-4a3f-a12b-a8c0daa472a0\",\n          \"status\": \"pass\"\n        }\n      ]\n    }\n  }\n]\n"
time="2019-03-24T15:33:24Z" level=warning msg="Image passed policy check" image="alpine:3.9"
...
```

## [06 Testing deny policy](./06-deny-policy.md)
