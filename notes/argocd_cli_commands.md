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

# Application Custom Health Check
>> argocd-cm 
```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: ConfigMap
data:
  resource.customizations.health.ConfigMap: |
    hs = {}
    hs.status = "Healthy"
      if obj.data.TRIANGLE_COLOR == "white" then
        hs.status = "Degraded"
        hs.message = "Use a different COLOR for TRIANGLE"
      end
    return hs
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cm","namespace":"argocd"}}
  creationTimestamp: "2023-09-19T08:58:02Z"
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cm
  namespace: argocd
  resourceVersion: "43188"
  uid: 268fa256-0400-48ea-8d71-c909fe7f72eb
```