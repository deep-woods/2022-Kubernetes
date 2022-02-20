# <span id='top'>01-03 ReplicaSet</span>

[[ReplicaSet]](#ReplicaSet)  
[[Update ReplicaSet]](#Update)  
[[Commands]](#Commands)  
[[References]](#ref)

## <span id='ReplicaSet'>ReplicaSet</span>

[[Top]](#top)

### Replica architecture

`replicaset`
├── `apiVersion`
├── `kind`
├── `metadata`
│ㅤㅤ└──
├── `spec`
│ㅤㅤ├── `template`
│ㅤㅤ├── `replicas`
│ㅤㅤ└── `selector`

<br>

`pod-definition.yml`

        apiVersion: v1
        kind: Pod
        ----------------CUT-AND-PASTE-BELOW--------------
        metadata:
            name: forest-pod
            labels:
                app: forest-app
                type: Pod
            spec:
                containers:
                    - name: nginx-container
                    image: nginx

`replicaset-definition.yml`

        apiVersion: apps/v1
        kind: ReplicaSet
        metadata:
            name: forest-rc
            labels:
                app: forest-app
                type: front-end

        spec:
            template:
                --------------PASTE-HERE---------------
                metadata:
                    name: forest-pod
                    labels:
                        app: forest-app
                        type: front-end
                spec:
                    containers:
                        - name: nginx-container
                          image: nginx
            replicas: 3
            selector:    <------- Helps ReplicaSet identify what pods fall under it.
                matchLabels:
                    type: front-end  <----- Using this, we can identify and select this replicas from among other replicas.

Watch out for the error:

        error: unable to recognize "replicaset-definition.yml": no matches for kind "ReplicationSet" in version "apps/v1"

- `selector`: is necessary because a `ReplicaSet` can control `pods` that were not created by it. It takes all pods that match the image specification into consideration when creating and managing `replicas`.

        $ kubectl create -f replicaset-definition.yml
        replicaset.apps/forest-rc-replicaset created

        $ kubectl get replicaset
        NAME                   DESIRED   CURRENT   READY   AGE
        forest-rc-replicaset   3         3         3       35s

        $ kubectl get pods
        NAME                         READY   STATUS    RESTARTS   AGE
        forest-rc-replicaset-jmqh7   1/1     Running   0          57s
        forest-rc-replicaset-hvv7h   1/1     Running   0          57s
        forest-rc-replicaset-7xssp   1/1     Running   0          57s

<br>

## <span id='Update'>Update ReplicaSet</span>

- `kubectl edit replicaset new-replica-set`: edit `.yml` file.
- `kubectl scale --replicas=6 -f replicaset-definition.yml`
- `kubectl scale --replicas=6 replicaset forest-rc-replicaset`: this does not update the `definition.yml`.

Update replicas by editing the `replicaset`.

        # Please edit the object below. Lines beginning with a '#' will be ignored,
        # and an empty file will abort the edit. If an error occurs while saving this file will be
        # reopened with the relevant failures.
        #
        apiVersion: apps/v1
        kind: ReplicaSet
        metadata:
            creationTimestamp: "2022-02-20T08:51:41Z"
            generation: 1
            name: new-forest-replica-set
            namespace: default
            resourceVersion: "1370"
            uid: c2e5748f-82a5-4251-8ebb-7bf701c063f7
        spec:
            replicas: 4
            selector:
                matchLabels:
                name: busybox-pod
            template:
                metadata:
                creationTimestamp: null
                labels:
                    name: busybox-pod
                spec:
                containers:
                - command:
                    - sh
                    - -c
                    - echo Hello Kubernetes! && sleep 3600
                    image: busybox
                    imagePullPolicy: Always
                    name: busybox-container
                    resources: {}
                    terminationMessagePath: /dev/termination-log
                    terminationMessagePolicy: File
                dnsPolicy: ClusterFirst
                restartPolicy: Always
                schedulerName: default-scheduler
                securityContext: {}
                terminationGracePeriodSeconds: 30
            status:
                fullyLabeledReplicas: 4
                observedGeneration: 1
                replicas: 4

        "/tmp/kubectl-edit-97354971.yaml" 44L, 1156B

Now delete the running `pods` so that the updated `replicaset` automatically create the substituting `pods` with updated image.

        $ kubectl get pods
        NAME                    READY   STATUS             RESTARTS   AGE
        new-replica-set-tvc7g   0/1     ImagePullBackOff   0          15m
        new-replica-set-p6wjf   0/1     ImagePullBackOff   0          15m
        new-replica-set-d54s7   0/1     ImagePullBackOff   0          15m
        new-replica-set-bcmz7   0/1     ImagePullBackOff   0          11m
        u

        $ kubectl delete pods new-forest-replica-set-tvc7g new-forest-replica-set-p6wjf new-forest-replica-set-d54s7 new-forest-replica-set-bcmz7
        pod "new-replica-set-tvc7g" deleted
        pod "new-replica-set-p6wjf" deleted
        pod "new-replica-set-d54s7" deleted
        pod "new-replica-set-bcmz7" deleted

        $ kubectl get pods
        NAME                    READY   STATUS    RESTARTS   AGE
        new-replica-set-nfsr7   1/1     Running   0          7s
        new-replica-set-k6dk8   1/1     Running   0          6s
        new-replica-set-2vbxx   1/1     Running   0          6s
        new-replica-set-jhclm   1/1     Running   0          7s

<br>

## <span id='Commands'>Commands</span>

[[Top]](#top)

- `kubectl replace -f replicaset-definition.yml`
- `kubectl get replicas`

### Update

- `kubectl scale --replicas=6 -f replicaset-definition.yml`
- `kubectl scale --replicas=6 replicaset forest-rc-replicaset`: this does not update the `definition.yml`.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
