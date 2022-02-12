# <span id='top'>☸️ReplicaSets</span>

[[`rc-definition.yml`]](#definition)  
[[Replicas]](#replicas)  
[[🤲Hands-on Practice]](#handson)  
[[References]](#ref)

<br>

## <span id='definition'>`rc-definition.yml`</span>

[[Top]](#top)

- `kubectl create -f replicaset-definition.yml`: create replicas
- `kubectl get replicaset`
- `kubectl delete replicaset <REPLICA NAME>`: deletes all underlying pods.
- `kubectl replace -f replicaset-definition.yml`: replace or update the replica
- `kubectl scale --replicas=6 -f <rc definition file>`

- `pod-definition.yml` becomes the child of `template.

        -------------- rc-definition.yml --------------

        apiVersion: v1
        kind: ReplicationController
        metadata:
           name: myapp-rc
           labels:
              app: myapp
              type: front-end
        spec:
            template:

                POD DEFINITION.YAML content HERE.

            replicas: <num>

Then run the file.

        kubectl create -f rc-definition.yml

        kubectl get replicationcontroller

<br>

## <span id='replicas'>Replicas</span>

[[Top]](#top)

Ways to scale `replicasets`

1. Update the number of replicas in the definition file to greater number.

- `kubectl replace -f replicaset-definition.yml`

2. Create new replicas using the definition file.

- `kubectl scale --replicas=6 -f replicaset-definition.yml`

3. This doesn't change the number of replicas defined in the `definition.yml`.

- `kubectl scale --replicas=6 replicaset myapp-replicaset`

<br>

## <span id='handson'>🤲Hands-on Practice</span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
