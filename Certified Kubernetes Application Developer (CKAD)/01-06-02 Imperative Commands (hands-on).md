# Imperative commands

- `kubectl run redis --image=redis:alpine --labels=tier=db`
- `kubectl expose pod redis --name=redis-service --port=6379 --target-port 6379`

2. Create a pod with labels. 

        $ kubectl run redis --image=redis:alpine --labels=tier=db
        pod/redis created

        $ kubectl get po
        NAME        READY   STATUS    RESTARTS   AGE
        nginx-pod   1/1     Running   0          117s
        redis       1/1     Running   0          11s
    
3. Create a service

        controlplane ~ ➜  kubectl expose pod redis --name=redis-service --port=6379 --target-port 6379
        service/redis-service exposed

        controlplane ~ ➜  kubectl get sv
        error: the server doesn't have a resource type "sv"

        controlplane ~ ✖ kubectl get svc
        NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
        kubernetes      ClusterIP   10.43.0.1     <none>        443/TCP    13m
        redis-service   ClusterIP   10.43.0.191   <none>        6379/TCP   24s


        kubectl describe svc redis-service
        Name:              redis-service
        Namespace:         default
        Labels:            tier=db  
        Annotations:       <none>
        Selector:          tier=db
        Type:              ClusterIP
        IP Family Policy:  SingleStack
        IP Families:       IPv4
        IP:                10.43.0.191
        IPs:               10.43.0.191
        Port:              <unset>  6379/TCP
        TargetPort:        6379/TCP             <----------------
        Endpoints:         10.42.0.10:6379      <----------------
        Session Affinity:  None
        Events:            <none>
  
4. Create a deployment. 
  
In two steps: 
  
        kubectl create deploy webapp --image=forest/webapp-color
        deployment.apps/webapp created

        controlplane ~ ➜  kubectl scale deployment --replicas=3 webapp
        deployment.apps/webapp scaled

        controlplane ~ ➜  kubectl get po
        NAME                      READY   STATUS    RESTARTS   AGE
        nginx-pod                 1/1     Running   0          8m30s
        redis                     1/1     Running   0          6m44s
        webapp-56847f875b-z4mk7   1/1     Running   0          18s
        webapp-56847f875b-cdqsx   1/1     Running   0          6s
        webapp-56847f875b-79764   1/1     Running   0          6s
  
Alternatively, in a single step:
  
        kubectl create deployment wepapp --image=forest/webapp-color  --replicas=3
        deployment.apps/wepapp created

        $ kubectl get deploy
        NAME     READY   UP-TO-DATE   AVAILABLE   AGE
        wepapp   3/3     3            3           12s

        $ kubectl get po
        NAME                      READY   STATUS    RESTARTS   AGE
        nginx-pod                 1/1     Running   0          10m
        redis                     1/1     Running   0          8m48s
        wepapp-75dd6fcf86-6txck   1/1     Running   0          17s
        wepapp-75dd6fcf86-jv84g   1/1     Running   0          17s
        wepapp-75dd6fcf86-q57mf   1/1     Running   0          17s

5. Create and expose a pod. 
      
        $ kubectl run custom-nginx --image=nginx --port=8080
              pod/custom-nginx created

        $ kubectl describe po custom-nginx

6. Create a namespace. 
      
        $ kubectl create ns dev-ns

7. Create a deployment. 
      
        $ kubectl create deploy redis-deploy --image=redis --namespace=dev-ns --dry-run=client -o yaml > redis-deployment.yaml

        $ nano redis-deployment.yaml    <---- CHANGE CONFIG AS NECESSARY (such as --replicas)
        $ kubectl apply -f redis-deployment.yaml

              deployment.apps/redis-deploy created

        $ kubectl get deployments.app -n dev-ns

              NAME            READY   UP-TO-DATE    AVAILABLE   AGE
              redis-deploy    2/2     2             2                                            

8. Create a pod with some additional configuration. 
                                            
        $ kubectl run httpd --image=httpd:alpine --port=80 --expose --dry-run=client -o yaml   <---- prints out .yaml
              service/httpd created
              pod/httpd created

        $ kubectl get pod httpd

              NAME      READY   STATUS             RESTARTS   AGE
              httpd     0/1     ContainerCreating  0          2m                                            

<br>

## References

- Kodekloud https://kodekloud.com/topic/solution-imperative-commands-optional/#
