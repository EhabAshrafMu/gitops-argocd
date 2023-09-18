# Create an Argocd Application using CLI

```
argocd app create

argocd app create solar-system-app-2 \
    --repo https://github.com/Richardbmk/gitops-playground/ \
    --path ./solar-system \
    --dest-namespace solar-system \
    --dest-server https://kubernetes.default.svc

argocd app list

argocd app sync solar-system-app-2
```