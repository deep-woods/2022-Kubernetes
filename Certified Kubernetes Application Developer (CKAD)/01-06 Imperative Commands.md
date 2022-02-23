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

        kubectl run nginx-pod --image=nginx:alpine
        pod/nginx-pod created

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
