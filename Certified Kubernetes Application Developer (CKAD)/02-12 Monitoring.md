# <span id='top'>02-12 Monitoring</span>

[[Monitoring Commands]](#Monitoring)  
[[]](#)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Monitoring'>Monitoring Commands</span>

[[Top]](#top)

- `kubectl top node`
- `kubectl top pod --sort-by='memory' --no-headers | head -1`
- `kubectl top pod --sort-by='cpu' --no-headers | tail -1`

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Run an application.

        $ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git

            Cloning into 'kubernetes-metrics-server'...
            remote: Enumerating objects: 24, done.
            remote: Counting objects: 100% (12/12), done.
            remote: Compressing objects: 100% (12/12), done.
            remote: Total 24 (delta 4), reused 0 (delta 0), pack-reused 12
            Unpacking objects: 100% (24/24), done.

        $ cd kubernetes-metrics-server
        $ ls

            README.md auth-reader.yaml metrics-server-service.yaml
            aggregated-metrics-reader.yaml metrics-apiservice.yaml resource-reader.yaml
            auth-delegator.yaml metrics-server-deployment.yaml

Create everything!

        $ kubectl create -f . 👈

            clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created👈
            clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created👈
            rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created👈
            apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created👈
            serviceaccount/metrics-server created👈
            deployment.apps/metrics-server created👈
            service/metrics-server created👈
            clusterrole.rbac.authorization.k8s.io/system:metrics-server created👈
            clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created👈

2.  Identify which node is consuming the most `CPU` resources and `MEMORY`.

        $ kubectl top node

            NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
            controlplane   459m         1%     1271Mi          0%
            node01         86m          0%     361Mi           0%

Alternatively,

        $ kubectl top pod --sort-by='memory' --no-headers | head -1
            rabbit     134m   201Mi

3.  Identify the most resource-costly pod.

        $ kubectl top pod --sort-by='cpu' --no-headers | tail -1

            NAME       CPU(cores)   MEMORY(bytes)
            elephant   20m          32Mi
            lion       1m           18Mi
            rabbit     141m         252Mi

        $ watch "kubectl top node"

            Every 2.0s: kubectl top node                                           controlplane: Fri Mar  4 09:53:28 2022

            NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
            controlplane   442m         1%     1185Mi          0%
            node01         66m          0%     340Mi           0%

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- Geeks for Geeks (n.d.) Watch command in linux with examples https://www.geeksforgeeks.org/watch-command-in-linux-with-examples/
