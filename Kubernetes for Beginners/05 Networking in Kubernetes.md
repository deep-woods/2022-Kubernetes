# <span id='top'>05 Networking in ☸️Kubernetes</span>

[[Networking]](#Networking)  
[[References]](#ref)

<br>

## <span id='Networking'>Networking</span>

[[Top]](#top)

### Cluster Networking

When Kubernetes is initially configured, an internal private network with the address 10.244.0.0 is created to which all pods are attached.

- All containers/PODs can communicate to one another without NAT (Network address translation).
- All nodes can communicate with all containers and vice versa without NAT.
  - Kubernetes cluster networking solutions: Cisco ACI networks, Cilium, Big Cloud Fabric, Flannel, VMware NSX-T, and Calico
  - Depending on the platform you're deploying your Kubernetes cluster on, you may choose what best suits your needs.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
