
```
sudo helm repo add argo https://argoproj.github.io/argo-helm
sudo helm install argocd-infra  argo/argo-cd --set notifications.enabled=false --set dex.enabled=false --set redis.enabled=true --set server.replicas=0 --set configs.cm.admin.enabled=false --set - configs.repositories.gitrepo.url=https://github.com/ThomasInfras/k8s-automated-kind --set applicationSet.replicas=0  --namespace argocd-infra --create-namespace


cat <<EOF > argocd-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: github
  namespace: argocd-infra
spec:
  project: default

  source:
    repoURL: 'https://github.com/ThomasInfras/k8s-automated-kind'
    targetRevision: HEAD
    path: argocd.kind.nixos.local/infra

  destination:
    server: 'https://kubernetes.default.svc'
    namespace: argocd-infra

  syncPolicy:
    automated:
      prune: true
      selfHeal: true
EOF
```


