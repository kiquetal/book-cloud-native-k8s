#### Kubernetes power tools

#### Imperative kubectl commands

    kubectl create namespace my-new-namespace
    
    kubectl delete namespace my-new-namespace

    kubectl edit deployments my-deployment

The major problem with imperative commands is that you have no single source of truth.

The `--dry-run=client` flag tells `kubectl` not to actually create the resource, but merely to print out what it would have created. The `o -yaml`flag gives you the resource manifest in YAML.


#### Forwarding a Container Port

Using `kubectl port-forward` to talk with a container or a service.


#### Executing Commands on Containers.

```bash
kubectl run alpine --image alpine --command -- sleep 999
kubectl exec -it alpine -- /bin/bash
```

If pod has more than one container you need to specify with `-c containerName`

```bash
kubectl exec -it -c container2 POD_name -- /bin/bash
```

#### Using busybox

`Busybox` image is particuarly useful because it contains  wealth of the most
commonly used Unix commands, such as `cat`, `echo`, `find`,`grep`, `kill`.

```bash
kubectl run busybox --image=busybox:1.28 --rm -it --restart=Never /bin/sh
```

#### Executing command on busybox

```bash
kubectl exec -it POD_NAME -- /bin/busybox sh
```

#### Context and namespaces

A context is a combination of a cluster, user and a namespace.

```bash
kubectl config get-contexts
```

#### Change context 

```bash
kubectl config use-context gke
```

#### Obtain current context

```bash
kubectl config current-context
```


