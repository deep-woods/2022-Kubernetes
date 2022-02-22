# <span id='top'>01-04 Deploymentes</span>

[[🤲Hands-on]](#handson)  
[[References]](#ref)

<br>

## <span id=''>Commands</span>

[[Top]](#top)

- `kubectl get all`

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
