# <span id='top'>☸️Architecture</span>

[[☸️Architecture]](#Architecture)  
[[🤲Hands-on]](#handson)  
[[References]](#ref)

<br>

## <span id='Architecture'>☸️Architecture</span>

[[Top]](#top)

Components
├── `API` server: frontend
├── `etcd`: distributed key-value store.
├── `Container runtime`: underlying software running the `containers` (which is docker)
├── `kubelet`: agent responsible for running the containers on the nodes.
├── `Scheduler`
└── `Controller`

<br>

- `Node` (minion): a physical or virtual machine where `☸️k8s` is installed.
- `Cluster`: a set of `nodes` grouped together.
- `Master`: another `node` which acts as a `Cluster` manager.

Master vs Worker Nodes
├── `Master node`
│ㅤㅤ├── `</>kube-apiserver`
│ㅤㅤ├── `etcd`
│ㅤㅤ├── `Controller`
│ㅤㅤ└── `Scheduler`
└── `Worker node`
ㅤㅤㅤ├── `</>kube-apiserver`
ㅤㅤㅤ└── `Container Runtime`
ㅤㅤㅤ
<br>

## <span id='handson'>🤲Hands-on</span>

[[Top]](#top)

        kubectl run hello-minikube

        kubectl cluster-info

        kubectl get nodes

<br>

Launch (run) a `pod`.

        $ kubectl run hello-minikube --image=hello-minikube
        pod/hello-minikube created

        $ kubectl get pods
        NAME             READY   STATUS         RESTARTS   AGE
        nginx            1/1     Running        0          40s
        newpods-fm585    1/1     Running        0          28s
        newpods-kpfs4    1/1     Running        0          28s
        newpods-d9lls    1/1     Running        0          28s
        hello-minikube   0/1     ErrImagePull   0          7s   <----

Get cluster's info.

        $ kubectl cluster-info

        Kubernetes control plane is running at https://127.0.0.1:6443
        CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
        Metrics-server is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

Get nodes' info.

        $ kubectl get nodes

        NAME           STATUS   ROLES                  AGE   VERSION
        controlplane   Ready    control-plane,master   18m   v1.22.2+k3s2

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
