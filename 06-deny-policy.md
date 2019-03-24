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

