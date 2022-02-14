# <span id='top'>09 ☸️ on the Cloud</span>

[[Google Cloud]](#Google)  
[[AWS EKS]](#AWS)
[[Azure AKS]](#Azure)  
[[References]](#ref)

<br>

## <span id='Google'>Google Cloud</span>

[[Top]](#top)

        gcloud container clusters get-credentials example-voting-app --zone us-central1-c --project example-voting-app-341306

        Fetching cluster endpoint and auth data.
        kubeconfig entry generated for example-voting-app.
        codingwoods@cloudshell:~$ kubectl get nodes

        NAME                                                STATUS   ROLES    AGE     VERSION
        gke-example-voting-app-default-pool-dda0f2a2-1plg   Ready    <none>   4m27s   v1.21.6-gke.1500
        gke-example-voting-app-default-pool-dda0f2a2-6mn2   Ready    <none>   4m27s   v1.21.6-gke.1500
        gke-example-voting-app-default-pool-dda0f2a2-h1k6   Ready    <none>   4m27s   v1.21.6-gke.1500

Clone the repo.

        $ git clone  https://github.com/kodekloudhub/example-voting-app.git

        example-voting-app/k8s-specifications$ ls

        postgres-deploy.yaml   redis-deploy.yaml   result-app-deploy.yaml   voting-app-deploy.yaml   worker-app-deploy.yaml
        postgres-service.yaml  redis-service.yaml  result-app-service.yaml  voting-app-service.yaml

Create `deployment` and `service` objects. Then:

        kubectl get deployments,services
        NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/postgres-deploy     1/1     1            1           5m44sdeployment.apps/redis-deploy        1/1     1            1           7m24sdeployment.apps/result-app-deploy   1/1     1            1           47s
        deployment.apps/voting-app-deploy   1/1     1            1           8m34s
        deployment.apps/worker-app-deploy   0/1     1            0           68s

        NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
        service/db               ClusterIP      10.72.13.241   <none>          5432/TCP       6m59s
        service/kubernetes       ClusterIP      10.72.0.1      <none>          443/TCP        23m
        service/redis            ClusterIP      10.72.12.88    <none>          6379/TCP       7m45s
        service/result-service   LoadBalancer   10.72.1.84     35.202.192.97   80:32158/TCP   2m8
        service/voting-service   LoadBalancer   10.72.15.183   35.239.14.176   80:30126/TCP   9m31s

<br>

## <span id='AWS'>AWS EKS</span>

[[Top]](#top)

### EKS Role Policy configuration

https://devops.stackexchange.com/questions/11722/cannot-configure-node-group-in-new-eks-cluster-due-to-no-node-iam-role-found

### Install AWS CLI

        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
        unzip awscliv2.zip
        sudo ./aws/install

<br>

## <span id='Azure'>Azure AKS</span>

[[Top]](#top)

- A service principal is used by the AKS service to manage the cloud resources

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- StackOverFlow (2020) Cannot configure node group in new EKS cluster due to no Node IAM role found https://devops.stackexchange.com/questions/11722/cannot-configure-node-group-in-new-eks-cluster-due-to-no-node-iam-role-found
- Installing or updating the latest version of the AWS CLI https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
