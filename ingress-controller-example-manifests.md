# Example manifests for getting started with Contour

Deploy two sample applications
```
kubectl run app1 --image=cloudfoundry/lattice-app --port=8080
kubectl expose deployment app1 --port=443 --target-port=8080
kubectl run app2 --image=cloudfoundry/lattice-app --port=8080
kubectl expose deployment app2 --port=443 --target-port=8080
```

Setup TLS Termination - Create a Cert and Key and input that as base64 encoded in yaml below.
```
apiVersion: v1
kind: Secret
metadata:
  name: webappsecret
  namespace: default
type: kubernetes.io/tls
data:
  tls.crt:

  tls.key:
```

Example HTTPProxy Manifest
```
apiVersion: projectcontour.io/v1
kind: HTTPProxy
metadata:
  name: basic
  namespace: default
spec:
  virtualhost:
    fqdn: apps.pks.tanzuplatform.net # Domain Need to Point Envoy SVC Load Balancer IP to this fqdn
    tls:
      secretName: webappsecret
  routes:

  # routes:
    # Route 1
  - conditions:
    - prefix: /app1
    pathRewritePolicy:
      replacePrefix:
      - prefix: /app1
        replacement: /
    services:
    - name: app1
      port: 443

  #   # Route 2
  - conditions:
    - prefix: /app2
    pathRewritePolicy:
      replacePrefix:
      - prefix: /app2
        replacement: /
    services:
    - name: app2
      port: 443
```
