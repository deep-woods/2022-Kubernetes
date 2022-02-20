# <span id='top'>01 Kubernetes Overview</span>

<br>

[[Nodes]](#Nodes)  
[[`Shortcuts`]](#Shortcuts)  
[[`kubectl`]](#kubectl)  
[[`Minikube`]](#Minikube)  
[[Setup]](#setup)  
[[References]](#ref)

<br>

## <span id='Shortcuts'>Shortcuts</span>

[[Top]](#top)

| Shortcuts | Meaning                  |
| --------- | ------------------------ |
| `po`      | `Pods`                   |
| `rs`      | `ReplicaSet`             |
| `deploy`  | `Deployment`             |
| `svc`     | `Services`               |
| `ns`      | `Namespaces`             |
| `netpol`  | `Network policies`       |
| `pv`      | `Persistent Volumes`     |
| `pvc`     | `PersistentVolumeClaims` |
| `sa`      | `service accounts`       |

<br>

## <span id='Nodes'>Nodes</span>

[[Top]](#top)

- A cluster: set of nodes grouped together

`Kubernetes` Components
├──`API server`: front end for Kubernetes. All components talk to this server to interact with `K8s`
├──`etcd`: reliable key value store used by Kubernetes to store all data used to manage the cluster; responsible for implementing locks within the cluster to ensure that there are no conflicts between the Masters.
├──`kublet`: the agent that runs on each node in the cluster.
│ㅤㅤ└── responsible for interacting with a master
│ㅤㅤ└── provides health information of the worker node
│ㅤㅤ└── carries out actions requested by the Master on the worker nodes
├──`Container runtime`: the underlying software that is used to run containers (`docker`).
├──`Controller`: the brain; responsible for noticing and responding when nodes, containers or end points goes down.
└──`Scheduler`: responsible for distributing work or containers across multiple containers. (Load balancer?)

| `</>kube-APIserver`<br>`etcd`<br>`⚙️controller`<br>`⚙️scheduler` | `</>kublet`<br><br><br>`🐳Container Runtime` |
| ---------------------------------------------------------------- | -------------------------------------------- |
| **☸️Master**                                                     | **☸️Worker**                                 |

<br>

## <span id='kubectl'>`kubectl`</span>

[[Top]](#top)

**Functions**

      kubectl run hello-minikube
      kubectl cluster-info
      kubectl get nodes

- deploy and manage applications on a Kubernetes cluster
- get cluster information, the status of other nodes in the cluster...

<br>

### Hands-on

- Get cluster info: `kubectl cluster-info`

        Kubernetes control plane is running at https://127.0.0.1:6443
        CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
        Metrics-server is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

        To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

- Get node info: `kubectl get nodes`

        NAME         STATUS     ROLES                AGE    VERSION
        controlplane Ready      control-plane,master 5m51s  v1.22.2+k3s2

- Get version info: `kubectl version`

        Client Version:
            version.Info{
                Major:"1",
                Minor:"22",
                GitVersion:"v1.22.2+k3s2", GitCommit:"3f5774b41eb475eb10c93bb0ce58459a6f777c5f",
                GitTreeState:"clean",
                BuildDate:"2021-10-05T20:29:33Z",
                GoVersion:"go1.16.8",
                Compiler:"gc",
                Platform:"linux/amd64"}

        Server Version:
            version.Info{
                Major:"1",
                Minor:"22",
                GitVersion:"v1.22.2+k3s2", GitCommit:"3f5774b41eb475eb10c93bb0ce58459a6f777c5f",
                GitTreeState:"clean",
                BuildDate:"2021-10-05T20:29:33Z",
                GoVersion:"go1.16.8",
                Compiler:"gc",
                Platform:"linux/amd64"}

- Get OS info: `kubectl get nodes -o wide`

        NAME             STATUS ROLES                AGE    VERSION
        controlplane     Ready  control-plane,master 12m    v1.22.2+k3s2

        INTERNAL-IP EXTERNAL-IP OS-IMAGE KERNEL-VERSION
        172.25.0.10 <none>      Alpine   Linux v3.14 5.4.0-1063-gcp

        CONTAINER-RUNTIME
        containerd://1.5.7-k3s1

<br>

## <span id='Minikube'>`Minikube`</span>

[[Top]](#top)

| `</>kubelet`<br>`</>kube-APIserver`<br>`etcd`<br>`⚙️node-controller`<br>`⚙️replica-controller`<br>`🐳Container Runtime` |
| ----------------------------------------------------------------------------------------------------------------------- |
| **☸️Minikube**                                                                                                          |

<br>

## <span id='setup'>Setup</span>

[[Top]](#top)

| `Minikube`<br>`MicroK8s`<br>`Kubeadm` | `Google Cloud`<br>`AWS`<br>`Azure` |
| ------------------------------------- | ---------------------------------- |
| Tools                                 | Platforms                          |

- Hypervisor
- `kubectl`
- Minikube executable

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud (n.d.) Kubernetes for the Absolute Beginners - Hands-on https://kodekloud.com/courses/kubernetes-for-the-absolute-beginners-hands-on/
