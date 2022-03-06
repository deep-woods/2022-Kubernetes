# <span id='top'>03-02 Rolling Updates and Rollbacks</span>

[[Rolling Updates and Rollbacks]](#Rolling)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Rolling'>Rolloing Updates and Rollbacks</span>

[[Top]](#top)

### Cheatsheet

| Category | Command                                                                                                         |
| -------- | --------------------------------------------------------------------------------------------------------------- |
| Create   | `kubectl craete -f deployment-definition.yaml`                                                                  |
| Get      | `kubectl get deployment`                                                                                        |
| Update   | `kubectl apply -f deployment-definition.yaml`<br>`kubectl set image deployment/<deploy name> nginx=nginx:1.9.1` |
| Status   | `kubectl rollout status deployment/<deploy name>`<br> `kubectl rollout history deployment/<deploy name>`        |
| Rollback | `kubectl rollout undo deployment/<deploy name>`                                                                 |

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Test the running application's connection.

        $ bash curl-test.sh 👈

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

            Hello, Application Version: v1 ; Color: blue OK

2.  Identify the image used for the running application.

        $ kubectl describe pod/frontend-rs-2-r5zt2 | grep -A5 image 👈

              Image:        kodekloud/webapp-color:v1 👈
              Port:         8080/TCP
              Host Port:    0/TCP
              Environment:  <none>
              Mounts:       <none>
            Volumes:        <none>

3.  Inspect the deployment and identify the rolling strategy.

        $ kubectl describe deploy frontend

            Name:                   frontend
            Namespace:              default
            CreationTimestamp:      Sun, 06 Mar 2022 06:55:44 +0000
            Labels:                 <none>
            Annotations:            deployment.kubernetes.io/revision: 1
            Selector:               name=webapp
            Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
            StrategyType:           RollingUpdate 👈
            MinReadySeconds:        20
            RollingUpdateStrategy:  25% max unavailable, 25% max surge 👈

Alternatively,

        $ kubectl describe deploy frontend | grep -i strategy

            StrategyType:           RollingUpdate
            RollingUpdateStrategy:  25% max unavailable, 25% max surge

4.  Upgrade the application by changing the image for the deployment.

        $ kubectl edit deployment frontend

            apiVersion: apps/v1
            kind: Deployment
            ...
            spec:
              replicas: 4
              ...
              strategy:               👈
                rollingUpdate:        👈
                  maxSurge: 25%       👈
                  maxUnavailable: 25% 👈
                type: RollingUpdate   👈

            $ kubectl get rs

              NAME            DESIRED   CURRENT   READY   AGE
              frontend-rs-1   4         4         4       2m55s
              frontend-rs-2   0         0         0       50m


            $ kubectl describe deploy frontend | grep -i -A20 event

              Events:
                Type    Reason             Age    From                   Message
                ----    ------             ----   ----                   -------
                Normal  ScalingReplicaSet  50m    deployment-controller  Scaled up 👆 replica set frontend-rs-2 to 4
                Normal  ScalingReplicaSet  2m40s  deployment-controller  Scaled up 👆 replica set frontend-rs-1 to 1
                Normal  ScalingReplicaSet  2m40s  deployment-controller  Scaled down 👇 replica set frontend-rs-2 to 3
                Normal  ScalingReplicaSet  2m39s  deployment-controller  Scaled up 👆 replica set frontend-rs-1 to 2
                Normal  ScalingReplicaSet  2m15s  deployment-controller  Scaled down 👇 replica set frontend-rs-2 to 1
                Normal  ScalingReplicaSet  2m14s  deployment-controller  Scaled up 👆 replica set frontend-rs-1 to 4
                Normal  ScalingReplicaSet  110s   deployment-controller  Scaled down 👇 replica set frontend-rs-2 to 0

5.  Test the connection to the updated application now.

        $ bash curl-test.sh

            Hello, Application Version: v2 👈 ; Color: green OK

            Hello, Application Version: v1 👈 ; Color: blue OK

            Hello, Application Version: v2 👈 ; Color: green OK

            Hello, Application Version: v1 👈 ; Color: blue OK

            Hello, Application Version: v1 👈 ; Color: blue OK

            Hello, Application Version: v2 👈 ; Color: green OK

            Hello, Application Version: v2 👈 ; Color: green OK

            Hello, Application Version: v1 👈 ; Color: blue OK

            ...

6.  Figure out the maximum pod unavailability during update. - `25% max unavailable`

        $ kubectl describe deploy | grep -i replica 👈

            Replicas:               4 desired 👈 | 4 updated | 4 total | 4 available | 0 unavailable
              Available      True    MinimumReplicasAvailable
              Progressing    True    NewReplicaSetAvailable
            OldReplicaSets:  <none>
            NewReplicaSet:   frontend-7c7fcfc8cb (4/4 replicas created)

        $ kubectl describe deploy | grep -i -A10 strategy

            StrategyType:           RollingUpdate 👈
            MinReadySeconds:        20
            RollingUpdateStrategy:  25% max unavailable 👈, 25% max surge👈

7.  Change the deployment strategy - `Recreate` or `RollingUpdate`.

        $ kubectl edit deploy frontend

            strategy:
              rollingUpdate:        ❌
                maxSurge: 25%       ❌ REMOVE
                maxUnavailable: 25% ❌
              type: Recreate 👈

            deployment.apps/frontend edited

Check if the update has been correctly applied.

        $ kubectl describe deploy frontend

            Name:               frontend
            Namespace:          default
            ...
            Replicas:           4 desired | 4 updated | 4 total | 4 available | 0 unavailable
            StrategyType:       Recreate 👈

8.  Upgrade the deployment.

        $ kubectl edit deploy frontend

              type: Recreate 👈
            template:
              metadata:
                creationTimestamp: null
                labels:
                  name: webapp
              spec:
                containers:
                - image: kodekloud/webapp-color:v3 👈 EDIT

Now observe the update behaviour.

The pods are all down at the same time,

            $ kubectl get deploy

                NAME       READY   UP-TO-DATE   AVAILABLE   AGE
                frontend   0/4 👇  0            0           16m

            $ kubectl get po

                NAME                        READY   STATUS         RESTARTS   AGE
                frontend-7c7fcfc8cb-qk5wp   1/1     Terminating👇  0          9m17s
                frontend-7c7fcfc8cb-pb4b7   1/1     Terminating👇  0          8m54s
                frontend-7c7fcfc8cb-xw9r4   1/1     Terminating👇  0          9m18s
                frontend-7c7fcfc8cb-gnp9x   1/1     Terminating👇  0          8m54s

and up at the same time.

            $ kubectl get po

                NAME                       READY   STATUS     RESTARTS   AGE
                frontend-c68667579-7tjdg   1/1     Running👆  0          6s
                frontend-c68667579-mxnc4   1/1     Running👆  0          6s
                frontend-c68667579-qkwpx   1/1     Running👆  0          6s
                frontend-c68667579-l2p7l   1/1     Running👆  0          6s

9.  ➕BONUS➕ Try editing the deploymet again and observe the behaviour.

Edit the deployment.

        $ kubectl edit deploy frontend

            deployment.apps/frontend edited

As the strategy is `Recreate`, all pods are down.

        $ kubectl get deploy

            NAME       READY   UP-TO-DATE   AVAILABLE   AGE
            frontend   0/4 👇  0            0           21m

They are all shutting down at the same time.

        $ kubectl get po

            NAME                        READY   STATUS         RESTARTS   AGE
            frontend-7776cb7d57-6tb8z   1/1     Terminating👇  0          58s
            frontend-7776cb7d57-mzfpx   1/1     Terminating👇  0          57s
            frontend-7776cb7d57-2q9bt   1/1     Terminating👇  0          58s
            frontend-7776cb7d57-qvvqs   1/1     Terminating👇  0          58s

`Curl-test` also demonstrates the strategy in action.

        $ bash curl-test.sh

            Failed

            Failed

            Failed

            Hello, Application Version: v3 ; Color: red OK

            Hello, Application Version: v3 ; Color: red OK

            Failed

            Hello, Application Version: v3 ; Color: red OK

            Hello, Application Version: v3 ; Color: red OK

            Hello, Application Version: v3 ; Color: red OK

            ...

The recreated pods (editted version) are all up and running again.

        $ kubectl get po

            NAME                       READY   STATUS    RESTARTS   AGE
            frontend-c68667579-v6gk6   1/1     Running   0          24s
            frontend-c68667579-cjvv6   1/1     Running   0          24s
            frontend-c68667579-qfct9   1/1     Running   0          24s
            frontend-c68667579-kns9j   1/1     Running   0          24s

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
