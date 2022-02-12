# <span id='top'>02 Pods - Short demo</span>

[[🤲Hands-on Pods]](#handson-pod)
[[References]](#ref)

<br>

## <span id='POD'>Pods</span>

[[Top]](#top)

### Pods

_Pods are the smallest deployable units of computing that you can create and manage in Kubernetes._

_A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. A Pod's contents are always co-located and co-scheduled, and run in a shared context. A Pod models an application-specific "logical host": it contains one or more application containers which are relatively tightly coupled. In non-cloud contexts, applications executed on the same physical or virtual machine are analogous to cloud applications executed on the same logical host._ ([k8s docs](https://kubernetes.io/docs/concepts/workloads/pods/))

1.  start a `pod`.

        kubectl run nginx --image=nginx
        pod/nginx created

2.  Check `pods`.

            $ kubectl get pods
            NAME    READY   STATUS    RESTARTS   AGE
            nginx   0/1     Pending   0          4s

3.  Examin a `pod` in depth.

        kubectl describe pod nginx
        Name:         nginx
        Namespace:    default
        Priority:     0
        Node:         <none>
        Labels:       run=nginx
        Annotations:  <none>
        Status:       Pending
        IP:
        IPs:          <none>
        Containers:
        nginx:
            Image:        nginx
            Port:         <none>
            Host Port:    <none>
            Environment:  <none>
            Mounts:
            /var/run/secrets/kubernetes.io/serviceaccount from default-token-zdc29 (ro)
        Conditions:
        Type           Status
        PodScheduled   False
        Volumes:
        default-token-zdc29:
            Type:        Secret (a volume populated by a Secret)
            SecretName:  default-token-zdc29
            Optional:    false
        QoS Class:       BestEffort
        Node-Selectors:  <none>
        Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                        node.kubernetes.io/unreachable:NoExecute op=Exists for 300s

You can find about the multiple stages this `pod` has gone through.

        Events:
        Type     Reason            From               Message
        ----     ------            ----               -------
        Warning  FailedScheduling  default-scheduler  0/1 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate.

        Normal   Scheduled         default-scheduler  Successfully assigned default/nginx to node1
        Warning  Evicted           kubelet            The node was low on resource: ephemeral-storage.
        Normal   Pulled            kubelet            Successfully pulled image "nginx" in 10.907054513s
        Normal   Killing           kubelet            Stopping container nginx
        Normal   SandboxChanged    kubelet            Pod sandbox changed, it will be killed and re-created.
        Normal   Pulling           kubelet            Pulling image "nginx"
        Normal   Pulled            kubelet            Successfully pulled image "nginx" in 1.082882951s
        Normal   Created           kubelet            Created container nginx
        Normal   Started           kubelet            Started container nginx

4.  Alternatively... check your `pod` this way:

        kubectl get pods -o wide

        NAME    READY   STATUS    RESTARTS   AGE     IP       NODE    NOMINATED NODE   READINESS GATES
        nginx   0/1     Evicted   0          8m39s   <none>   node1   <none>           <none>

<br>

### Notes

Create a pod:

- by running (... or)

        kubectl run <POD NAME> --image=<IMAGE>

- by creating

        kubectl create deployment <POD NAME> --image=<IMAGE>

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- Concepts https://kubernetes.io/docs/concepts/
- Pods https://kubernetes.io/docs/concepts/workloads/pods/
