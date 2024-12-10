## Create CSR
- ```openssl genrsa -out puto.key 2048``` & ```openssl req -new -key puto.key -out puto.csr -subj "/CN=puto/O=group1"```

- Sign CSE with Kubernetes CA
```cat puto.csr | base64 | tr -d '\n'``` (get the base64 string)

- create csr.yaml
```nano csr.yaml```

- Modify the csr file with base64
```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: puto
spec:
  request: <BASE_64_STRING>
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

- Apply: ```kubectl apply -f csr.yaml```
- Approve: ```kubectl certificate approve puto```

## Create a cert
- kubectl get csr puto -o jsonpath='{.status.certificate}' | base64 --decode > puto.crt


## Create A Role
- ```nano role.yaml```
- Add Role here:
```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: puto
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

- Apply the role: ```kubectl apply -f role.yaml```
```
ninja@putos-MacBook-Air Kubernetes % kubectl apply -f role.yaml                                                           
role.rbac.authorization.k8s.io/pod-reader created
rolebinding.rbac.authorization.k8s.io/read-pods created
```

## let kubeconfig file know the changes
- kubectl config set-credentials puto --client-certificate=puto.crt --client-key=puto.key
- kubectl config set-context puto-context --cluster=kubernetes --namespace=default --user=puto
- Check: kubectl config get-contexts
``` 
ninja@putos-MacBook-Air Kubernetes % kubectl config get-contexts                                                                   
CURRENT   NAME             CLUSTER          AUTHINFO         NAMESPACE
*         docker-desktop   docker-desktop   docker-desktop   
          puto-context    kubernetes       puto            default
```

## Switch to current context
- kubectl config use-context docker-desktop
- kubectl config get-contexts
- OP:
```
ninja@putos-MacBook-Air Kubernetes % kubectl config get-contexts             
CURRENT   NAME             CLUSTER          AUTHINFO         NAMESPACE
          docker-desktop   docker-desktop   docker-desktop   
*         puto-context    kubernetes       puto            default
```

## delete context
- kubectl config delete-context puto-context
- kubectl config unset users.puto

