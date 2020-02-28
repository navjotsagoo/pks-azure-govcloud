# Ingress Controller (Contour) Deployment

Manually create TLS certs for encrypted traffic between Envoy and Contour.
https://projectcontour.io/docs/v1.2.0/grpc-tls-howto/

Make sure the file names of the certificats and keys match as described in the documentation. Otherwise, you will run into certificate errors.

Download the Contour K8 Deployment Manifest files from here.
https://github.com/projectcontour/contour/tree/master/examples/contour
- Remove 02-job-certgen.yaml (since we already created our certs.)

- Upload certs in a new `projectcontour` namespace

```bash
kubectl create namespace projectcontour

kubectl create secret -n projectcontour generic cacert \
        --from-file=./certs/cacert.pem

kubectl create secret -n projectcontour tls contourcert \
        --key=./certs/contourkey.pem --cert=./certs/contourcert.pem

kubectl create secret -n projectcontour tls envoycert \
        --key=./certs/envoykey.pem --cert=./certs/envoycert.pem
```

- Change the location of the docker `images` to internal private registry in `03-contour.yaml` and `O3-envoy.yaml`.

- Create a `docker-registry` secret and modify the the two deployment files above to pull images with the proper credentials in the manifest.
  ```
  imagePullSecrets:
  - name: harbor-registry-secret
  ```
  `kubectl create secret docker-registry acr --docker-server=https://harbor-registry-domain --docker-username=username --docker-password=password -n projectcontour`

- Update the `02-service-envoy.yaml` file with Azure Load Balancer annotation and a Pre-Provisioned Public IP Address that will be assigned to the load balancer (this Load Balancer will be dedicated for the ingress of application workloads). Sample Reference: https://gist.github.com/nsagoo-pivotal/7524d338feab96f2ebbd85d2646d6905

- `kubectl apply` on all the deployment files)

References:
- https://projectcontour.io/docs/v1.2.0/
