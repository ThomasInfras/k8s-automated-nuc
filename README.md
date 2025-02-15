
Déployer argocd-infra avec helm
```
sudo helm repo add argo https://argoproj.github.io/argo-helm
sudo helm install argocd-infra  argo/argo-cd --set notifications.enabled=false --set dex.enabled=false --set redis.enabled=true --set server.replicas=1 --set configs.cm.admin.enabled=false --set configs.repositories.gitrepo.url=https://github.com/ThomasInfras/k8s-automated-nuc --set applicationSet.replicas=0  --namespace argocd-infra --create-namespace

sudo kubectl apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: k8s-argocd-infra.git
  namespace: argocd-infra
spec:
  ignoreDifferences:
    - group: argoproj.io
      kind: Application
      jsonPointers:
        - /operation
  project: default
  source:
    repoURL: 'https://github.com/ThomasInfras/k8s-automated-nuc'
    targetRevision: HEAD
    path: k8s-argocd-infra

  destination:
    server: 'https://kubernetes.default.svc'
    namespace: argocd-infra

  syncPolicy:
    automated:
      prune: true
      selfHeal: true
EOF
```


- Pour windows

```
$content = @"
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: k8s-argocd-infra.git
  namespace: argocd-infra
spec:
  ignoreDifferences:
  - group: argoproj.io
    kind: Application
    jsonPointers:
    - /operation
  project: default
  source:
    repoURL: 'https://github.com/ThomasInfras/k8s-automated-nuc'
    targetRevision: HEAD
    path: k8s-argocd-infra
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: argocd-infra
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
"@

$content | kubectl apply -f -
```


rajouter dans le hostname les nom dns choisi

