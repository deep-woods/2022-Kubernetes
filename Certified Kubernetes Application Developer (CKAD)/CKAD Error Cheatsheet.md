# <span id='top'>CKAD Error Cheatsheet</span>

[[]](#)  
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

## <span id=''> </span>

[[Top]](#top)

### Resource is missing.

        $ kubectl get po simple-forest-2 -oyaml > app2-pod.yaml

        $ kubectl apply👈 -f app2-pod.yaml

            Warning: resource👈 pods/simple-forest-2 is missing👈 the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
            The Pod "simple-forest-2" is invalid: spec:

<br>

### Spelling error

        $ kubectl create -f app2-pod.yaml

            error: error validating "app2-pod.yaml": error validating data: ValidationError(Pod.spec.containers[0].livenessProbe): unknown field "initalDelaySeconds" 👈 in io.k8s.api.core.v1.Probe; if you choose to ignore these errors, turn validation off with --validate=false

<br>

### Indentation error

        $ kubectl create -f app2-pod.yaml

            error: error validating "app1-pod.yaml": error validating data: ValidationError(Pod.spec): unknown field 👈"livenessProbe"👈 in io.k8s.api.core.v1.PodSpec; if you choose to ignore these errors, turn validation off with --validate=false

<br>

### Multiplicity problem: Specify which one.

        $ kubectl get po

            NAME       READY   STATUS    RESTARTS   AGE
            forest-1   1/1     Running   0          4m59s
            forest-2   2/2 👈  Running   0          2m47s

If you want to check the logs of an instance and there are multiple of them, you have to specify which one.

        $ kubectl logs forest-2 👈

            error: a container name must be specified 👈 for pod forest-2, choose one of: [simple-forest db]

<br>

## <span id=''></span>

[[Top]](#top)

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

<br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
