# <span id='top'>01-06 ☸️Imperative Commands</span>

[[Imperative Commands]](#Imperative)  
[[🤲Hands-on]](#handson)  
[[References]](#ref)

<br>

## <span id='Imperative'>Imperative Commands</span>

- `--dry-run`: By default, the resource will be created as soon as the command is run.
- `--dry-run=client`: simply test your command. The resource is NOT created, instead, it tells you whether the resource can be created and if your command is right.
- `-o yaml`: This will output the resource definition in YAML format on screen.

### Compare:

|            | Declarative<br>(Create)                                                                                                                                                                                                              | Imperative<br>(Generate `Manifest.yaml`)                                                                                                                                  |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| POD        | `kubectl run nginx --image=nginx`                                                                                                                                                                                                    | `kubectl run nginx --image=nginx --dry-run=client -o yaml`<br>Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)                                        |
| Deployment | `kubectl create deployment --image=nginx nginx`                                                                                                                                                                                      | `kubectl create deployment --image=nginx nginx --dry-run -o yaml`                                                                                                         |
|            | `kubectl create deployment nginx --image=nginx --replicas=4`                                                                                                                                                                         | `kubectl scale deployment nginx --replicas=4`                                                                                                                             |
|            |                                                                                                                                                                                                                                      | `kubectl create deployment nginx --image=nginx--dry-run=client -o yaml > nginx-deployment.yaml`                                                                           |
| `Service`  | Create a Service named redis-service of type ClusterIP to expose pod redis on port 4753.<br>`kubectl expose pod redis --port=4753 --name redis-service --dry-run=client -o yaml`<br>automatically uses the pod's label as selectors. | `kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml`<br>This does not use the pods labels as selectors, but assumes selectors as `app=redis` |
|            | `kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml`                                                                                                                                   | `kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`                                                                            |

- You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service.
- Create a Service named nginx of type NodePort to expose pod nginx’s port 80 on port 30080 on the nodes:
- Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port.

<br>

## <span id='handson'>🤲Hands-on</span>

[[Top]](#top)

1.  Create a pod imperatively.

        $ kubectl run nginx-pod --image=nginx:alpine
        pod/nginx-pod created

        $ kubectl get po
        NAME        READY   STATUS              RESTARTS   AGE
        nginx-pod   0/1     ContainerCreating   0          9s

2.  Create a `pod.yaml` definition file.

        $ kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis-pod.yaml

        $ nano redis-pod.yaml
        $ cat redis-pod.yaml

                apiVersion: v1
                kind: Pod
                metadata:
                creationTimestamp: null
                labels:
                    tier: db
                name: redis
                spec:
                containers:
                - image: redis:alpine
                    name: redis
                dnsPolicy: ClusterFirst
                restartPolicy: Always

Create a `k8s` object using the definition file.

        $ kubectl create -f redis-pod.yaml
        pod/redis created

        $ kubectl get po
        NAME        READY   STATUS    RESTARTS   AGE
        nginx-pod   1/1     Running   0          2m22s
        redis       1/1     Running   0          9s

3.  Create a service that exposes a pod.

        $ kubectl expose pod redis --port=6379 --name redis-service
        service/redis-service exposed

        $ kubectl get service
        NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
        kubernetes      ClusterIP   10.43.0.1     <none>        443/TCP    14m
        redis-service   ClusterIP   10.43.12.16   <none>        6379/TCP   40s

4.  Create a deployment.

        $ kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3
        deployment.apps/webapp created

        $ kubectl get deploy
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        webapp   0/3     3            0           8s

5.  Create a pod.

        $ kubectl run custom-nginx --image=nginx --port=8080
        pod/custom-nginx created

        controlplane ~ ➜  kubectl get po
        NAME                      READY   STATUS              RESTARTS   AGE
        nginx-pod                 1/1     Running             0          14m
        redis                     1/1     Running             0          12m
        webapp-56847f875b-82pm9   1/1     Running             0          112s
        webapp-56847f875b-hhcwz   1/1     Running             0          112s
        webapp-56847f875b-7q4qz   1/1     Running             0          112s
        custom-nginx              0/1     ContainerCreating   0          10s

6.  Create a namespace.

        kubectl create ns dev-ns
        namespace/dev-ns created

        controlplane ~ ➜  kubectl get ns
        NAME              STATUS   AGE
        kube-system       Active   20m
        default           Active   20m
        kube-public       Active   20m
        kube-node-lease   Active   20m
        dev-ns            Active   5s

7.  Create a deployment in a specific namespace.

        kubectl create deployment redis-deploy --namespace=dev-ns --image=redis --replicas=2
        deployment.apps/redis-deploy created

        controlplane ~ ➜  kubectl get deploy
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        webapp   3/3     3            3           4m1s       <---------------- NOT HERE

        controlplane ~ ➜  kubectl get deploy --namespace=dev-ns
        NAME           READY   UP-TO-DATE   AVAILABLE   AGE
        redis-deploy   2/2     2            2           32s    <-------------- HERE

8.  Create a pod and then a service by the same name.

Meaning, the created pod will be exposed as a service.

        $ kubectl run httpd --image=httpd:alpine --port=80 --expose
        service/httpd created
        pod/httpd created

Check the result.

        kubectl get all
        NAME                          READY   STATUS    RESTARTS   AGE
        pod/nginx-pod                 1/1     Running   0          24m
        pod/redis                     1/1     Running   0          22m
        pod/webapp-56847f875b-82pm9   1/1     Running   0          12m
        pod/webapp-56847f875b-hhcwz   1/1     Running   0          12m
        pod/webapp-56847f875b-7q4qz   1/1     Running   0          12m
        pod/custom-nginx              1/1     Running   0          10m
        pod/httpd                     1/1     Running   0          110s

        NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
        service/kubernetes      ClusterIP   10.43.0.1       <none>        443/TCP    29m
        service/redis-service   ClusterIP   10.43.12.16     <none>        6379/TCP   15m
        service/httpd           ClusterIP   10.43.179.220   <none>        80/TCP     109s

        NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/webapp   3/3     3            3           12m

        NAME                                DESIRED   CURRENT   READY   AGE
        replicaset.apps/webapp-56847f875b   3         3         3       12m

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
