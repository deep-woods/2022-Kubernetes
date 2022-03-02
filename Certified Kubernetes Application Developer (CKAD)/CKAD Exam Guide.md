# <span id='top'>Exam Guide</span>

[[CKAD]](#CKAD)  
[[Exam Resources]](#Resources)  
[[Formatting]](#Formatting)  
[[Editing pods and deployments]](#Editing)  
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

## <span id='CKAD'>Certified Kubernetes Application Developer (CKAD)</span>

[[Top]](#top)

### 18% - Configuration

- Understand `ConfigMaps`
- Understand `SecurityContexts`
- Define an application's `resource requirements`
- Create & consume `Secrets`
- Understand `ServiceAccounts`

<br>

## <span id='Resources'>Exam Resources</span>

[[Top]](#top)

- [Kubernetes in Action](https://www.manning.com/books/kubernetes-in-action)

<br>

## <span id='Formatting'>Formatting</span>

What's editable:

- `spec.containers[*].image`
- `spec.initContainers[*].image`
- `spec.activeDeadlineSeconds`
- `spec.tolerations`

What's not editable:

- `environment variables`
- `service accounts`
- `resource limits`

### Edit `deployment`

- Pod template is a child of the deployment specification.

        kubectl edit deployment <deploy name>

<br>
<br>

## <span id='Editing'>Editing pods and deployments</span>

[[Top]](#top)

- `-o json`: Outputs a JSON formatted API object.
- `-o name`: Prints only the resource name and nothing else.
- `-o wide`: Outputs in the plain-text format with any additional information.
- `-o yaml`: Outputs a YAML formatted API object.

### Examples

- `json`
  $ kubectl create namespace thick-woods --dry-run -o json

            {
                "kind": "Namespace",
                "apiVersion": "v1",
                "metadata": {
                    "name": "thick-woods",
                    "creationTimestamp": null
                },
                "spec": {},
                "status": {}
            }

- `yaml`

        $ kubectl create namespace thick-woods --dry-run -o yaml

            apiVersion: v1
            kind: Namespace
            metadata:
            creationTimestamp: null
            name: thick-woods
        - `spec: {}
            status: {}

$ kubectl get pods -o wide
NAME READY STATUS RESTARTS AGE IP NODE NOMINATED NODE READINESS GATES
busybox 1/1 Running 0 3m39s 10.36.0.2 node01 <none> <none>
ningx 1/1 Running 0 7m32s 10.44.0.1 node03 <none> <none>
redis 1/1 Running 0 3m59s 10.36.0.1 node01 <none> <none>

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
- Haripraghash Subramaniam (2019) CKAD(Certified Kubernetes Application Developer) — My journey https://medium.com/@harioverhere/ckad-certified-kubernetes-application-developer-my-journey-3afb0901014
- Overview of kubectl https://kubernetes.io/docs/reference/kubectl/overview/
- kubectl Cheat Sheet https://kubernetes.io/docs/reference/kubectl/cheatsheet/
