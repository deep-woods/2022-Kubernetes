# <span id='top'>02-08 Node Selectors and Affinity</span>

[[Node Selectors and Logging]](#Selectors)  
[[Node Affinity]](#Affinity)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Selectors'>Node Selectors and Logging</span>

[[Top]](#top)

- `kubectl label nodes <node-name> <label-key>=<label-value>`
- `kubectl label nodes node-1 size=Large`

        nodeSelector:
          size: Large

_You can constrain a Pod so that it can only run on particular set of Node(s). There are several ways to do this and the recommended approaches all use label selectors to facilitate the selection. Generally such constraints are unnecessary, as the scheduler will automatically do a reasonable placement (e.g. spread your pods across nodes so as not place the pod on a node with insufficient free resources, etc.) but there are some circumstances where you may want to control which node the pod deploys to - for example to ensure that a pod ends up on a machine with an SSD attached to it, or to co-locate pods from two different services that communicate a lot into the same availability zone._ (Kubernetes)

<br>

## <span id='Affinity'>Node Affinity</span>

[[Top]](#top)

_Node affinity is conceptually similar to nodeSelector -- it allows you to constrain which nodes your pod is eligible to be scheduled on, based on labels on the node._

### Node affinity types

- Available:

  - `requiredDuringSchedulingIgnoredDuringExecution`
  - `preferredDuringSchedulingIgnoredDuringExecution`

- Planned:
  - `requiredDuringSchedulingRequiredDuringExecution`

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  How many labels are there?

        $ kubectl describe node node01 | grep -i -A20 label

          Labels:             beta.kubernetes.io/arch=amd64 👈
                              beta.kubernetes.io/os=linux
                              kubernetes.io/arch=amd64
                              kubernetes.io/hostname=node01
                              kubernetes.io/os=linux

2.  Label a node.

        $ kubectl label node node01 colour=scarlet

          node/node01 labeled

        $ kubectl describe node node01 | grep -i -A20 label

          Labels:             beta.kubernetes.io/arch=amd64
                              beta.kubernetes.io/os=linux
                              colour=scarlet 👈
                              kubernetes.io/arch=amd64
                              kubernetes.io/hostname=node01
                              kubernetes.io/os=linux

Check if the node has been correctly labelled.

        $ kubectl get node node01 --show-labels

          NAME     STATUS   ROLES    AGE    VERSION   LABELS
          node01   Ready    <none>   7m8s   v1.20.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,color=scarlet👈,kubernetes.io/arch=amd64,kubernetes.io/hostname=node01,kubernetes.io/os=linux

3.  Create a pod with conditions.

_As we now know, a pod is the smallest unit of Kubernetes used to house one or more containers and run applications in a cluster, while deployment is a tool that manages the performance of a pod._

- Approache 1:

- `kubectl create deploy lushgreen --image=nginx --replicas=3 --dry-run=client -oyaml > lushgreen-deploy.pod`
- `nano lushgreen-deploy.pod`
- `kubectl get deploy`

        $ kubectl create deploy lushgreen --image=nginx --replicas=3 --dry-run=client -oyaml > lushgreen-deploy.pod 👈

        $ cat lushgreen-deploy.pod
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          creationTimestamp: "2022-03-01T15:43:11Z"
          generation: 1
          labels:
            app: lushgreen
          managedFields:
          - apiVersion: apps/v1
            fieldsType: FieldsV1
            fieldsV1:
          name: lushgreen 👈
          namespace: default
        spec:
          progressDeadlineSeconds: 600
          replicas: 3  👈
          revisionHistoryLimit: 10
          selector:
            matchLabels:
              app: lushgreen 👈
          template:
            metadata:
              creationTimestamp: null
              labels:
                app: lushgreen
            spec:
              containers:
              - image: nginx
                imagePullPolicy: Always
                name: nginx 👈
                terminationMessagePath: /dev/termination-log
                terminationMessagePolicy: File
              dnsPolicy: ClusterFirst
              restartPolicy: Always
              schedulerName: default-scheduler
              terminationGracePeriodSeconds: 30

        $ kubectl get deploy

          NAME                    READY   STATUS    RESTARTS   AGE
          lushgreen-7bb46df96d-dfz4b   1/1     Running   0          2m53s
          lushgreen-7bb46df96d-tm8qv   1/1     Running   0          2m53s
          lushgreen-7bb46df96d-zctx7   1/1     Running   0          2m53s

Approach 2:

- `kubectl create deployment blue --image=nginx`
- `kubectl scale deployment blue --replicas=3`

        $ kubectl create deployment blue --image=nginx
          deployment.apps/blue created

        $ kubectl scale deploy blue --replicas=3
          deployment.apps/blue scaled

4.  Set node affinity to a deployment.

        $ kubectl create -f lushgreen-deploy.yaml

            deployment.apps/lushgreen created

        lushgreen-deploy.yaml

          apiVersion: apps/v1
          kind: Deployment 👈
          metadata:
            creationTimestamp: null
            labels:
              app: lushgreen 👈
            name: lushgreen  👈
          spec:
            replicas: 3      👈
            selector:
              matchLabels:
                app: lushgreen
            strategy: {}
            template:
              metadata:
                creationTimestamp: null
                labels:
                  app: lushgreen
              spec:
                affinity:        👈
                  nodeAffinity:  👈
                    requiredDuringSchedulingIgnoredDuringExecution: 👈
                      nodeSelectorTerms:
                      - matchExpressions:
                        - key: color   👈
                          operator: In
                          values:
                          - lushgreen  👈
                containers:
                - image: nginx
                  name: nginx
                  resources: {}
          status: {}

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
- Gary Sevens (2021) Best of 2021 – Kubernetes Pods Vs. Deployments https://containerjournal.com/editorial-calendar/best-of-2021/kubernetes-pods-vs-deployments/#:~:text=As%20we%20now%20know%2C%20a,the%20performance%20of%20a%20pod
