# <span id='top'>01-05 Namespace☸️</span>

[[Namespace Commands]](#Commands)  
[[Resource Quota]](#Quota)  
[[🤲Hands-on lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Commands'>Namespace Commands</span>

- `kubectl get ns`
- `kubectl get ns --no-headers`
- `kubectl get ns --no-headers | wc -l`
- `kubectl -n research get po --no-headers | wc -l`
- `kubectl run pod redis --image=redis --namespace=finance`
- `kubectl -n research get pods --no-headers`
- `kubectl run redis --image=redis --dry-run=client -o yaml > pod.yaml`

        $ kubectl get ns

        NAME              STATUS   AGE
        default           Active   4m16s
        kube-system       Active   4m16s
        kube-public       Active   4m15s
        kube-node-lease   Active   4m15s
        finance           Active   72s
        marketing         Active   72s
        dev               Active   72s
        prod              Active   72s
        manufacturing     Active   72s
        research          Active   72s

- `kubectl get ns --no-headers | wc -l`

        $ kubectl get ns --no-headers | wc -l
        10

- `kubectl -n research get po --no-headers | wc -l`

       $ kubectl -n research get po --no-headers | wc -l
       2

- `kubectl run pod redis --image=redis --namespace=finance`

        $ kubectl run pod redis --image=redis --namespace=finance
        pod/pod created

- `kubectl -n research get pods --no-headers`

        dna-1   0/1   CrashLoopBackOff   7 (93s ago)   12m
        dna-2   0/1   CrashLoopBackOff   7 (93s ago)   12m

- `kubectl run redis --image=redis --dry-run=client -o yaml > pod.yaml`

        $ kubectl get po --namespace=finance

        NAME      READY   STATUS             RESTARTS      AGE
        payroll   1/1     Running            0             13m
        redis     1/1     Running            0             2m1s
        pod       0/1     CrashLoopBackOff   5 (50s ago)   4m5s

<br>

## <span id='Quota'>Resource Quota</span>

[[Top]](#top)

Limit resources in a namespace.

        compute-quota.yaml

        apiVersion: v1
        kind: ResourceQuota
        metadata:
            name: compute-quota
            namespace: dev

        spec:
            hard:
                prods: "10"
                requests.cpu: "4"
                requests.memory: 5Gi
                limits.cpu: "10"
                limits.memory: 10Gi

        $ kubectl create -f compute-quota.yaml

<br>

## <span id='handson'>Hands-on Lab</span>

[[Top]](#top)

- `kubectl get pods --namespace=research`
- `kubectl -n research get pods --no-headers | wc -l`
- `kubectl run redis --image=redis --dry-run=client -o yaml > pod.yaml`
- `kubectl apply -f pod.yaml`
- `kubectl get pods --all-namespaces`
- `kubectl get po --all-namespaces | grep blue`

1.  How many namespaces?

        kubectl get ns
        NAME              STATUS   AGE
        kube-system       Active   29m
        default           Active   29m
        kube-public       Active   29m
        kube-node-lease   Active   29m
        finance           Active   24m
        marketing         Active   24m
        dev               Active   24m
        prod              Active   24m
        manufacturing     Active   24m
        research          Active   24m

2.  How many pods in a specific namespace?

- `kubectl get pods --namespace=research`
- `kubectl -n research get pods --no-headers | wc -l`

        kubectl get pods --namespace=research
        NAME    READY   STATUS             RESTARTS        AGE
        dna-2   0/1     CrashLoopBackOff   10 (112s ago)   28m
        dna-1   0/1     CrashLoopBackOff   10 (96s ago)    28m

3.  Create a pod in a specific namespace.

        kubectl run redis --image=redis --namespace=finance
        pod/redis created

        $ kubectl get pods --namespace=finance
        NAME      READY   STATUS    RESTARTS   AGE
        payroll   1/1     Running   0          32m
        redis     1/1     Running   0          14s

Or alternatively, do it this way:

- `kubectl run redis --image=redis --dry-run=client -o yaml > pod.yaml`

        controlplane ~ ➜  ls
        pod.yaml     sample.yaml

        $ cat pod.yaml

        apiVersion: v1
        kind: Pod
        metadata:
          creationTimestamp: null
          labels:
            run: redis
          name: redis
        spec:
          containers:
          - image: redis
            name: redis
            resources: {}
          dnsPolicy: ClusterFirst
          restartPolicy: Always
        status: {}


        $ kubectl apply -f pod.yaml
        pod/redis created


        $ kubectl -n finance get po redis
        NAME    READY   STATUS    RESTARTS   AGE
        redis   1/1     Running   0          20s

4.  See which pods belong to which namespaces.

        kubectl get pods --all-namespaces
        NAMESPACE       NAME                                     READY   STATUS             RESTARTS         AGE
        kube-system     helm-install-traefik-crd--1-smrq8        0/1     Completed          0                40m
        kube-system     helm-install-traefik--1-wk4zh            0/1     Completed          1                40m
        kube-system     svclb-traefik-q5hp9                      2/2     Running            0                39m
        kube-system     traefik-74dd4975f9-hl9ww                 1/1     Running            0                39m
        kube-system     coredns-85cb69466-t6vcs                  1/1     Running            0                40m
        kube-system     local-path-provisioner-64ffb68fd-th5f2   1/1     Running            0                40m
        kube-system     metrics-server-9cf544f65-2j6z5           1/1     Running            0                40m
        dev             redis-db                                 1/1     Running            0                35m
        marketing       redis-db                                 1/1     Running            0                35m
        manufacturing   red-app                                  1/1     Running            0                35m
        finance         payroll                                  1/1     Running            0                35m
        marketing       blue                                     1/1     Running            0                35m
        research        dna-2                                    0/1     CrashLoopBackOff   11 (4m23s ago)   35m
        research        dna-1                                    0/1     CrashLoopBackOff   11 (4m14s ago)   35m
        default         redis                                    1/1     Running            0                3m29s
        finance         redis                                    1/1     Running            0                3m17s

Alternatively, find out about a specific pod.

        $ kubectl get po --all-namespaces | grep blue
        marketing       blue                                     1/1     Running            0               9m12s

5.  DNS name - the Blue application to access the database db-service in the same namespace - marketing

        Host Name           Host Port
        db-service          6379

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- https://kubernetes.io/docs/reference/kubectl/conventions/
