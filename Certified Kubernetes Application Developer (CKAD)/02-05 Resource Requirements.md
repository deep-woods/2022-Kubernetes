# <span id='top'>02-05 Resource Requirements</span>

[[Resource Requirements]](#Requirements)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Requirements'>Resource Requirements</span>

[[Top]](#top)

**Resource Management for Pods and Containers**

_When you specify a Pod, you can optionally specify how much of each resource a container needs. The most common resources to specify are CPU and memory (RAM); there are others._

_When you specify the resource request for containers in a Pod, the kube-scheduler uses this information to decide which node to place the Pod on. When you specify a resource limit for a container, the kubelet enforces those limits so that the running container is not allowed to use more of that resource than the limit you set. The kubelet also reserves at least the request amount of that system resource specifically for that container to use._

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Explore CPU requirements.

        $ kubectl describe po rabbit

        Containers:
          cpu-stress:
            Container ID:  containerd://
            ...
            Ready:          False
            Restart Count:  4
            Limits:
              cpu:  2
            Requests:
              cpu:        1    <---------------- CPU requirement
            Environment:  <none>
            Mounts:
              /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-khx4s (ro)

2.  Identify the reason of failure (pod not running).

- `OOMKilled`: the pod ran out of memory.

        $ kubectl describe pod elephant  | grep -A5 State

            State:          Waiting
              Reason:       CrashLoopBackOff    <----------
            Last State:     Terminated
              Reason:       OOMKilled           <----------
              Exit Code:    1
              Started:      Sat, 26 Feb 2022 15:03:37 +0000
              Finished:     Sat, 26 Feb 2022 15:03:37 +0000
            Ready:          False

3.  Increase the memory requirements.

        apiVersion: v1
        kind: Pod
        metadata:
          name: elephant
          namespace: default
          resourceVersion: "1021"
        spec:
          containers:
          - args:
            - --vm
            - "1"
            - --vm-bytes
            - 15M
            - --vm-hang
            - "1"
            command:
            - stress
            image: polinux/stress
            imagePullPolicy: Always
            name: mem-stress
            resources:        <--------------------
              limits:
                memory: 20Mi    <------------------ SEE NOTES BELOW.
              requests:
                memory: 20Mi    <------------------

- `limits: memory` must be equal to or greater than `requests`: `memory`.
- This may lead to the following problem otherwise.

### Common errors - Invalid value for requirements

        $ kubectl create -f elephant.yaml
            The Pod "elephant" is invalid: spec.containers[0].resources.requests: Invalid value: "20Mi": must be less than or equal to memory limit

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- Resource Management for Pods and Containers https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
