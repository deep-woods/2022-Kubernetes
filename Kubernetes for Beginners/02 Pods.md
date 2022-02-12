# <span id='top'>Pods</span>

<br>

[[Pods]](#POD)  
[[`definition.yaml`]](#yaml)  
[[🤲Hands-on Pods]](#handson-pod)  
[[References]](#ref)

<br>

## <span id='POD'>Pods</span>

[[Top]](#top)

`POD`: a single instance of an application

- the smallest object (most basic unit) that you can create in Kubernetes.
- `yaml` as inputs for the creation of objects such as POD's, replicas, deployment services

**Syntax**

    pod-definition.yml

    apiVersion: v1
    kind: Pod
    metadata:
        name: myapp-pod
        labels:
            app: myapp
            type: front-end
    spec:
        containers: [List/Array]
            - name: nginx-container
              image: nginx

- `metadata`: data about the object
- `kubectl create -f pod-definition.yml`
- `kubectl get pods`
- `kubectl describe pod myapp-pod`

| Kind       | Version |
| ---------- | ------- |
| Pod        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

<br>

## <span id='yaml'>`definition.yaml`</span>

[[Top]](#top)

    tortoise-definition.yaml

    apiVersion: v1
    kind: Pod
    metadata:
        name: tortoise
        labels:
            app: nginx
            tier: frontend
    spec:
    containers:
    - name: aldabra-giant
        image: nginx
    - name: galapagos-giant
        image: busybox

Now create a `pod` based on the `tortoise-definition.yaml`

    kubectl apply -f tortoise-definition.yaml
    pod/tortoise created

Check status

    kubectl get pods
    NAME       READY   STATUS               RESTARTS      AGE
    tortoise   1/2     CrashLoopBackOff😂   3 (19s ago)   82s

<br>

### `Yaml` tips

`Yaml` for `K8s` on `VS code`: To enable Kubernetes support, we have to configure it to work with YAML files which we will develop for `k8s`.

Menu-click on `☸️gear icon` > `Extension Settings` > `Settings.json`  
Then ad the following:

        "yaml.schemas": {
                "kubernetes": "*.yaml"
            }

<br>

## <span id='handson-pod'>🤲Hands-on Pods</span>

[[Top]](#top)

1.  Get `pod` information.

        kubectl get pods
        No resources found in default namespace.

2.  Create a `pod`.

- `kubectl run [pod name] --image=[image]`

        kubectl run hello-whale --image=nginx
        pod/hello-whale created

        kubectl get pods
        NAME          READY   STATUS    RESTARTS   AGE
        hello-whale   1/1     Running   0          10s

3.  Count `pods`.

        kubectl get pods

        NAME            READY   STATUS    RESTARTS   AGE
        hello-whale     1/1     Running   0          3m43s
        whale-kube   1/1     Running   3 (4m2s ago)   54m
        octopus-kube   1/1     Running   3 (4m2s ago)   54m
        siren-kube   1/1     Running   3 (4m2s ago)   54m

4.  Explore `pod` data.

- [`hello-whale `](#hello-whale)
- [`whale-kube`](#whale-kube)
- [`octopus-kube`](#octopus-kube)
- [`siren-kube`](#siren-kube)

        kubectl describe

        Name:         hello-whale
        Namespace:    default
        Priority:     0
        Node:         controlplane/172.25.0.82
        Start Time:   Mon, 07 Feb 2022 14:36:47 +0000
        Labels:       run=hello-whale
        Annotations:  <none>
        Status:       Running
        IP:           10.42.0.9
        IPs:
        IP:  10.42.0.9
        Containers:
        hello-whale:
        Container ID:   containerd://a50782ccbc6e77cc52603f34902c490770b7e285ffc70bde0220ff912a1d556d
        Image:          nginx
        Image ID:       docker.io/library/nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
        Port:           <none>
        Host Port:      <none>
        State:          Running
        Started:      Mon, 07 Feb 2022 14:36:58 +0000
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bhv5t (ro)
        Conditions:
        Type              Status
        Initialized       True
        Ready             True
        ContainersReady   True
        PodScheduled      True
        Volumes:
        kube-api-access-bhv5t:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
        QoS Class:                   BestEffort
        Node-Selectors:              <none>
        Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
        Events:
        Type    Reason     Age   From               Message
        ----    ------     ----  ----               -------
        Normal  Scheduled  48s   default-scheduler  Successfully assigned default/hello-whale to controlplane
        Normal  Pulling    48s   kubelet            Pulling image "nginx"
        Normal  Pulled     41s   kubelet            Successfully pulled image "nginx" in 6.385871474s
        Normal  Created    41s   kubelet            Created container hello-whale
        Normal  Started    38s   kubelet            Started container hello-whale

<span id='whale-kube'>whale-kube</span>

        Name:         whale-kube
        Namespace:    default
        Priority:     0
        Node:         controlplane/172.25.0.82
        Start Time:   Mon, 07 Feb 2022 14:36:54 +0000
        Labels:       tier=busybox
        Annotations:  <none>
        Status:       Running
        IP:           10.42.0.10
        IPs:
        IP:           10.42.0.10
        Controlled By:  ReplicaSet/newpods
        Containers:
        busybox:
        Container ID:  containerd://674251cda6b1fc98bc7e58f70fac1aff0eb301ebe54cf2afded2fc71abf0fea4
        Image:         busybox
        Image ID:      docker.io/library/busybox@sha256:afcc7f1ac1b49db317a7196c902e61c6c3c4607d63599ee1a82d702d249a0ccb
        Port:          <none>
        Host Port:     <none>
        Command:
        sleep
        1000
        State:          Running
        Started:      Mon, 07 Feb 2022 14:37:03 +0000
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gql9c (ro)
        Conditions:
        Type              Status
        Initialized       True
        Ready             True
        ContainersReady   True
        PodScheduled      True
        Volumes:
        kube-api-access-gql9c:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
        QoS Class:                   BestEffort
        Node-Selectors:              <none>
        Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
        Events:
        Type    Reason     Age   From               Message
        ----    ------     ----  ----               -------
        Normal  Scheduled  42s   default-scheduler  Successfully assigned default/whale-kube to controlplane
        Normal  Pulling    38s   kubelet            Pulling image "busybox"
        Normal  Pulled     35s   kubelet            Successfully pulled image "busybox" in 2.775044157s
        Normal  Created    33s   kubelet            Created container busybox
        Normal  Started    33s   kubelet            Started container busybox

<span id='octopus-kube'>octopus-kube</span>

        Name:         octopus-kube
        Namespace:    default
        Priority:     0
        Node:         controlplane/172.25.0.82
        Start Time:   Mon, 07 Feb 2022 14:36:54 +0000
        Labels:       tier=busybox
        Annotations:  <none>
        Status:       Running
        IP:           10.42.0.11
        IPs:
        IP:           10.42.0.11
        Controlled By:  ReplicaSet/newpods
        Containers:
        busybox:
        Container ID:  containerd://3d31bfdda8afb73d509c9c442e045957a858dab2a60eee13d7f2673105921194
        Image:         busybox
        Image ID:      docker.io/library/busybox@sha256:afcc7f1ac1b49db317a7196c902e61c6c3c4607d63599ee1a82d702d249a0ccb
        Port:          <none>
        Host Port:     <none>
        Command:
        sleep
        1000
        State:          Running
        Started:      Mon, 07 Feb 2022 14:37:04 +0000
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-cct46 (ro)
        Conditions:
        Type              Status
        Initialized       True
        Ready             True
        ContainersReady   True
        PodScheduled      True
        Volumes:
        kube-api-access-cct46:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
        QoS Class:                   BestEffort
        Node-Selectors:              <none>
        Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
        Events:
        Type    Reason     Age   From               Message
        ----    ------     ----  ----               -------
        Normal  Scheduled  42s   default-scheduler  Successfully assigned default/octopus-kube to controlplane
        Normal  Pulling    38s   kubelet            Pulling image "busybox"
        Normal  Pulled     35s   kubelet            Successfully pulled image "busybox" in 2.665018536s
        Normal  Created    33s   kubelet            Created container busybox
        Normal  Started    32s   kubelet            Started container busybox

<span id='siren-kube'>siren-kube</span>

        Name:         siren-kube
        Namespace:    default
        Priority:     0
        Node:         controlplane/172.25.0.82
        Start Time:   Mon, 07 Feb 2022 14:36:54 +0000
        Labels:       tier=busybox
        Annotations:  <none>
        Status:       Running
        IP:           10.42.0.12
        IPs:
        IP:           10.42.0.12
        Controlled By:  ReplicaSet/newpods
        Containers:
        busybox:
        Container ID:  containerd://c6c4d366e9c34cc58e2c2aeafa7e516833285a67d26d7a4c838f8dc3fee01a09
        Image:         busybox
        Image ID:      docker.io/library/busybox@sha256:afcc7f1ac1b49db317a7196c902e61c6c3c4607d63599ee1a82d702d249a0ccb
        Port:          <none>
        Host Port:     <none>
        Command:
        sleep
        1000
        State:          Running
        Started:      Mon, 07 Feb 2022 14:37:04 +0000
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lbcf6 (ro)
        Conditions:
        Type              Status
        Initialized       True
        Ready             True
        ContainersReady   True
        PodScheduled      True
        Volumes:
        kube-api-access-lbcf6:
        Type:                    Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds:  3607
        ConfigMapName:           kube-root-ca.crt
        ConfigMapOptional:       <nil>
        DownwardAPI:             true
        QoS Class:                   BestEffort
        Node-Selectors:              <none>
        Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                                node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
        Events:
        Type    Reason     Age   From               Message
        ----    ------     ----  ----               -------
        Normal  Scheduled  42s   default-scheduler  Successfully assigned default/siren-kube to controlplane
        Normal  Pulling    38s   kubelet            Pulling image "busybox"
        Normal  Pulled     35s   kubelet            Successfully pulled image "busybox" in 2.961016247s
        Normal  Created    33s   kubelet            Created container busybox
        Normal  Started    32s   kubelet            Started container busybox

5.  Check on `node` the pods are placed on.

- `kubectl get pods -o wide`
- `kubectl describe pod <pod name>`
- `READY` indicates how many containers are ready out of total.

        kubectl get pods -o wide

        NAME            READY   STATUS             RESTARTS      AGE     IP           NODE           NOMINATED NODE   READINESS GATES
        hello-whale     1/1     Running            0             7m56s   10.42.0.9    controlplane   <none>           <none>
        newpods-sbxzr   1/1     Running            0             7m44s   10.42.0.12   controlplane   <none>           <none>
        newpods-cmbh5   1/1     Running            0             7m44s   10.42.0.10   controlplane   <none>           <none>
        newpods-vwprp   1/1     Running            0             7m44s   10.42.0.11   controlplane   <none>           <none>
        turtle-pod      0/1     CrashLoopBackOff   6 (61s ago)   7m6s    10.42.0.13   controlplane   <none>           <none>

`kubectl describe pod <pod name>`

        kubectl describe pod turtle-pod

        Name: turtle-pod
        Namespace: default
        Priority: 0
        Node: controlplane/172.25.0.40
        Start Time: Mon, 07 Feb 2022 15:45:58 +0000
        Labels: run=turtle-pod
        Annotations: <none>
        Status: Running
        IP: 10.42.0.13
        IPs:
        IP: 10.42.0.13
        Containers:
        turtle-pod:
        Container ID: containerd://11d142798b35a0054928185d01d1b21c185a4eca26385f9038e3f378a1234dda
        Image: busybox
        Image ID: docker.io/library/busybox@sha256:afcc7f1ac1b49db317a7196c902e61c6c3c4607d63599ee1a82d702d249a0ccb
        Port: <none>
        Host Port: <none>
        State: Waiting
        Reason: CrashLoopBackOff
        Last State: Terminated
        Reason: Completed
        Exit Code: 0
        Started: Mon, 07 Feb 2022 15:52:03 +0000
        Finished: Mon, 07 Feb 2022 15:52:03 +0000
        Ready: False
        Restart Count: 6
        Environment: <none>
        Mounts:
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bdpj5 (ro)
        Conditions:
        Type Status
        Initialized True
        Ready False
        ContainersReady False
        PodScheduled True
        Volumes:
        kube-api-access-bdpj5:
        Type: Projected (a volume that contains injected data from multiple sources)
        TokenExpirationSeconds: 3607
        ConfigMapName: kube-root-ca.crt
        ConfigMapOptional: <nil>
        DownwardAPI: true
        QoS Class: BestEffort
        Node-Selectors: <none>
        Tolerations: node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
        node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
        Events:
        Type Reason Age From Message

        ---

        Normal Scheduled 8m57s default-scheduler Successfully assigned default/turtle-pod to controlplane
        Normal Pulled 8m56s kubelet Successfully pulled image "busybox" in 173.53696ms
        Normal Pulled 8m55s kubelet Successfully pulled image "busybox" in 192.254625ms
        Normal Pulled 8m40s kubelet Successfully pulled image "busybox" in 199.837684ms
        Normal Pulled 8m9s kubelet Successfully pulled image "busybox" in 186.128051ms
        Normal Created 8m8s (x4 over 8m56s) kubelet Created container turtle-pod
        Normal Started 8m8s (x4 over 8m56s) kubelet Started container turtle-pod
        Normal Pulling 7m17s (x5 over 8m57s) kubelet Pulling image "busybox"
        Warning BackOff 3m44s (x25 over 8m54s) kubelet Back-off restarting failed container

6. Verify the number of containers in `pods`.

- See `READY` IN `kubectl get pods`
- `READY`: `Running Containers in POD / Total Containers in POD`

        NAME            READY   STATUS             RESTARTS        AGE
        hello-whale     1/1     Running            0               14m
        newpods-sbxzr   1/1     Running            0               14m
        newpods-cmbh5   1/1     Running            0               14m
        newpods-vwprp   1/1     Running            0               14m
        turtle-pod      0/1     CrashLoopBackOff   7 (2m23s ago)   13m
        penguin-kube          1/2     ImagePullBackOff   0               2m57s

7.  Explore `container` data.
    8.1 Check the `state` of a `container`.

            kubectl describe pod penguin-kube

            Name:         penguin-kube
            Namespace:    default
            ...

            Containers:    <------------------------------------------------ HERE
                nginx:
                    Container ID:   containerd://171184edaed45b0a0d555e27b3c8534d692419bffc919e33577daa312b706592
                    Image:          nginx
                    Image ID:       docker.io/library/nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
                    Port:           <none>
                    Host Port:      <none>
                    State:          Running
                    Started:      Mon, 07 Feb 2022 15:56:36 +0000
                    Ready:          True
                    Restart Count:  0
                    Environment:    <none>
                    Mounts:
                    /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-mg2kz (ro)

                agentx:
                    Container ID:
                    Image:          agentx
                    Image ID:
                    Port:           <none>
                    Host Port:      <none>
                    State:          Waiting         <----------------- HERE
                    Reason:       ImagePullBackOff
                    Ready:          False
                    Restart Count:  0
                    Environment:    <none>
                    Mounts:
                    /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-mg2kz (ro)
            ...

8.2 Observe the various `STATUS` of pods.

- `Running`
- `CrashLoopBackOff`
- `ImagePullBackOff`: A Docker image with this name doesn't exist on Docker Hub

                kubectl get pods

                NAME            READY   STATUS             RESTARTS        AGE
                hello-whale     1/1     Running            0               14m
                newpods-sbxzr   1/1     Running            0               14m
                newpods-cmbh5   1/1     Running            0               14m
                newpods-vwprp   1/1     Running            0               14m
                turtle-pod      0/1     CrashLoopBackOff   7 (2m23s ago)   13m
                penguin-kube          1/2     ImagePullBackOff   0               2m57s

  8.3 Examine the event history.

                Events:
                Type     Reason     From               Message
                ----     ------     ----               -------
                Normal   Scheduled  default-scheduler  Successfully assigned default/webapp to controlplane
                Normal   Pulling    kubelet            Pulling image "nginx"
                Normal   Pulled     kubelet            Successfully pulled image "nginx" in 363.624584ms
                Normal   Created    kubelet            Created container nginx
                Normal   Started    kubelet            Started container nginx
                Normal   Pulling    kubelet            Pulling image "agentx"
                Warning  Failed     kubelet            Failed to pull image "agentx": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/agentx:latest": failed to resolve reference "docker.io/library/agentx:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
                Warning  Failed     kubelet            Error: ErrImagePull
                Normal   BackOff    kubelet            Back-off pulling image "agentx"
                Warning  Failed     kubelet            Error: ImagePullBackOff

9.  Delete a `pod`.

        kubectl delete pod penguin-kube
        pod "penguin-kube" deleted

10. Create a `pod-definition.yaml` file.

- `kubectl run [pod name] --image=[image] --dry-run=client -o yaml > [file name].yaml`
- `kubectl create -f [file name].yaml`
- `kubectl get pods`

<br>

- `kubectl run [pod name] --image=[image] --dry-run=client -o yaml > [file name].yaml`: create a `manifest file`.
- `-o`: Formatting output. To output details to your terminal window in a specific format, add the `-o` (or `--output`) flag to a supported `kubectl` command.
- `dry-run`: Must be "none", "server", or "client". If client strategy, only print the object that would be sent, without sending it. If server strategy, submit server-side request without persisting the resource.

        kubectl run penguin-pod --image=redis --dry-run=client -o yaml

        apiVersion: v1
        kind: Pod
        metadata:
        creationTimestamp: null
        labels:
            run: penguin-pod
        name: penguin-pod
        spec:
        containers:
        - image: redis
            name: penguin-pod
            resources: {}
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        status: {}


        kubectl create -f penguin-definition.yaml
        pod/penguin-redi created


        kubectl get pods

        NAME            READY   STATUS             RESTARTS         AGE
        hello-whale     1/1     Running            0                45m
        newpods-cmbh5   1/1     Running            2 (12m ago)      45m
        newpods-vwprp   1/1     Running            2 (12m ago)      45m
        newpods-sbxzr   1/1     Running            2 (12m ago)      45m
        penguin-redi    1/1     Running            0                5m57s
        turtle-pod      0/1     CrashLoopBackOff   13 (3m12s ago)   45m
        redis           0/1     ImagePullBackOff   0                17m

  <br>

11. If you want to modify the `pod`...

- Either modify the `-definition.yaml`, or
- Edit: `kubectl edit pod <pod ID>`
- `kubectl apply -f [image-definition].yaml`

        kubectl apply -f penguin-definition.yaml

        Warning: resource pods/redis is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically...YALA YALA

        pod/redis configured   <----------- WHAT MATTERS.

<br>

12. Alternatively, you can also get a bit of help from documenatation: https://kubernetes.io/docs/concepts/workloads/pods/

        apiVersion: v1
        kind: Pod
        metadata:
          name: redis
        spec:
          containers:
            - name: redis
              image: busybox

13. Update `pod` data and reload it.

        kubectl get pods
        NAME            READY   STATUS         RESTARTS   AGE
        nginx           1/1     Running        0          16m
        newpods-smcsg   1/1     Running        0          16m
        newpods-lrkfs   1/1     Running        0          16m
        newpods-p5brb   1/1     Running        0          16m
        redis           0/1     ErrImagePull   0          4s    <----------- something's WRONG...

        kubectl edit pod redis

        kubectl get pods
        NAME            READY   STATUS    RESTARTS        AGE
        nginx           1/1     Running   0               21m
        newpods-p5brb   1/1     Running   1 (4m13s ago)   20m
        newpods-lrkfs   1/1     Running   1 (4m13s ago)   20m
        newpods-smcsg   1/1     Running   1 (4m13s ago)   20m
        redis           1/1     Running   0               4m40s  <----------- FIXed !

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- Docs on `kubectl print options` https://kubernetes.io/docs/reference/kubectl/_print/
- Docs on `kubectl-commands` https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands
- Issues with `yaml` extension setting and auto-complate https://github.com/redhat-developer/vscode-yaml/issues/299
