# <span id='top'>☸️ReplicaSets</span>

A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods ([Docs](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)).

[[`rc-definition.yml`]](#definition)  
[[Replicas]](#replicas)  
[[🤲Hands-on Practice]](#handson)  
[[References]](#ref)

<br>

## <span id='definition'>`rc-definition.yml`</span>

[[Top]](#top)

- `kubectl create -f replicaset-definition.yml`: create replicas
- `kubectl get replicaset`
- `kubectl delete replicaset <REPLICA NAME>`: deletes all underlying pods.
- `kubectl edit replicaset myapp-replicaset`
- `kubectl replace -f replicaset-definition.yml`: replace or update the replica
- `kubectl scale --replicas=6 -f <rc definition file>`
- `kubectl scale replicaset <replicaset name> --replicas=<num>`

- `pod-definition.yml` becomes the child of `template.

        -------------- rc-definition.yml --------------

        apiVersion: v1
        kind: ReplicationController
        metadata:
           name: myapp-rc
           labels:
              app: myapp
              type: front-end
        spec:
            template:

                POD DEFINITION.YAML content HERE.

            replicas: <num>

Then run the file.

        kubectl create -f rc-definition.yml

        kubectl get replicationcontroller

<br>

## <span id='replicas'>Replicas</span>

[[Top]](#top)

Ways to scale `replicasets`

1. Update the number of replicas in the definition file to greater number.

- `kubectl replace -f replicaset-definition.yml`

2. Create new replicas using the definition file.

- `kubectl scale --replicas=6 -f replicaset-definition.yml`

3. This doesn't change the number of replicas defined in the `definition.yml`.

- `kubectl scale --replicas=6 replicaset myapp-replicaset`

<br>

## <span id='handson'>🤲Hands-on Practice</span>

[[Top]](#top)

1.  Create a `pod.yaml` and `replicaset.yaml`.

2.  Create a `replica` based off of the `.yaml`.

    kubectl create -f replicaset.yaml

    The ReplicaSet "myapp-replicaset" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"myapp"}: `selector` does not match template `labels`

         --------------- replicaset.yaml ---------------

         apiVersion: apps/v1
         kind: ReplicaSet
         metadata:
         name: myapp-replicaset
         labels:
             app: myapp
         spec:
         selector:            ---LABELS-UNDER-THIS-LINE------
             matchLabels:
                app: myapp    <--MUST-BE---------------------
         replicas: 3
         template:
             metadata:
             name: nginx-2
             labels:
                 app: myapp   <--THE-SAME-------------------
             spec:
             containers:
                 - name: nginx
                 image: nginx

The label had been wrongly labelled. Having fixed the issue, try again.

    kubectl create -f replicaset.yaml
    replicaset.apps/myapp-replicaset created

Check the status.

    kubectl get replicaset
    NAME               DESIRED   CURRENT   READY   AGE
    myapp-replicaset   3         3         0       6m33s

Check the pod status.

    kubectl get pods
    NAME                     READY   STATUS    RESTARTS   AGE
    myapp-replicaset-bdpc5   0/1     Pending   0          7m13s
    myapp-replicaset-nmfw2   0/1     Pending   0          7m13s
    myapp-replicaset-v6lks   0/1     Pending   0          7m13s

Delete one of the three pods.

    kubectl delete pod myapp-replicaset-bdpc5
    pod "myapp-replicaset-bdpc5" deleted

Then check the pod status again. Note that there still are 3 pods. One of them has been created just 4 seconds ago. This is because `kubectl` ensure that there are sufficient pods available at all times.

    kubectl get pods
    NAME                     READY   STATUS    RESTARTS   AGE
    myapp-replicaset-7mvws   0/1     Pending   0          4s   <------
    myapp-replicaset-nmfw2   0/1     Pending   0          8m34s
    myapp-replicaset-v6lks   0/1     Pending   0          8m34s

Explore the replicaset.

    kubectl describe replicaset myapp-replicaset
    Name:         myapp-replicaset
    Namespace:    default
    Selector:     env=production
    Labels:       app=myapp
    Annotations:  <none>
    Replicas:     3 current / 3 desired
    Pods Status:  0 Running / 3 Waiting / 0 Succeeded / 0 Failed
    Pod Template:
        Labels:  env=production
        Containers:
        nginx:
            Image:        nginx   <---------- CREATED AS DEFINED.
            Port:         <none>
            Host Port:    <none>
            Environment:  <none>
            Mounts:       <none>
        Volumes:        <none>
    Events:
    Type    Reason            Age   From                   Message
    ----    ------            ----  ----                   -------
    Normal  SuccessfulCreate  10m   replicaset-controller  Created pod: myapp-replicaset-nmfw2
    Normal  SuccessfulCreate  10m   replicaset-controller  Created pod: myapp-replicaset-bdpc5
    Normal  SuccessfulCreate  10m   replicaset-controller  Created pod: myapp-replicaset-v6lks
    Normal  SuccessfulCreate  114s  replicaset-controller  Created pod: myapp-replicaset-7mvws

3.  Create a pod outside the `replicaset`. The `pod` is terminated immediated.

        kubectl create -f nginx.yaml

        kubectl describe replicaset myapp-replicaset
        Name: myapp-replicaset
        Namespace: default
        Selector: env=production
        Labels: app=myapp
        Annotations: <none>
        Replicas: 3 current / 3 desired
        Pods Status: 0 Running / 3 Waiting / 0 Succeeded / 0 Failed
        Pod Template:
        Labels: env=production
        Containers:
        nginx:
        Image: nginx
        Port: <none>
        Host Port: <none>
        Environment: <none>
        Mounts: <none>
        Volumes: <none>
        Events:
        Type Reason Age From Message

        ***

        Normal SuccessfulCreate 15m replicaset-controller Created pod: myapp-replicaset-nmfw2
        Normal SuccessfulCreate 15m replicaset-controller Created pod: myapp-replicaset-bdpc5
        Normal SuccessfulCreate 15m replicaset-controller Created pod: myapp-replicaset-v6lks
        Normal SuccessfulCreate 6m57s replicaset-controller Created pod: myapp-replicaset-7mvws
        Normal SuccessfulDelete 15s (x2 over 33s) replicaset-controller Deleted pod: nginx-2

4.  `kubectl edit replicaset`

- A temporary file that’s created by Kubernetes in memory to allow for editing the configuration of an existing object on Kubernetes.

- Changes made to this file are directly applied on the running configuration on the cluster as soon as the file is saved.

        kubectl edit replicaset myapp-replicaset

Change the number of replicas (from 3 to 5) and check the status again.

        kubectl get pods
        NAME                     READY   STATUS    RESTARTS   AGE
        myapp-replicaset-7mvws   0/1     Pending   0          13m
        myapp-replicaset-nmfw2   0/1     Pending   0          21m
        myapp-replicaset-v5vhw   0/1     Pending   0          5s   <-----
        myapp-replicaset-v6lks   0/1     Pending   0          21m
        myapp-replicaset-z6mgm   0/1     Pending   0          5s   <-----

Another way to scale (down) the replicas:

- `kubectl scale replicaset <replicaset name> --replicas=<num>`

        kubectl scale replicaset myapp-replicaset --replicas=2
        replicaset.apps/myapp-replicaset scaled
        [node1 pods]$ kubectl get pods
        NAME                     READY   STATUS    RESTARTS   AGE
        myapp-replicaset-nmfw2   0/1     Pending   0          23m
        myapp-replicaset-v6lks   0/1     Pending   0          23m

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
