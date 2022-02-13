# <span id='top'>☸️03 ReplicaSets - Lab Demo</span>

[[🤲Hands-on Lab Demo]](#lab)  
[[References]](#ref)

<br>

## <span id='lab'>🤲Hands-on Lab Demo</span>

[[Top]](#top)

1. Know your pods: check resources

- `kubectl get pods`
- `kubectl get replicaset`
- `kubectl get rs`

2.  Understand your `replicaset`.

        kubectl get replicaset
        NAME DESIRED CURRENT READY AGE
        forest-replica-set 4 4 0 7s

- There is a total of only one `replicaset`.
- That `replicaset` desires 4 `pods`.

        kubectl describe replicaset forest-replica-set
        Name:         forest-replica-set
        Namespace:    default
        Selector:     name=busybox-pod
        Labels:       <none>
        Annotations:  <none>
        Replicas:     4 current / 4 desired
        Pods Status:  0 Running / 4 Waiting / 0 Succeeded / 0 Failed
        Pod Template:
        Labels:  name=busybox-pod
        Containers:
        busybox-container:
            Image:      busybox777  <---------------------- IMAGE USED.
            Port:       <none>
            Host Port:  <none>
            Command:
            sh
            -c
            echo Hello Kubernetes! && sleep 3600
            Environment:  <none>
            Mounts:       <none>
        Volumes:        <none>
        Events:
        Type    Reason            Age   From                   Message
        ----    ------            ----  ----                   -------
        Normal  SuccessfulCreate  118s  replicaset-controller  Created pod: forest-replica-set-nwvbn
        Normal  SuccessfulCreate  118s  replicaset-controller  Created pod: forest-replica-set-nqpnp
        Normal  SuccessfulCreate  118s  replicaset-controller  Created pod: forest-replica-set-9bpgz
        Normal  SuccessfulCreate  118s  replicaset-controller  Created pod: forest-replica-set-5dxmf

3. Create a `replicaset`.

- In kubernetes 1.16 replicaset was moved to apps/v1 apiVersion from extensions/v1beta1 - meaning: `apiVersion: v1` should be >> `apiVersion: apps/v1`

        kubectl create -f replicaset-definition-1.yaml
        replicaset.apps/replicaset-1 created

- The labels below the selector must match.

        selector:
            matchLabels:
                tier: nginx         <-------------MUST
        template:
            metadata:
                labels:
                    tier: frontend  <-------------MATCH
            spec:
                containers:
                  - name: nginx
                    image: nginx

        kubectl create -f replicaset-definition-2.yaml
        replicaset.apps/replicaset-2 created

4.  Delete `replicasets`.

        $ kubectl delete replicaset replicaset-1
        replicaset.apps "replicaset-1" deleted

        $ controlplane ~ ➜  kubectl delete replicaset replicaset-2
        replicaset.apps "replicaset-2" deleted

5.  Update the `replicaset`.

- Solution 1. Delete and recreate the ReplicaSet
- Solution 2. Update the existing ReplicaSet and then delete all PODs. The automatically created new ones will have the correct image.

        $ kubectl edit replicaset forest-replica-set
        replicaset.apps/forest-replica-set edited

        $ kubectl delete pod forest-replica-set-9bpgz forest-replica-set-rdfqf forest-replica-set-5dxmf forest-replica-set-nqpnp

6.  Scale up the `replicaset`!

        kubectl scale replicaset forest-replica-set --replicas=5
        replicaset.apps/forest-replica-set scaled

7.  Scale down the `replicaset` this time!

        kubectl scale replicaset forest-replica-set --replicas=2
        replicaset.apps/forest-replica-set scaled

        controlplane ~ ➜  kubectl get pods
        NAME                    READY   STATUS        RESTARTS   AGE
        forest-replica-set-nvmv7   1/1     Running       0          3m44s
        forest-replica-set-tc4gj   1/1     Running       0          3m44s
        forest-replica-set-lrb89   1/1     Terminating   <--------  79s
        forest-replica-set-p9vmv   1/1     Terminating   <--------  3m44s
        forest-replica-set-wmr6t   1/1     Terminating   <--------  3m44s

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
