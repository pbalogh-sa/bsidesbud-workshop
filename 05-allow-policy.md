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
kubectl run alpine-allow --namespace default --generator=run-pod/v1 --image=alpine:3.9 -- sleep 3600
```
```shell
kubectl get po -n default
```
**output:**
```shell
NAME           READY   STATUS    RESTARTS   AGE
alpine-allow   1/1     Running   0          22s
```

```shell
kubectl logs validator-anchore-policy-validator-6bc977564c-5mxrp -n validator
```
**output:**
```shell
...
time="2019-03-24T15:33:24Z" level=info msg="Anchore Response Body" response="[\n  {\n    \"analysis_status\": \"analyzed\",\n    \"analyzed_at\": \"2019-03-24T14:45:47Z\",\n    \"annotations\": {},\n    \"created_at\": \"2019-03-24T14:45:36Z\",\n    \"imageDigest\": \"sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82\",\n    \"image_content\": {\n      \"metadata\": {\n        \"arch\": \"amd64\",\n        \"distro\": \"alpine\",\n        \"distro_version\": \"3.9.2\",\n        \"dockerfile_mode\": \"Guessed\",\n        \"image_size\": 2754729,\n        \"layer_count\": 1\n      }\n    },\n    \"image_detail\": [],\n    \"image_status\": \"active\",\n    \"image_type\": \"docker\",\n    \"last_updated\": \"2019-03-24T14:45:47Z\",\n    \"parentDigest\": \"sha256:644fcb1a676b5165371437feaa922943aaf7afcfa8bfee4472f6860aad1ef2a0\",\n    \"userId\": \"admin\"\n  }\n]\n"
time="2019-03-24T15:33:24Z" level=info msg="Sending request" bodyParams="map[]" url="http://anchore-anchore-engine-api.anchore.svc.cluster.local:8228/v1/images/sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82/check?history=false&detail=false&tag=3.9"
time="2019-03-24T15:33:24Z" level=info msg="Anchore Response Body" response="[\n  {\n    \"sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82\": {\n      \"docker.io/3.9:latest\": [\n        {\n          \"detail\": {},\n          \"last_evaluation\": \"2019-03-24T15:33:24Z\",\n          \"policyId\": \"97b33e2c-3b57-4a3f-a12b-a8c0daa472a0\",\n          \"status\": \"pass\"\n        }\n      ]\n    }\n  }\n]\n"
time="2019-03-24T15:33:24Z" level=warning msg="Image passed policy check" image="alpine:3.9"
...
```
