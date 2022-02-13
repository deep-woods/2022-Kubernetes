# <span id='top'>☸️04 Deployment</span>

[[🤲Hands-on Demo]](#Demo)  
[[🤲Hands-on Lab]](#Lab)  
[[References]](#ref)

<br>

## <span id='Demo'>🤲Hands-on Demo</span>

[[Top]](#top)

- `kubectl get deployments`
- `kubectl describe deployment <deployment name>`
- `kubectl get all`

        $ kubectl create -f deployment.yaml
        deployment.apps/forest-app-deployment created

        $ kubectl get deployments
        NAME               READY   UP-TO-DATE   AVAILABLE   AGE
        forest-app-deployment   0/3     2            0           60s

Know your `deployment` - by getting pods:

        kubectl get pods
        NAME                               READY   STATUS    RESTARTS   AGE
        forest-app-deployment-fd8649446-6xgjd   0/1     Pending   0          2m2s
        forest-app-deployment-fd8649446-kmhch   0/1     Pending   0          2m2s
        forest-app-replicaset-nmfw2             0/1     Pending   0          119m
        forest-app-replicaset-v6lks             0/1     Pending   0          119m

Or by getting the description of your `deployment`.

        $ kubectl describe deployment forest-app-deployment
        Name:                   forest-app-deployment
        Namespace:              default
        CreationTimestamp:      Sun, 13 Feb 2022 17:37:04 +0000
        Labels:                 app=nginx
                                tier=frontend
        Annotations:            deployment.kubernetes.io/revision: 1
        Selector:               app=forest-app
        Replicas:               3 desired | 2 updated | 4 total | 0 available | 4 unavailable
        StrategyType:           RollingUpdate
        MinReadySeconds:        0
        RollingUpdateStrategy:  25% max unavailable, 25% max surge
        Pod Template:
            Labels:  app=forest-app
            Containers:
            nginx:
                Image:        nginx
                Port:         <none>
                Host Port:    <none>
                Environment:  <none>
                Mounts:       <none>
            Volumes:        <none>
        Conditions:
        Type           Status  Reason
        ----           ------  ------
        Available      False   MinimumReplicasUnavailable
        Progressing    True    ReplicaSetUpdated
        OldReplicaSets:  forest-app-replicaset (2/2 replicas created)
        NewReplicaSet:   forest-app-deployment-fd8649446 (2/2 replicas created)
        Events:
        Type    Reason             Age    From                   Message
        ----    ------             ----   ----                   -------
        Normal  ScalingReplicaSet  2m28s  deployment-controller  Scaled up replica set forest-app-deployment-fd8649446 to 2

Alternatively, try:

        kubectl get all

        NAME                                   READY   STATUS    RESTARTS   AGE
        pod/forest-app-deployment-fd8649446-6xgjd   0/1     Pending   0          4m25s
        pod/forest-app-deployment-fd8649446-kmhch   0/1     Pending   0          4m25s
        pod/forest-app-replicaset-nmfw2             0/1     Pending   0          121m
        pod/forest-app-replicaset-v6lks             0/1     Pending   0          121m

        NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   135m

        NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/forest-app-deployment   0/3     2            0           4m25s

        NAME                                         DESIRED   CURRENT   READY   AGE
        replicaset.apps/forest-app-deployment-fd8649446   2         2         0       4m25s
        replicaset.apps/forest-app-replicaset             2         2         0       121m

<br>

## <span id='Lab'>🤲Hands-on Lab</span>

[[Top]](#top)

1. Commands for `replicasets` and `deployments`

- `kubectl get pods`
- `kubectl get replicaset`
- `kubectl get rs`
- `kubectl get deployment`
- `kubectl get deploy`

2.  Check the status of `replica pods`.

        kubectl get replicaset
        NAME                             DESIRED   CURRENT   READY   AGE
        front-forest-deployment-56d8ff5458   4         4         0       31s

        kubectl get pod
        NAME                                   READY   STATUS             RESTARTS   AGE
        front-forest-deployment-56d8ff5458-qnst6   0/1     ErrImagePull       0          75s
        front-forest-deployment-56d8ff5458-thchn   0/1     ImagePullBackOff   0          75s
        front-forest-deployment-56d8ff5458-58mkx   0/1     ImagePullBackOff   0          75s
        front-forest-deployment-56d8ff5458-kzcgl   0/1     ImagePullBackOff   0          75s

3.  Check `deployment` info.

        kubectl describe deployment front-forest-deployment
        Name:                   front-forest-deployment
        Namespace:              default
        CreationTimestamp:      Sun, 13 Feb 2022 17:54:20 +0000
        Labels:                 <none>
        Annotations:            deployment.kubernetes.io/revision: 1
        Selector:               name=busybox-pod
        Replicas:               4 desired | 4 updated | 4 total | 0 available | 4 unavailable
        StrategyType:           RollingUpdate
        MinReadySeconds:        0
        RollingUpdateStrategy:  25% max unavailable, 25% max surge
        Pod Template:
            Labels:  name=busybox-pod
            Containers:
                busybox-container:
                    Image:      busybox
                    Port:       <none>
                    Host Port:  <none>
                    Command:
                        sh
                        -c
                        echo Hello Kubernetes! && sleep 3600
                Environment:  <none>
                Mounts:       <none>
            Volumes:        <none>
        Conditions:
        Type           Status  Reason
        ----           ------  ------
        Available      False   MinimumReplicasUnavailable
        Progressing    True    ReplicaSetUpdated
        OldReplicaSets:  <none>
        NewReplicaSet:   front-forest-deployment-56d8ff5458 (4/4 replicas created)
        Events:
        Type    Reason             Age    From                   Message
        ----    ------             ----   ----                   -------
        Normal  ScalingReplicaSet  3m11s  deployment-controller  Scaled up replica set front-forest-deployment-56d8ff5458 to 4

4.  Create new `deployment`.

        $  kubectl create -f httpd-frontend.yaml
        deployment.apps/httpd-frontend created

        $  kubectl get deployment
        NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
        deployment-1          2/2     2            2           3m7s
        frontend-deployment   0/4     4            0           11m
        httpd-frontend        0/3     3            0           9s

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
