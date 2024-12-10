## whats inside pods
- ```kubectl run nginx --image=nginx```
- how kubectl talks with Kube clusters: using ```cat ~/.kube/config``` this file ```kubeconfig file```
- Check the flow here
![Alt text](./Kubernetes.png)

## Contains of kubeconfig file
```
ninja@Sohams-MacBook-Air projects % kubectl config view
apiVersion: v1
clusters:           #This section defines the different Kubernetes clusters you can connect to
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://127.0.0.1:6443
  name: docker-desktop
contexts:           # is a combination of a cluster, a user, and a namespace
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: docker-desktop
kind: Config
preferences: {}
users:             # authentication details for connecting to the Kubernetes clusters
- name: docker-desktop
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
```
    - A context specifies the default settings to use when interacting with the Kubernetes cluster.

## How Kubernetes finds kubeconfig file
- Check if exported on OS shell level
  ```export KUBECONFIG=path_to_YOUR_CONFIG_FILE```
- As a flag
  ```kubectl get nodes --kubeconfig ~/.kube/config```
- If not then default location: ~/.kube/config

## Merging multiple KubeConfig files
 - export KUBECONFIG=/path/to/first/config:/path/to/second/config:/path/to/third/config

## GVK & GVR
- GVK = Group Version Kind [Pods: Group is core; apiVersion: v1, kind: Pod]
```
ninja@Sohams-MacBook-Air ~ % kubectl run nginx --image=nginx --dry-run=client -o json
{
    "kind": "Pod",
    "apiVersion": "v1",
    "metadata": {
        "name": "nginx",
        "creationTimestamp": null,
        "labels": {
            "run": "nginx"
        }
    },
    "spec": {
        "containers": [
            {
                "name": "nginx",
                "image": "nginx",
                "resources": {}
            }
        ],
        "restartPolicy": "Always",
        "dnsPolicy": "ClusterFirst"
    },
    "status": {}
}
```
- GVR = Group Version Resource [kind: Deployment, apiVersion: apps/v1]
```
ninja@Sohams-MacBook-Air ~ % kubectl create deployment nginx --image=nginx --dry-run=client -o json              
{
    "kind": "Deployment",
    "apiVersion": "apps/v1",
    "metadata": {
        "name": "nginx",
        "creationTimestamp": null,
        "labels": {
            "app": "nginx"
        }
    },
    "spec": {
        "replicas": 1,
        "selector": {
            "matchLabels": {
                "app": "nginx"
            }
        },
        "template": {
            "metadata": {
                "creationTimestamp": null,
                "labels": {
                    "app": "nginx"
                }
            },
            "spec": {
                "containers": [
                    {
                        "name": "nginx",
                        "image": "nginx",
                        "resources": {}
                    }
                ]
            }
        },
        "strategy": {}
    },
    "status": {}
}
```

## CIDR Range
- every pod has different id
- Classless Inter-Domain Routing

## Pod lifecycle
- once created -> state <pending>; until no node is assigned
- Container Creating: pull image; attaches networks
- Running: 
- Error:
- crash loop back off: process dying too many times
- success

## init container
- runs before the main application containers in a Kubernetes pod.
- Perform setup tasks before main containers start
- Run initialization logic
- Runs to completion before main containers
- Runs sequentially
- Must complete successfully for pod to proceed to main containers

