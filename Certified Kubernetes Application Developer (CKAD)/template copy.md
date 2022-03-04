# <span id='top'>03-1 Labels and Selectors</span>

[[Labels and Selectors]](#Labels)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Labels'>Labels and Selectors</span>

[[Top]](#top)

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

0.               $ kubectl get all -o wide

        NAME              READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
        pod/db-2-mfj84    1/1     Running   0          36m   10.42.0.19   controlplane   <none>           <none>
        pod/app-1-mr92j   1/1     Running   0          36m   10.42.0.10   controlplane   <none>           <none>
        pod/app-1-zzxdf   1/1     Running   0          36m   10.42.0.17   controlplane   <none>           <none>
        pod/db-1-dd4ns    1/1     Running   0          36m   10.42.0.18   controlplane   <none>           <none>
        pod/app-1-hsgc8   1/1     Running   0          36m   10.42.0.11   controlplane   <none>           <none>
        pod/auth          1/1     Running   0          36m   10.42.0.15   controlplane   <none>           <none>
        pod/app-1-28qq7   1/1     Running   0          36m   10.42.0.9    controlplane   <none>           <none>
        pod/db-1-cwj6v    1/1     Running   0          36m   10.42.0.16   controlplane   <none>           <none>
        pod/app-2-br258   1/1     Running   0          36m   10.42.0.12   controlplane   <none>           <none>
        pod/db-1-6b78z    1/1     Running   0          36m   10.42.0.13   controlplane   <none>           <none>
        pod/db-1-fff5s    1/1     Running   0          36m   10.42.0.14   controlplane   <none>           <none>

        NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE   SELECTOR
        service/kubernetes   ClusterIP   10.43.0.1       <none>        443/TCP    39m   <none>
        service/app-1        ClusterIP   10.43.170.245   <none>        3306/TCP   36m   name=app-2

        NAME                    DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES    SELECTOR
        replicaset.apps/db-2    1         1         1       36m   busybox      busybox   env=prod,tier=db👈
        replicaset.apps/app-1   3         3         3       36m   busybox      busybox   env=dev,tier=frontend👈
        replicaset.apps/app-2   1         1         1       36m   busybox      busybox   !bu,env=prod,tier=frontend👈
        replicaset.apps/db-1    4         4         4       36m   busybox      busybox   env=dev,tier=db👈

1.  Filter out pods with a specific label / selector.

        $ kubectl get po
            NAME          READY   STATUS    RESTARTS   AGE
            db-2-mfj84    1/1     Running   0          28m
            app-1-mr92j   1/1     Running   0          28m
            app-1-zzxdf   1/1     Running   0          28m
            db-1-dd4ns    1/1     Running   0          28m
            app-1-hsgc8   1/1     Running   0          28m
            auth          1/1     Running   0          28m
            app-1-28qq7   1/1     Running   0          28m
            db-1-cwj6v    1/1     Running   0          28m
            app-2-br258   1/1     Running   0          28m
            db-1-6b78z    1/1     Running   0          28m
            db-1-fff5s    1/1     Running   0          28m

        $ kubectl get po --selector env=dev --no-headers | wc -l 👈
            7

        $ kubectl get po --selector env=dev 👈
            NAME          READY   STATUS    RESTARTS   AGE
            app-1-mr92j   1/1     Running   0          29m
            db-1-dd4ns    1/1     Running   0          29m
            app-1-hsgc8   1/1     Running   0          29m
            app-1-28qq7   1/1     Running   0          29m
            db-1-cwj6v    1/1     Running   0          29m
            db-1-6b78z    1/1     Running   0          29m
            db-1-fff5s    1/1     Running   0          29m

2.  Filter out the corresponding pods with a different selector.

        $ kubectl get po --selector bu=finance

            NAME          READY   STATUS    RESTARTS   AGE
            db-2-mfj84    1/1     Running   0          38m
            app-1-mr92j   1/1     Running   0          38m
            app-1-zzxdf   1/1     Running   0          38m
            app-1-hsgc8   1/1     Running   0          38m
            auth          1/1     Running   0          38m
            app-1-28qq7   1/1     Running   0          38m

        $ kubectl get po --selector bu=finance --no-headers | wc -l
        6

3.  Get all resources with the same selector.

        $ kubectl get all -o wide --selector env=prod

        NAME              READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
        pod/db-2-mfj84    1/1     Running   0          40m   10.42.0.19   controlplane   <none>           <none>
        pod/app-1-zzxdf   1/1     Running   0          40m   10.42.0.17   controlplane   <none>           <none>
        pod/auth          1/1     Running   0          40m   10.42.0.15   controlplane   <none>           <none>
        pod/app-2-br258   1/1     Running   0          40m   10.42.0.12   controlplane   <none>           <none>

        NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE   SELECTOR
        service/app-1   ClusterIP   10.43.170.245   <none>        3306/TCP   40m   name=app-2

        NAME                    DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES    SELECTOR
        replicaset.apps/db-2    1         1         1       40m   busybox      busybox   env=prod,tier=db
        replicaset.apps/app-2   1         1         1       40m   busybox      busybox   !bu,env=prod,tier=frontend

        $ kubectl get all -o wide --selector env=prod --no-headers | wc -l
            7👈

4.  Get pods satisfying multiple selectors and/or labels.

- `kubectl get all --selector env=prod,bu=finance,tier=frontend`👈
- `kubectl get po --selector env=prod --selector bu=finance --selector tier=frontend`👈

5.  Create a replicaset.

        apiVersion: apps/v1
        kind: ReplicaSet
        metadata:
          name: replicaset-1
        spec:
          replicas: 2 👈
          selector:
              matchLabels:
                tier: frontend👈
          template:
            metadata:
              labels:
                tier: frontend👈
            spec:
              containers:
              - name: nginx
                image: nginx

        $ kubectl create -f replicaset-definition-1.yaml

            replicaset.apps/replicaset-1 created

        $ kubectl get rs

            NAME           DESIRED   CURRENT   READY   AGE
            db-2           1         1         1       49m
            app-1          3         3         3       49m
            app-2          1         1         1       49m
            db-1           4         4         4       49m
            replicaset-1   2         2         2       29s

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
