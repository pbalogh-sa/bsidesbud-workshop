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

```

### Port-forwarding anchore-engine service:
```shell
kubectl port-forward svc/anchore-anchore-engine -n anchore 8228
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
