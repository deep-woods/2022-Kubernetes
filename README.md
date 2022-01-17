# ☸️ 2022-Kubernetes

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/container-orchestration.png" width=600 />

  - Kubernetes learn forest
  - All PPT slide images from: [freeCodeCamp docker tutorial by Nana](#ref)

  - `Kubectl cli`: for configuring the minikube cluster
  - `Minikube cli`: for start-up/deleting the cluster 

<br>

<span id="top">Learn index</span>

[[Basics]](#basics)   
[[Installations]](#install)  
[[☸️Kubectl commands]](#kubectl)  
[[Apply configuration with Yaml]](#config)  
[[Commands]](#commands)  
[[Secret configuration file]](#secret)  
[[Service configuration file]](#service)  
[[Deployment Service and Config Map]](#map)  
[[Mongo Express External Service]](#external)  

<br>


## <span id="basics">Basics</span>2

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/01%20minikube.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/02%20minikube%20hypervisor.png" width=600 />

  **☸️What is Kubernetes?**   
  
    - Container orchestration tool developed by Google   
    - Helps you manage containerised applications in different deployment environments including physical setup, virtual, and cloud `envs`.   
    - A need for tool like ☸️Kubernetes is on the rise as the tech world is shifting towards Microservices from Monolithic service and architecture.   

  **Waht features do orchestration tools offer?**   

    - High availability or no downtime  
    - Scalability and high performance  
    - Disaster recovery through backup and restoration capabilities  

<br>

## <span id="install">Installation</span>

[[☝️top]](#top)

  - `Kubectl cli`: for configuring the minikube cluster
  - `Minikube cli`: for start-up/deleting the cluster 

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/03%20kubectl%20get%20nodes.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/04%20minikube%20status.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/05%20kubectl%20version.png" width=600 />

  **Install minikube**

    - Download https://minikube.sigs.k8s.io/docs/start/     
    - Run `minikube start`     
    - Run `minikube start --vm-driver=hyperkit`     
    - Run `kubectl get nodes`   
    - `minikube status`   
    - `kubectl version`   

<br>

## <span id="kubectl">☸️Kubectl commands</span>

[[☝️top]](#top)

  **Get status of different components**    

    - `kubectl get nodes`   
    - `kubectl get pods`   
    - `kubectl get services`    

<br>

  **Create a pod/deployment**

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/06%20kubectl%20create%20deployment.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/07%20kubectl%20create%20get%20pod.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/07-02%20kubectl%20delete%20deployment.png" width=600 />

    - `Pod` is the smallest unit,
    - But we create `deployment`, an abstraction over `pods`

    **Create/delete `Pod`**
    - `kubectl create deployment NAME --image=IMAGE`
    - `kubectl get pod`
    - `kubectl delete deployment image-depl`

<br>

  **Check logs**

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/08%20kubectl%20logs%20POD_ID.png" width=600 />

    - `kubectl logs`

<br>

  **Exec**

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/09%20MongoDB.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/10%20MongoDB%20bin_bash.png" width=600 />

    - `kubectl exec -it CONTAINER_ID` 

<br>

## <span id="apply">Apply configuration with Yaml</span>

[[☝️top]](#top)

  **Create a `.yaml` file**

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/11%20deployment%20yaml.png" width=600 />

    - Mac: `touch config-file.yaml`
    - Windows: `type nul >> nginx-deployment.yaml`
    - `notepad nginx-deployment.yaml`
    - 
    - `kubectl apply -f config-file.yaml`

<br>

## <span id="commands">Commands summary</span>

[[☝️top]](#top)

|  Function  |  Commands  |
| -- | -- |
|  Create command<br>- Create deployment  | <br>`kubectl create deployment [name]   |
|  Status of different K8s components  |  `kubectl get nodes`<br>`kubectl get pod`<br>`kubectl get services`<br>`kubectl get replicaset`<br>`kubectl create deployment`  |
| **Debugging pods**<br>- Log to consol<br>- Get interactive terminal<br>- Get info about pod   |  <br>`kubectl logs [pod name]`<br>`kubectl exec -it [pod name] -- bin/bash`<br>`kubectl describe pod [pod name]`  |
| **Use configuration file for CRUD**<br>- Apply a configuration file<br>- Delete with configuration file   |  <br>`kubectl apply -f [file name]`<br>`kubectle delete -f [file name]`  |

<br>

## <span id="secret">Secret Configuration file</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/12%20secret.png" width=600 />

| kind   |  "Secret"  |
| -- | -- |
| metadata/name   | a random name   |
| type   | "Opaque" - default for arbitrary key-value pairs   |
| data   | the actual contents - in key-value pairs   |

- `type nul >> mongo-secret.yaml`
- Generate encrypted username & password: `echo -n 'username' | base64`
- `kubectl apply -f mongo-secret.yaml` 
- `kubectl get secret`
- Then go to `mongodb-deployment.yaml` to reference the `secret`.

Later check if everything is running well.
- `kubectl get all`
- `kubectl get pod`
- `kubectl get pod --watch`

<br>

## <span id="service">Service Configuration file</span>

[[☝️top]](#top)

| kind   |  "Service"  |
| -- | -- |
| metadata/name   | a random name   |
| selector | to connect o Pod through label |
| - ports<br>port:<br>targetPort   | <br>Service port<br>containerPort of deployment |

- `kubectl get service`
- `kubectl describe service`
- `kubectl get pod -o wide`

<br>

## <span id="map">Deployment Service and Config Map</span>

[[☝️top]](#top)

- Which database to connect?
- Which credentials to authenticate?
- Config Map
  - external configuration
  - centralised
  - other components can use it

<br>

## <span id="external">Mongo Express External Service</span>

<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/13%20internal%20external%20service%20.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Kubernetes/blob/main/images/14%20service%20running.png" width=600 />

[[☝️top]](#top)

- type: "Loadbalancer"  
- assigns service an `external IP` address in order to accept external requets
- `nodePort`: must be between `30000-32767`

- `kubectl apply -f mongo-express.yaml`
- `kubectl get service`
- `minikube service [service name]`
  - `minikube service mongo-express-service`

<br>

**Architecture**

`Mongo Express External service` -- `Mongo Express Pod` -- `Mongo DB Internal Service` -- `Mongo DB Pod`


<br>

### <span id="ref">References</span>

[[☝️top]](#top)

  - freeCodeCamp (2020) Docker and Kubernetes - Full Course for Beginners https://www.youtube.com/watch?v=Wf2eSG3owoA&t=1615s