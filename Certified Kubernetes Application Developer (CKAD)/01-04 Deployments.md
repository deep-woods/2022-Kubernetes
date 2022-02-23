# <span id='top'>01-04 Deploymentes</span>

[[Common deployment errors]](#errors)  
[[deployment]](#deployment)
[[🤲Hands-on]](#handson)  
[[References]](#ref)

<br>

## <span id=''>Commands</span>

[[Top]](#top)

- `kubectl get all`

## <span id='errors'>Common deployment errors</span>

[[Top]](#top)

    NAME                                   READY   STATUS             RESTARTS   AGE
    frontend-deployment-56d8ff5458-7fmpm   0/1     ImagePullBackOff   0          57s
    frontend-deployment-56d8ff5458-9rc6w   0/1     ImagePullBackOff   0          58s
    frontend-deployment-56d8ff5458-lsg8j   0/1     ErrImagePull       0          58s
    frontend-deployment-56d8ff5458-5zm7b   0/1     ErrImagePull       0          58s


    $ kubectl describe po frontend-deployment-56d8ff5458-7fmpm

    Containers:
      busybox-container:
        Container ID:
        Image:         busybox888
        Image ID:
        Port:          <none>
        Host Port:     <none>
        Command:
          echo Hello Kubernetes! && sleep 3600
        State:          Waiting                       <------------ STATE
          Reason:       ImagePullBackOff              <------------ REASON

    Events:
    Type     Reason     Age                From               Message
    ----     ------     ----               ----               -------
    Normal   Scheduled  91s                default-scheduler  Successfully assigned default/frontend-deployment-56d8ff5458-7fmpm to controlplane
    Normal   BackOff    18s (x4 over 89s)  kubelet            Back-off pulling image "busybox888"
    Warning  Failed     18s (x4 over 89s)  kubelet            Error: ImagePullBackOff          <------------
    Normal   Pulling    3s (x4 over 90s)   kubelet            Pulling image "busybox888"       <------------
    Warning  Failed     2s (x4 over 89s)   kubelet            Failed to pull image "busybox888": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/busybox888:latest": failed to resolve reference "docker.io/library/busybox888:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
    Warning  Failed     2s (x4 over 89s)   kubelet            Error: ErrImagePull


    $ kubectl describe po frontend-deployment-56d8ff5458-lsg8j

    State:          Waiting
      Reason:       ImagePullBackOff

    Events:
    Type     Reason     Age                    From               Message
    ----     ------     ----                   ----               -------
    Normal   Scheduled  6m33s                  default-scheduler  Successfully assigned default/frontend-deployment-56d8ff5458-lsg8j to controlplane
    Normal   Pulling    5m2s (x4 over 6m32s)   kubelet            Pulling image "busybox888"
    Warning  Failed     5m1s (x4 over 6m31s)   kubelet            Failed to pull image "busybox888": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/busybox888:latest": failed to resolve reference "docker.io/library/busybox888:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
    Warning  Failed     5m1s (x4 over 6m31s)   kubelet            Error: ErrImagePull
    Warning  Failed     4m47s (x6 over 6m30s)  kubelet            Error: ImagePullBackOff
    Normal   BackOff    87s (x21 over 6m30s)   kubelet            Back-off pulling image "busybox888"

<br>

## <span id='deployment'>`deployment`</span>

[[Top]](#top)

### Create a `deployment` from command line

- `kubectl scale deployment --<tag>=<value> <deployment name>`
- `kubectl scale --<tag>=<value> deployment.apps/<deployment name>`
- `kubectl get <deployment full name>`

- `kubectl get pods`

  - `kubectl get pods --namespace=default`
  - `kubectl get pods --namespace=prod`

- `kubectl config set-context $(kubectl config current-context) --namespace=dev`
- `kubectl config set-context $(kubectl config current-context) --namespace=prod`

- `kubectl get pods --all-namespaces`

<br>

### Examples

      $ kubectl create deployment httpd-frontend --image=httpd:2.4-alpine
      deployment.apps/httpd-frontend created

Check the status. There are currently only one deployment in aciton, one replicaset running,

      $ kubectl get all  NAME                                  READY   STATUS    RESTARTS   AGE
      pod/httpd-frontend-5ddf995bdf-j96q4   1/1     Running   0          33s

      NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
      service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   9m37s

      NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
      deployment.apps/httpd-frontend   1/1     1            1           33s

      NAME                                        DESIRED   CURRENT   READY   AGE
      replicaset.apps/httpd-frontend-5ddf995bdf   1         1         1       33s

And one pod running.

      $ kubectl get pod
      NAME                              READY   STATUS    RESTARTS   AGE
      httpd-frontend-5ddf995bdf-j96q4   1/1     Running   0          72s

Now scale it.

      $ kubectl scale --replicas=5 deployment.apps/httpd-frontend
      deployment.apps/httpd-frontend scaled

You can check the status:

      $ kubectl get rs
      NAME                        DESIRED   CURRENT   READY   AGE
      httpd-frontend-5ddf995bdf   5         5         5       3m31s

      $ kubectl get po
      NAME                              READY   STATUS    RESTARTS   AGE
      httpd-frontend-5ddf995bdf-j96q4   1/1     Running   0          3m35s
      httpd-frontend-5ddf995bdf-5tl2n   1/1     Running   0          11s
      httpd-frontend-5ddf995bdf-bfm66   1/1     Running   0          11s
      httpd-frontend-5ddf995bdf-h7dnz   1/1     Running   0          11s
      httpd-frontend-5ddf995bdf-x5rnh   1/1     Running   0          11s

      $ kubectl get deployment.apps/httpd-frontend
      NAME             READY   UP-TO-DATE   AVAILABLE   AGE
      httpd-frontend   5/5     5            5           7m15s

<br>

## <span id='handson'>🤲Hands-on</span>

[[Top]](#top)

        kubectl get all
        NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   4m55s

<br>

        kubectl get all
        NAME                                       READY   STATUS             RESTARTS   AGE
        pod/forest-end-deployment-56d8ff5458-69d2s   0/1     ImagePullBackOff   0          30s
        pod/forest-end-deployment-56d8ff5458-s87wt   0/1     ImagePullBackOff   0          30s
        pod/forest-end-deployment-56d8ff5458-mmhms   0/1     ErrImagePull       0          30s
        pod/forest-end-deployment-56d8ff5458-xrldj   0/1     ErrImagePull       0          30s

        NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   6m27s

        NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/forest-end-deployment   0/4     4            0           30s

        NAME                                             DESIRED   CURRENT   READY   AGE
        replicaset.apps/forest-end-deployment-56d8ff5458   4         4         0       30s

<br>

### Create a custom `deployment-definition.yaml` 1

        apiVersion: apps/v1
        kind: Deployment
            metadata:
        name: httpd-frontend
        spec:
            replicas: 5
            selector:
                matchLabels:
                name: httpd-pod
            template:
                metadata:
                labels:
                    name: httpd-pod
                spec:
                containers:
                - name: httpd-container
                    image: httpd:2.4-alpine
                    command:
                    - sh
                    - "-c"
                    - echo Hello Kubernetes! && sleep 3600

<br>

### Create a custom `deployment-definition.yaml` 2

        apiVersion: apps/v1
        kind: Deployment
        metadata:
            name: httpd-frontend
        spec:
            replicas: 3
            selector:
                matchLabels:
                name: httpd-pod
            template:
                metadata:
                labels:
                    name: httpd-pod
                spec:
                containers:
                - name: httpd-container
                    image: httpd:2.4-alpine
                    command:
                    - sh
                    - "-c"
                    - echo Hello Kubernetes! && sleep 3600


        $ kubectl get all
        NAME                                  READY   STATUS    RESTARTS   AGE
        pod/httpd-frontend-54886bbf76-6mzt8   1/1     Running   0          6s
        pod/httpd-frontend-54886bbf76-mb9qg   1/1     Running   0          6s
        pod/httpd-frontend-54886bbf76-nw4cv   1/1     Running   0          6s

        NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
        service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   25m

        NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/httpd-frontend   3/3     3            3           6s

        NAME                                        DESIRED   CURRENT   READY   AGE
        replicaset.apps/httpd-frontend-54886bbf76   3         3         3       6s

###

### Troubleshoot

        $ kubectl delete deploy deployment.apps/deployment-1 deployment.apps/frontend-deployment

        error: there is no need to specify a resource type as a separate argument when passing arguments in resource/name form (e.g. 'kubectl get resource/<resource_name>' instead of 'kubectl get resource resource/<resource_name>'

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
