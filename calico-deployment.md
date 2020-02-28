# Calico Deployment

Note: Calico is an open-source project with enterprise support available from Tigera. VMWare does not provide support for this product.

Here is a modified deployment manifest that deploys Calico on PKS which comes in with flannel CNI.

1. Obtain PKS cluster with **priveleged containers** support enabled

    - this is necessary for Calico to be installed on the nodes (otherwise you will receive `The DaemonSet "calico-node" is invalid: spec.template.spec.containers[0].securityContext.privileged: Forbidden: disallowed by cluster policy` error)

1. `kubectl apply -f calico.yaml`

1. `kubectl -n kube-system get pod` and see that `calico-node-*` pod is running for each node

1. run through [test_procedure.md](test_procedure.md)

## Modifications

YAMLs were obtained from [Installing Calico for policy (advanced)](https://docs.projectcalico.org/v3.11/getting-started/kubernetes/installation/other).

- Modifications Made
  - use Flannel given MTU (Flannel specific)
  - use Flannel given pod CIDR (Flannel specific)
    - this change was necessary since Flannel does not update node objects' spec.podCIDR which is used by Calico's usePodCidr configuration
  - configure default Calico pod CIDR (probably is not used) (PKS specific)
  - configure CNI binaries location (PKS specific)
    - we delegate to host-local IPAM and need to rename the node-local pod subnet from .1/24 to .0

Ref:
- https://docs.projectcalico.org/introduction/
