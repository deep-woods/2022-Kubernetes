# <span id='top'>02-03 Secrets</span>

[[Secrets]](#secret)  
[[🤲Hands-on Lab]](#handson)  
[[References]](#ref)

<br>

## <span id='secret'>Secrets</span>

[[Top]](#top)

_A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Such information might otherwise be put in a Pod specification or in a container image. Using a Secret means that you don't need to include confidential data in your application code._

_Because Secrets can be created independently of the Pods that use them, there is less risk of the Secret (and its data) being exposed during the workflow of creating, viewing, and editing Pods. Kubernetes, and applications that run in your cluster, can also take additional precautions with Secrets, such as avoiding writing confidential data to nonvolatile storage._

_Secrets are similar to ConfigMaps but are specifically intended to hold confidential data._ (Kubernetes)

<br>

## <span id='handson'>🤲Hands-on Lab</span>

[[Top]](#top)

1.  Study a `secret`.

        $ kubectl describe secrets default-token

        Name:         default-token-6kx8q
        Namespace:    default
        Labels:       <none>
        Annotations:  kubernetes.io/service-account.name: default
                      kubernetes.io/service-account.uid:   8193278b-9442-4bce-a2dc-3a03ec16cfe0

        Type:  kubernetes.io/service-account-token

        Data
        ====
        ca.crt:     566 bytes
        namespace:  7 bytes
        token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IlVsZ2V6cTZKenBYZW00RmlyTEV4TVc5amwtWVB0SUptS3lNVTdXaWp4bDAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tNmt4OHEiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjgxOTMyNzhiLTk0NDItNGJjZS1hMmRjLTNhMDNlYzE2Y2ZlMCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.PQ_MPyIC30iIHs_Uy9POZaudmeCa2CBX6q2PrenAhhbZcSXly-Sy0t6SGxtvPISNoT5ZDLhOVL-D8XvH5OQ6vxAKKGsaDPG9WEzxydM7-SrK7nNnpGN3J9CgPh8UHwYTaNax3cr-BneFxk7X7LzI8X_CZb5pbNQ4lzux1lX9CnPhPTahLVelrcndkPuFP6SVhSrAuasVum46dnq-wECpLEP98sTRZOvyCX8RsA4hb3vCD2JhX8-cuv5ycH8Z0F8auVgXkEgU8nsK4-4F36x1dCO-L-5Fw5Yo8AqhUyyUzF2ikfBUVR6qg-yrKcKqoarqS_NV_WsRUUnENbgAKr1qMA

2.  Considering the below architecture, create a `secret`.

- Architecture

        $ kubectl get po,svc
            NAME             READY   STATUS    RESTARTS   AGE
            pod/webapp-pod   1/1     Running   0          28s
            pod/mysql        1/1     Running   0          27s

            NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
            service/kubernetes       ClusterIP   10.43.0.1       <none>        443/TCP          9m47s
            service/webapp-service   NodePort    10.43.228.151   <none>        8080:30080/TCP   28s
            service/sql01            ClusterIP   10.43.65.120    <none>        3306/TCP         27s

        $ kubectl get secret
            NAME                  TYPE                                  DATA   AGE
            default-token-6kx8q   kubernetes.io/service-account-token   3      52m

- Create secrete imperatively.

        $ kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123

            secret/db-secret created

        $ kubectl describe secret/db-secret
            Name:         db-secret
            Namespace:    default
            Labels:       <none>
            Annotations:  <none>

            Type:  Opaque

            Data
            ====
            DB_Host:      5 bytes
            DB_Password:  11 bytes
            DB_User:      4 bytes

3.  Configure a running pod to load the env variables from the newly created secret.

        $ kubectl explain po --recursive | less

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/certified-kubernetes-application-developer-ckad/
- Kubernetes - Secrets https://kubernetes.io/docs/concepts/configuration/secret/
