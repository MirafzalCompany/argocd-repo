# Cluster bootstrapping

1. Install ArgoCD
```
kubectl create ns argocd
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm upgrade -i -n argocd argocd argo/argo-cd --version 6.7.2
```
2. Port-forward in dedicated tab of terminal
```
kubectl port-forward service/argocd-server -n argocd 8080:443
```
3. Open the browser on http://localhost:8080
4. Get admin password
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
5. Login with user admin and password above
6. Apply ```argocd-git-repo-template.yaml```
```
kubectl apply -f infra-tools-stage/argocd-git-repo-template.yaml
```
7. Apply ```infra-tools-stage-app-of-apps.yaml```
```
kubectl apply -f infra-tools-stage/infra-tools-app-of-apps.yaml
```
8. Sync ONLY namespaces and applications
9. After all the applications are sync and their pods are ready,
   apply other resources in infra-tools-app-of-apps (ClusterSecretStore, IPAddressPool, L2Advertisement)

Sync order:
1. Ingress nginx
2. Cert-manager
3. Argocd-self