# <span id='top'>02-10 Readiness and Liveness</span>

[[Readiness Conditions]](#Readiness)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Readiness'>Readiness Conditions</span>

[[Top]](#top)

    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Send multiple requests.

The script looks like:

        ./curl-test.sh

        for i in {1..20}; do
          kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/ready 2>&1` && echo "$test OK" || echo "Failed"';
          echo ""

There are currently 2 pods running.

        NAME              READY   STATUS    RESTARTS   AGE
        little-forest-app-1   1/1     Running   0          7m34s
        little-forest-app-2   1/1     Running   0          3m5s

        $ bash /root/curl-test.sh

            Failed 👈

            Failed

            Message from little-forest-app-1 : I am ready! OK 👈

            Failed

            Failed

            Message from little-forest-app-1 : I am ready! OK

            Failed

            Failed

            Failed

            Failed

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Failed

            Failed

            Failed

            Message from little-forest-app-1 : I am ready! OK

            Failed

            Message from little-forest-app-1 : I am ready! OK

            Failed

            Message from little-forest-app-1 : I am ready! OK

2.  Configure a pod's `readinessProbe`.

Edit the definition.

        pod-definition.yaml

          apiVersion: v1
          kind: Pod
          metadata:
            name: small-forest-app
            labels:
              name: small-forest-app
          spec:
            containers:
            - name: small-forest-app
              image: small-forest-app
              ports:
              - containerPort: 8080
                protocol: TCP
            readinessProbe:       👈
              httpGet:            👈
                path: /api/ready  👈
                port: 8080        👈

Delete the existing pod.

        $ kubectl get po

            NAME              READY   STATUS    RESTARTS   AGE
            little-forest-app-1   1/1     Running   0          18m
            little-forest-app-2   1/1     Running   0          13m

        $ kubectl delete po little-forest-app-2 --force

            pod "little-forest-app-2" deleted

Create a new one with the edited definition.

        $ kubectl create -f little-forest-app-2-pod.yaml

            pod/little-forest-app-2 created

3. Run the curl test and see what happens.

Currently, there are two pods running but the container in the app-2 is not ready.

        $ kubectl get po

            NAME                  READY      STATUS    RESTARTS   AGE
            little-forest-app-1   1/1        Running   0          19m
            little-forest-app-2   0/1 👈     Running   0          6s

We run the curl test, and understandably:

        $ bash /root/curl-test.sh

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

4.  Now that both pods are ready, the traffic is evenly distributed.

        $ kubectl get po

            NAME              READY   STATUS    RESTARTS   AGE
            little-forest-app-1   1/1     Running   0          13m
            little-forest-app-2   1/1     Running   0          4m5s

        $ bash /root/curl-test.sh

            Message from little-forest-app-1👈 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2👈👈 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

5.  Figure out what happens when the app crashes.

Once the application crashes, the container is restarted.

        $ crash-app.sh

            Message from little-forest-app-1 : Mayday! Mayday! Going to crash!

        $ kubectl get pod

        NAME              READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
        little-forest-app-1   1/1     Running   2          29m     10.244.0.5   controlplane   <none>           <none>
        little-forest-app-2   1/1     Running   0          9m39s   10.244.0.7   controlplane   <none>           <none>

6.  What happens when the app freezes? - _**New users will be impacted.**_

Both pods are up and running atm.

            NAME              READY    STATUS    RESTARTS   AGE
            little-forest-app-1   1/1      Running   1          19m
            little-forest-app-2   1/1 👈  Running   1          9m59s

        $ ls
            crash-app.sh  curl-test.sh  freeze-app.sh  little-forest-app-2-pod.yaml

        $ bash freeze-app.sh
        $ nohup: appending output to 'nohup.out' 👈 BUT FREEZES

Then it's not ready anymore.

        $ kubectl get po

            NAME              READY   STATUS    RESTARTS   AGE
            little-forest-app-1   1/1     Running   1          20m
            little-forest-app-2   0/1 👈  Running   1          11m

7.  Edit the pod definition and run them again.

        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            name: little-forest-app
          name: little-forest-app-1
          namespace: default
        spec:
          containers:
          - env:
            - name: APP_START_DELAY
              value: "80"
            image: kodekloud/webapp-delayed-start
            imagePullPolicy: Always
            name: little-forest-app
            ports:
            - containerPort: 8080
              protocol: TCP
            readinessProbe:  👈
              httpGet:       👈
                path: /ready 👈
                port: 8080   👈
            livenessProbe:   👈
              httpGet:       👈
                path: /live  👈
                port: 8080   👈
              periodSeconds: 1        👈
              initialDelaySeconds: 80 👈

        $ kubectl create -f app1-pod.yaml

            pod/little-forest-app-1 created

        $ kubectl create -f app2-pod.yaml

            pod/little-forest-app-2 created

8.  Now that the 'livenessProbe' is configured on both pods, they will be restarted right back like roly polys in case anything happens.

        $ kubectl get po

            NAME              READY   STATUS    RESTARTS   AGE
            little-forest-app-1   1/1     Running   0          56s
            little-forest-app-2   1/1     Running   0          4m1s

        $ $ kubectl get po

            NAME              READY   STATUS    RESTARTS   AGE
            little-forest-app-1   1/1     Running   0          112s
            little-forest-app-2   1/1     Running   0          4m57s

        $ $ kubectl get po

            NAME              READY   STATUS    RESTARTS   AGE
            little-forest-app-1   1/1     Running   0          115s
            little-forest-app-2   1/1     Running   0          5m

        $ $ bash curl-test.sh

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK


            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

            Message from little-forest-app-1 : I am ready! OK

            Message from little-forest-app-2 : I am ready! OK

<br>

### Troubleshooting - Error in the definition file

Spelling error

        $ kubectl create -f app2-pod.yaml

            error: error validating "app2-pod.yaml": error validating data: ValidationError(Pod.spec.containers[0].livenessProbe): unknown field "initalDelaySeconds" 👈 in io.k8s.api.core.v1.Probe; if you choose to ignore these errors, turn validation off with --validate=false

Indentation error

            error: error validating "app1-pod.yaml": error validating data: ValidationError(Pod.spec): unknown field 👈"livenessProbe"👈 in io.k8s.api.core.v1.PodSpec; if you choose to ignore these errors, turn validation off with --validate=false

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
