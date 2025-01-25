Pour déployer un kind avec un ingress
```
https://kind.sigs.k8s.io/docs/user/ingress/

cat <<EOF | kind create cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

```



Déployer argocd-infra avec helm

```
sudo helm repo add argo https://argoproj.github.io/argo-helm
sudo helm install argocd-infra  argo/argo-cd --set notifications.enabled=false --set dex.enabled=false --set redis.enabled=true --set server.replicas=1 --set 

configs.cm.admin.enabled=false --set configs.repositories.gitrepo.url=https://github.com/ThomasInfras/k8s-automated-kind --set applicationSet.replicas=0  --namespace argocd-infra --create-namespace



kubectl apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: k8s-argocd-infra
  namespace: argocd-infra
spec:
  ignoreDifferences:
    - group: argoproj.io
      kind: Application
      jsonPointers:
        - /operation
  project: default
  source:
    repoURL: 'https://github.com/ThomasInfras/k8s-automated-kind'
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

rajouter dans le hostname les nom dns choisi

