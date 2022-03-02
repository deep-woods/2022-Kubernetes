# <span id='top'>02-09 Multi-Container Pods</span>

[[Multi-Container Pods]](#Multicontainer)  
[[]](#)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

├──
│ㅤㅤ├──
│ㅤㅤ└──
├──
│ㅤㅤ├──
│ㅤㅤ└──
├──
├──
│  
├──
└──

## <span id='Multicontainer'>Multi-Container Pods</span>

[[Top]](#top)

<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

When creating a multi-container pod, create a `.yaml` file as usual. As an image must be assigned to create a pod, the image used in this case will simply be one among the multiple containers after the definition has been modified.

<br>

<br>
<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Create a multi-container pod with 2 containers.

        $ kubectl run yellow --image=busybox --restart=Never --dry-run=client -oyaml > yellow-pod.yaml

        $ kubectl apply -f yellow-pod.yaml 👈
            pod/yellow created

        $ kubectl get po

            NAME        READY   STATUS              RESTARTS   AGE
            app         1/1     Running             0          16m
            blue        2/2     Running             0          15m
            fluent-ui   1/1     Running             0          16m
            red         3/3     Running             0          15m
            yellow      0/2     ContainerCreating   0          9s

        $ kubectl describe po yellow

            Events:
            Type    Reason     Age   From               Message
            ----    ------     ----  ----               -------
            Normal  Scheduled  58s   default-scheduler  Successfully assigned default/yellow to controlplane
            Normal  Pulling    57s   kubelet            Pulling image "busybox" 👈
            Normal  Pulled     56s   kubelet            Successfully pulled image "busybox" in 769.960148ms
            Normal  Created    56s   kubelet            Created container lemon
            Normal  Started    55s   kubelet            Started container lemon 👈
            Normal  Pulling    55s   kubelet            Pulling image "redis"   👈
            Normal  Pulled     46s   kubelet            Successfully pulled image "redis" in 9.549388553s
            Normal  Created    46s   kubelet            Created container gold
            Normal  Started    45s   kubelet            Started container gold  👈

Check the status if the containers are running fine.

        gold:
            Container ID:   docker://1570c01aa06db1be26098a7476c153e80935cc4f2fe9954c9be8c9e01bd1d33b
            Image:          redis
            Image ID:       docker-pullable://redis@sha256:dcb5cdaf6876a2df344ad98443665d2292bc5fec3abddd626fa412a1da6ffc83
            Port:           <none>
            Host Port:      <none>
            State:          Running 👈
            Started:      Wed, 02 Mar 2022 23:07:40 +0000 👈

        lemon:
            Container ID:   docker://97a80a248eab9194674dae9aa47c9477a7306a8d94027c4a0b71b79f1f94da00
            Image:          busybox
            Image ID:       docker-pullable://busybox@sha256:afcc7f1ac1b49db317a7196c902e61c6c3c4607d63599ee1a82d702d249a0ccb
            Port:           <none>
            Host Port:      <none>
            State:          Terminated 👈
            Reason:       Completed
            Exit Code:    0
            Started:      Wed, 02 Mar 2022 23:07:30 +0000 👈
            Finished:     Wed, 02 Mar 2022 23:07:30 +0000 👈

2.  Namespace inspection - Be aware of the composite syntax !

        $ kubectl -n elastic-stack get pod,svc
            NAME                 READY   STATUS    RESTARTS   AGE
            pod/app              1/1     Running   0          22m
            pod/fatastic-search   1/1     Running   0          22m
            pod/Nirvana           1/1     Running   0          22m

        $ NAME                  TYPE      CLUSTER-IP     EXTERNAL-IP PORT(S)                       AGE
        service/fantasticsearch NodePort  10.105.205.175 <none>      9200:30200/TCP,9300:30300/TCP 22m
        service/Nirvana         NodePort  10.108.5.53    <none>      5601:30601/TCP                22m

3.  Configure a side-car container.

### 3-1. Something is going wrong inside a container. Inspect the log to figure out why.

- Syntax: `kubectl -n <ns name> exec -it <pod name> <command>

          $ kubectl -n elastic-stack exec -it app cat /log/app.log 👈

          ...
              [2022-03-02 23:33:48,020] INFO in event-simulator: USER1 logged in
              [2022-03-02 23:33:48,535] INFO in event-simulator: USER1 is viewing page2
              [2022-03-02 23:33:49,021] INFO in event-simulator: USER3 is viewing page1
              [2022-03-02 23:33:49,536] INFO in event-simulator: USER3 is viewing page2
              [2022-03-02 23:33:50,022] INFO in event-simulator: USER4 is viewing page1
              [2022-03-02 23:33:50,537] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
              [2022-03-02 23:33:50,537] INFO in event-simulator: USER4 logged in
              [2022-03-02 23:33:51,023] INFO in event-simulator: USER3 logged in
              [2022-03-02 23:33:51,539] WARNING in event-simulator: USER5 Failed 👈 to Login as the account is locked due to MANY FAILED ATTEMPTS.
          ...

4.  Add a `side-car` container to the running pod to save the log.

            apiVersion: v1
            kind: Pod
            metadata:
              name: app
              namespace: fantastic-stack
              labels:
                name: app
            spec:
              containers:
              - name: app
                image: botanical-garden/event-simulator
                volumeMounts:
                - mountPath: /log
                  name: log-volume

              - name: sidecar
                image: botanical-garden/filebeat-configured
                volumeMounts:
                - mountPath: /var/log/event-simulator/
                  name: log-volume

              volumes:
              - name: log-volume
                hostPath:
                  # directory location on host
                  path: /var/log/webapp
                  # this field is optional
                  type: DirectoryOrCreate

        $ kubectl create -f app-pod.yaml
            pod/app created

  <br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- botanical-garden https://botanical-garden.com/courses/certified-kubernetes-application-developer-ckad/
- Mumshad Mannambeth (2019) Kubernetes CKAD - Kibana Dashboard https://www.loom.com/share/c2ae70197e8340a0ba77fc1de8179182
