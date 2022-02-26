# <span id='top'>02-02 ConfigMaps</span>

[[`ConfigMap`]](#configmap)  
[[🤲Hands-on Lab]](#handson)  
[[Common Errors]](#Errors)
[[References]](#ref)

<br>

## <span id='configmap'>`ConfigMap`</span>

[[Top]](#top)

_A ConfigMap is an `API object` used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume._

_A ConfigMap allows you to decouple environment-specific configuration from your container images, so that your applications are easily portable._

_Caution: ConfigMap does not provide secrecy or encryption. If the data you want to store are confidential, use a Secret rather than a ConfigMap, or use additional (third party) tools to keep your data private._ (Kubernetes)

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1. Update environment variable of a running pod.

First create a `.yaml` file.

- `kubectl get pod <pod name> -o yaml > pod.yaml`

        $ kubectl get pod forestapp-colour -o yaml > pod.yaml

Then modify the file for update.

        $ nano pod.yaml

            spec:
                containers:
                    - env:
                    - name: APP_COLOR
                        value: green

Delete the running pod.

        $ kubectl get po
            NAME           READY   STATUS    RESTARTS   AGE
            forestapp-colour   1/1     Running   0          7m44s

        $ kubectl delete po forestapp-colour
            pod "forestapp-colour" deleted

Run the pod using the updated definition file.

        $ kubectl create -f pod.yaml
            pod/forestapp-colour created

        $ kubectl get po
            NAME           READY   STATUS    RESTARTS   AGE
            forestapp-colour   1/1     Running   0          4s

2.  Inspect a `ConfigMap`.

        $ kubectl get configmaps --namespace=default

            NAME               DATA   AGE
            kube-root-ca.crt   1      26m
            db-config          3      15s


        $ kubectl describe configmap db-config

            Name:         db-config
            Namespace:    default
            Labels:       <none>
            Annotations:  <none>

            Data
            ====
            DB_NAME:
            ----
            SQL01
            DB_PORT:
            ----
            3306
            DB_HOST:
            ----
            SQL01.example.com

            BinaryData
            ====

            Events:  <none>

3.  Create a `ConfigMap`.

        $ kubectl create cm forest-app-config-map --from-literal=APP_COLOR=darkblue

            configmap/forest-app-config-map created

        $ kubectl get cm

            NAME                    DATA   AGE
            kube-root-ca.crt        1      39m
            db-config               3      13m
            forest-app-color        1      2m
            forest-app-config-map   1      7s

        $ kubectl describe cm forest-app-config-map

            Name:         forest-app-config-map   <-----------
            Namespace:    default
            Labels:       <none>
            Annotations:  <none>

            Data
            ====
            APP_COLOR:      <-----------
            ----
            darkblue        <-----------

            BinaryData
            ====

            Events:  <none>

4.  Update the `ConfigMap`.

- `kubectl explain po --recursive | grep envFrom -A3`

         envFrom        <[]Object>
            configMapRef        <Object>
               name     <string>
               optional <boolean>

With the hint above, not update the `ConfigMap`.

        apiVersion: v1
        kind: Pod
        metadata:
          creationTimestamp: "2022-02-26T01:35:18Z"
          labels:
            name: forest-app-color
          name: forest-app-color   <----------------------
          namespace: default
          resourceVersion: "901"
          uid: 16208199-57e5-40e5-b769-4791d4306296
        spec:
          containers:
          - envFrom:
            - configMapRef:
                name: forest-app-config-map   <----------------------
            image: forest/forest-app-color
            imagePullPolicy: Always
            name: forest-app-color
            resources: {}

Creation successful.

        $ kubectl apply -f pod.yaml
          pod/webapp-color created

<br>

### <span id='errors'>Common Errors</span>

[[Top]](#top)

- Key not found

        $ kubectl apply -f pod.yaml
            error: error parsing pod.yaml: error converting YAML to JSON: yaml: line 15: did not find expected key

- Wrong character type

        $  kubectl apply -f pod.yaml
            error: error parsing pod.yaml: error converting YAML to JSON: yaml: line 15: found character that cannot start any token

- ValidationError
- `ValidationError(Pod.spec.containers[0].envFrom[0]`
- `ValidationError(Pod.spec.containers[0].envFrom[0].configMapRef)`

        $  kubectl apply -f pod.yaml
            error: error validating "pod.yaml": error validating data: [ValidationError(Pod.spec.containers[0].envFrom[0]): unknown field "name" in io.k8s.api.core.v1.EnvFromSource, ValidationError(Pod.spec.containers[0].envFrom[0]): unknown field "value" in io.k8s.api.core.v1.EnvFromSource]; if you choose to ignore these errors, turn validation off with --validate=false

        $  kubectl apply -f pod.yaml
            error: error validating "pod.yaml": error validating data: ValidationError(Pod.spec.containers[0].envFrom[0].configMapRef): invalid type for io.k8s.api.core.v1.ConfigMapEnvSource: got "array", expected "map"; if you choose to ignore these errors, turn validation off with --validate=false

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- ConfigMaps https://kubernetes.io/docs/concepts/configuration/configmap/
