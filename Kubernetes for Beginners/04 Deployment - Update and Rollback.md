# <span id='top'>04 Deployment - Update and Rollback</span>

[[Update and Rollback]](#Rollback)  
[[🤲Hands-on Lab]](#Lab)  
[[References]](#ref)

<br>

## <span id='Rollback'>Update and Rollback</span>

[[Top]](#top)

### `Recreate` and `RollingUpdate`

- Create
  - `kubectl create -f deployment-definition.yml`
- Get
  - `kubectl get deployments`
- Update
  - `kubectl apply -f deployment-definition.yml`
  - `kubectl set image deployment/forest-app-deployment nginx=nginx:1.20.1`
- Status
  - `kubectl rollout status deployment/forest-app-deployment`
  - `kubectl rollout history deployment/forest-app-deployment`
- Rollback
  - `kubectl rollout undo deployment/forest-app`

<br>

### `kubectl apply`

- `kubectl apply -f deploy-definition.yml`
- `kubectl set image deployment/<deployment name> <image name>=<image>:<version>`
  - `kubectl set image deployment/forest-app-deployment nginx=nginx:1.20.1

<br>

### Upgrades

- Kubernetes deployments allow you to roll back to a previous revision.
- `kubectl rollout undo deployment/forest-app-deployment`

<br>

## <span id='Lab'>🤲Hands-on Lab</span>

[[Top]](#top)

    $ kubectl get deployment
    NAME               READY   UP-TO-DATE   AVAILABLE   AGE
    myapp-deployment   0/6     6            0           6s

    $ kubectl rollout status deployment.apps/myapp-deployment
    Waiting for deployment "myapp-deployment" rollout to finish: 0 of 6 updated replicas are available...

<br>

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
