# <span id='top'>06 Services – NodePort</span>

[[Services – NodePort]](#NodePort)  
[[Services – ClusterIP]](#ClusterIP)  
[[Service - LoadBalancer]](#LoadBalancer)  
[[🤲Hands-on Demo]](#Demo)  
[[🤲Hands-on Lab]](#Lab)  
[[References]](#ref)

<br>

## <span id='NodePort'>Services – NodePort</span>

[[Top]](#top)

- `NodePort`: Exposes the Service on each Node's IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You'll be able to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>.
- `LoadBalancer`: Exposes the Service externally using a cloud provider's load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.

<br>

## <span id='ClusterIP'>Services – ClusterIP</span>

[[Top]](#top)

<br>

## <span id='LoadBalancer'>Services – LoadBalancer</span>

[[Top]](#top)

<br>

## <span id='Demo'>🤲Hands-on Demo</span>

[[Top]](#top)

### Install `minikube`

_`minikube` is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes._

_All you need is Docker (or similarly compatible) container or a Virtual Machine environment, and Kubernetes is a single command away: minikube start_

        curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
        sudo install minikube-linux-amd64 /usr/local/bin/minikube

        minikube start

### Service

Even if there are pods up and running in your cluster, end-users cannot access them. In order to enable them to do so on their web browser, we have to create a `Service`.

        $ kubectl create -f service-definition.yaml
        service/myapp-service created

        $ kubectl get svc
        NAME            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
        kubernetes      ClusterIP      10.96.0.1       <none>        443/TCP        8m34s
        my-nginx-svc    LoadBalancer   10.99.55.241    <pending>     80:32283/TCP   8m17s
        myapp-service   NodePort       10.101.15.145   <none>        80:30005/TCP   25s

<br>

## <span id='Lab'>🤲Hands-on Lab</span>

[[Top]](#top)

### Default Service (`ClusterIP`)

        kubectl get service
        NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   7m23s

1.  What is the `targetPort` of this default service?

- `Labels`
- `TargetPort`
- `Endpoints`

        kubectl describe service kubernetes
        Name:              kubernetes
        Namespace:         default
        Labels:            component=apiserver   <----------
                           provider=kubernetes   <----------
        Annotations:       <none>
        Selector:          <none>
        Type:              ClusterIP
        IP Family Policy:  SingleStack
        IP Families:       IPv4
        IP:                10.43.0.1
        IPs:               10.43.0.1
        Port:              https  443/TCP
        TargetPort:        6443/TCP         <-------------
        Endpoints:         10.44.218.3:6443
        Session Affinity:  None
        Events:            <none>

2.  Explore the deployment.

        kubectl get deployment
        NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
        forest-webapp-deployment   4/4     4            4           25s

3.  Create a service so you can access it from external browser as an end-user.

        kubectl create -f service-definition.yaml
        service/forest-service created

        controlplane ~ ➜  kubectl get service
        NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kubernetes       ClusterIP   10.43.0.1      <none>        443/TCP          16m
        forest-service   NodePort    10.43.58.192   <none>        8080:30080/TCP   4s

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- Install Minikube https://minikube.sigs.k8s.io/docs/start/
