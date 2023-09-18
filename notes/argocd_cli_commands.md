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

# Create ArgoCD Project

```
argocd proj list

kubectl get applications -n argocd
kubectl get appproj -n argocd
```

# Reconciliation loop
$ kubectl describe pod argocd-repo-server -n argocd 
$ kubectl describe pod argocd-repo-server -n argocd | grep -i "ARGOCD_RECONCILIATION_TIMEOUT:" -B1 
$ kubectl patch configmap argocd-cm -n argocd --patch='{"data":{"timeout.reconciliation":"300s"}}'