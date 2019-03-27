# Installing Anchore-engine

```shell
kubectl create ns anchore
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
helm install --name anchore --namespace anchore stable/anchore-engine
```

```shell
kubectl get all -n anchore
```
output:
```shell
NAME                                                     READY   STATUS    RESTARTS   AGE
pod/anchore-anchore-engine-analyzer-8bf7bd7d7-86cgl      1/1     Running   0          113s
pod/anchore-anchore-engine-api-999cb48d-bfptp            1/1     Running   0          113s
pod/anchore-anchore-engine-catalog-dbf9f8cd5-tblph       1/1     Running   0          113s
pod/anchore-anchore-engine-policy-bcf48ffbf-j8zv2        1/1     Running   0          113s
pod/anchore-anchore-engine-simplequeue-7dfdf97f8-kbxmv   1/1     Running   0          113s
pod/anchore-postgresql-64cb469655-cf2ss                  1/1     Running   0          113s

NAME                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/anchore-anchore-engine-api           ClusterIP   10.104.98.114    <none>        8228/TCP   113s
service/anchore-anchore-engine-catalog       ClusterIP   10.97.154.17     <none>        8082/TCP   113s
service/anchore-anchore-engine-policy        ClusterIP   10.96.135.187    <none>        8087/TCP   113s
service/anchore-anchore-engine-simplequeue   ClusterIP   10.101.223.135   <none>        8083/TCP   113s
service/anchore-postgresql                   ClusterIP   10.102.142.161   <none>        5432/TCP   113s

NAME                                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/anchore-anchore-engine-analyzer      1/1     1            1           113s
deployment.apps/anchore-anchore-engine-api           1/1     1            1           113s
deployment.apps/anchore-anchore-engine-catalog       1/1     1            1           113s
deployment.apps/anchore-anchore-engine-policy        1/1     1            1           113s
deployment.apps/anchore-anchore-engine-simplequeue   1/1     1            1           113s
deployment.apps/anchore-postgresql                   1/1     1            1           113s

NAME                                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/anchore-anchore-engine-analyzer-8bf7bd7d7      1         1         1       113s
replicaset.apps/anchore-anchore-engine-api-999cb48d            1         1         1       113s
replicaset.apps/anchore-anchore-engine-catalog-dbf9f8cd5       1         1         1       113s
replicaset.apps/anchore-anchore-engine-policy-bcf48ffbf        1         1         1       113s
replicaset.apps/anchore-anchore-engine-simplequeue-7dfdf97f8   1         1         1       113s
replicaset.apps/anchore-postgresql-64cb469655                  1         1         1       113s
```

### Port-forwarding anchore-engine service:
```shell
kubectl port-forward svc/anchore-anchore-engine-api -n anchore 8228
```

## Using anchore-engine manually:
```shell
curl --insecure -X GET -u "admin":"foobar" http://localhost:8228/v1/status 
```
**response:**
```json
{
  "available": true,
  "busy": false,
  "db_version": "0.0.9",
  "detail": {},
  "message": "all good",
  "up": true,
  "version": "0.3.3"
}
```

### Listing scanned images:
```shell
curl -X GET -u "admin":"foobar" http://localhost:8228/v1/images
```

### Scan image with anchore:
```shell
curl -X POST -u "admin":"foobar" -H "Content-Type: application/json" -d '{"tag": "docker.io/alpine:3.9"}' http://localhost:8228/v1/images
```

### Listing policies:
```shell
curl -X GET -u "admin":"foobar" http://localhost:8228/v1/policies
```
**response:**
```json                                                          
[
  {
    "active": true,
    "created_at": "2019-03-24T14:33:33Z",
    "last_updated": "2019-03-24T14:33:33Z",
    "name": "Default bundle",
    "policyId": "2c53a13c-1765-11e8-82ef-23527761d060",
    "policy_source": "local",
    "policybundle": {},
    "policybundlemeta": {},
    "userId": "admin"
  }
]
```
### Checking feeds population
```shell
curl -X GET -u "admin":"foobar" http://localhost:8228/v1/system/feeds
```
**output:**
```json
...
      {
        "created_at": "2019-03-26T19:26:26.956178",
        "last_sync": "2019-03-26T19:27:50.589630",
        "name": "alpine:3.9",
        "record_count": 1097
      },
...
```

### List vulnerabilities:
```shell
curl -X GET -u "admin":"foobar" http://localhost:8228/v1/images/sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82/vuln/all
```
**response:**
```json
{
  "imageDigest": "sha256:d05ecd4520cab5d9e5d877595fb0532aadcd6c90f4bbc837bc11679f704c4c82",
  "vulnerabilities": [
    {
      "feed": "vulnerabilities",
      "feed_group": "alpine:3.9",
      "fix": "1.1.1b-r1",
      "package": "libcrypto1.1-1.1.1a-r1",
      "package_cpe": "None",
      "package_name": "libcrypto1.1",
      "package_path": "None",
      "package_type": "APKG",
      "package_version": "1.1.1a-r1",
      "severity": "Medium",
      "url": "http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-1543",
      "vuln": "CVE-2019-1543"
    },
    {
      "feed": "vulnerabilities",
      "feed_group": "alpine:3.9",
      "fix": "1.1.1b-r1",
      "package": "libssl1.1-1.1.1a-r1",
      "package_cpe": "None",
      "package_name": "libssl1.1",
      "package_path": "None",
      "package_type": "APKG",
      "package_version": "1.1.1a-r1",
      "severity": "Medium",
      "url": "http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-1543",
      "vuln": "CVE-2019-1543"
    }
  ],
  "vulnerability_type": "all"
}
```

## [04 Deployin ValidatingWebhook](./04-validating-webhook.md)
