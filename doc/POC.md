# Argo CD Installation and Setup

#### Prepare demo cluster
`k3d cluster create gitops-argocd`

#### Create argocd namespace
`kubectl create namespace argocd`

#### Apply argocd installation manifest file
`kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`

#### Check argocd cluster entities
`kubectl get all -n argocd`

#### Access ArgoCD UI with port forwarding
`kubectl port-forward svc/argocd-server -n argocd 8080:443&`

#### Extract admin password from secret
`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

#### Open ArgoCD UI via web browser
Accept the self signed certificate and login with the extracted password and login `admin`. (At real prodiction environment, a proper CA signed certificate should be configured.)
[https://localhost:8080/](https://localhost:8080/)

![Argo CD Login](.data/ArgoCD_Login.gif)