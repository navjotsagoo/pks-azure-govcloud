# Role Based Access for Developers in PKS

Enable `Configure created clusters to use UAA as the OIDC provider` setting in PKS **UAA** tile config to allow this functionality.

Example ClusterRole
```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: developer-role
rules:
- apiGroups: ["","extensions","apps","batch"]
  resources: ["pods","deployments","namespaces","jobs","configmaps"]
  verbs: ["get","list","watch","create","update","patch","delete"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get","list","watch"]
```

Example ClusterRoleBinding
```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: brice-developer-role-binding
subjects:
- kind: User
  name: oidc:developer-1-name # developer-1-name should exist in UAA.
  apiGroup: rbac.authorization.k8s.io
- kind: User
  name: oidc:developer-2-name # oidc: prefix is required here in the manifest and it comes from UAA section of the PKS tile
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: developer-role
  apiGroup: rbac.authorization.k8s.io
```

Instead of using PKS CLI, developers can instead used a kubectl plugin `pkstoken` to download their unique credentials in a self-service manner. Make sure when you put the plugin in your PATH, it is named `kubectl-pkstoken`

References:
- https://docs.pivotal.io/pks/1-6/manage-cluster-permissions.html
- https://github.com/pivotal/pkstoken
