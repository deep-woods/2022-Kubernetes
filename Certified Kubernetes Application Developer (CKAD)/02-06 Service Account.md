# <span id='top'>02-06 Service Account</span>

[[Service Account]](#serviceaccount)  
[[Commands]](#Commands)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='serviceaccount'>Service Account</span>

[[Top]](#top)

| `User Account`           | `Service Account`  |
| ------------------------ | ------------------ |
| Used by humans :ok_woman | Used by machines🤖 |

- Every application needs a ServiceAccount with the right permissions to be created to authenticate to Kubernetes.
- Whenever a pod is created, the default service account and its token are automatically mounted to that pod as a volume mount.
- Kubernetes service accounts are Kubernetes resources, created and managed using the Kubernetes API, meant to be used by in-cluster Kubernetes-created entities, such as Pods, to authenticate to the Kubernetes API server or external services.

<br>

      pod-definition.yml

        apiVersion: v1
        kind: pod
        metadata:
          name: k8s-dashboard
        spec:
          containers:
            - name: k8s-dashboard
              image: k8s-dashboard
          serviceAccount: k8s-dashboard

- It is not possible to edit the service account of an existing pod, you must delete and recreate the pod.
  in case of a deployment, you will be able to edit the service account, as any changes to the pod definition file will automatically trigger a new rollout for the deployment. The deployment will take care of deleting and recreating new pods with the right service account.

<br>

## <span id='Commands'>Commands</span>

[[Top]](#top)

- `kubectl create sa dashboard-sa`
- `kubectl get sa`
- `kubectl describe sa dashboard-sa`: the token is stored as a secret object.
- `kubectl exec -it k8s-dashboard ls /var/run/secrets/kubernetes.io/serviceaccount`
- `kubectl exec -it k8s-dashboard cat /var/run/secrets/kubernetes.io/serviceaccount/token`

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Figure out the path to the `ServiceAccount credentials` within a pod.

        $ kubectl describe po forest

            Containers:
              web-dashboard:
                Container ID:   docker://04a3bebcf3b0125e86a131c2e25041b5d737bd8ed74fe1b68a736efd405e83bb
                Image:          gcr.io/kodekloud/customimage/my-kubernetes-dashboard
                Image ID:       docker-pullable://gcr.io/kodekloud/customimage/my-kubernetes-dashboard@sha256:7d70
                Port:           8080/TCP
                Host Port:      0/TCP
                State:          Running
                  Started:      Sun, 27 Feb 2022 05:56:43 +0000
                Ready:          True
                Restart Count:  0
                Environment:
                  PYTHONUNBUFFERED:  1
                Mounts:
                  /var/run/secrets/kubernetes.io/serviceaccount from default-token-xx65r (ro)

2.  Inspect a pod and find out its service account mounted on it.

        $ kubectl describe po web-dashboard-5899cf7849-ds9gb

        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-nhjpd (ro)

3.  Create a new serviceaccount.

        $ kubectl create sa dashboard-sa

        $ kubectl get sa

            NAME           SECRETS   AGE
            dashboard-sa   1         8s
            default        1         28m

        $ kubectl describe sa dashboard-sa

            Name:                dashboard-sa
            Namespace:           default
            Labels:              <none>
            Annotations:         <none>
            Image pull secrets:  <none>
            Mountable secrets:   dashboard-sa-token-kvnx6
            Tokens:              dashboard-sa-token-kvnx6
            Events:              <none>

4.  (Optional) Some updates to the token

        $ ls /var/rbac

            dashboard-sa-role-binding.yaml  pod-reader-role.yaml

        $ cat /var/rbac/dashboard-sa-role-binding.yaml

            ---
            kind: RoleBinding
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
              name: read-pods
              namespace: default
            subjects:
            - kind: ServiceAccount
              name: dashboard-sa # Name is case sensitive
              namespace: default
            roleRef:
              kind: Role #this must be Role or ClusterRole
              name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
              apiGroup: rbac.authorization.k8s.io

5.  Inspect a token.

        $ kubectl get secrets

            NAME                       TYPE                                  DATA   AGE
            dashboard-sa-token-kvnx6   kubernetes.io/service-account-token   3      7m42s
            default-token-nhjpd        kubernetes.io/service-account-token   3      35m

        $ kubectl describe secret dashboard-sa-token-kvnx6

            Name:         dashboard-sa-token-kvnx6
            Namespace:    default
            Labels:       <none>
            Annotations:  kubernetes.io/service-account.name: dashboard-sa
                          kubernetes.io/service-account.uid: ae781911-edaf-4222-9c4d-92ce3657a166

            Type:  kubernetes.io/service-account-token

            Data
            ====
            ca.crt:     1066 bytes
            namespace:  7 bytes
            token:      eyJhbG.....2EifQ.L0uN...7A7SE3gg

6.  Change the `ServiceAccount`.

        $ kubectl get deploy

            NAME            READY   UP-TO-DATE   AVAILABLE   AGE
            web-dashboard   1/1     1            1           17m

        $ kubectl get deploy web-dashboard -oyaml > web-dashboard-deploy.yaml 👈

            bin   etc         initrd.img.old  lost+found  opt   run   srv  usr      vmlinuz.old
            boot  home        lib             media       proc  sbin  sys  var
            web-dashboard-deploy.yaml 👈
            dev   initrd.img  lib64           mnt         root  snap  tmp  vmlinuz

        $ nano web-dashboard-deploy.yaml

            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: web-dashboard
              namespace: default
            spec:
              replicas: 1
              selector:
                matchLabels:
                  name: web-dashboard
              strategy:
                rollingUpdate:
                  maxSurge: 25%
                  maxUnavailable: 25%
                type: RollingUpdate
              template:
                metadata:
                  creationTimestamp: null
                  labels:
                    name: web-dashboard
                spec:
                  serviceAccountName: dashboard-sa  👈👈
                  containers:
                  - image: gcr.io/kodekloud/customimage/my-kubernetes-dashboard
                    imagePullPolicy: Always
                    name: web-dashboard
                    ports:
                    - containerPort: 8080
                      protocol: TCP

        $ kubectl apply -fweb-dashboard-deploy.yaml

            Warning: resource deployments/web-dashboard is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
            deployment.apps/web-dashboard configured 👈

### Dashboard application

        Name	State	Containers	Service Account	IP

        web-dashboard-5899cf7849-ds9gb	Running	1	default
        web-dashboard-7fdbdb6869-gmngc	Running	1	dashboard-sa 👈	10.50.0.5

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
