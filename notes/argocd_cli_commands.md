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

```
kubectl describe pod argocd-repo-server -n argocd 
kubectl describe pod argocd-repo-server -n argocd | grep -i "ARGOCD_RECONCILIATION_TIMEOUT:" -B1 
kubectl patch configmap argocd-cm -n argocd --patch='{"data":{"timeout.reconciliation":"300s"}}'
```

# Application Custom Health Check


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


# Declarative Setup – Mono Application

```
kubectl apply -f declarative/mono-app/geocentric-app.yml -n argocd
```

# App of Apps

```
kubectl apply -f declarative/multi-app/app-of-apps.yml -n argocd
```

# Deploy apps using HELM Chart
```
kubectl apply -f declarative/multi-app/app-of-apps.yml -n argocd
```
# Deploy apps using HELM Chart
```
argocd app create helm-random-shapes \
  --repo https://github.com/Richardbmk/gitops-playground \
  --path helm-chart \
  --helm-set replicaCount=2 \
  --helm-set color.circle=pink \
  --helm-set color.square=violet \
  --helm-set service.type=NodePort \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc

argocd app create nginx \
  --repo https://charts.bitnami.com/bitnami \
  --helm-chart nginx \
  --revision 12.0.3 \
  --values-literal-file values.yaml \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc

helm list
```

# Multi-cluster application deployment

```
kubectl config set-cluster prod --server=https://1.2.3.4 --certificate-authority=prod.crt
kubectl config set-credentials admin --client-certificate=admin.crt --client-key=admin.key
kubectl config set-context admin-prod --cluster=prod --user=admin --namespace=prod-app

argocd cluster add admin-prod
argocd cluster list

kubectl describe secret cluster-1.2.3.4-9876543 -n argocd
```