# <span id='top'>02-07 Taints</span>

[[Taints]](#Taints)  
[[🤲Hands-on Lab]](#handson)  
[[Troubleshoot]](#Troubleshoot)
[[References]](#ref)

<br>

## <span id='Taints'>Taints</span>

[[Top]](#top)

- `kubectl taint nodes node-name key=value:taint-effect`
  - 3 taint-effects
    - `NoSchedule`
    - `PreferNoSchedule`
    - `NoExecute`
- `kubectl taint nodes node1 app=blue:NoSchedule`
- `kubectl describe node kubemaster | grep Taint`

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

- `kubectl run forest --image=nginx --restart=Never`
- `kubectl explain pod --recursive | less`
- `kubectl explain pod --recursive | grep -A5 tolerations`
- `kubectl create -f <FILE-NAME>.yaml`

      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>

1.  Study your taints.

        $ kubectl get nodes

            NAME STATUS ROLES AGE VERSION
            controlplane Ready control-plane,master 19m v1.20.0
            node01 Ready <none> 18m v1.20.0

        $ kubectl describe node controlplane | grep Taint
        Taints: node-role.kubernetes.io/master:NoSchedule

        $ kubectl describe node node01 | grep Taint
        Taints: <none>

2.  Create a taint on a `forest-node` with key of `spray`, value of `Forest` and effect of `NoSchedule`

- `kubectl taint node <node name> <key>=<value>:<effect>

        $ kubectl taint nodes forest-node spray=Forest:NoSchedule
            node/node01 tainted

3.  Create a new pod.

            $ docker images

                REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
                nginx  👈                           alpine              b9e2356ea1be        7 months ago        22.8MB
                nginx                                latest              4cdc5dd7eaad        7 months ago        133MB
                busybox                              latest              69593048aa3a        8 months ago        1.24MB
                k8s.gcr.io/kube-proxy                v1.20.0             10cc881966cf        14 months ago       118MB
                k8s.gcr.io/kube-scheduler            v1.20.0             3138b6e3d471        14 months ago       46.4MB
                k8s.gcr.io/kube-controller-manager   v1.20.0             b9fa1895dcaa        14 months ago       116MB
                k8s.gcr.io/kube-apiserver            v1.20.0             ca9843d3b545        14 months ago       122MB
                quay.io/coreos/flannel               v0.13.1-rc1         f03a23d55e57        15 months ago       64.6MB
                k8s.gcr.io/etcd                      3.4.13-0            0369cf4303ff        18 months ago       253MB
                k8s.gcr.io/coredns                   1.7.0               bfe3a36ebd25        20 months ago       45.2MB
                quay.io/coreos/flannel               v0.12.0-amd64       4e9f801d2217        23 months ago       52.8MB
                k8s.gcr.io/pause                     3.2                 80d28bedfe5d        2 years ago         683kB
                kodekloud/fluent-ui-running          latest              bd30270a8b9a        3 years ago         969MB

            $ kubectl run siren --image=nginx
                pod/siren created 👈

            $ kubectl get po
            NAME         READY   STATUS    RESTARTS   AGE
            siren 👈  0/1     Pending   0          2s  👈

4.  The pod just created is pending. Why?

- _"Because POD siren cannot tolerate taint forest"_
- `kubectl describe po siren | grep -i taint`
  - `grep -i`: `--ignore-case`

5.  P-1) Create a pod tainted with certain key:value.

        $ kubectl create -f bee-pod.yaml
        pod/bee created

        $ kubectl get po
        NAME READY STATUS RESTARTS AGE
        bee 0/1 ContainerCreating 👈 0 17s

             NAME   READY   STATUS      RESTARTS   AGE
             bee    1/1     Running 👈  0          2m10s

6.  P-2) Taint a pod with specific key:value.

        apiVersion: v1
        kind: Pod
        metadata:
        creationTimestamp: null
        labels:
        run: bee
        name: bee
        spec:
        containers:

        - image: nginx
          name: bee
          resources: {}
          dnsPolicy: ClusterFirst
          restartPolicy: Always
          tolerations: 👈
        - key: spray 👈
          value: mortein 👈
          effect: NoSchedule 👈
          operator: Equal
          status: {}

7.  Explore the taints of a specific node.

        $ kubectl describe node forest-plane | grep -A2 Taint

        Taints:             node-role.kubernetes.io/master:NoSchedule
        Unschedulable:      false
        Lease:

8.  Untain the node.

- `kubectl taint nodes <node name> <taint config>`

        $ kubectl taint 👈 nodes forest-plane node-role.kubernetes.io/master:NoSchedule- :point_left

            node/forest-plane untainted 👈

9.  Check out which node pods are on.

        $ kubectl get pods -o wide

        NAME      READY  STATUS   RESTARTS  AGE  IP         NODE      NOMINATED NODE  READINESS GATES
        bee       1/1    Running  0         94s  10.244.1.2 node01 👈 <none>          <none>
        mosquito  0/1    Pending  0         25m  <none>     <none>    <none>          <none>

10. Untaint a tainted node.

        $ kubectl taint node controlplane node-role.kubernetes.io/master:NoSchedule- 👈 MINUS SIGN MATTERS.

          node/controlplane untainted

<br>

## <span id='Troubleshoot'>Troubleshoot</span>

[[Top]](#top)

1.  `value must be empty when `operator`is 'Exists'`

        $ kubectl create -f bee-pod.yaml

            The Pod "bee" is invalid:
            * spec.tolerations[0].operator: Invalid value: core.Toleration{Key:"spray", Operator:"Exists", Value:"mortein", Effect:"NoSchedule", TolerationSeconds:(*int64)(nil)}: value must be empty when `operator` is 'Exists' 👈
            * spec.tolerations[1].operator: Invalid value: core.Toleration{Key:"spray", Operator:"Exists", Value:"mortein", Effect:"NoSchedule", TolerationSeconds:(*int64)(nil)}: value must be empty when `operator` is 'Exists'
            root@controlplane:~# nano bee-pod.yaml

2.  ?

            Warning  FailedScheduling  59s (x2 over 59s)  default-scheduler  0/2 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 1 node(s) had taint {spray: mortein}, that the pod didn't tolerate.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
