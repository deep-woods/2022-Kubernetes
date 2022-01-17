# ☸️ 2022-Kubernetes

  - Kubernetes learn forest
  - All PPT slide images from: [freeCodeCamp docker tutorial by Nana](#ref)

  - `Kubectl cli`: for configuring the minikube cluster
  - `Minikube cli`: for start-up/deleting the cluster 

<br>

<span id="top">Learn index</span>

[Basics](#basics)   
[Installations](#install)  
[☸️Kubectl commands](#kubectl)  
[Apply configuration with Yaml](#config)  
[Commands](#commands)  
[](#)  
[](#)  
[](#)  
[](#)  

<br>


## <span id="basics">Basics</span>

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
|  Create command\n- Create deployment  | \n`kubectl create deployment [name]   |
|  Status of different K8s components  |  `kubectl get nodes`\n`kubectl get pod`\n`kubectl get services`\n`kubectl get replicaset`\n`kubectl create deployment`  |
| **Debugging pods**\n- Log to consol\n- Get interactive terminal\n- Get info about pod   |  \n`kubectl logs [pod name]`\n`kubectl exec -it [pod name] -- bin/bash`\n`kubectl describe pod [pod name]`  |
| **Use configuration file for CRUD**\n- Apply a configuration file\n- Delete with configuration file   |  \n`kubectl apply -f [file name]`\n`kubectle delete -f [file name]`  |



<br>

### <span id="ref">References</span>

[[☝️top]](#top)

  - freeCodeCamp (2020) Docker and Kubernetes - Full Course for Beginners https://www.youtube.com/watch?v=Wf2eSG3owoA&t=1615s