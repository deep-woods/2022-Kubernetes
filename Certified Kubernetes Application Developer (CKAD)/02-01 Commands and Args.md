# <span id='top'>02-01 Commands and Args</span>

[[Comparative Commands]](#commands)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='commands'>Comparative Commands</span>

[[Top]](#top)

| 🐳Docker               | ☸️K8s                  |
| ---------------------- | ---------------------- |
| `FROM ubuntu`          |                        |
| `ENTRYPOINT ["sleep"]` | `command: ["sleep2.0]` |
| `CMD ["5"]`            | `args: ["10"]`         |

- The ENTRYPOINT in the Dockerfile is overridden by the command in the pod definition.

        - name: simple-webapp
          image: kodekloud/webapp-color
          command: ["python", "app.py"]
          args: ["--color", "pink"]

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Create a running pod.

        $ cat ubuntu-sleeper-2.yaml

            apiVersion: v1
            kind: Pod
            metadata:
            name: ubuntu-sleeper-2
            spec:
            containers:
            - name: ubuntu
                image: ubuntu
                command:     <----- Docker CMD ["sleep", "5000"]
                - "sleep"
                - "5000"

        $ kubectl create -f ubuntu-sleeper-2.yaml
            pod/ubuntu-sleeper-2 created

        $ kubectl get po
            NAME               READY   STATUS    RESTARTS   AGE
            ubuntu-sleeper     1/1     Running   0          5m20s
            ubuntu-sleeper-2   1/1     Running   0          4s

2.  Fix the bug in the definition file and create a pod.

        kubectl create -f ubuntu-sleeper-3.yaml
            Error from server (BadRequest): error when creating "ubuntu-sleeper-3.yaml": Pod in version "v1" cannot be handled as a Pod: v1.Pod.Spec: v1.PodSpec.Containers: []v1.Container: v1.Container.Command: []string: ReadString: expects " or n, but found 1, error found in #10 byte of ...|["sleep",1200],"imag|..., bigger context ...|ault"},"spec":{"containers":[{"command":["sleep",1200],"image":"ubuntu","name":"ubuntu"}]}}|...

        cat ubuntu-sleeper-3.yaml
            apiVersion: v1
            kind: Pod
            metadata:
                name: ubuntu-sleeper-3
            spec:
                containers:
                - name: ubuntu
                  image: ubuntu
                  command:
                    - "sleep"
                    - "1200"

        $ kubectl create -f ubuntu-sleeper-3.yaml
            pod/ubuntu-sleeper-3 created

        $ kubectl get po
            NAME               READY   STATUS    RESTARTS   AGE
            ubuntu-sleeper     1/1     Running   0          14m
            ubuntu-sleeper-2   1/1     Running   0          9m1s
            ubuntu-sleeper-3   1/1     Running   0          8s

3.  Comparative coding: `🐳Docker` vs `☸️K8s`

        $ cat Dockerfile
            FROM python:3.6-alpine

            RUN pip install flask

            COPY . /opt/

            EXPOSE 8080

            WORKDIR /opt

            ENTRYPOINT ["python", "app.py"]

Compare the above with the below.

- `python app.py --color red`

            FROM python:3.6-alpine

            RUN pip install flask

            COPY . /opt/

            EXPOSE 8080

            WORKDIR /opt

            ENTRYPOINT ["python", "app.py"]

            CMD ["--color", "red"]   <-------------------

<br>

4.  Create a pod based on a definition file.

            apiVersion: v1
            kind: Pod
            metadata:
              creationTimestamp: "2022-02-25T15:31:45Z"
              labels:
                run: webapp-green
              name: webapp-green
              namespace: default
              resourceVersion: "1121"
              uid: a04d134a-a044-4cae-b6c8-cc403869d6fa
            spec:
              containers:
              - image: kodekloud/webapp-color
                imagePullPolicy: Always
                name: webapp-green
                resources: {}
                terminationMessagePath: /dev/termination-log
                terminationMessagePolicy: File
                volumeMounts:
                - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
                  name: kube-api-access-s52ht
                  readOnly: true
                command:
                - "--color"
                - "green"
            dnsPolicy: ClusterFirst
            enableServiceLinks: true
              preemptionPolicy: PreemptLowerPriority
              priority: 0
              restartPolicy: Always
              schedulerName: default-scheduler
              securityContext: {}
              serviceAccount: default
              serviceAccountName: default
              terminationGracePeriodSeconds: 30
              tolerations:
              - effect: NoExecute
                key: node.kubernetes.io/not-ready
                operator: Exists
                tolerationSeconds: 300
              - effect: NoExecute
                key: node.kubernetes.io/unreachable
                operator: Exists
                tolerationSeconds: 300
              volumes:
              - name: kube-api-access-s52ht
                projected:
                defaultMode: 420
                sources:
                - serviceAccountToken:
                    expirationSeconds: 3607
                    path: token
                - configMap:
                    items:
                    - key: ca.crt
                        path: ca.crt
                    name: kube-root-ca.crt
                - downwardAPI:
                    items:
                    - fieldRef:
                        apiVersion: v1
                        fieldPath: metadata.namespace
                        path: namespace
                status:
                  phase: Pending
                  qosClass: BestEffort

5.  Troubleshoot the issue.

        $ kubectl create -f green-webapp.yaml
            pod/webapp-green created

        $ kubectl get po
            NAME               READY   STATUS             RESTARTS     AGE
            ubuntu-sleeper     1/1     Running            0            36m
            ubuntu-sleeper-2   1/1     Running            0            31m
            ubuntu-sleeper-3   1/1     Running            0            25m
            webapp-green       0/1     CrashLoopBackOff   1 (3s ago)   6s   <--------

        $ kubectl describe po webapp-green

            Events:
            Type     Reason     Age               From               Message
            ----     ------     ----              ----               -------
            Normal   Scheduled  21s               default-scheduler  Successfully assigned default/webapp-green to controlplane
            Normal   Pulled     21s               kubelet            Successfully pulled image "kodekloud/webapp-color" in 177.682778ms
            Normal   Pulled     20s               kubelet            Successfully pulled image "kodekloud/webapp-color" in 171.322565ms
            Normal   Pulling    5s (x3 over 21s)  kubelet            Pulling image "kodekloud/webapp-color"
            Normal   Pulled     5s                kubelet            Successfully pulled image "kodekloud/webapp-color" in 154.474902ms
            Normal   Created    5s (x3 over 21s)  kubelet            Created container webapp-green
            Warning  Failed     4s (x3 over 20s)  kubelet            Error: failed to create containerd task: failed to create shim: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "--color": executable file not found in $PATH: unknown
            Warning  BackOff    4s (x3 over 19s)  kubelet            Back-off restarting failed container

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
