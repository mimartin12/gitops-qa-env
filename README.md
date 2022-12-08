# gitops-qa-env
POC a GitOps process with a QA environment

## Getting started

Install the following tools
- Docker
- k3d
- kubectl


## Creating your dev cluster

cd into the `./cluster` folder and run `k3d cluster create`. K3d will magically pick up the config file :)

Once the cluster has been created, your kube-config file should be automatically update. You can then run `kubectl cluster-info` if you see the that the control plane is running on `https://0.0.0.0` you know you have a running cluster, locally!

## Deploying ArgoCD

For GitOps we need to depoy an application called ArgoCD. This is going to be our main way of deploying applications to the cluster from a git repo.

Create the ArgoCD namespace `kubectl create namespace argocd`

Navigate to the `./argocd` folder and apply the deployment manifest (`install.yaml`) into the `argocd` namespace.
`kubectl apply -f install.yaml -n argocd`

That should do it. Lets get connected by getting the ArgoCD service name and port forwarding to it.

List services: `kubectl get service -n argocd`

The one we are looking for should be `argocd-server`.

Using `kubectl`, port forward to the server port with `kubectl port-forward svc/argocd-server -n argocd 8080:443`

Open your favorite browser and navigate to `https://localhost:8080`, accept the self-signed certificate error and you should be presented with the ArgoCD page.

Lets get the credentials to log into the ArgoCD dashboard. ArgoCD stores this in a Kubernetes secret that was created when we installed ArgoCD. 

Retrieve secret value: `echo $(kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}") | base64 -d`

Using the secret, log into ArgoCD

Username: admin
Password: <secret-value>
