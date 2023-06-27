#  ArgoCD Installation
1. Create ArgoCD namespace and install ArgoCD from yaml
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
(optional) To check whether nodes are running use this command:
```bash
kubectl get all -n argocd
```
2. Forward the port to access ArgoCD GUI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
3. Retrieve the password  
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
4. Enter 127.0.0.1:8080 in the address bar to navigate to ArgoCD login page, use "admin" as a login and paste the password that you've retreived earlier.

![image](https://github.com/gidra39/AsciiArtify/assets/95176865/c18c6443-7f0a-4d1e-8a65-b70cc01038e6)
