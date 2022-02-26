# <span id='top'>02-04 Security</span>

[[Security]](#Security)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='Security'>Security</span>

[[Top]](#top)

- Capabilities are only supported at the container level and not at the POD level.
- A `security context` defines privilege and access control settings for a Pod or Container. Security context settings include, but are not limited to:

### Configure a Security Context for a Pod or Container

- Discretionary Access Control: Permission to access an object, like a file, is based on user ID (UID) and group ID (GID).

- Security Enhanced Linux (SELinux): Objects are assigned security labels.

- Running as privileged or unprivileged.

- Linux Capabilities: Give a process some privileges, but not all the privileges of the root user.

- AppArmor: Use program profiles to restrict the capabilities of individual programs.

- Seccomp: Filter a process's system calls.

  - `allowPrivilegeEscalation`: Controls whether a process can gain more privileges than its parent process. This bool directly controls whether the `no_new_privs` flag gets set on the container process. `allowPrivilegeEscalation` is always true when the container:

    - is run as privileged, or
    - has `CAP_SYS_ADMIN`

- `readOnlyRootFilesystem`: Mounts the container's root filesystem as read-only.

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1. Figure out the user that is used in a running pod.

   $ kubectl exec ubuntu-sleeper -- whoami
   root

2. Edit a pod definition to assign a specific `user`.

There is a pod already running. Extract a `.yaml` from it and modified the user.

        $ kubectl get po ubuntu-sleeper -o yaml > ubuntu-sleeper-pod.yaml

        $ ls
        multi-pod.yaml           sample.yaml              ubuntu-sleeper-pod.yaml    <--------- CREATED BY THE ABOVE COMMAND


        $ cat ubuntu-sleeper-pod.yaml
            apiVersion: v1
            kind: Pod
            metadata:
                name: ubuntu-sleeper
                namespace: default
                resourceVersion: "796"
            spec:
                containers:
                - command:
                    - sleep
                    - "4800"
                    image: ubuntu
                    imagePullPolicy: Always
                    name: ubuntu
                dnsPolicy: ClusterFirst
                securityContext:
                    runAsUser: 1010

Kill the running pod then start a new one using the `.yaml` file.

        $ kubectl get po
            NAME             READY   STATUS    RESTARTS   AGE
            ubuntu-sleeper   1/1     Running   0          12m

        $ kubectl delete po ubuntu-sleeper
            pod "ubuntu-sleeper" deleted

        $ ls
        multi-pod.yaml           sample.yaml              ubuntu-sleeper-pod.yaml   <--------------------- THIS

        $ kubectl create -f ubuntu-sleeper-pod.yaml
            pod/ubuntu-sleeper created

Alternatively, you can also `apply`:

        $ kubectl apply -f ubuntu-sleeper.yaml
            pod/ubuntu-sleeper configured  <-------- UPDATED

3.  Multiple user Configuration: explore.

        $ cat multi-pod.yaml

          apiVersion: v1
          kind: Pod
          metadata:
            name: multi-pod
          spec:
            securityContext:                pod-level
              runAsUser: 1001     <-------- default user

            containers:
            -  image: ubuntu
              name: botanical-web
              command: ["sleep", "5000"]
              securityContext:
                runAsUser: 1002   <------- container user

            -  image: ubuntu
              name: birchwood     <-------- default user
              command: ["sleep", "5000"]

4.  Update user capabilities for a pod in definition file.

        apiVersion: v1
        kind: Pod
        metadata:
          name: ubuntu-sleeper
          namespace: default
        spec:
          containers:
          - command:
            - sleep
            - "4800"
            image: ubuntu
            imagePullPolicy: Always
            name: ubuntu-sleeper
            securityContext:
              capabilities:
                add: ["SYS_TIME"]   <--------- UPDATED

Now, create a `pod` using the updated definition file.

        $  kubectl create -f ubuntu-sleeper.yaml
            pod/ubuntu-sleeper created

        $  kubectl get po
        NAME             READY   STATUS    RESTARTS   AGE
            ubuntu-sleeper   1/1     Running   0          5s

5. Update capabilities.

### Common erros

- Depending on the editor, the tab ` ` might not be parsable and cause error.

        $  kubectl create -f ubuntu-sleeper.yaml
            error: error parsing ubuntu-sleeper.yaml: error converting YAML to JSON: yaml: line 18: found character that cannot start any token

- Indentation matters!

        $ kubectl create -f ubuntu-sleeper.yaml
            error: error validating "ubuntu-sleeper.yaml": error validating data: ValidationError(Pod.spec.securityContext): unknown field "capabilities" in io.k8s.api.core.v1.PodSecurityContext; if you choose to ignore these errors, turn validation off with --validate=false

- DO NOT delete the `resource` tag in `spec`.

        $ kubectl apply -f ubuntu-sleeper.yaml
            Warning: resource pods/ubuntu-sleeper is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
            pod/ubuntu-sleeper configured

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
- Encrypting Secret Data at Rest https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/
